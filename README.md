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
   
  5. 关于mac交叉编译linux版本
    
    目前为止我遇到的错误:
    Internal error occurred: Failed to find tool. Is `musl-gcc` installed?  --->  
        brew install FiloSottile/musl-cross/musl-cross
        ln -s /usr/local/bin/x86_64-linux-musl-gcc /usr/local/bin/musl-gcc

    error occurred: Failed to find tool. Is `musl-g++` installed?    ---> 
        然而并未找到可以解决这个的问题
    
    所以在知乎上找到一个解决方案
      链接地址: https://zhuanlan.zhihu.com/p/376471249
      
  6. 交叉编译不成功之后在centos上直接编译
    
    失败：报错
    error: linking with `cc` failed: exit status: 1
    = note: "cc" "-m64" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.0.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.1.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.10.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.11.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.12.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.13.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.14.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.15.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.2.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.3.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.4.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.5.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.6.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.7.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.8.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.build_script_build.19887d06-cgu.9.rcgu.o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a.40u2j0b0m2rkp502.rcgu.o" "-Wl,--as-needed" "-L" "/home/admin/rust_src/star_code_middleware/target/debug/deps" "-L" "/usr/lib64/llvm" "-L" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib" "-Wl,-Bstatic" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libcc-dcca41d1b58fd635.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libvcpkg-b3bf30d5f8a5367a.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libpkg_config-5abe0320b4197c6a.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libopencv_binding_generator-ead8b485cb58b10f.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libmaplit-3ab555efaab62bb0.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libregex-84dc590185c6397b.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libaho_corasick-d54b0d9e8dd84d99.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libmemchr-6061dad8797913cc.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libregex_syntax-5f99d85e7aa20c3c.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libpercent_encoding-ab63f59cece6956f.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libclang_sys-dda1a10344c9b4c8.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libjobserver-ace04bdef08a2feb.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/liblibc-5af17482beb691ed.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libshlex-e092b08f03baf28e.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libsemver-c0453dc51ce2a047.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libsemver_parser-c374f4ade63c50b9.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libpest-6da4ccd115db57e2.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libucd_trie-fb32211bfa135433.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libonce_cell-7fa1babf08843306.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libglob-7f4b6d05fc88e096.rlib" "/home/admin/rust_src/star_code_middleware/target/debug/deps/libdunce-75bd6320fbe0957f.rlib" "-Wl,--start-group" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libstd-7c582493123fc1dd.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libpanic_unwind-1392776590706175.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libminiz_oxide-2eb6edf4d031cd1e.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libadler-33a7ad3b5f7fedf6.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libobject-7d32adce541987d9.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libmemchr-5cb369120f224726.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libaddr2line-6ab2efd1d2f431a9.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libgimli-567f611439253c7e.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libstd_detect-ebbc63efd6d2efc5.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/librustc_demangle-862830f0d224a2e1.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libhashbrown-cad0401ae7a80e32.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/librustc_std_workspace_alloc-fd54290077194763.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libunwind-4fc3313c8ccb1ec0.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libcfg_if-19bf8dffe82b09d4.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/liblibc-afc95b1640c4beca.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/liballoc-aff6658baa87e3d1.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/librustc_std_workspace_core-8be8a1689a4f7b48.rlib" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libcore-5284934f66073844.rlib" "-Wl,--end-group" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libcompiler_builtins-2a0b2a4f96acb821.rlib" "-Wl,-Bdynamic" "-lclang" "-lgcc_s" "-lutil" "-lrt" "-lpthread" "-lm" "-ldl" "-lc" "-Wl,--eh-frame-hdr" "-Wl,-znoexecstack" "-L" "/home/admin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib" "-o" "/home/admin/rust_src/star_code_middleware/target/debug/build/opencv-e85d84372df8863a/build_script_build-e85d84372df8863a" "-Wl,--gc-sections" "-pie" "-Wl,-zrelro" "-Wl,-znow" "-nodefaultlibs"
    = note: /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Entity::evaluate':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1839: undefined reference to `clang_Cursor_Evaluate'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Entity::evaluate::{{closure}}':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1841: undefined reference to `clang_EvalResult_getKind'
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1844: undefined reference to `clang_EvalResult_getAsDouble'
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1845: undefined reference to `clang_EvalResult_getAsStr'
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1846: undefined reference to `clang_EvalResult_getAsStr'
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1847: undefined reference to `clang_EvalResult_getAsStr'
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1848: undefined reference to `clang_EvalResult_getAsStr'
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1851: undefined reference to `clang_EvalResult_dispose'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Entity::evaluate::evaluate_integer':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1826: undefined reference to `clang_EvalResult_isUnsignedInt'
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1829: undefined reference to `clang_EvalResult_getAsLongLong'
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:1827: undefined reference to `clang_EvalResult_getAsUnsigned'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Entity::get_exception_specification':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:2006: undefined reference to `clang_getCursorExceptionSpecificationType'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Entity::get_storage_class':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:2239: undefined reference to `clang_Cursor_getStorageClass'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Entity::is_abstract_record':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:2355: undefined reference to `clang_CXXRecord_isAbstract'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Entity::is_const_method':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:2393: undefined reference to `clang_CXXMethod_isConst'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Entity::is_function_like_macro':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:2437: undefined reference to `clang_Cursor_isMacroFunctionLike'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Entity::is_mutable':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:2461: undefined reference to `clang_CXXField_isMutable'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Entity::is_scoped':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:2478: undefined reference to `clang_EnumDecl_isScoped'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Parser::parse':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:2849: undefined reference to `clang_parseTranslationUnit2'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Type::get_elaborated_type':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:3256: undefined reference to `clang_Type_getNamedType'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Type::get_template_argument_types':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/lib.rs:3370: undefined reference to `clang_Type_getNumTemplateArguments'
            /home/admin/rust_src/star_code_middleware/target/debug/deps/libclang-1a53dcc85fff53bc.rlib(clang-1a53dcc85fff53bc.clang.d927368b-cgu.3.rcgu.o): In function `clang::Type::get_template_argument_types::{{closure}}':
            /home/admin/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/clang-1.0.3/src/utility.rs:77: undefined reference to `clang_Type_getTemplateArgumentAsType'
            collect2: error: ld returned 1 exit status

    = help: some `extern` functions couldn't be found; some native libraries may need to be installed or have their path specified
    = note: use the `-l` flag to specify native libraries to link
    = note: use the `cargo:rustc-link-lib` directive to specify the native libraries to link with Cargo (see https://doc.rust-lang.org/cargo/reference/build-scripts.html#cargorustc-link-libkindname)

    error: could not compile `opencv` due to previous error
    warning: build failed, waiting for other jobs to finish...
    error: build failed
  
    或者 
  
    提示什么 pkg_config,vcpkg,cmake 没有安装之类的信息，这个错误信息是我使用opencv-3.4.16版本后，并且降低了rust里opencv的版本之后报的，原因不明
    
    总结：核心是版本没有与gcc以及cmake等匹配，安装后的路径没有设置对
  
    目前尝试成功了 
    Cargo.toml
    [dependencies]
    opencv={versions="0.41.0",default_feature=false,features=["opencv-4"]}
  
    实际centos安装的版本是 opencv-4.5.4
    期间2，3都安装了不好使，可能因为期间有些库或者环境不匹配的原因
  
    现将成功步骤总结如下
    有些依赖需要的gcc版本>=6.0 而有些linux自带的版本4.8或者更低，所以需要我们自己手动去更新升级一下版本
  
    1> 升级gcc,cmake,llvm 
      1.1 gcc升级至8
    
      安装centos-release-scl
      sudo yum install centos-release-scl
    
      安装devtoolset
      sudo yum install devtoolset-8-gcc*
    
      激活对应的devtoolset
      scl enable devtoolset-8 bash 
    
      查看对应版本
      gcc -v
      显示为 gcc version 8.3.1 20190311 (Red Hat 8.3.1-3) (GCC)

      这个只对此次终端有效，想要一劳永逸，则需要替换掉原来gcc版本的可执行文件
      这个安装之后的目录是在 /opt/rh/devtoolset-8 下

      要先找到gcc在哪儿
      whereis gcc
      /usr/bin/gcc /usr/lib/gcc /usr/libexec/gcc     

      whereis g++
      /usr/bin/g++

      rm -rf /usr/bin/gcc 
      rm -rf /usr/bin/g++
      ln -s /opt/rh/devtoolset-8/root/bin/gcc /usr/bin/gcc
      ln -s /opt/rh/devtoolset-8/root/bin/g++ /usr/bin/g++

      为了防止执行加载的库还是gcc-4.8的，我将/usr/libexec/gcc下的依赖库也做了软链接
      ln -s /opt/rh/devtoolset-8/root/lib/gcc/x86_64-redhat-linux/8 /usr/libexec/gcc/x86_64-redhat-linux
      ln -s /opt/rh/devtoolset-8/root/lib/gcc/x86_64-redhat-linux/8 /usr/lib/gcc/x86_64-redhat-linux

      至此就将gcc,g++升级成功了

      1.2 升级cmake

      我的服务器的centos-7.9 默认的cmake版本是2.8.12.2，在项目编译过程中，我察觉到有个库需要cmake>=3.0 固尝试决定升级cmake来解决该问题
      先下载文件，下载在一个自定义的目录下，我的涉及系统的c/c++或者其他软件以来的程序一般都下载在/usr/local/src目录下
      cd /usr/local/src
      wget https://cmake.org/files/v3.14/cmake-3.14.2.tar.gz 
      tar -vzxf cmake-3.14.2.tar.gz 

      删除已安装的cmake版本
      yum remove cmake -y
      cd cmake-3.14.2

      安装基本的编译工具
      ./configure              # 可携带此参数指定安装目录 --prefix=/usr/local/cmake 。 我直接在源码里安装的
      make && make install 

      创建软链接
      ln -s /usr/local/bin/cmake /usr/bin/cmake

      查看版本
      cmake --version

      1.3 安装llvm 安装这个是比较麻烦的，依赖的东西比较多,我下载的版本是11.0.0 因为在编译过程中也报了clang的版本太低，需要升级

      cd /usr/local/src 
      wget https://github.com/llvm/llvm-project/releases/download/llvmorg-11.0.0/llvm-11.0.0.src.tar.xz
      wget https://github.com/llvm/llvm-project/releases/download/llvmorg-11.0.0/clang-11.0.0.src.tar.xz
      wget https://github.com/llvm/llvm-project/releases/download/llvmorg-11.0.0/compiler-rt-11.0.0.src.tar.xz
      wget https://github.com/llvm/llvm-project/releases/download/llvmorg-11.0.0/clang-tools-extra-11.0.0.src.tar.xz
      wget https://github.com/llvm/llvm-project/releases/download/llvmorg-11.0.0/libcxx-11.0.0.src.tar.xz
      wget https://github.com/llvm/llvm-project/releases/download/llvmorg-11.0.0/libcxxabi-11.0.0.src.tar.xz

      对下载的包进行解压
      tar xvf llvm-11.0.0.src.tar.xz
      tar xvf clang-11.0.0.src.tar.xz
      tar xvf compiler-rt-11.0.0.src.tar.xz
      tar xvf clang-tools-extra-11.0.0.src.tar.xz
      tar xvf libcxx-11.0.0.src.tar.xz
      tar xvf libcxxabi-11.0.0.src.tar.xz

      对文件名进行重命名
      mv clang-11.0.0.src clang 
      mv compiler-rt-11.0.0.src compiler-rt
      mv clang-tools-extra-11.0.0.src extra
      mv libcxx-11.0.0.src libcxx
      mv libcxxabi-11.0.0.src libcxxabi

      cd llvm-11.0.0.src
      mkdir build
      cd build
      cmake -DCMAKE_BUILD_TYPE=Release -DLLVM_ENABLE_RTTI=ON -DLLVM_ENABLE_PROJECTS="clang;libcxx;libcxxabi" -G "Unix Makefiles" ..
      make && make install 

      查看版本
      clang -v
    
    2> 下载opencv(官网地址: https://opencv.org/releases/)
      cd /usr/local/src
      wget https://github.com/opencv/opencv/archive/4.5.4.zip
      unzip opencv-4.5.4.zip
      cd opencv-4.5.4
      mkdir build
      cd build 
      cmake -D BUILD_SHARED_LIBS=ON -D BUILD_TESTS=OFF -D CMAKE_BUILD_TYPE=RELEASE  ..
      make && make install

      vim /etc/ld.so.conf.d/opencv.conf
      /usr/local/lib64
      /usr/local/lib

      检验是否成功
      pkg-config --libs opencv
      pkg-config --cflags opencv
    
  
  
  


    
     
