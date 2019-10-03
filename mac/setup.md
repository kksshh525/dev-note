>   Macbook 개발 환경 Setting 
>
>   2019/10/3일 작성….



## 1. 프로그램 설치

#### Devtool

-   iterms
-   jetbrain toolbox (intellij)
-   postman



#### Utility

-   google backup
-   alfred4
-   karabiner
-   magnet
-   textmate
-   typora



## 2. 설치

#### homebrew install

```shell
 /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
 
 brew update 
 brew install node 
```

#### oh-my-zsh install

```shell
brew install zsh
curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh
chsh -s /bin/zsh
```

#### vim

```shell
brew install neovim
brew tap caskroom/fonts
brew cask install font-hack-nerd-font

curl -sLf https://spacevim.org/install.sh | bash
```



**테마변경**

`~/.SpaceVim.d/init.vim` 파일에 다음 항목 추가

```
let g:spacevim_colorscheme = 'onedark'
```





## 3. 키 변환 & SSH 키 등록

#### 한/영키 right command로 변경

1.  [karabiner 설치](https://pqrs.org/osx/karabiner/)
2.  Simple modifications 탭에서 right_command - f18로 추가
3.  System Preference - Keyboard - 단축키 탭 - 입력소스 - 이전소스 입력을 (right command) 키로 변경



#### github ssh키 등록

-   `ssh-keygen -t rsa` 를 통해서 ssh key를 만든다.
-   cat ~/.ssh/id_psa.pub 을 통해서 퍼블릭키를 복사한다.
-   github 계정의 SSH and GPG keys 에서 `New SSH Key` 를 등록한다.







## 4. Plugins

#### vscode 

-   atom one dark theme
-   intellij keybinding
-   Auto close tag
-   auto Import
-   Beautify
-   Bracket Pair Colorizer
-   Indenticator
-   Node.js extension pack
-   output colorizer
-   prettier - code formatter
-   react extension pack
-   material icon theme
-   Vscode-styld-components
-   todo highlight



# Intellij 

-   lombok
-   save action 
-   gittoolbox 
-   code glance



#### Chrom Extension 

-   octotree
-   momentum
-   google translate
-   json formatter
-   cookie



## 5. Custom Setting

#### Intellij 폰트 변경 

-   naverd2 coding 



#### 테마 변경