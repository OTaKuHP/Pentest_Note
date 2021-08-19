# GitHub

## Tools

```text
# GitDump
https://github.com/Ebryx/GitDump
# GitDumper 
  https://github.com/internetwache/GitTools
  If we have access to .git folder: 
  ./gitdumper.sh http://example.com/.git/ /home/user/dump/ 
  git cat-file --batch-check --batch-all-objects | grep blob git cat-file -p HASH
# GitGot 
  https://github.com/BishopFox/GitGot
  ./gitgot.py --gist -q CompanyName./gitgot.py -q '"example.com"'./gitgot.py -q "org:github cats"
# GitRob https://github.com/michenriksen/gitrob
  gitrob website.com
# GitHound https://github.com/tillson/git-hound 
  echo "domain.com" | githound --dig --many-results --languages common-languages.txt --threads 100
# GitGrabber https://github.com/hisxo/gitGraber
# SSH GIT https://shhgit.darkport.co.uk/
# GithubSearch
  https://github.com/gwen001/github-search
# Trufflehog
trufflehog https://github.com/Plazmaz/leaky-repo
trufflehog --regex --entropy=False https://github.com/Plazmaz/leaky-repo
# If you have public .git
https://github.com/HightechSec/git-scanner
# GitMiner
# wordpress configuration files with passwords
  python3 gitminer-v2.0.py -q 'filename:wp-config extension:php FTP\_HOST in:file ' -m wordpress -c pAAAhPOma9jEsXyLWZ-16RTTsGI8wDawbNs4 -o result.txt
# brasilian government files containing passwords
  python3 gitminer-v2.0.py --query 'extension:php "root" in:file AND "gov.br" in:file' -m senhas -c pAAAhPOma9jEsXyLWZ-16RTTsGI8wDawbNs4
# shadow files on the etc paste
  python3 gitminer-v2.0.py --query 'filename:shadow path:etc' -m root -c pAAAhPOma9jEsXyLWZ-16RTTsGI8wDawbNs4
# joomla configuration files with passwords 
  python3 gitminer-v2.0.py --query 'filename:configuration extension:php "public password" in:file' -m joomla -c pAAAhPOma9jEsXyLWZ-16RTTsGI8wDawbNs4
  
# GitLeaks
sudo docker pull zricethezav/gitleaks
sudo docker run --rm --name=gitleaks zricethezav/gitleaks -v -r https://github.com/zricethezav/gitleaks.git
or (repository in /tmp)
sudo docker run --rm --name=gitleaks -v /tmp/:/code/ zricethezav/gitleaks -v --repo-path=/code/repository

# GitJacker - for exposed .git paths
# https://github.com/liamg/gitjacker
curl -s "https://raw.githubusercontent.com/liamg/gitjacker/master/scripts/install.sh" | bash
gitjacker url.com

# Then visualize a commit:
https://github.com/[git account]/[repo name]/commit/[commit ID]
https://github.com/zricethezav/gitleaks/commit/744ff2f876813fbd34731e6e0d600e1a26e858cf

# Manual local checks inside repository
git log
# Checkout repo with .env file
git checkout f17a07721ab9acec96aef0b1794ee466e516e37a
ls -la
cat .env

# Find websites from GitHub
https://github.com/Orange-Cyberdefense/versionshaker
```



