title: github - webhook服务签名算法 - nodejs
date: 2015-08-24 20:16:27
tags: webhook github nodejs
---

## 前言

`webhook`是`github`推出的一项免费服务，当仓库出现变动（`push`、`issue`变动等）的时候，会向仓库设置的指定地址，发送一个post请求，请求包括非常多的内容。

我自己开发的`weichat.io`部署在`DigitalOcean`上面，每次更新代码都要远程登录到服务器上手动更新代码。

我初中(2004-2007)的一个数学老师，曾经说过，聪明的人都是很懒的，当时用来举例如何用最简单的方式解决题目。近几年也有看到文章讲过类似的话。正因为懒，所以才会有这么多工具出现。

说了几句题外话，言归正传。一开始我找的方式是`git hook`，指定某个行为之后执行某个脚本，脚本就是`shell`。在网上搜了很多文章，觉得还是不够优雅。

无意中看到`github`提供的`webhook`服务，设置方便，还可以配置签名。


## 设置

设置如下：
1. 进入仓库设置页面
![进入仓库设置页面][1]

2. 进入`Webhooks & services`设置选项![进入`Webhooks & services`设置选项][2]

3. 设置页面![设置页面][3]
4. 设置内容![设置内容][4]
    - 第一个设置是`github`将会发信息的地址，请求类型是`post`，内容很多，包括此次提交的信息，提交的人等信息。
    - 第二个是发送请求的`Content type`，可以是`application/json`或者`application/x-www-form-urlencoded`，也就是一个过来的是`json`一个是`form`形式，看自己选择。
    - 第三个`Secret`，用来加密匹配的，在发送请求的头里面有一个字段`x-hub-signature`里面是对请求的`body`计算`sha1`的值。
    - 注意：`Secret`计算的`body`的值，是以`buffer`(对于nodejs来说，也就是没有`decode`过请求的内容)值来做的。同时`x-hub-signature`字段在`headers`里面是有大小写的，只不过在`express`里面经过中间件之后全部变成小写了。


## 示例代码

```js
// express，直接给出req和res。
// 可以放在自己的controller里面，或者直接写在路由里
// 直接写在路由里:
// app.post('/update',function(req,res){
//     // 下面函数的内容
// })

var express = require('express');
var router = express.Router();

var shell = require('shelljs');
var crypto = require('crypto');

router.post('/', function ( req,res,next ) {

	console.log(req);


    // abcdefg 这个是在github那里设置的Secret
	var hmac = crypto.createHmac('sha1','abcdefg');

	// 注意：这里已经被express中间件转化为对象了，需要先转换成字符串再恢复为一个Buffer对象，然后进行的算法才是正确的
	hmac.update(new Buffer(JSON.stringify(req.body)));
	console.log(req.body);
	var signature = 'sha1=' + hmac.digest('hex');

    // 这里已经全都变成小写了，实际上首字符都是大写的
	var _signature = req.headers['x-hub-signature'];

	console.log(signature);
	console.log(_signature);

	if(req.headers['x-hub-signature'] != signature){
		console.log('匹配失败');
		return res.end()
	}

    // github会记录每次推送的结果，就像访问一个页面一样，如果服务器成功接受建议返回一点内容，不要404
	res.end('ok');

	/*  下面只是实例代码，基本更新流程就是这样
	*   如果使用多个分支的话，需要自行判断更新的分支
	*   不然开发分支的push会造成生产环境分支也更新重启
	*/

    // 打印当前目录
	shell.pwd();

	// fetch + merge
	shell.exec('git pull');

	// 重启pm2
	shell.exec('pm2 restart all')

});

module.exports = router;
```


## webhook记录每次推送
+ 推送结果显示![推送结果显示][5]

+ 推送失败的显示![推送失败显示][6]

## webhook服务推送请求的body样子

以下是我测试时,`webhook`发送过来的一个请求的`body`的内容。

+ 我这里更新了dev分支
+ 我的用户名是`sinoon`
+ `https://github.com/sinoon/weichat.io`是仓库地址
+ 熟悉`git`的人，可以从请求里面看出很多东西

```json
{ ref: 'refs/heads/dev',
  before: '08babdb11f190bdff563c23ad9643385e18a3b2b',
  after: '2ea1bf7b521c652bab1e0a8aee3751d3bc7688bc',
  created: false,
  deleted: false,
  forced: false,
  base_ref: null,
  compare: 'https://github.com/sinoon/weichat.io/compare/08babdb11f19...2ea1bf7b521c',
  commits:
   [ { id: '2ea1bf7b521c652bab1e0a8aee3751d3bc7688bc',
       distinct: true,
       message: '* 测试webhook签名',
       timestamp: '2015-08-23T18:49:26+08:00',
       url: 'https://github.com/sinoon/weichat.io/commit/2ea1bf7b521c652bab1e0a8aee3751d3bc7688bc',
       author: [Object],
       committer: [Object],
       added: [],
       removed: [],
       modified: [Object] } ],
  head_commit:
   { id: '2ea1bf7b521c652bab1e0a8aee3751d3bc7688bc',
     distinct: true,
     message: '* 测试webhook签名',
     timestamp: '2015-08-23T18:49:26+08:00',
     url: 'https://github.com/sinoon/weichat.io/commit/2ea1bf7b521c652bab1e0a8aee3751d3bc7688bc',
     author:
      { name: 'sinoon',
        email: 'sinoon1218@gmail.com',
        username: 'sinoon' },
     committer:
      { name: 'sinoon',
        email: 'sinoon1218@gmail.com',
        username: 'sinoon' },
     added: [],
     removed: [],
     modified: [ 'package.json' ] },
  repository:
   { id: 41168931,
     name: 'weichat.io',
     full_name: 'sinoon/weichat.io',
     owner: { name: 'sinoon', email: 'sinoon1218@gmail.com' },
     private: false,
     html_url: 'https://github.com/sinoon/weichat.io',
     description: '',
     fork: false,
     url: 'https://github.com/sinoon/weichat.io',
     forks_url: 'https://api.github.com/repos/sinoon/weichat.io/forks',
     keys_url: 'https://api.github.com/repos/sinoon/weichat.io/keys{/key_id}',
     collaborators_url: 'https://api.github.com/repos/sinoon/weichat.io/collaborators{/collaborator}',
     teams_url: 'https://api.github.com/repos/sinoon/weichat.io/teams',
     hooks_url: 'https://api.github.com/repos/sinoon/weichat.io/hooks',
     issue_events_url: 'https://api.github.com/repos/sinoon/weichat.io/issues/events{/number}',
     events_url: 'https://api.github.com/repos/sinoon/weichat.io/events',
     assignees_url: 'https://api.github.com/repos/sinoon/weichat.io/assignees{/user}',
     branches_url: 'https://api.github.com/repos/sinoon/weichat.io/branches{/branch}',
     tags_url: 'https://api.github.com/repos/sinoon/weichat.io/tags',
     blobs_url: 'https://api.github.com/repos/sinoon/weichat.io/git/blobs{/sha}',
     git_tags_url: 'https://api.github.com/repos/sinoon/weichat.io/git/tags{/sha}',
     git_refs_url: 'https://api.github.com/repos/sinoon/weichat.io/git/refs{/sha}',
     trees_url: 'https://api.github.com/repos/sinoon/weichat.io/git/trees{/sha}',
     statuses_url: 'https://api.github.com/repos/sinoon/weichat.io/statuses/{sha}',
     languages_url: 'https://api.github.com/repos/sinoon/weichat.io/languages',
     stargazers_url: 'https://api.github.com/repos/sinoon/weichat.io/stargazers',
     contributors_url: 'https://api.github.com/repos/sinoon/weichat.io/contributors',
     subscribers_url: 'https://api.github.com/repos/sinoon/weichat.io/subscribers',
     subscription_url: 'https://api.github.com/repos/sinoon/weichat.io/subscription',
     commits_url: 'https://api.github.com/repos/sinoon/weichat.io/commits{/sha}',
     git_commits_url: 'https://api.github.com/repos/sinoon/weichat.io/git/commits{/sha}',
     comments_url: 'https://api.github.com/repos/sinoon/weichat.io/comments{/number}',
     issue_comment_url: 'https://api.github.com/repos/sinoon/weichat.io/issues/comments{/number}',
     contents_url: 'https://api.github.com/repos/sinoon/weichat.io/contents/{+path}',
     compare_url: 'https://api.github.com/repos/sinoon/weichat.io/compare/{base}...{head}',
     merges_url: 'https://api.github.com/repos/sinoon/weichat.io/merges',
     archive_url: 'https://api.github.com/repos/sinoon/weichat.io/{archive_format}{/ref}',
     downloads_url: 'https://api.github.com/repos/sinoon/weichat.io/downloads',
     issues_url: 'https://api.github.com/repos/sinoon/weichat.io/issues{/number}',
     pulls_url: 'https://api.github.com/repos/sinoon/weichat.io/pulls{/number}',
     milestones_url: 'https://api.github.com/repos/sinoon/weichat.io/milestones{/number}',
     notifications_url: 'https://api.github.com/repos/sinoon/weichat.io/notifications{?since,all,participating}',
     labels_url: 'https://api.github.com/repos/sinoon/weichat.io/labels{/name}',
     releases_url: 'https://api.github.com/repos/sinoon/weichat.io/releases{/id}',
     created_at: 1440178744,
     updated_at: '2015-08-21T18:01:19Z',
     pushed_at: 1440326986,
     git_url: 'git://github.com/sinoon/weichat.io.git',
     ssh_url: 'git@github.com:sinoon/weichat.io.git',
     clone_url: 'https://github.com/sinoon/weichat.io.git',
     svn_url: 'https://github.com/sinoon/weichat.io',
     homepage: 'http://weichat.io',
     size: 0,
     stargazers_count: 0,
     watchers_count: 0,
     language: 'JavaScript',
     has_issues: true,
     has_downloads: true,
     has_wiki: true,
     has_pages: false,
     forks_count: 0,
     mirror_url: null,
     open_issues_count: 0,
     forks: 0,
     open_issues: 0,
     watchers: 0,
     default_branch: 'dev',
     stargazers: 0,
     master_branch: 'dev' },
  pusher: { name: 'sinoon', email: 'sinoon1218@gmail.com' },
  sender:
   { login: 'sinoon',
     id: 6065488,
     avatar_url: 'https://avatars.githubusercontent.com/u/6065488?v=3',
     gravatar_id: '',
     url: 'https://api.github.com/users/sinoon',
     html_url: 'https://github.com/sinoon',
     followers_url: 'https://api.github.com/users/sinoon/followers',
     following_url: 'https://api.github.com/users/sinoon/following{/other_user}',
     gists_url: 'https://api.github.com/users/sinoon/gists{/gist_id}',
     starred_url: 'https://api.github.com/users/sinoon/starred{/owner}{/repo}',
     subscriptions_url: 'https://api.github.com/users/sinoon/subscriptions',
     organizations_url: 'https://api.github.com/users/sinoon/orgs',
     repos_url: 'https://api.github.com/users/sinoon/repos',
     events_url: 'https://api.github.com/users/sinoon/events{/privacy}',
     received_events_url: 'https://api.github.com/users/sinoon/received_events',
     type: 'User',
     site_admin: false } }
```

## 结束语
我曾调试`webhook`签名好久，每次都要先推送到远程服务器，然后再手动创建一次推送让`webhook`发送推送。

写这篇文章希望看到的人可以直接知道该签名算法以及`webhook`推送的`body`内容，便于大家开发。

其实只要记住是`String`类型的`Buffer`，以及`express`下都会是小写。

祝大家工作顺利，事事如意。


  [1]: http://i1.tietuku.com/087c5f14a4c8ff9c.png
  [2]: http://i1.tietuku.com/7a2e449b4ab7f585.png
  [3]: http://i1.tietuku.com/96de7557404d53a8.jpg
  [4]: http://i3.tietuku.com/2822d2470ec9a8f4.jpg
  [5]: http://i3.tietuku.com/74dec150aba40466.jpg
  [6]: http://i3.tietuku.com/4dc079a84d7bc4b5.png