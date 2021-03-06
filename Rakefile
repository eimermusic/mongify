require 'bundler'
Bundler::GemHelper.install_tasks

require 'cucumber/rake/task'
require 'rspec/core/rake_task'


task :cleanup_rcov_files do
  rm_rf 'coverage.data'
end

desc 'clobber generated files'
task :clobber do
  rm_rf "pkg"
  rm_rf "tmp"
  rm "Gemfile.lock" if File.exist?("Gemfile.lock")
end

namespace :rcov do
  Cucumber::Rake::Task.new(:cucumber) do |t|    
    t.rcov = true
    t.rcov_opts = %w{--exclude osx\/objc,gems\/,spec\/,features\/ --aggregate coverage.data}
    t.rcov_opts << %[-o "coverage"]
  end
 
  RSpec::Core::RakeTask.new(:rspec) do |t|
    t.rcov = true
    t.rcov_opts = %w{--exclude osx\/objc,gems\/,spec\/,features\/}
  end
 
  desc "Run both specs and features to generate aggregated coverage"
  task :all do |t|
    rm "coverage.data" if File.exist?("coverage.data")
    Rake::Task["rcov:cucumber"].invoke
    Rake::Task["rcov:rspec"].invoke
  end
end

namespace :test do
  namespace :mysql do
    desc "Setup a mysql database based on the spec/support/database.yml settings"
    task :setup do
      require 'spec/support/config_reader'
      require 'active_record'
      ::CONNECTION_CONFIG = ConfigReader.new('spec/support/database.yml')
      ActiveRecord::Base.establish_connection(CONNECTION_CONFIG.mysql)
      conn = ActiveRecord::Base.connection
      conn.create_table(:users, :force => true) do |t|
        t.string :first_name, :last_name
        t.timestamps
      end

      conn.create_table(:posts, :force => true) do |t|
        t.string :title
        t.integer :owner_id
        t.text :body
        t.datetime :published_at
        t.timestamps
      end

      conn.create_table(:comments, :force => true) do |t|
        t.text :body
        t.integer :post_id
        t.integer :user_id
        t.timestamps
      end
      puts "Finished"
    end
  end
end


task :default => ['rcov:all']