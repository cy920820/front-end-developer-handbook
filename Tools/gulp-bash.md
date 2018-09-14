# gulp  前端工作流基本母版


```javascript
const gulp = require('gulp')
const useref = require('gulp-useref')
const uglify = require('gulp-uglify')
const csso = require('gulp-csso')
const filter = require('gulp-filter')
const rev = require('gulp-rev')
const revReplace = require('gulp-rev-replace')

// 定义过滤器
const jsFilter = filter('**/*.js', {restore: true})
const cssFilter = filter('**/*.css', {restore: true})
const indexHtmlFilter = filter(['**', '!**/index.html'], {restore: true})

gulp.task('default', function() {
  return gulp.src('/**/*.html')
    .pipe(useref())
    .pipe(jsFilter)
    .pipe(uglify())
    .pipe(jsFilter.restore)
    .pipe(cssFilter)
    .pipe(csso())
    .pipe(cssFilter.restore)
    .pipe(indexHtmlFilter)
    .pipe(rev())
    pipe(indexHtmlFilter.restore)
    .pipe(revReplace())
    .pipe(gulp.dest('/dist'))
})

```
