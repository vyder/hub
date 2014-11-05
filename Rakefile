# TODO:
# 1. Let user pick custom install path
# 2. Create a loop from verifying ID to getting a new ID

require 'colorize'
require 'erb'
require 'json'

BINARY_NAME = "hub"
BINARY_TEMPLATE = File.read(File.expand_path("hub.erb"))
INSTALL_PATH = File.join(File.expand_path("~/bin"), "hub")
OPTIONS_FILEPATH = File.expand_path("options.json")

desc "Install '#{BINARY_NAME}'"
task :install => 'install:all'

namespace :install do
  options = {
    'github_id' => nil,
    'custom_install_path' => nil
  }

  task :all => [ :confirm_overwrite, :generate_options, :validate_username, :generate_binary, :copy_binary, :done ]

  task :confirm_overwrite do
    # Check if a 'hub' binary is already installed
    if File.exist? INSTALL_PATH
      puts "Looks like you already have a '#{BINARY_NAME}' installed.".light_yellow
      continue = prompt "Would you like to overwrite '#{BINARY_NAME}'? (y/n)".light_yellow

      abort unless continue
    end
  end

  task :generate_options do
    # Check if options file exists
    begin
      options = JSON.parse(File.read(OPTIONS_FILEPATH)) if File.exist? OPTIONS_FILEPATH
    rescue JSON::ParserError => e
      puts e.inspect
      abort
      puts "Looks like '#{OPTIONS_FILEPATH}' is corrupt or unreadable.".light_yellow
      continue = prompt "Would you like to overwrite #{OPTIONS_FILEPATH}? (y/n)".light_yellow

      abort unless continue
    end
  end

  task :validate_username do
    # Check if github ID is an option
    hasID = false
    unless options['github_id'].nil? or options['github_id'].empty?
      puts "The Github ID we have on file is: '".light_yellow + options['github_id'].blue + "'.".light_yellow
      continue = prompt "Would you like to continue installing with ID '".light_yellow +
                              options['github_id'].blue + "'? (y/n)".light_yellow

      hasID = true if continue
    end

    # Get a new Github ID to use for install
    unless hasID
      github_id = prompt_for_value "Please enter you Github ID: ".light_yellow

      puts "Generating options with ID '".light_yellow + github_id.blue + "'.".light_yellow

      options['github_id'] = github_id
    end

    unless profile_exists? options['github_id']
      puts "The Github ID you entered: '".light_yellow + options['github_id'].blue + "' does not seem to exist.".light_yellow
      continue = prompt "Would you like to continue installing with ID '".light_yellow +
                              options['github_id'].blue + "' anyway? (y/n)".light_yellow

      abort unless continue
    end
  end

  task :generate_binary do
    # Create template
    template = ERB.new(BINARY_TEMPLATE)

    # Generate binary
    File.open(BINARY_NAME, 'w') do |f|
      f.write template.result(binding)
    end
  end

  task :copy_binary do
    puts "Copying '#{BINARY_NAME}' to '#{INSTALL_PATH}'...".green
    FileUtils.cp( "./#{BINARY_NAME}", INSTALL_PATH )

    # Make the file executable
    FileUtils.chmod("+x", INSTALL_PATH)

    puts "Done.".green
  end

  task :done do
    # Finally, write the current options down to file
    File.open(OPTIONS_FILEPATH, 'w') do |f|
      f.write JSON.pretty_generate(options)
    end
  end
end

desc "Uninstall '#{BINARY_NAME}'"
task :uninstall do
  puts "Removing '#{INSTALL_PATH}'...".green

  FileUtils.rm( INSTALL_PATH )
  puts "Done.".green
end

def abort
  puts "\nInstall aborted!".red
  exit 1
end

# This nifty ruby snippet is copied and modified
# from defunkt's dotjs repository.
# Source: https://github.com/defunkt/dotjs/blob/master/Rakefile
def prompt( ask = "" )
  answer = nil

  puts ask
  begin
    until %w( k ok y yes n no ).include?(answer = $stdin.gets.chomp.downcase)
      puts "(psst... please type y or n)"
      puts ask
    end
  rescue Interrupt
    return false
  end

  return false if answer =~ /n/

  return true
end

# def prompt_for_value( ask = "", &validator )
def prompt_for_value( ask = "" )
  answer = nil

  # Default validator validates
  # unless validator
  #   validator = {|x| true}
  # end

  puts ask
  begin
    answer = $stdin.gets.chomp
  rescue Interrupt
    abort
  end

  return answer
end

# Validate username by pinging github profile page and checking HTTP status code
def profile_exists? github_id
  http_status_code = %x( curl -s -o /dev/null -w "%{http_code}" https://github.com/#{github_id})

  http_status_code == "200"
end
