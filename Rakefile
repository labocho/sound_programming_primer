ZIP_FILE_URLS = %w(
  http://floor13.sakura.ne.jp/book06/chapter01.zip
  http://floor13.sakura.ne.jp/book06/chapter02.zip
  http://floor13.sakura.ne.jp/book06/chapter03.zip
  http://floor13.sakura.ne.jp/book06/chapter04.zip
  http://floor13.sakura.ne.jp/book06/chapter05.zip
  http://floor13.sakura.ne.jp/book06/chapter06.zip
  http://floor13.sakura.ne.jp/book06/chapter07.zip
  http://floor13.sakura.ne.jp/book06/chapter08.zip
  http://floor13.sakura.ne.jp/book06/chapter09.zip
  http://floor13.sakura.ne.jp/book06/chapter10.zip
  http://floor13.sakura.ne.jp/book06/chapter11.zip
  http://floor13.sakura.ne.jp/book06/chapter12.zip
)

DIRS = []
ZIP_FILES = []
ZIP_FILE_URLS.each do |url|
  zip_file = url.gsub(%r(.+/), "")
  dir = zip_file.gsub(/\.zip$/, "")

  ZIP_FILES << zip_file
  DIRS << dir

  file zip_file do
    sh "curl #{url} > #{zip_file}"
  end

  directory dir => "#{dir}.zip" do
    case dir
    when "chapter07"
      sh "unzip #{dir}.zip -d chapter07"
    else
      sh "unzip #{dir}.zip"
    end
  end
end

task :unzip => DIRS

task :clean_dirs do
  sh "rm", "-rf", *DIRS
end

task :clean do
  sh "rm", "-rf", *DIRS
  sh "rm", *ZIP_FILES
end

task :convert_encoding => :unzip do
  Dir.glob("**/*.{c,h}") do |path|
    s = File.read(path, encoding: "utf-8")
    next if s.valid_encoding?

    warn "Convert encoding and new line character: #{path}"
    File.write(path, s.force_encoding("cp932").encode("utf-8").gsub(/\r\n/, "\n").gsub(/^\s+$/, ""))
  end
end

task :patch => :convert_encoding do
  Dir.glob("**/wave.h") do |path|
    system "patch -sN #{path} wave.h.patch"
    sh "rm -f #{path}.rej"
  end
end

task :default => :patch
