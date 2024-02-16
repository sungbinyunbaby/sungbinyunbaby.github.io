---
layout: post
title: Git-pages 만들기
date: 2024-02-16 14:12:20 +0900
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: gitpages2.png # Add image post (optional)
fig-caption: mac.jpg
tags: [BackEnd]
---
# 만약 있다면 local 접속 방법
1. visual code에서 sungbinyunbaby.github.io 폴더 열기
2. local에 접속하기 위한 명령어
	1. bundle install
	2. bundle exec jekyll serve
3. 원하는대로 만들고 ㄹㅇ페이지에 추가하기.
	1. git add .
	2. git commit -m '쪼대로 적으세용><'
	3. git push
4. github에 들어가서 확인 - sungbinyunbaby.github.io > action에 들아가면 업로드 중을 볼 수 있다.
업로드가 끝나면 업로드된 페이지에 들어갈 수 있다.  
![git에업로드](../../assets/img/gitpagesUpload.png)

# Github-pages를 처음 만든다면
github에서 new로 자신의 아이디.github.io로 파일 생성 후 클론.  
클론 후 visual studio code에서 클론 폴더 열기.
1. visual code 터미널에서 더보기>bash선택
![터미널](../../assets/img/터미널.png)
2. gem install bundler
3. gem install github-pages
4. gem install jekyll (sass 충돌, 종속성 어쩌구저꺼구 -> Y)
5. 디렉터리가 비어있는 상태로 터미널에 jekyll new . 입력 -> 그럼 파일들이 나올 것이다.
6. bundle install
7. bundle exec jekyll serve
8. git add .
9. git commit -m ',,,'
10. git push
11. https://github.com/artemsheludko/flexible-jekyll > git clone하기
12. clone한 파일을 복사하고 sungbinyunbaby.github.io안의 파일들을 전체 삭제 후 붙여넣기.
13. config.yaml에 baseurl: "", url: "http://sungbinyun.github.io"로 변경.(나머지도 바꾸면 된다.)
14. bundle install
15. bundle exec jekyll serve

# 기능
_posts에 파일을 추가하면 게시글이 생성된다.

사진과 링크는 경로로 지정해야 한다.

_includes/head.html에서 파비콘을 변경가능. (웹 브라우저의 주소창에 표시되는 웹페이지를 대표하는 아이콘.)  
![파비콘변경](../../assets/img/파비콘변경.png)  
![파비콘](../../assets/img/파비콘.png)