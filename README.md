# General useful utility functions for Python projects.

These are functions I use every day. Ideally they should make their way into the standard library.
I understand we cannot put everything in the standard library, hence this package. ;)

The various modules have the following requirements:

```
# util_email
imapclient
pyzmail36

# util_fs
text-unidecode

# util_ftp
ftputil
paramiko

# util_image
pillow

# util_logging
logging_tree

# showfile
python-magic
```

If you only need one module it is recommended you install from source and manage the requirments yourself.


## Example usage:

First we have a JSON improvement over netrc.

```python
from lcutil.netrc import Netrc

# If we start with the following example ~/netrc.json file.
"""
{
    "smtp" : {
        "host": "mail.example.com",
        "port": 587,
        "username": "alice@example.com",
        "password": "password",
        "from": "alice@example.com",
        "to": "bob@example.com"
    }
}
"""

# Select a new non-existing section.
netrc = Netrc(path='~/netrc.json', section='example-ftp')

# Attribute access.
netrc.host = 'ftp.example.com'
netrc.port = 21
netrc.username = 'louis'
netrc.password = 'supers3cr3tpassword'
netrc.save()

# Will result in the updated netrc.json file.
"""
{
    "smtp": {
        "host": "mail.example.com",
        "port": 587,
        "username": "alice@example.com",
        "password": "password",
        "from": "alice@example.com",
        "to": "bob@example.com"
    },
    "example-ftp": {
        "host": "ftp.example.com",
        "port": 21,
        "username": "louis",
        "password": "supers3cr3tpassword"
    }
}
"""
```

Then we can use the secret credentials stored in ~/netrc.json.

```python
from PIL import Image

from lcutil import util_email as ue
from lcutil import util_image as ui
from lcutil.netrc import Netrc


face = ui.scale_width(Image.open('face.jpg'), 100)
back = ui.scale_width(Image.open('back.jpg'), 100)

stiched = ui.stitch_vertical(face, back)
stiched.save('/tmp/stiched.jpg')

creds = Netrc(section='smtp')

ue.html_email(from_='me <me@example.com>',
              to=['alice <alice@example.com>'],
              bcc=['bob <bob@example.com>'],
              subject='Here is an image for you',
              html_body='<html><body><h1>Hello World!</h1></body></html>',
              files=['/tmp/stiched.jpg'],
              smtp_server=creds.host,
              smtp_port=creds.port,
              smtp_username=creds.username,
              smtp_password=creds.password)
```
