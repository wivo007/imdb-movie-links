require 'uri'
require 'rake/clean'
references  = 'references.csv'
imdb_mirror =  URI.parse('ftp://ftp.fu-berlin.de/pub/misc/movies/database/')
movie_links =  URI.parse(imdb_mirror.to_s + 'movie-links.list.gz')

CLEAN.include(references, 'graph.dot', 'graph.svg')

def mirror(uri, verbose = false)
  sh "wget #{verbose ? '-v' : '-q'} -c --no-host-directories -N -r -l 1 --no-remove-listing #{uri}"
end

desc "perform a full imdb mirror"
task :mirror do
  mirror imdb_mirror
end

file movie_links.path do
  mirror movie_links
end

file references => movie_links.path do
  sh "zcat < #{movie_links.path} | ./links.rb references"
end

task :rank => references do
  sh "./rank.py #{references} --rank"
end

file 'graph.dot' => references do
  sh "./rank.py #{references} --graph --max=#{ENV['MAX'] || 150} > graph.dot"
end

file 'graph.svg' => 'graph.dot' do
 sh "dot -Tsvg graph.dot -o graph.svg"
end
