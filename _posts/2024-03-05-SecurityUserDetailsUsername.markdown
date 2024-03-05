---
layout: post
title: Security에서 토큰의 Username 가져오는 방법.
date: 2024-03-05 14:12:20 +0900
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img:  # Add image post (optional)
fig-caption: mac.jpg
tags: [BackEnd]
---

# 기존의 내가 사용했던 방식
- SecurityContextHoler과 메서드 체인을 사용.
    1. controller
        ```java
        @PostMapping("/createShop")
            public String createShop(
                    @RequestBody Shop shop
            ) {
                // 이 코드처럼 SecurityContextHoler를 사용해서 메서드 체인을 사용해서 토큰의 Username을 가져왔다.
                String tokenUsername = SecurityContextHolder.getContext().getAuthentication().getName();

                // 분류의 종류는 서비스 제작자에 의해 미리 정해져 있다.
                ApplyShopDto dto = shopService.createShop(tokenUsername, shop);
                return dto.toString();
            }
        ```
    2. service
        ```java
        public User tokenUsername(String tokenUsername) {
            Optional<User> username = userRepository.findByUsername(tokenUsername);
            return username.orElseThrow(()-> new UsernameNotFoundException(tokenUsername));
        }
        ```

# 새로운 방법 - UserDetails 사용. 큰 변화는 없음.
- @AuthenticationPripal UserDetails userdetails 사용
    ```java
    @PostMapping("/authenticationPrincipal")
    public String getCurrentUser(@AuthenticationPrincipal UserDetails userDetails) {
            return "현재 사용자: " + userdetails.getUsername();
    }
    ```

    service에서는 크게 다를게 없다. 만약 단순히 username, userpassword, userAuthorization을 확인하는게 아니면 보통 원하는 것은 UserEntity일 것이니 userEntity를 원한다면 다를게 없다.



