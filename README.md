### How to Run the Project
#### Install Dependencies
1. pull the submodules
```bash
git submodule update --init --recursive
``` 
2. install the dependencies
```bash
pnpm install
```
3. install hexo cli
```bash
pnpm install -g hexo-cli
```
#### Devlopment to Run the Project
```bash
hexo clean && hexo generate
hexo server
```
#### Production to deploy the Project
```bash
hexo clean && hexo generate
hexo deploy
```