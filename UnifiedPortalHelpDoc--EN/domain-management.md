#Domain Management
You can see all the domains list created under selected subscription ID.
You can Modify Configurations, Configure Caching Rules and Access Control Management.

![009](images/009.png)

**Modify Origin Domain**

Windows Azure CDN has default caching rules. You can also customize caching rules and customized rules will be applied first. If doesn’t hit customized rules, it will apply default caching rules.

![010](images/010.png)

**Modify caching rules**

- Folder caching rules: Folder must begin with ‘/’, e.g. "/pic", "/doc", "/htdoc/data", caching rules will apply to all sub folders.
- File caching rules: Use common suffix, e.g. "jpg", "png", "gif", "txt", "m4v", "mp3", caching rules will apply to all files with set suffix under specified folder.
- Caching order: Caching rules follow first-set, first apply. 

![011](images/011.png)

**Access Control Management**

Can configure Referer Block List and Allowed List to achieve anti-stealing link.
Each rule applies to both folder and files, e.g. all png files under /*.png.
Block list: when Referer is in block list, access is denied. Otherwise can do.
Allow list: only when Referer is in allow list, access is allowed. Otherwise, access denied.

![012](images/012.png)
