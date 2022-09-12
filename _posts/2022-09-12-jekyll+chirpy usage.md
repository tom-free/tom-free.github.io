---
title: Jekyll+Chirpy模板博客搭建
author: 付瑞彪
date: 2022-09-12 22:49:00 +0800
categories: [博客, 教程]
tags: [博客搭建]
pin: true
---

## 前提条件

按照 [Jekyll安装说明](https://jekyllrb.com/docs/installation/) 安装 `Ruby`、`RubyGems`、`Jekyll`和`Bundler`。 另外，也需要安装 [Git](https://git-scm.com/) 。

## 安装

### 创建新站点

有两个途径创建Chirpy主题的仓库:

- **使用Chirpy Starter获取** - Easy to upgrade, isolates irrelevant project files so you can focus on writing.
- **从GitHub获取** - Convenient for custom development, but difficult to upgrade. Unless you are familiar with Jekyll and are determined to tweak or contribute to this project, this approach is not recommended.

#### Option 1. 使用Chirpy Starter获取

Create a new repository from the [**Chirpy Starter**][use-starter] and name it `<GH_USERNAME>.github.io`, where `GH_USERNAME` represents your GitHub username.

#### Option 2. 从GitHub获取

[Fork **Chirpy**](https://github.com/cotes2020/jekyll-theme-chirpy/fork) on GitHub and rename it to `<GH_USERNAME>.github.io`. Please note that the default branch code is in development.  If you want the site to be stable, please switch to the [latest tag][latest-tag] and start writing.

And then execute:

```console
$ bash tools/init.sh
```

> If you don't want to deploy your site on GitHub Pages, append option `--no-gh` at the end of the above command.
{: .prompt-info }

The above command will:

1. Removes some files or directories from your repository:
    - `.travis.yml`{: .filepath}
    - files under `_posts`{: .filepath}

2. If the option `--no-gh` is provided, the directory `.github`{: .filepath} will be deleted. Otherwise, set up the GitHub Action workflow by removing the extension `.hook`{: .filepath} of `.github/workflows/pages-deploy.yml.hook`{: .filepath}, and then remove the other files and directories in the folder `.github`{: .filepath}.

3. Removes item `Gemfile.lock` from `.gitignore`{: .filepath}.

4. Creates a new commit to save the changes automatically.

### 安装依赖文件

Before running for the first time, go to the root directory of your site, and install dependencies as follows:

```console
$ bundle
```

## 使用说明

### 配置

Update the variables of `_config.yml`{: .filepath} as needed. Some of them are typical options:

- `url`
- `avatar`
- `timezone`
- `lang`

### 自定义样式表

If you need to customize the stylesheet, copy the theme's `assets/css/style.scss`{: .filepath} to the same path on your Jekyll site, and then add the custom style at the end of the style file.

Starting from [`v4.1.0`][chirpy-4.1.0], if you want to overwrite the SASS variables defined in `_sass/addon/variables.scss`{: .filepath}, create a new file `_sass/variables-hook.scss`{: .filepath} and assign new values to the target variable in it.

### 自定义静态资源

Static assets configuration was introduced in version `5.1.0`. The CDN of the static assets is defined by file `_data/assets/cross_origin.yml`{: .filepath }, and you can replace some of them according to the network conditions in the region where your website is published.

Also, if you'd like to self-host the static assets, please refer to the [_chirpy-static-assets_](https://github.com/cotes2020/chirpy-static-assets#readme).

### 运行本地服务器

You may want to preview the site contents before publishing, so just run it by:

```console
$ bundle exec jekyll s
```

Or run the site on Docker with the following command:

```console
$ docker run -it --rm \
    --volume="$PWD:/srv/jekyll" \
    -p 4000:4000 jekyll/jekyll \
    jekyll serve
```

After a while, the local service will be published at _<http://127.0.0.1:4000>_.

## 部署

Before the deployment begins, check out the file `_config.yml`{: .filepath} and make sure the `url` is configured correctly. Furthermore, if you prefer the [**project site**](https://help.github.com/en/github/working-with-github-pages/about-github-pages#types-of-github-pages-sites) and don't use a custom domain, or you want to visit your website with a base URL on a web server other than **GitHub Pages**, remember to change the `baseurl` to your project name that starts with a slash, e.g, `/project-name`.

Now you can choose ONE of the following methods to deploy your Jekyll site.

### 使用`GitHub Actions`部署

Ensure your Jekyll site has the file `.github/workflows/pages-deploy.yml`{: .filepath}. Otherwise, create a new one and fill in the contents of the [sample file][workflow], and the value of the `on.push.branches` should be the same as your repo's default branch name. And then rename your repository to `<GH_USERNAME>.github.io` on GitHub.

Now publish your Jekyll site:

1. Browse to your repository on GitHub. Select the tab _Settings_, then click _Pages_ in the left navigation bar. Then, in the **Source** section (under _Build and deployment_), select [**GitHub Actions**][pages-workflow-src] from the dropdown menu.

2. Push any commit to remote to trigger the GitHub Actions workflow. In the _Actions_ tab of your repository, you should see the "Deploy Jekyll with GitHub Pages" workflow with at least one job running. Once the build is complete and successful, the site should be deployed automatically.

3. Visit your website at the address indicated by GitHub.

### 手动构建和部署

On self-hosted servers, you cannot enjoy the convenience of **GitHub Actions**. Therefore, you should build the site on your local machine and then upload the site files to the server.

Go to the root of the source project, and build your site as follows:

```console
$ JEKYLL_ENV=production bundle exec jekyll b
```

Or build the site on Docker:

```console
$ docker run -it --rm \
    --env JEKYLL_ENV=production \
    --volume="$PWD:/srv/jekyll" \
    jekyll/jekyll \
    jekyll build
```

Unless you specified the output path, the generated site files will be placed in folder `_site`{: .filepath} of the project's root directory. Now you should upload those files to the target server.

## 升级

It depends on how you use the theme:

- If you are using the theme gem (there will be `gem "jekyll-theme-chirpy"` in the `Gemfile`{: .filepath}), editing the `Gemfile`{: .filepath} and update the version number of the theme gem, for example:

  ```diff
  - gem "jekyll-theme-chirpy", "~> 3.2", ">= 3.2.1"
  + gem "jekyll-theme-chirpy", "~> 3.3", ">= 3.3.0"
  ```
  {: .nolineno file="Gemfile" }

  And then execute the following command:

  ```console
  $ bundle update jekyll-theme-chirpy
  ```

  As the version upgrades, the critical files (for details, see the [Startup Template][starter]) and configuration options will change. Please refer to the [Upgrade Guide](https://github.com/cotes2020/jekyll-theme-chirpy/wiki/Upgrade-Guide) to keep your repo's files in sync with the latest version of the theme.

- If you forked from the source project (there will be `gemspec` in the `Gemfile`{: .filepath} of your site), then merge the [latest upstream tags][latest-tag] into your Jekyll site to complete the upgrade.
The merge is likely to conflict with your local modifications. Please be patient and careful to resolve these conflicts.

[starter]: https://github.com/cotes2020/chirpy-starter
[use-starter]: https://github.com/cotes2020/chirpy-starter/generate
[workflow]: https://github.com/cotes2020/jekyll-theme-chirpy/blob/master/.github/workflows/pages-deploy.yml.hook
[chirpy-4.1.0]: https://github.com/cotes2020/jekyll-theme-chirpy/releases/tag/v4.1.0
[pages-workflow-src]: https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site#publishing-with-a-custom-github-actions-workflow
[latest-tag]: https://github.com/cotes2020/jekyll-theme-chirpy/tags
