# grunt

###install
			npm install grunt-cli -g
			进入项目根目录配置package.json,gruntFile
			npm install
			grunt

##### gruntFile
		
		module.exports = function(grunt){
			grunt.initConfig({
				pkg:grunt.file.readJSON('package.json'),
				uglify:{
					banner:'',
					beautify:{
						beautify:false,
						ascii-only:true
					}
				},
				....
			})
			
			grunt.loadNpmTask('uglify');
			
			grunt.registerTask('default',['uglify']);	
		}