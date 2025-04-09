require 'rake'
require 'fileutils'
require "tmpdir"

GITHUB_PAGES_BRANCH = "gh-pages"
SITE_DIR = "_site"

desc "Build the site with Jekyll"
task :build, [:baseurl] do |task, args|
  baseurl = args[:baseurl] || ""
  sh "bundle install"
  sh "bundle exec jekyll build -d #{SITE_DIR} --baseurl #{baseurl}"
end

desc "Commit source code to main"
task :commit_source do
  sh "git add ."
  sh %(git commit -m "Update source site content" || echo 'Nothing to commit on main')
  sh "git push origin main"
end

desc "Deploy #{SITE_DIR} to #{GITHUB_PAGES_BRANCH} branch"
task :deploy do
  origin = `git config --get remote.origin.url`
  fail "origin is empty" if origin.empty?

  Rake::Task[:build].invoke

  current_public_folder = Dir.pwd
  Dir.mktmpdir do |tmp|
    cp_r "#{SITE_DIR}/.", tmp

    Dir.chdir tmp

    sh "git init"
    sh "git checkout #{GITHUB_PAGES_BRANCH} 2>/dev/null || git checkout --orphan #{GITHUB_PAGES_BRANCH}"
    sh "git add . && git commit -m 'Site updated at #{Time.now.utc}'"

    sh "git remote add origin #{origin}"

    puts "Pushing to #{origin}"
    sh "git push --force origin #{GITHUB_PAGES_BRANCH}"

    Dir.chdir current_public_folder
  end
end

desc "Full deploy: commit source and publish site"
task commit_and_push: [ :commit_source, :deploy ]
