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

---

## The Script

```python
import os
import yaml

# The base directory for the Hugo content
base_directory = '/home/daniel/Git/daniel-goes-prompting-v3/content'

# Function to create _index.md if it doesn't exist
def create_index_file(directory):
    index_file_path = os.path.join(directory, '_index.md')
    folder_name = os.path.basename(directory).replace('-', ' ').title()
    yaml_frontmatter = f'---\ntitle: "{folder_name}"\n---\n'

    if not os.path.exists(index_file_path):
        # Create _index.md with frontmatter
        with open(index_file_path, 'w') as f:
            f.write(yaml_frontmatter)
            f.write('\n')  # Add an extra newline to separate frontmatter from content

        print(f'Created: {index_file_path}')  # For debugging

# Function to extract titles and corresponding links from markdown files
def extract_titles_and_links(directory):
    titles_links = []
    
    # Read all files in the directory
    for file in os.listdir(directory):
        if file.endswith('.md') and file != '_index.md':
            file_path = os.path.join(directory, file)
            with open(file_path, 'r') as f:
                frontmatter_started = False
                yaml_content = []
                for line in f:
                    if line.strip() == '---':
                        if not frontmatter_started:
                            frontmatter_started = True  # Start of YAML frontmatter
                        else:
                            break  # End of YAML frontmatter
                    elif frontmatter_started:
                        yaml_content.append(line)
                
                # Parse YAML content
                yaml_data = yaml.safe_load(''.join(yaml_content))
                title = yaml_data.get('title', 'No Title')
                link = os.path.splitext(file)[0]  # Remove .md extension for link
                titles_links.append((title, link))

    # Sort and remove duplicates
    titles_links = sorted(set(titles_links))  # Sort by title, remove duplicates
    return titles_links

# Function to create bullet points in _index.md
def update_index_file_with_bullets(directory):
    index_file_path = os.path.join(directory, '_index.md')
    
    if os.path.exists(index_file_path):
        titles_links = extract_titles_and_links(directory)
        bullet_points = [f'- [{title}]({link})' for title, link in titles_links]

        # Read existing content of _index.md (excluding YAML frontmatter)
        with open(index_file_path, 'r') as f:
            lines = f.readlines()

        # Find where the YAML frontmatter ends (after the second "---")
        yaml_end_idx = next(i for i, line in enumerate(lines) if line.strip() == '---' and i > 0)
        
        # Write back the YAML and append the new bullet points
        with open(index_file_path, 'w') as f:
            f.writelines(lines[:yaml_end_idx + 1])  # Keep YAML frontmatter intact
            f.write('\n' + '\n'.join(bullet_points) + '\n')  # Append bullet points

        print(f'Updated: {index_file_path}')  # For debugging

# Traverse directories recursively to create or update _index.md files
for root, dirs, files in os.walk(base_directory):
    create_index_file(root)  # Ensure _index.md exists
    update_index_file_with_bullets(root)  # Update it with bullet points
```