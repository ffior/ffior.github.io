---
layout: post
title: hooks组件开发
date: 2024-10-17 22:20:00
categories: [语言]
tags: [react, hooks]
---

## 一、配置hooks组件开发

### 1、配置package.json

```
{
    "name": "zhooks",
    "version": "1.0.2",
    "description": "",
    "main": "dist/index.js",
    "typing": "dist/index.d.ts",
    "scripts": {
        "test": "jest",
        "build": "tsc --outDir dist src/index.ts",
        "release": "rm -rf dist && npm run build && npm publish"
    },
    "repository": {
        "type": "git",
        "url": "git+https://zhifou@github.com/zhifou/hooks.git"
    },
    "keywords": [],
    "author": "zhaoyadong",
    "license": "ISC",
    "bugs": {
        "url": "https://github.com/zhifou/hooks/issues"
    },
    "homepage": "https://github.com/zhifou/hooks#readme",
    "devDependencies": {
        "react": "^17.0.1",
        "typescript": "^4.2.3"
    },
    "publishConfig": {
        "registry": "https://registry.npmjs.org/"
    }
}
```

### 2、tsconfig.json配置

```
{
    "compilerOptions": {
        "target": "ES5",
        "moduleResolution": "node",
        "jsx": "react",
        "allowJs": true,
        "esModuleInterop": true,
        "downlevelIteration": true,
        "sourceMap": true,
        "baseUrl": ".",
        "paths": {
            "@/*": [
                "src/*"
            ]
        },
        "allowSyntheticDefaultImports": true,
        "skipLibCheck": true,
        "declaration": false,
        "strictNullChecks": true
    },
    "include": [
        "src"
    ],
    "exclude": [
        "node_modules",
        "lib",
        "es",
        "dist",
        "**/__tests__",
        "**/__test__",
        "**/demo"
    ]
}
```

### 3、模块导出

```
/**
* @file entry file
* @author zhifou
*/
import useLocalStorage from './useLocalStorage';
import useWindowSize from './useWindowSize';
export {
    useLocalStorage,
    useWindowSize
};
```

## 二、配置jest单元测试

## 1、单元测试采用了jest + testing-library/react-hooks 结合方式

下图红框部门是依赖组件包，包括：jest、ts-jest、@types/jest这三个是jest组件和ts下的jest的依赖组件@testing-library/react是用于测试react组件使用的，@testing-library/react-hooks是用于测试react-hooks组件使用的，react-dom和react-test-renderer是模拟dom和render方法使用的组件

![配置](http://www.abcstatic.com/images/jpg/react-hooks-jest-2.jpg)

### 2、配置jest.config.js文件

```
module.exports = {
    preset: 'ts-jest/presets/js-with-ts',
    clearMocks: true,
    globals: {
        'ts-jest': {
            tsConfig: 'tsconfig.json',
        },
    },
};
```

### 3、创建test文件，首先在每一个hooks组件目录下创建__test__目录，并在目录下创建index.test.ts文件

举例如下：

```
import {renderHook} from "@testing-library/react-hooks";
import useWindowSize from '../index';
describe('useWindowSize', () => {
    it('should be defined', () => {
        expect(useWindowSize).toBeDefined();
    });
    it('with argument', () => {
        const hook = renderHook(() => useWindowSize());
        expect(hook.result.current.width).toEqual(0);
        expect(hook.result.current.height).toEqual(0);
    });
});
```

### 4、运行单元测试，npm run test

```
"scripts": {
    "test": "jest",
    "build": "tsc --outDir dist src/index.ts",
    "release": "rm -rf dist && npm run build && npm publish"
}
```

下面是运行结果：

![测试结果](http://www.abcstatic.com/images/jpg/react-hooks-jest-1.jpg)
