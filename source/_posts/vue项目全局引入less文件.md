---
title: vue项目全局引入less文件
? date
tags: webpack
categories:
    - 前端
---

### 背景

在app.vue页面引入全局的less文件，例如base.less。若base.less中定义了一些变量，例如颜色@df-color: red，那么在其他页面中是无法直接使用这个变量的。我需要在几乎每个页面都要引入一遍base.less。这不是我们期望的。我们所崇尚的是一劳永逸！

> 若仅是引用css，不涉及mixins、变量等复杂语法，则仅需要在入口文件app.vue中引入即可。

### 工具

插件[sass-resources-loader]('https://github.com/shakacode/sass-resources-loader')可以帮助我们将共享的mixins和变量等引用到全局。虽然名字中含有sass，但是它对less, post-css等同样有效。

<!-- more -->

### 方法

修改build/utils.js的cssloader方法。

```bash
exports.cssLoaders = function(options) {
    options = options || {}

    const cssLoader = {
        loader: 'css-loader',
        options: {
            sourceMap: options.sourceMap
        }
    }

    const postcssLoader = {
        loader: 'postcss-loader',
        options: {
            sourceMap: options.sourceMap
        }
    }

    const px2remLoader = {
        loader: 'px2rem-loader',
        options: {
            remUnit: 37.5
        }
    }

    // generate loader string to be used with extract text plugin
    function generateLoaders(loader, loaderOptions) {
        const loaders = options.usePostCSS
            ? [cssLoader, px2remLoader, postcssLoader]
            : [cssLoader, px2remLoader]

        if (loader) {
            loaders.push({
                loader: loader + '-loader',
                options: Object.assign({}, loaderOptions, {
                    sourceMap: options.sourceMap
                })
            })
        }

        // Extract CSS when that option is specified
        // (which is the case during production build)
        if (options.extract) {
            return ExtractTextPlugin.extract({
                use: loaders,
                fallback: 'vue-style-loader'
            })
        } else {
            return ['vue-style-loader'].concat(loaders)
        }
    }

    // fixme: 全局引用base.less
    function lessResourceLoader() {
        var loaders = [
            cssLoader,
            postcssLoader,
            px2remLoader,
            'less-loader',
            {
                loader: 'sass-resources-loader',
                options: {
                    resources: [
                        path.resolve(__dirname, '../static/less/base.less')
                    ]
                }
            }
        ]
        if (options.extract) {
            return ExtractTextPlugin.extract({
                use: loaders,
                fallback: 'vue-style-loader'
            })
        } else {
            return ['vue-style-loader'].concat(loaders)
        }
    }

    // https://vue-loader.vuejs.org/en/configurations/extract-css.html
    return {
        css: generateLoaders(),
        postcss: generateLoaders(),
        less: lessResourceLoader('less'),
        sass: generateLoaders('sass', { indentedSyntax: true }),
        scss: generateLoaders('sass'),
        stylus: generateLoaders('stylus'),
        styl: generateLoaders('stylus')
    }
}

```