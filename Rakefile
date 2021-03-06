require 'rake'
require 'rake/testtask'
require 'rake/rdoctask'
require 'rake/gempackagetask'

$LOAD_PATH.unshift("lib")
require 'shoulda'
load 'tasks/shoulda.rake'

# Test::Unit::UI::VERBOSE
test_files_pattern = 'test/{unit,functional,other,matchers}/**/*_test.rb'
Rake::TestTask.new do |t|
  t.libs << 'lib' << 'test'
  t.pattern = test_files_pattern
  t.verbose = false
end

Rake::RDocTask.new { |rdoc|
  rdoc.rdoc_dir = 'doc'
  rdoc.title    = "Shoulda -- Making tests easy on the fingers and eyes"
  rdoc.options << '--line-numbers'
  rdoc.template = "#{ENV['template']}.rb" if ENV['template']
  rdoc.rdoc_files.include('README.rdoc', 'CONTRIBUTION_GUIDELINES.rdoc', 'lib/**/*.rb')
}

desc "Run code-coverage analysis using rcov"
task :coverage do
  rm_rf "coverage"
  files = Dir[test_files_pattern]
  system "rcov --rails --sort coverage -Ilib #{files.join(' ')}"
end

desc 'Update documentation on website'
task :sync_docs => 'rdoc' do
  `rsync -ave ssh doc/ dev@dev.thoughtbot.com:/home/dev/www/dev.thoughtbot.com/shoulda`
end

desc 'Default: run tests for all supported versions of Rails'
task :default do
  %w(2.3.8 3.0.0.beta3).each do |version|
    system("RAILS_VERSION=#{version} rake -s test;")
  end
end

eval("$specification = begin; #{IO.read('shoulda.gemspec')}; end")
Rake::GemPackageTask.new $specification do |pkg|
  pkg.need_tar = true
  pkg.need_zip = true
end

desc "Clean files generated by rake tasks"
task :clobber => [:clobber_rdoc, :clobber_package]

