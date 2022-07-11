# Playbooks

### What are they? 
Playbooks are an in-house project combining operator TTPs and the latest information from MITRE with Jupyter Notebooks. The goal is to be able to have a simple and portable format that works anywhere (home/work/mission). In addition these threat hunting analytics and concepts can be easily shared to the broader CPT community. Playbooks are meant to leverage operator knowledge with data sources from various applications, operating systems, and network traffic in order to identify TTPs that are being used by adversaires.  

These are also used in conjunction with the [836 - Playbook Generator](https://gitlab.90cos.cdl.af.mil/836-cos/playbook-generator) which allows you to update the top half of your playbooks directly from MITRE! Any new additions or updates will also generate and build out a template for you it doesn't exist, and will not overwrite any of your analytics. 

### Why Jupyter Notebooks?

  - Portability (Lightweight JSON files / `.ipynb` )
  - Automation capabilities (Python)
  - Ability to query the kit directly and compare multiple data sources (Python ElasticSearch)
  - Easy to download
  - Easy to update
  - `Git` version control
  - The industry does this as well!... Be like industry!

### Background

We had originally used Confluence in order to hold all of our Playbooks, however there were several problems with that. For starters, the Playbooks were scattered all over the place. There were a plethora of locations within Confluence that had playbooks - some being duplicates, while others had distinct modifications. No one knew where the most up to date playbooks were. Secondly, in order to download them for use, you had to go from section to section to download them manually. This led to several hours spent pulling every copy down and organizing them locally. Furthermore, the files that were downloaded were HTML documents. This did not enable operators who were hunting with actual mission data to update their analytics on the fly, nor did it enable operators to effectively use them while on mission.

In comes Jupyter Notebooks! This tool enables operators to update analytics on-the-fly from a central location. Jupyter Notebooks are JSON files (`.ipynb`), which allows them to be stored in [836 - GitLab](https://gitlab.90cos.cdl.af.mil/836-cos/). Because the files are JSON, it allows for very clean version control, sprints to work on TTPs, and a simple `git clone` to pull down all of the Playbooks. Jupyter Notebooks are very flexible as well, which enables you to run them locally on a laptop using VSCode, or through the Jupyter Lab web application which enables your entire team to update and use them from a central location. Furthermore the primary benefit of using Jupyter Notebooks enables us to perform targeted analytics on large data sets rapidly. Allowing us to reduce lead time of conducting analysis across millions of data points. 

----
# Playbooks - Where do I get them?!

## 0 - Install Git
  - Note: We must have Git installed, so let's check!
  - Note: There is a `git-all` which installs ALL extras. The minimal install `git` should suffice however.
```bash
# Is Git Installed?
git --version

# Installation (Minimal)
sudo apt update && sudo apt install git && git config --global color.ui auto
```
---
## 1 - Generate Git Access Token
  - Note: You need to setup a access token to store locally for your `git` commands. This is a one time operation.
    - Visit: https://gitlab.90cos.cdl.af.mil/-/profile/personal_access_tokens
      - **Token name:**: "JD_Git-Pulls_Work-Laptop" <--- Can be whatever you want.
      - **Expiration date:** *Unset.*
      - **Scope:** "read_repository" + "write_repository"
    - Click: "Create Personal Access Token"
    - Copy: "Your new personal access token" <-- This won't be shown again so make sure you have it saved somewhere!
 --- 
## 2 - Install Jupyter Labs / Notebooks
Note: Open a Ubuntu terminal and perform the following:
```bash
# Check for PIP & Python v>3.7
verCheck="pip --version | grep 'python 3.[7,8,9]'" && if $verCheck; then echo "Good to move on!"; else echo "Check versions!"; fi

# Install Jupyter Labs & Set Password
pip install jupyterlab && jupyter-notebook password

# Start/Background Server
nohup jupyter-notebook --no-browser --port=8888 &

# Open Jupyter Labs in browser
sensible-browser --new-window http://localhost:8888/lab/

```
---
## 3 - Playbook Acquisition
  - > **NOTICE**: You are about to acquire the playbook files (`.ipynb`). **IF** you get an <span style="color:red">**authentication error**</span> after attempting your clone operation, you'll need to acquire the DOD root certificates. See the instructions expandable section below.
    - <details>
      <summary>DOD Root Certificates - Installation Instructions</summary>

      ## --== Only if needed! ===--
      1. **Visit**: [90 GitLab - Desktop Anywhere](https://gitlab.com/90cos/public/desktop-anywhere)
      2. **Download** Files: 
         * [addcerts-linux.sh](https://gitlab.com/90cos/public/desktop-anywhere/-/raw/master/addcerts-linux.sh?inline=false)
         * [addcerts-firefox.sh](https://gitlab.com/90cos/public/desktop-anywhere/-/raw/master/addcerts-firefox.sh?inline=false)
         * [addcerts-chrome.sh](https://gitlab.com/90cos/public/desktop-anywhere/-/raw/master/addcerts-chrome.sh?inline=false)
      3. **Install** certificates:
        ```bash
        # Install Certs!
        # Note: You can ignore "certutil: function failed: SEC_ERROR_BAD_DATABASE: security library: bad database."
        sudo bash ./addcerts-linux.sh && sudo bash ./addcerts-firefox.sh; sudo bash ./addcerts-chrome.sh
    
        # Install Certutil (If Needed)
        # Note: This is only if the above command fails to run because you do NOT have Certutil installed.
        sudo apt update && sudo apt install libnss3-tools
        ```
   
    </details>

### Option **A** - Jupyter Labs
  - Install the Git Add-on:
  ```bash
  # Note: Ensure Jupter Labs server is SHUTDOWN before entering.
  pip install --upgrade jupyterlab jupyterlab-git
  nohup jupyter-notebook --no-browser --port=8888 &
  ```
  - **Click**: "Git" on left-hand side --> "Clone a Repository"
  - **Copy/Paste**: "https://gitlab.90cos.cdl.af.mil/836-cos/playbooks.git"
  - **Enter** your username.
  - **Enter** your password. (Use your access token from above!)
  - **Click**: "Save my login temporarily"
  - **Navigate**: To the "playbooks" folder.


### Option **B** - Microsoft Visual Code Studio
  - Clone the Repo:
    - **Press**: *Ctrl+Shift+P*
    - **Type**: `Git:Clone` + *Enter*
    - **Copy/Paste**: "https://gitlab.90cos.cdl.af.mil/836-cos/playbooks.git" + *Enter*
    - **Select** your directory for the local repository copy of the playbooks, IE: `/home/assessor/Downloads`
    - **Enter** your username.
    - **Enter** your password. (Use your access token from above!)
    - **Click**: Bottom-right corner of VS Code - select: "Add to your Workspace". (Trust source if asked.)
  - Store your creds:
    - **Press**: *Ctrl+~*
    - **Type**: `git config --global credential.helper store`


### Option **C** - Directly in CLI
```bash
cd /home/assessor/Downloads/ && mkdir "836_Working-Folder" && cd "836_Working-Folder"
git config user.name "John Doe"
git config user.email "john.doe.1@us.af.mil"
git clone "https://gitlab.90cos.cdl.af.mil/836-cos/playbooks.git" && cd playbooks/ && git status && ls -l
# Enter your username.
# Enter your password. (Use your access token from above!)
# Enter CMD below to store creds.
git config --global credential.helper store
```
---
## 4 - Playbook Modification(s)

### Option **A** - Jupyter Labs
1. Create your Working Branch
   - **Click**: "Git" on left-hand side
   - **Click**: "Current Branch - master" --> "New Branch"
   -  **Enter**: A relevant name, IE: "T1005 Host Addition - LastName" --> "Create Branch"
2. Modify / Create TTPs
    - **Open** TTP - IE: `T1005 Data From Local System.ipynb` via file browser (*Ctrl+Shift+F*)
    - **Modify** Cell(s) --> Double-click / *Shift-Enter* shifts between Jupyter clean-view & markdown for modification.
    - **Save**: *Ctrl+S* 
3. Push to Git
    - **Click**: "Git" on left-hand side
    - **Select**: The files you changes / wish to upload under "Changed" & right-click --> "Stage"
    - **Enter**: A relevant name, IE: "T1005 Host Addition - LastName" under "Summary"
    - **Click**: "COMMIT"
    - **Click**: "Publish branch" near the top under "Kernel".
      - - Note: You may push to your branch, modify, or delete it but it will NOT become a part of the master branch until you create a merge request and the appropriate parties approve it.

### Option **B** - Microsoft Visual Code Studio
1. Enable Jupyter Extension
    - **Press**: *Ctrl+Shift+X*
    - **Type**: "Jupyter"
    - **Click**: *Install* on "Jupyter" by Microsoft.
2. Create your Working Branch
    - **Click**: On "master*" in the bottom-left corner --> Select "Create new branch..." towards the top.
    - **Enter**: A relevant name, IE: "T1005 Host Addition - LastName" --> *Enter*
3. Modify / Create TTPs
    - **Open** TTP - IE: `T1005 Data From Local System.ipynb`
    - **Modify** Cell(s) --> Double-click / *Esc* shifts between Jupyter clean-view & markdown for modification.
    - **Save**: *Ctrl+S* 
4. Push to Git
    - **Press**: *Ctrl+Shift+G"
    - **Select**: The files you changes / wish to upload & right-click --> "Stage Changes"
    - **Enter**: A relevant message, IE: "Modified T1005 Host Analytics." --> *Ctrl+Enter*
    - **Click**: "Publish Branch"
      - Note: You may push to your branch, modify, or delete it but it will NOT become a part of the master branch until you create a merge request and the appropriate parties approve it.

### Option **C** - Directly in CLI
```bash
# -= WIP =-
# Note: Will populate for those choosing to modify .ipynb files with their own IDE / text editor.
```

----
# Playbooks - Training Resources

- Git:
  - [DigitalU (UDemy) - Git Complete](https://digitalu.udemy.com/course/git-complete/) <-- Thorough (6h)
  - [Git - Basic Branching & Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
  - [GitLab - CLI Basics](https://docs.gitlab.com/ee/gitlab-basics/start-using-git.html)
  - [GitLab - Git Cheat Sheet](https://about.gitlab.com/images/press/git-cheat-sheet.pdf)
  - [GitLab - Useful CMDs](https://docs.gitlab.com/ee/topics/git/useful_git_commands.html)
  - [Oh Shit - Git](https://ohshitgit.com/) <-- Make a mistake?!
  - [Roger Dudler's - Simple Git Guide](https://rogerdudler.github.io/git-guide/)
- Jupyter Notebooks:
  - [DataQuest - How to use Jupyter Notebooks](https://www.dataquest.io/blog/jupyter-notebook-tutorial/)
  - [MyBinder - Threat Hunter Playbooks](https://mybinder.org/v2/gh/OTRF/ThreatHunter-Playbook/master) <-- Browser loadable Jupyter Notebooks w/example analytics below!!!
  - [Threat Hunter Playbook - Example Analytics](https://threathunterplaybook.com/notebooks/windows/intro.html) <-- Example analytics/methods you can use.
  - [Threat Hunter Playbook - Jupyter Notebooks Basics](https://threathunterplaybook.com/tutorials/jupyter/introduction.html) <-- Install + NumPy/Pandas
- Visual Studio Code:
  - [MS - Use Git in VS Code](https://docs.microsoft.com/en-us/learn/modules/use-git-from-vs-code/)

----
# Other Relevant Resources

- Connecting Jupyter Notebooks --> Elasticsearch (Python): 
    - [Elastic - Connecting w/Python API](https://www.elastic.co/guide/en/elasticsearch/client/python-api/master/connecting.html)
    - [ElasticSearch-PY - Python Elasticsearch Client](https://elasticsearch-py.readthedocs.io/en/v7.13.4/)
    - [InstaClustr - Connecting to ES w/Python](https://www.instaclustr.com/support/documentation/elasticsearch/using-elasticsearch/connecting-to-elasticsearch-with-python/)
    - [ObjectRocket - How to connect Python to ES](https://kb.objectrocket.com/elasticsearch/how-to-connect-the-python-client-to-elasticsearch)
    - [TryoLabs - Python + ES, First steps](https://tryolabs.com/blog/2015/02/17/python-elasticsearch-first-steps/)
    - > Note: There are many ways to go about this (some better than others). Please reference Johnson's scripts for some of the current best ways to go about data retrieval from a kit.

- Threat Emulation:
  * [Blogspot - How to use Covenant](https://3xpl01tc0d3r.blogspot.com/2020/02/gadgettojscript-covenant-donut.html)
  * [GitHub - Covenant](https://github.com/cobbr/Covenant)
  * [Security Datasets - Intro](https://securitydatasets.com/introduction.html) <-- Previously MordorDataSets
  * [SpecterOps - Enter Mordor](https://posts.specterops.io/enter-mordor-pre-recorded-security-events-from-simulated-adversarial-techniques-fdf5555c9eb1) <-- Discusses Mordor(SecurityDatasets), HELK, and Covenant!

- Other:
  * [GitHub - Sigma](https://github.com/SigmaHQ/sigma) <-- Generic signiture format that can auto-convert to ES/Kibana/PS/Splunk/etc.