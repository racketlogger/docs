require 'nanoc3/tasks'

task :default => [:compile]

desc "Compile the site"
task :compile do
  `nanoc compile`
end

desc "Test the output"
task :test => [:clean, :remove_output_dir, :compile] do
  require 'html/proofer'
  HTML::Proofer.new("./output").run
end

desc "Remove the output dir"
task :remove_output_dir do
  FileUtils.rm_r('output') if File.exist?('output')
end

# Prompt user for a commit message; default: P U B L I S H :emoji:
def commit_message(no_commit_msg = false)
  publish_emojis = [':boom:', ':rocket:', ':metal:', ':bulb:', ':zap:',
    ':sailboat:', ':gift:', ':ship:', ':shipit:', ':sparkles:', ':rainbow:']
  default_message = "P U B L I S H #{publish_emojis.sample}"

  unless no_commit_msg
    print "Enter a commit message (default: '#{default_message}'): "
    STDOUT.flush
    mesg = STDIN.gets.chomp.strip
  end

  mesg = default_message if mesg.nil? || mesg == ''
  mesg.gsub(/'/, '') # Allow this to be handed off via -m '#{message}'
end

desc "Publish!"
task :publish, [:no_commit_msg] => [:clean, :remove_output_dir] do |t, args|
  mesg = commit_message(args[:no_commit_msg])
  sh "bundle exec nanoc compile"

  `git checkout master`

  ENV['GIT_DIR'] = File.expand_path(`git rev-parse --git-dir`.chomp)
  ENV['RUBYOPT'] = nil
  old_sha = `git rev-parse refs/remotes/origin/gh-pages`.chomp
  Dir.chdir('output') do
    ENV['GIT_INDEX_FILE'] = gif = '/tmp/dev.gh.i'
    File.unlink(gif) if File.file?(gif)
    ENV['GIT_WORK_TREE'] = Dir.pwd
    `git add -A`
    tsha = `git write-tree`.strip
    puts "Created tree   #{tsha}"
    csha = `git commit-tree #{tsha} -m '#{mesg}'`.strip
    puts "Created commit #{csha}"
    puts `git show #{csha} --stat`
    puts "Updating gh-pages from #{old_sha}"
    `git update-ref refs/heads/gh-pages #{csha}`
    `git push -f origin gh-pages`
  end
  `git reset HEAD`
end
