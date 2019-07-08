# important-python-scripts
Important Python Scripts

## Index
* [Download file from Google Drive](#download-file-from-google-drive)
* [Edit individual files on server using Sublime Text using RemoteSubl](#edit-individual-files-on-server-using-Sublime-Text-using-remotesubl)
* [Docker Cheat Sheet](https://github.com/wsargent/docker-cheat-sheet#docker-cheat-sheet)


### Edit individual files on server using Sublime Text using RemoteSubl

**On server**

```
curl -o /usr/local/bin/rmate https://raw.githubusercontent.com/aurora/rmate/master/rmate
sudo chmod +x /usr/local/bin/rmate
```
**On local**

1. Install rsub Sublime3 package:
On Sublime Text 3, open Package Manager (Ctrl-Shift-P on Linux/Win, Cmd-Shift-P on Mac, Install Package), and search for **RemoteSubl** and install it

2. Open command line and connect to remote server:
``` ssh -R 52698:localhost:52698 user@example.com ```

To make your life easier, add the following to ``` ~/.ssh/config ```
```
Host example.com
    RemoteForward 52698 localhost:52698
    User user
```
From now on, you only have to do ``` ssh example.com ```

3. After connect to server run this command on server:
``` rmate test.txt ```

4. File opening auto in Sublime 3


### Download file from Google Drive

```python
import requests, zipfile, os

def download_file_from_google_drive(id, destination):
    URL = "https://docs.google.com/uc?export=download"
    session = requests.Session()
    response = session.get(URL, params = { 'id' : id }, stream = True)
    token = get_confirm_token(response)
    if token:
        params = { 'id' : id, 'confirm' : token }
        response = session.get(URL, params = params, stream = True)
    save_response_content(response, destination)    
    
def get_confirm_token(response):
    for key, value in response.cookies.items():
        if key.startswith('download_warning'):
            return value
    return None

def save_response_content(response, destination):
    CHUNK_SIZE = 32768
    with open(destination, "wb") as f:
        for chunk in response.iter_content(CHUNK_SIZE):
            if chunk: # filter out keep-alive new chunks
                f.write(chunk)

def unzip_file(file_name, unzip_path):
    zip_ref = zipfile.ZipFile(file_name, 'r')
    zip_ref.extractall(unzip_path)
    zip_ref.close()
    os.remove(file_name)
    
# https://drive.google.com/file/d/1Aeng7Opa7efzXEJeyMSZFFBjumcr9X3r/view?usp=sharing 

file_id = '1Aeng7Opa7efzXEJeyMSZFFBjumcr9X3r'
file_name = 'test_zip.zip'
chpt_path = os.getcwd() # Download directory
destination = os.path.join(chpt_path, file_name)

download_file_from_google_drive(file_id, destination) # Downloads file
unzip_file(destination, chpt_path) # Unzips the file, deletes the zip
```
