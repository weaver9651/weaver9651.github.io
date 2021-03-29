---
layout: post
title: "우분투 20.04 Realtek Ethernet 문제 해결"
date: 2021-01-10 16:20:00 +0900
categories:
- troubleshooting
- linux
comments: true
changefreq: daily
---
# 발단
얼마전 새로 산 컴퓨터에 Windows와 Ubuntu 20.04를 설치했는데 Ubuntu에서 이더넷 드라이버를 아예 찾지 못하는 문제가 발생했다. 구글에 검색을 해보면 Realtek 홈페이지에서 해당 드라이버를 설치하라고 나오는데 dependency 문제로 여러 패키지를 설치해야 한다. 문제는 인터넷이 안되기 때문에 해당 패키지들도 다른 컴퓨터에서 수동으로 검색하여 다운받아 설치해야하는데 여간 어려운 일이 아니다.

# 해결 방법
여기서는 kernel을 상위 버전으로 패치하여 문제를 해결하는 방법을 소개한다. 아래 파일들을 다운받을 수 있는, 인터넷이 연결된 다른 컴퓨터가 필요하다. 설치 당시 5.10.4 버전이 안정화된 최신 버전이어서 5.10.4 버전을 예로 들어 설명한다. 
1. 다음 네 가지 항목을 다운받는다.
  - https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.10.4/amd64/linux-headers-5.10.4-051004-generic_5.10.4-051004.202012301142_amd64.deb
  - https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.10.4/amd64/linux-headers-5.10.4-051004_5.10.4-051004.202012301142_all.deb
  - https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.10.4/amd64/linux-image-unsigned-5.10.4-051004-generic_5.10.4-051004.202012301142_amd64.deb
  - https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.10.4/amd64/linux-modules-5.10.4-051004-generic_5.10.4-051004.202012301142_amd64.deb

2. 위 파일들을 Ubuntu에 옮긴 다음 아래 명령으로 모두 설치해준다.  
  `sudo dpkg -i *.deb `

3. 재시작하면 자동으로 Ethernet을 잡아줘서 유선 연결이 되었음을 확인할 수 있다.

