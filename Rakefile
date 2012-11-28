require 'csv'
require 'json'
require 'tmpdir'

task :default => :convert

desc 'Convert to .grn'
task :convert do
  source = 'http://www.post.japanpost.jp/zipcode/dl/kogaki/zip/ken_all.zip'

  File.open('groonga/01-data.grn', 'w') do |grn|
    Dir.mktmpdir('ken_all-') do |tmpdir|
      Dir.chdir(tmpdir) do
        sh "curl -O -s #{source}"
        sh 'unzip ken_all.zip'
        sh 'iconv -f cp932 -t utf8 < KEN_ALL.CSV > ken_all.utf8.csv'

        CSV.open('ken_all.utf8.csv') do |csv|
          grn.puts 'load --table zip \\'
          zips = csv.map do |row|
            zip7 = row[2]
            address = row[6..8].join
            {
              '_key' => zip7,
              'address' => address
            }
          end
          grn.puts zips.to_json
        end
      end
    end
  end
end
