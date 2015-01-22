require 'open3'
task default: :pdf
pdfs = []
Dir.glob('**/*.md') { |f| pdfs << f.sub(/\.md$/, '.pdf') }
pdfs = pdfs.sort
task :pdf => pdfs

extensions = "tex_math_single_backslash+footnotes+raw_tex+grid_tables+implicit_figures+fenced_code_blocks+definition_lists+pipe_tables"
filters = ['pandoc-plantuml-filter']
filters = filters.join ' --filter '

errors = []


rule '.pdf' => '.md' do |t| 
  sh "cat #{t.source} | gpp -H -x -DTEX=1 | pandoc  --template latex.tex -S -f markdown+#{extensions} -o #{t.name} --filter pandoc-citeproc --filter #{filters}" do |o, r|
    errors << "Error on #{t.name}: #{r}" if ! o
  end
end


rule '.pdf' => '.slide.md' do |t| 
  sh "cat #{t.source} | gpp -H -x -DTEX=1 | pandoc  -f markdown+#{extensions} -t beamer -o #{t.name}  --filter pandoc-citeproc -S" do |o, r|
    errors << "Error on #{t.name}: #{r}" if ! o
  end
end

namespace :lint do
  task :spell, [:document] do |t, args|
    Open3.popen3("hunspell -a #{args.document}") do |i, o, e, tz| 
      a = o.read
      a = a.gsub(/^\*$/, '').gsub("\n*\n", '').gsub("\n\n", '')
      puts a
    end
  end
  task :grammar, [:document] do |t, args|
    Open3.popen3("java -jar  ~/LanguageTool-2.6/languagetool-commandline.jar -d WHITESPACE_RULE -d EN_QUOTES #{args.document}") do |i, o, e, tz| 
      a = o.read
      puts a
    end
  end
end

puts errors
