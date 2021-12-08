# rust-china-install-fast
国内rust快速安装, centos7

  1. rust官网安装-慢
    https://rustup.rs 
    
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh 
  
  2. 国内镜像或者离线包方式安装
    设置环境变量 (用于更新toolchain)
      export RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static
      export RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup
    
    下载镜像安装脚本
      wget https://cdn.jsdelivr.net/gh/rust-lang-nursery/rustup.rs/rustup-init.sh
    
    执行安装脚本(无权限要授权 chmod +x rustup-init.sh)
      ./rustup-init.sh 
    
    选择安装方式
      1使用默认配置，2使用自定义配置，3取消安装
     
    配置cargo的环境变量
      source $HOME/.cargo/env 
    
    测试安装成功
      cargo 
