---
layout: post
title: What to learn Next
date: 2024-02-16 14:12:20 +0900
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: springboot4.png # Add image post (optional)
fig-caption: mac.jpg
tags: [BackEnd]
---
# DB
데이터베이스란:  
전자적을 저장되고 사용되는 관련있는 데이터들의 조직화된 집합.으로  
데이터의 중복을 제거할 수 있고, 찾으려는 데이터를 더 빨리 찾을 수 있고, 데이터의 불일치를 막을 수 있다.

관련있는 데어터란: 하나의 같은 서비스 안에서 생성되는 데이터.

DBMS란:  
사용자에게 데이터베이스를 정의하거나 만들고 관리하는 기능을 제공하는 소프트웨어.  
mysql, oracle 등이 있다.

ER diagram:  
entity-relationship model의 줄임말. conceptual data models.

데이터베이스 스키마란?  
데이터 모델을 바탕으로 데이터베이스의 구조를 기술 한 것.  
스키마는 데이터베이스를 설계할 때 정해지면 한 번 정해진 후에는 자주 바뀌지 않는다.

- three scjema architecture:
    데이터베이스 시스템을 구축하는 아키텍쳐 중 하나.
    user application으로 부터 물리적인 데이터베이스를 분리시키는 목적.
    세가지 level이 존재하며 각각의 level 마다 schema가 정의되어 있다.
    internal schema, external schema, conceptual schema가 있다.  
    각 레벨을 독립시켜서 어느 레벨에서의 변화가 상위 레벨에 영향을 주지 않게 한다.  
    데이터를 가지고 있는거는 conceptual level이다.

- internal schema란?
물리적으로 데이터가 어떻게 저장되는지 phsical data model을 통해 표현한다. 실체가 있는 내용을 기술한다.  
data storage, access path 등.

- external schema란?
    데이터베이스에서 외부에 제공되는 뷰나 인터페이스를 정의하는 구조.  
    사용자가 데이터에 접근할 때 볼 수 있는 논리적인 구조를 정의한다.  
    데이터의 물지적인 저장 방식에 대해 알 필요 없이 외부 스키마를 통해 데이터에 접근할 수 있다.
    특정 유저들이 필요로 하는 데이터만 표현한다.  
    알려줄 필요가 없는 데이터는 숨긴다.

- conceptual schema란?
    전체 데이터베이스에 대한 구조를 나타내고, 논리적 구조를 정의한다.  
    물리적인 저장 구조에 관한 내용은 숨긴다.  
    데이터베이스의 전체적인 구조와 데이터 간의 관계를 나타낸다.

- DDL:
    data definition(정의) language.  
    conceptual schema를 정의하기 위해 사용되는 언어이다.

- DML:
    data manipulation(조종) language.  
    데이터베이스에 있는 실제 데이터를 활용하기 위한 언어.  
    데이터 추가, 삭제, 수정, 검색 등의 기능을 제공하는 언어.

- primary key란?
    tuple을 unique하게 식별하기 위해 선택된 candidate key입니다.

- candidate key란?
    어느 한 속성이라도 제거하면 unique하게 tuoles를 식별할 수 없는 super key입니다.

- unique key란?
    primary key가 아닌 candidate keys  

- foreign key란?
    다른 테이블의 pk를 참조하는데 사용되는 제약조건입니다.  
    무결성을 유지할 수 있습니다.  
    외래 키는 데이터를 결합하고 검색하기 위해 사용될 수 있습니다. 부모 테이블과 조인하여 관련된 정보를 가져올 수 있습니다.

- SQL이란:
    Structured Query Language.  
    relational DBMS의 표준 언어이다.  
    RDBMS마다 제공하는 sql의 스펙이 조금씩 다르다.
    종합적인 database 언어: ddl, dml, vdl

