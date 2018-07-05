安装
  npm install gulp -g
  接着我们要进去到项目的根目录再安装一遍（确保你根目录存在package.json文件）：npm install gulp --save-dev

安装Gulp插件:
npm install gulp-ruby-sass gulp-autoprefixer gulp-minify-css gulp-jshint gulp-concat gulp-uglify gulp-imagemin gulp-notify gulp-rename gulp-livereload gulp-cache del --save-dev
  sass的编译（gulp-ruby-sass）
  自动添加css前缀（gulp-autoprefixer）
  压缩css（gulp-minify-css）
  js代码校验（gulp-jshint）
  合并js文件（gulp-concat）
  压缩js代码（gulp-uglify）
  压缩图片（gulp-imagemin）
  自动刷新页面（gulp-livereload）
  图片缓存，只有图片替换了才压缩（gulp-cache）
  更改提醒（gulp-notify）
  清除文件（del）
  
加载插件
var gulp = require('gulp'),
    sass = require('gulp-ruby-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    minifycss = require('gulp-minify-css'),
    jshint = require('gulp-jshint'),
    uglify = require('gulp-uglify'),
    imagemin = require('gulp-imagemin'),
    rename = require('gulp-rename'),
    concat = require('gulp-concat'),
    notify = require('gulp-notify'),
    cache = require('gulp-cache'),
    livereload = require('gulp-livereload'),
    del = require('del');
    
 建立任务
     gulp.task('styles', function () {...}):gulp.task这个API用来创建任务，在命令行下可以输入$ gulp styles来执行上面的任务
     return gulp.src('src/styles/main.scss'):gulp.src这个API设置需要处理的文件的路径，可以是多个文件以数组的形式[main.scss, vender.scss]，也可以是正则表达式/**/*.scss     	
    .pipe(sass({ style: 'expanded' })):我们使用.pipe()这个API将需要处理的文件导向sass插件
    .pipe(gulp.dest('dist/assets/css')):gulp.dest()API设置生成文件的路径，一个任务可以有多个生成路径，一个可以输出未压缩的版本，另一个可以输出压缩后的版本




     1:编译sass、自动添加css前缀和压缩
       首先我们编译sass，添加前缀，保存到我们指定的目录下面，还没结束，我们还要压缩，给文件添加.min后缀再输出压缩文件到指定目录，最后提醒任务完成了
       gulp.task('styles', function() {
                return gulp.src('src/styles/main.scss')
                  .pipe(sass({ style: 'expanded' }))
                  .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 8', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'))
                  .pipe(gulp.dest('dist/assets/css'))
                  .pipe(rename({suffix: '.min'}))
                  .pipe(minifycss())
                  .pipe(gulp.dest('dist/assets/css'))
                  .pipe(notify({ message: 'Styles task complete' }));
                });
     2:js代码校验、合并和压缩
     gulp.task('scripts', function() {
        return gulp.src('src/scripts/**/*.js')
          .pipe(jshint('.jshintrc'))
          .pipe(jshint.reporter('default'))
          .pipe(concat('main.js'))
          .pipe(gulp.dest('dist/assets/js'))
          .pipe(rename({suffix: '.min'}))
          .pipe(uglify())
          .pipe(gulp.dest('dist/assets/js'))
          .pipe(notify({ message: 'Scripts task complete' }));
      });
      3.压缩图片
      使用imagemin插件把所有在src/images/目录以及其子目录下的所有图片（文件）进行压缩，我们可以进一步优化，利用缓存保存已经压缩过的图片，使用之前装过的gulp-cache插件
      gulp.task('images', function() {
          return gulp.src('src/images/**/*')
            .pipe(cache(imagemin({ optimizationLevel: 5, progressive: true, interlaced: true })))
            .pipe(gulp.dest('dist/assets/img'))
            .pipe(notify({ message: 'Images task complete' }));
        });
      4:清除文件:在任务执行前，最好先清除之前生成的文件
      gulp.task('clean', function(cb) {
              del(['dist/assets/css', 'dist/assets/js', 'dist/assets/img'], cb)
          });
      5:设置默认任务（default）:我们在命令行下输入 gulp执行的就是默认任务，现在我们为默认任务指定执行上面写好的三个任务
      clean任务执行完成了才会去运行其他的任务，在gulp.start()里的任务执行的顺序是不确定的，所以将要在它们之前执行的任务写在数组里面
      gulp.task('default', ['clean'], function() {
                gulp.start('styles', 'scripts', 'images');
            });
      6L;监听文件:为了监听文件的是否修改以便执行相应的任务，我们需要创建一个新的任务，然后利用gulp.watchAPI实现：
          gulp.task('watch', function() {
            // Watch .scss files
            gulp.watch('src/styles/**/*.scss', ['styles']);
            // Watch .js files
            gulp.watch('src/scripts/**/*.js', ['scripts']);
            // Watch image files
            gulp.watch('src/images/**/*', ['images']);
          });
      7:自动刷新页面:gulp也可以实现当文件修改时自动刷新页面，我们要修改watch任务，配置LiveReload：
        gulp.task('watch', function() {
          // Create LiveReload server
          livereload.listen();
          // Watch any files in dist/, reload on change
          gulp.watch(['dist/**']).on('change', livereload.changed);
        });
        
        
        
        
        
        /*!
 * gulp
 * $ npm install gulp-ruby-sass gulp-autoprefixer gulp-minify-css gulp-jshint gulp-concat gulp-uglify gulp-imagemin gulp-notify gulp-rename gulp-livereload gulp-cache del --save-dev
 */
// Load plugins
var gulp = require('gulp'),
    sass = require('gulp-ruby-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    minifycss = require('gulp-minify-css'),
    jshint = require('gulp-jshint'),
    uglify = require('gulp-uglify'),
    imagemin = require('gulp-imagemin'),
    rename = require('gulp-rename'),
    concat = require('gulp-concat'),
    notify = require('gulp-notify'),
    cache = require('gulp-cache'),
    livereload = require('gulp-livereload'),
    del = require('del');
// Styles
gulp.task('styles', function() {
  return gulp.src('src/styles/main.scss')
    .pipe(sass({ style: 'expanded', }))
    .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 8', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'))
    .pipe(gulp.dest('dist/styles'))
    .pipe(rename({ suffix: '.min' }))
    .pipe(minifycss())
    .pipe(gulp.dest('dist/styles'))
    .pipe(notify({ message: 'Styles task complete' }));
});
// Scripts
gulp.task('scripts', function() {
  return gulp.src('src/scripts/**/*.js')
    .pipe(jshint('.jshintrc'))
    .pipe(jshint.reporter('default'))
    .pipe(concat('main.js'))
    .pipe(gulp.dest('dist/scripts'))
    .pipe(rename({ suffix: '.min' }))
    .pipe(uglify())
    .pipe(gulp.dest('dist/scripts'))
    .pipe(notify({ message: 'Scripts task complete' }));
});
// Images
gulp.task('images', function() {
  return gulp.src('src/images/**/*')
    .pipe(cache(imagemin({ optimizationLevel: 3, progressive: true, interlaced: true })))
    .pipe(gulp.dest('dist/images'))
    .pipe(notify({ message: 'Images task complete' }));
});
// Clean
gulp.task('clean', function(cb) {
    del(['dist/assets/css', 'dist/assets/js', 'dist/assets/img'], cb)
});
// Default task
gulp.task('default', ['clean'], function() {
    gulp.start('styles', 'scripts', 'images');
});
// Watch
gulp.task('watch', function() {
  // Watch .scss files
  gulp.watch('src/styles/**/*.scss', ['styles']);
  // Watch .js files
  gulp.watch('src/scripts/**/*.js', ['scripts']);
  // Watch image files
  gulp.watch('src/images/**/*', ['images']);
  // Create LiveReload server
  livereload.listen();
  // Watch any files in dist/, reload on change
  gulp.watch(['dist/**']).on('change', livereload.changed);
});
      
