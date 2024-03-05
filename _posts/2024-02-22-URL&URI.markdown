---
layout: post
title: URL과 URI의 차이와 PUT과 GET의 차이.
date: 2024-02-22 14:12:20 +0900
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img:  # Add image post (optional)
fig-caption: mac.jpg
tags: [BackEnd]
---
# URL과 URI
리소스: 웹 상에서 식별 가능한 모든 것을 가르킨다. 문서, 이미지, 비디오, 데이터베이스 레코드, 서비스 등과 같은 다양한 형태의 정보를 포함한다.  
웹 서버에 의해 제공되거나 웹에서 식별할 수 있는 모든 것이 리소스가 될 수 있다.

URL은 Uniform Resource Location이다.  
리소스가 위치한 주소를 나타내는 문자열이다.  
URL은 URI의 하위 개념이다. 모든 URL은 URI이다.


URI는 Uniform Resource Identifier이다.  
리소스를 식별하기 위한 ㅅ일반적인 방법을 제공하는 문자열이다.  
URI는 URL외 다른 식별 방법이 있다.

GET 요청은 주로 URL을 사용하여 리소스를 요청한다. 즉, 요청된 데이터가 URL의 일부로 전달된다.  
POST 요청은 