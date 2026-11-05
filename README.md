# librdkafka 1.9.2 on loongarch64

## 构建（mklove configure，不是 cmake）

    patch -p1 < doris-thirdparty/patches/librdkafka-1.9.2.patch
    ./configure --prefix=$P --enable-static --disable-shared \
        CFLAGS="-fPIC -O2" CXXFLAGS="-fPIC -O2 -include cstdint"
    make -j4 && make install

`conf.log` 里能看到 mklove 正确识别 `gcc/g++`、`aosc os`、zlib / libcrypto / libssl 全部 `ok`，
版本解析为 `0x010902ff`（1.9.2）。产物：`installed/lib/librdkafka.a`、`librdkafka++.a`、`librdkafka-static.a`，
头文件在 `include/librdkafka/`。

## 注意

- 上游 mklove 的 `__atomic_*` 探测在 loongarch64 上通过，无需额外开关。
- 不要漏打 Doris 的 `librdkafka-1.9.2.patch`（官方 `build-thirdparty.sh` 会打）。
- 头文件装到了 `include/librdkafka/` 子目录，BE 引用方式要与之匹配。

## 验证

已链接进 `doris_be`，0 编译错误、0 undefined symbol。
