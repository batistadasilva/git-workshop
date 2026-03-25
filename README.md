## Creating a Git repo

```sh
mkdir path/to/repo
cd path/to/repo
git init
ls -al .git # The entire repository is stored here
```

## Creating a blob object

1. Create your `README.md` file, using the editor

    ```
    # Git workshop

    Customize your text here...
    ```

1. Start the Python console, then run the following

    ```python
    import os
    import hashlib
    import zlib

    content = open("README.md", "rb").read()

    # Generate header
    content_type = "blob"
    content_size = len(content)
    header = f"{content_type} {content_size}".encode("utf8")

    # Create blob object
    blob_object = header + b"\0" + content

    # Compute object SHA1
    h = hashlib.sha1()
    h.update(blob_object)
    sha1 = h.hexdigest()

    # Compute filepath
    dirpath = f".git/objects/{sha1[0:2]}"
    filename = sha1[2:]
    os.makedirs(dirpath)

    # Compress & store into Git database
    compressed = zlib.compress(blob_object)
    file = open(f"{dirpath}/{filename}", "wb")
    file.write(compressed)
    file.close()

    # Print the Blob SHA1
    print(sha1)
    ```

1. Read the object from Git database

    Exit the Python console, then run the following command

    ```
    git cat-file -p <sha1>
    ```

    It's supposed to print your `README.md` file content to output

## Creating a tree object

1. Start the Python console, then run the following

    ```python
    import os
    import hashlib
    import zlib

    # Create tree content
    content = b"100644 README.md\0" + bytes.fromhex("< Blob SHA1 >") # Enter the hex value here

    # Generate header
    content_type = "tree"
    content_size = len(content)
    header = f"{content_type} {content_size}".encode("utf8")

    # Create blob object
    tree_object = header + b"\0" + content

    # Compute object SHA1
    h = hashlib.sha1()
    h.update(tree_object)
    sha1 = h.hexdigest()

    # Compute filepath
    dirpath = f".git/objects/{sha1[0:2]}"
    filename = sha1[2:]
    os.makedirs(dirpath)

    # Compress & store into Git database
    compressed = zlib.compress(tree_object)
    file = open(f"{dirpath}/{filename}", "wb")
    file.write(compressed)
    file.close()

    # Print the Tree SHA1
    print(sha1)
    ```

1. Read the object from Git database

    Exit the Python console, then run the following command

    ```
    git cat-file -p <sha1>
    ```

    It's supposed to print the tree

## Creating a commit object

1. Start the Python console, then run the following

    ```python
    import os
    import hashlib
    import zlib
    from datetime import datetime

    # Create commit content
    content = "tree < Tree SHA1 >\n"
    content += f"author < Author name > {int(datetime.now().timestamp())} +0100\n"
    content += f"committer < Committer name > {int(datetime.now().timestamp())} +0100\n"
    content += "\n"
    content += "This is the commit message\n"

    # Generate header
    content_type = "commit"
    content_size = len(content)
    header = f"{content_type} {content_size}".encode("utf8")

    # Create commit object
    commit_object = header + b"\0" + content.encode("utf8")

    # Compute object SHA1
    h = hashlib.sha1()
    h.update(commit_object)
    sha1 = h.hexdigest()

    # Compute filepath
    dirpath = f".git/objects/{sha1[0:2]}"
    filename = sha1[2:]
    os.makedirs(dirpath)

    # Compress & store into Git database
    compressed = zlib.compress(commit_object)
    file = open(f"{dirpath}/{filename}", "wb")
    file.write(compressed)
    file.close()

    # Print the commit SHA1
    print(sha1)
    ```

You have just created a commit without using `git commit`!

```
git show <sha1>
```

## Creating a branch

```sh
echo <sha1> > .git/refs/heads/<branch-name>
```

## Exercise

1. Create a new commit that adds a new file inside a subdirectory

    Simulate the following

    ```sh
    echo 'Hello' > dir/test.txt
    git add dir/test.txt
    git commit -m "Commit message"
    ```
