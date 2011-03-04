require 'bundler'
Bundler::GemHelper.install_tasks

require 'rspec/core/rake_task'
RSpec::Core::RakeTask.new(:spec)

# We define a custom_spec that is default because it depends on the spec:prepare_fixtures task
# This way, CI servers should execute the spec:prepare_fixtures before the spec task, allowing all specs to pass!
task :custom_spec => "spec:prepare_fixtures" do
  `rake spec`
end
task :default => :custom_spec
  
namespace :spec do
  desc "Run all specs on multiple ruby versions (requires rvm)"
  task :portability do
    %w[1.8.6 1.8.7 1.9.2 jruby rubinius].each do |version|
      system <<-BASH
        bash -c 'source ~/.rvm/scripts/rvm;
                 rvm #{version};
                 echo "--------- version #{version} ----------\n";
                 gem install bundler
                 bundle install;
                 gem install rspec --version=1.3.1
                 rake spec:prepare_fixtures;
                 rake spec;'
      BASH
    end
  end

  desc "Run bundle install on each fixtures directories with Gemfile"
  task :prepare_fixtures do
    Dir.foreach("spec/fixtures") do |dir|
      if File.exists?("spec/fixtures/#{dir}/Gemfile")
        system <<-BASH
          cd spec/fixtures/#{dir};
          bundle install;
        BASH
      end
    end
  end
end
