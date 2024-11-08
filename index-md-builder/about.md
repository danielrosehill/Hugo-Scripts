# Hugo `_index.md` Generation Script (With Link Pages)

This script is for a website built with the [Hextra](https://imfing.github.io/hextra/) theme by [imfing](https://github.com/imfing).

Its purpose is to build index pages for folders which contain links to all the markdown files nested under them. 

## Example Usage

Here's an example of what it generates. The links were filled by the script:

![alt text](../images/1.png)

It was "written" by AI with my instructions ... you have been warned.

## Script Steps / Functionalities

Its purpose is to try do perform a few actions in quick sequence:

1: It establishes the base directory for the Hugo content repository (note: this is hard-coded so you'll need to edit it for your repo)  

Change this line:

```
base_directory = '/home/daniel/Git/daniel-goes-prompting-v3/content'
```

2: It creates missing `_index.md` files if they're not there. 

3: It tries to give the `_index.md` files frontmatter that looks good. The idea here is to allow the author (in this case, me!) to use robust folder-naming (ie, no spaces) but be able to make the visible text a bit nicer looking and easier to read. 

4: Within each `_index.md` it creates a bullet point formatted list of all the files in that folder. The link titles are (supposed to be) pulled from the YAML front-matter of the files. If titles are already set, the script is (supposed to!) not tamper with them (this is in order to allow for manual review and updating).

5: It scrubs for duplicates and organises the files in alphabetical order. 

6: I told it to sing and dance after running but instead it just exits. Somehow, it appears to actually work!