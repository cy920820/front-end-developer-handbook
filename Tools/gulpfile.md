## gulpfile

> gulp版本基于v4.0.0
> 使用函数定义任务，最终通过gulp.series进行串并行执行任务

```javascript
const gulp = require('gulp')
// const stylus = require('gulp-stylus')
const babel = require('gulp-babel')
const uglyfy = require('gulp-uglify')
const rev = require('gulp-rev')
const revRewrite = require('gulp-rev-replace')
const htmlmin = require('gulp-htmlmin')
const postcss = require('gulp-postcss')
const autoprefixer = require('autoprefixer')
const del = require('del')

const paths = {
  images: {
    src: './src/images/**',
    dest: './dist/images',
    manifest: './manifest/images.manifest.json',
    manifestDest: './dist'
  },
  styles: {
    src: './src/css/**',
    dest: './dist/css',
    manifest: './manifest/css.manifest.json',
    manifestDest: './dist'
  },
  scripts: {
    src: './src/js/*.js',
    dest: './dist/js',
    manifest: './manifest/js.manifest.json',
    manifestDest: './dist'
  },
  html: {
    src: './src/*.html',
    dest: './dist',
    imagesManifestSrc: './dist/manifest/images.manifest.json',
    cssManifestSrc: './dist/manifest/css.manifest.json',
    jsManifestSrc: './dist/manifest/js.manifest.json'
  }
}

function clean() {
  // You can use multiple globbing patterns as you would with `gulp.src`,
  // for example if you are using del 2.0 or above, return its promise
  return del([ './dist' ])
}

/*
 * Define our tasks using plain functions
 */

function images() {
  return gulp.src(paths.images.src)
  .pipe(rev())
  .pipe(gulp.dest(paths.images.dest))
  .pipe(rev.manifest(paths.images.manifest))
  .pipe(gulp.dest(paths.images.manifestDest))
}

function styles() {
  return gulp.src(paths.styles.src)
  .pipe(rev())
  .pipe(postcss([ autoprefixer() ]))
  .pipe(gulp.dest(paths.styles.dest))
  .pipe(rev.manifest(paths.styles.manifest))
  .pipe(gulp.dest(paths.styles.manifestDest))
}

function scripts() {
  return gulp.src(paths.scripts.src, { sourcemaps: true })
  .pipe(babel({
    presets: ['env']
  }))
  .pipe(uglyfy())
  .pipe(rev())
  .pipe(gulp.dest(paths.scripts.dest))
  .pipe(rev.manifest(paths.scripts.manifest))
  .pipe(gulp.dest(paths.scripts.manifestDest))
}

function html() {
  const imagesManifest = gulp.src(paths.html.imagesManifestSrc)
  const cssManifest = gulp.src(paths.html.cssManifestSrc)
  const jsManifest = gulp.src(paths.html.jsManifestSrc)
  return gulp.src(paths.html.src)
  .pipe(revRewrite({ manifest: imagesManifest }))
  .pipe(revRewrite({ manifest: cssManifest }))
  .pipe(revRewrite({ manifest: jsManifest }))
  .pipe(htmlmin({
    minifyCSS: true,
    minifyJS: true,
    collapseWhitespace: true
  }))
  .pipe(gulp.dest(paths.html.dest))
}

const build = gulp.series(clean, images, styles, scripts, html)

gulp.task('default', build)
```
