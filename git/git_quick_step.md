# 1 git仓库创建之后要做的事
### Get started by creating a new file pr updating an existing file.We recommendevery repository include a README,LICENSE, and .gitignore.

## 1.1 Create a new repository on the command line
```
curl -u "vodaka" https://api.github.com/user/repos -d '{"name":"repo_name","description":"Descriptions for this repo."}'
echo "# dockerfile" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/vodaka/dockerfile.git
git push -u origin master
```

## 1.2 Push an existing repository from the command line
```
git remote add origin https://github.com/vodaka/dockerfile.git
git push -u origin master
```

## 1.3 Import code from another repository
### You can initialize this reposity with code from a Subversion, Mercurial, or TFS project.

