### 准备一个新的Grunt Project
一个Grunt project 通常会包含两个文件：`package.json`和`Gruntfile`。

1. package.json:这个文件通常是用被[npm][]用来记录项目作为modules发布所需的元数据。你可以把Grunt文件和项目用到的Grunt plugins 添加在`package.json` 的[devDependencies][]节点中。

2. Gruntfile:这个文件被命名为`Gruntfile.js`或者`Gruntfile.coffee`，被用来配置或定义任务以及加载Grunt plugins。

#### package.json
`package.json`和`Gruntfile`文件一般在项目的根目录下，他们都应该被提交到项目的源码库中。在`package.json`的目录下执行`npm install`会自动下载`package.json`的dependencies中定义的modules。
创建项目的`package.json`文件一般有以下2种方式：

-  使用[grunt-init][]模版
-  使用[npm init][]命令

##### 安装Grunt和gruntplugins
 最简单的方式就是使用`npm install <module> --save-dev`命令。不仅会下载`<module>`模块，还会自动把它添加到`package.json`中devDenpendencies中
 例如下面的命令将安装最新的grunt版本，并添加到devDependencies中：
 > npm install grunt --save-dev
 
其他gruntplugins的安装与上面是相同的方式。例如安装JSHint task 模块
 > npm install grunt-contrib-jshint --save-dev
 
 可以在[plugins][]页面查找当前可用的gruntplugins。一定要确保把`package.json`和`Gruntfile`上传只代码库中。
 
#### Gruntfile
一个`Gruntfile`文件有以下4部分构成：

- 外层函数 "wrapper" function 
- 项目和任务的配置 Project and task configuration
- 加载Grunt plugins 和任务 Loading Grunt plugins and tasks
- 客户自定义的任务 Custom tasks

##### 看下面Gruntfile的例子吧
	module.exports=function(grunt){
		
		//Project configuration
		grunt.initConfig({
			pkg:grunt.file.readJSON('package.json'),
			uglify:{
				options:{
					banner:'/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
				},
				build:{
					src:'src/<%= pkg.name %>.js',
					dest:'build/<%= pkg.name %>.min.js'
				}
			}
		});
		
		//load the plugin that provides the "uglify" task
		grunt.loadNpmTasks('grunt-contrib-uglify');
		
		//default task
		grunt.registerTask('default',['uglify']);
	}
######  外层函数 wrapper function

每一个`Gruntfile`(或则插件)都会如下的格式，我们必须在这个函数内去编码。

	module.exports=function(grunt){
		grunt.initConfig({});
	}
###### 项目和任务的配置 project and tasks configuration

基本上，Grunt任务的配置都是在一个配置对象内定义的，把这个配置对象传递给`grunt.initConfig()` 即可实现Grunt的配置。在上面的例子中`grunt.file.readJSON('package.json')`就是把`package.json`文件获取并转换为JSON对象`pkg`，后面就可以使用`pkg`这个JSON对象。其实，我们还可以在这个配置对象内部定义我们自己的一些变量，只要这些变量不要与Gruntplugins冲突。Gruntfile是一个 js文件，因此，配置对象里的变量不仅仅是JSON 的数据格式，实际上可以是任何合法的js代码。

上面的[grunt-contrib-uglify][]插件，在配置对象中默认的任务名称为uglify，其他很多Grunt插件都遵循上面的规则。

	grunt.initConfig({
		pkg:grunt.file.readJSON('package.json),
		uglify:{
			options:{
				banner:'/*! <%= pkg.name %> <%= grunt.template.today("yyyy=mm-dd") %> */\n'
			}
			build:{
				src:'src/<%= pkg.name %>.js,
				dest:'build/<%= pkg.name %>.min.js'
			}
		}
	});

###### 加载Gruntplugins和任务 loading Grunt plugins and tasks

通常任务都是一个个Grunt插件，例如[concatenation][],[minification][],[linting][]等等，更多插件可查[grunt plugins][plugins]。只要插件在`package.json`中的dependencies中声明了，就可以通过`npm install`去下载到本地项目中，默认存放在`node_modules`文件夹下。可以通过下面的命令去加载插件任务:

	grunt.loadNpmTasks("grunt-contrib-uglify");
 
 可以用`grunt --help`命令列出所有可用的任务。
 
###### 用户自定义的任务  Custom tasks
我们可以通过定义default的任务，就可以使用命令`grunt`或者`grunt default`，默认顺序执行在第二个数组参数中列出将要执行的任务。 上面的例子中将会执行`uglify`任务。或者使用命令`grunt`指定要执行的任务名称，例如`grunt uglify`。

	grunt.registerTask('default',[uglify]);

如果在项目中，我们想执行的任务在Grunt插件中没有，则可以自己去定义一个任务。自定义的任务不需要像`uglify`在JSON中去定义任务名称,如果他们是外部的js文件，则通过[grunt.loadTasks][]方法去加载。

	grunt.registterTask('myTask','Log some stuff.',function(){
		grunt.log.write('Logging some stuff....').ok();
	});
	
[npm]:https://www.npmjs.com/	
[devDependencies]:https://docs.npmjs.com/files/package.json#devdependencies
[grunt-init]:http://gruntjs.com/project-scaffolding
[npm init]:https://docs.npmjs.com/cli/init
[plugins]:http://gruntjs.com/plugins
[concatenation]:https://github.com/gruntjs/grunt-contrib-concat
[minification]:https://github.com/gruntjs/grunt-contrib-uglify
[linting]:https://github.com/gruntjs/grunt-contrib-jshint
[grunt.loadTasks]:http://gruntjs.com/api/grunt#grunt.loadtasks

