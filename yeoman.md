# 使用 yeoman 搭建脚手架

## 添加 yeoman 依赖库

```bash
npm install -g yo
```

### 新建 generator-XX 命名的文件名，新建`package.json`并配置，或者使用`npm init`

```json
{
  "name": "generator-create-react-web",
  "version": "0.0.0",
  "description": "",
  "homepage": "",
  "author": "",
  "files": ["generators"],
  "main": "generators/index.js",
  "keywords": ["yeoman-generator"],
  "dependencies": {
    "yeoman-generator": "^2.0.1",
    "chalk": "^2.1.0",
    "yosay": "^2.0.1"
  },
  "license": "MIT"
}
```

### 配置目录结构，使用的脚手架内容便是 templates 下的模块

```javascript
|-generator-xx
  |-app
    |-templates
      |--dva
        |--ts
        |--js
      |--ss
        |--ts
        |--js
      |--index.js
  |--package.json
```

### 配置脚手架内容`generator-xx/app/templates/index.js`

```javascript
const Generator = require('yeoman-generator');
const chalk = require('chalk');
const yosay = require('yosay');
const path = require('path');

module.exports = class extends Generator {
  prompting() {
    // Have Yeoman greet the user.
    this.log(yosay(`Welcome to the wicked ${chalk.red('generator-create-react-web')} generator!`));

    const prompts = [
      {
        type: 'input',
        name: 'name',
        message: 'name:',
        default: path.basename(process.cwd())
      },
      {
        type: 'input',
        name: 'description',
        message: 'description:',
        default: ''
      },
      {
        type: 'list', // 提供用户选择是否要ss还是dva
        name: 'structure',
        message: 'Please select project framework Structure?',
        choices: [
          {
            name: 'dd',
            value: 'dd'
          },
          {
            name: 'dva',
            value: 'dva'
          }
        ]
      },
      {
        type: 'list', // 提供用户选择是否是要js还是ts
        name: 'type',
        message: 'Please select project language type?',
        choices: [
          {
            name: 'js',
            value: 'js'
          },
          {
            name: 'ts',
            value: 'ts'
          }
        ]
      },
      {
        type: 'input',
        name: 'repo',
        message: 'git repository:',
        default: ''
      },
      {
        type: 'input',
        name: 'license',
        message: 'license:',
        default: 'ISC'
      },
      {
        type: 'input',
        name: 'author',
        message: 'author:',
        default: ''
      },
      {
        type: 'input',
        name: 'port',
        message: 'port:',
        default: '8888'
      }
    ];

    return this.prompt(prompts).then(props => {
      this.customParameters = props;
    });
  }

  writing() {
    this.fs.copyTpl(
      this.templatePath(`${this.customParameters.structure}/${this.customParameters.type}/package.json`),
      this.destinationPath('package.json'),
      this
    );
    this.fs.copy(this.templatePath(`${this.customParameters.structure}/${this.customParameters.type}/index.js`), this.destinationPath('index.js'));
  }

  install() {
    //进行安装依赖
    this.installDependencies();
  }
};
```

### 脚手架的内容的变量配置

```javascript
<%= value %>
```

<br />
### 配置完成后，一个基本脚手架即可运行，在你要搭建项目的目录下进行`yo xx`(xx就是脚手架项目名)。就可生成一个配置好的templates下选择的项目结构。
  
<br />

### yeoman 还提供了更多的功能以及函数供我们使用，具体还得看 yeoman 官网 http://yeoman.io/
