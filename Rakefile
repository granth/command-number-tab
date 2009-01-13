SCRIPTS_DIR = "#{ENV['HOME']}/Library/Scripts/Applications"

desc "Install AppleScripts for Safari and Terminal"
task :install => [:install_safari, :install_terminal]

desc "Compile AppleScripts for Safari and Terminal"
task :compile => [:compile_safari, :compile_terminal]

# utility bits
tab_n    = (1..9).map {|n| "Go to Tab #{n}.scpt" }
last_tab = 'Go to Last Tab.scpt'

def compile(file, source)
  sh "osacompile -d -o '#{file}' -e '#{source}'"
end

def hide_extension(file)
  sh "test -e /usr/bin/SetFile && /usr/bin/SetFile -a E '#{file}'"
end

def install_scripts(app)
  source_dir  = app
  install_dir = "#{SCRIPTS_DIR}/#{app.capitalize}"

  mkdir_p install_dir
  cp Dir["#{source_dir}/*.scpt"], install_dir
  Dir["#{install_dir}/*.scpt"].each {|f| hide_extension f }
end

desc "Install Safari AppleScripts"
task :install_safari => :compile_safari do
  install_scripts 'safari'
end

desc "Install Terminal AppleScripts"
task :install_terminal => :compile_terminal do
  install_scripts 'terminal'
end

desc "Compile AppleScripts for Safari"
task :compile_safari  # built up below

directory 'safari'
tab_n.map {|f| "safari/#{f}"}.each do |f|
  file f => 'safari' do
    n = f[/\d/]
    compile f, <<-END
      tell application "Safari"
        try
          tell front window to set current tab to tab #{n}
        end try
      end tell
    END
  end
  file :compile_safari => f
end

file "safari/#{last_tab}" do |f|
  compile f, <<-END
    tell application "Safari"
      try
        tell front window to set current tab to last tab
      end try
    end tell
  END
end
file :compile_safari => "safari/#{last_tab}"

desc "Compile AppleScripts for Terminal"
task :compile_terminal  # built up below

directory 'terminal'
tab_n.map {|f| "terminal/#{f}"}.each do |f|
  file f => 'terminal' do
    n = f[/\d/]
    compile f, <<-END
      tell application "Terminal"
        try
          set selected of tab #{n} of front window to true
        end try
      end tell
    END
  end
  file :compile_terminal => f
end

file "terminal/#{last_tab}" do |f|
  compile f, <<-END
    tell application "Terminal"
      try
        set selected of last tab of front window to true
      end try
    end tell
  END
end
file :compile_terminal => "terminal/#{last_tab}"

desc "Remove generated files"
task :clean do
  rm_rf ["scripts", "safari", "terminal"]
end
