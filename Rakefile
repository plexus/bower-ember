require 'pathname'
require 'json'

ROOT          = Pathname(__FILE__).dirname
EMBER_ROOT    = ROOT.join('../ember.js')
EMBER_VERSION = "1.3.0-beta.4"

FEATURES = { "query-params" => true }

DIST_FILES = [
  'ember.js',
  'ember.prod.js',
]

BOWER_JSON = {
  "name"         => "ember",
  "version"      => EMBER_VERSION,
  "main"         => ["./ember.js"],
  "dependencies" => {
    "jquery"=>">= 1.7",
    "handlebars"=>">= 1.0.0"
  }
}

task :package_dir do
  Dir.chdir ROOT
end

task :ember_dir do
  Dir.chdir EMBER_ROOT
end

task :set_features => :ember_dir do
  feature_file = EMBER_ROOT.join('features.json')
  feature_set  = JSON.parse(feature_file.read)
  File.write(feature_file, JSON.pretty_generate(feature_set.merge(FEATURES)))
end

task :checkout_version => :ember_dir do
  sh "git checkout v#{EMBER_VERSION}"
end

DIST_FILES.each do |fn|
  file fn => EMBER_ROOT.join('dist', fn) do |t|
    cp t.prerequisites.first, ROOT.join(t.name), :verbose => true
  end
end

task :build_ember => :ember_dir do
  sh "rake dist"
end

task :bower_json do
  File.write(
    ROOT.join('bower.json'),
    JSON.pretty_generate(
      BOWER_JSON
    )
  )
end

desc "Complete build"
task :build => [:ember_dir, :clean, :checkout_version, :set_features, :build_ember, :bower_json, *DIST_FILES]

task :clean => [:ember_dir] do
  sh "git checkout ."
  sh "git clean -fd"
  sh "git checkout master"
end

task :default => :build

task :tag => [:build, :package_dir] do
  sh "git add ."
  sh "git commit -m 'Package #{EMBER_VERSION}'"
  sh "git tag v#{EMBER_VERSION}"
end
