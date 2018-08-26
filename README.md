# A coder's blog
> Thinking, coding and writing. Giving, loving and sharing.
## What
Code for [http://www.gistop.com](http://www.gistop.com/)
## Command lines
### Clone
```bash
git clone --recursive git@github.com:hsuehic/blog.git
git submodule update --init --recursive
git submodule foreach --recursive git checkout master
git submodule foreach git pull
```

### Write
```bash
hexo server
```
### Generate
```bash
hexo generate
```
### Deploy
```bash
hexo deploy
```