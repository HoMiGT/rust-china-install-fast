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
      
  3. 修改cargo的国内源
    
    vim $HOME/.cargo/config 
    
    [source.crates-io]
    registry = "https://github.com/rust-lang/crates.io-index"
    # 指定镜像
    replace-with = 'tuna' # 如：tuna、sjtu、ustc，或者 rustcc

    # 注：以下源配置一个即可，无需全部

    # 中国科学技术大学
    [source.ustc]
    registry = "https://mirrors.ustc.edu.cn/crates.io-index"
    # >>> 或者 <<<
    # registry = "git://mirrors.ustc.edu.cn/crates.io-index"

    # 上海交通大学
    [source.sjtu]
    registry = "https://mirrors.sjtug.sjtu.edu.cn/git/crates.io-index/"

    # 清华大学
    [source.tuna]
    registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"

    # rustcc社区
    [source.rustcc]
    registry = "https://code.aliyun.com/rustcc/crates.io-index.git"
        
4. 当在mac中使用rust编译依赖opencv库的项目是报如下错：

  dyld: Library not loaded: @rpath/libclang.dylib
  出现这个错误的原因一般是无法找到c库，需要配置路径mac里需要下载x-code软件
   
   
    vim ~/.bash_profile 
    export DYLD_FALLBACK_LIBRARY_PATH="$(xcode-select --print-path)/usr/lib/" 
    或者
    export DYLD_FALLBACK_LIBRARY_PATH="$(xcode-select --print-path)/Toolchains/XcodeDefault.xctoolchain/usr/lib/"
   
    一般编译过程中依然会出现失败，我出现的异常是：
    Can't probe using: pkg_config, continuing with other methods because: `"pkg-config" "--libs" "--cflags" "opencv4"` did not exit successfully:
    原因是我装了几个版本，我把所有的版本重新卸载掉，重新装了opencv@3（brew install opencv@3）
    之后配置了关于opencv的路径
   
    vim ~/.bash_profile
    export PATH="/usr/local/opt/opencv@3/bin:$PATH"
    export LDFLAGS="-L/usr/local/opt/opencv@3/lib"
    export CPPFLAGS="-I/usr/local/opt/opencv@3/include"
    export PKG_CONFIG_PATH="/usr/local/opt/opencv@3/lib/pkgconfig"
   
    之后执行 RUST_BACKTRACE=full cargo build -vv 没有异常出现，编译成功！
   
     
     
