require 'digest/md5'
require 'securerandom'
require 'open3'
require 'erb'

task :default => [:process, :gen, :sync]

desc 'Process images/videos in tmp/ and move them to img/'
task :process do
  puts "Copying images from tmp/ to img/"
  incoming = Dir.new('tmp').reject { |f| f == '..' || f == '.' }
  incoming.each do |file|
    finfo = file.split('.')
    finfo[0] = SecureRandom.hex(8)

    if finfo[1] == 'mp4'
      cmd = "ffmpeg -i tmp/#{file} img/#{finfo[0]}.gif"

      sout, serr, status = Open3.capture3(cmd)

      if status.success?
        File.delete("tmp/#{file}")
      else
        puts serr
      end
    else
      File.rename("tmp/#{file}", "img/#{finfo.join('.')}")
    end
  end
end

desc 'Generate a new index.html with the images in ./img'
task :gen do
  puts "Generating index.html"
  template = File.read('index.html.erb')

  @images = Dir.new('img').to_a.select { |f| f.downcase.match(/\.gif/) }

  File.open('index.html', 'w') do |file|
    file.write(ERB.new(template).result)
  end
end

desc 'Detect duplicate files in the img directory'
task :dupes do
  known = {}

  Dir.glob('img/*', File::FNM_DOTMATCH).each do |f|
    next if File.directory?(f)

    key = Digest::MD5.hexdigest(IO.read(f)).to_sym

    if known.has_key?(key)
      known[key].push(f)
    else
      known[key] = [f]
    end
  end

  known.each_value do |a|
    next if a.length == 1

    puts '=== Identical Files ==='

    a.each { |f| puts "\t" + f }
  end
end

desc 'Upload the site to the server'
task :sync => :gen do
  system('find . -name .DS_Store -delete')
  system('find . -type f -exec chmod 644 {} \;')
  system('find . -type d -exec chmod 755 {} \;')
  system('rsync -azvv --exclude ".git" --delete . sinope:domains/beefed.it')
end
