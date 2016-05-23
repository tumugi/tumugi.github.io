## Your first tumugi workflow

This guide explain about how to write a workflow doing following tasks by tumugi.

1. Download archived daily access logs from remote servers using `wget` command
2. Count number of rows group by URI and save result into CSV file

This guide assumes you are using Unix like OS, such as Ubuntu, CentOS, or Mac OS X.

## Prerequisities

- Ruby 2.1 or later
- Bundler
- graphviz

## Installation

Create `Gemfile` and write following contents:

```ruby
source 'https://rubygems.org'

gem 'tumugi', '~> 0.4.5'
gem 'tumugi-plugin-command', '~> 0.1.0'
```

And then execute:

```bash
$ bundle install
```

## Define workflow by tumugi DSL

You can define workflow above using tumugi DSL.

```rb
require 'ltsv'
require 'zip'

####################################################
# 1. Archived log download
####################################################

task :download_log, type: :command do
  param :host, default: 'https://tumugi.github.io'
  param :log_filename, type: :string
  param :day, type: :time # <= This value is auto binding from CLI parameter

  param_set :log_filename, -> {
    "access_#{day.strftime('%Y%m%d')}.log.zip"
  }
  param_set :command, -> {
    url = "#{host}/data/#{log_filename}"
    "wget #{url} -O #{output.path}"
  }

  output {
    target(:local_file, "/tmp/#{log_filename}")
  }
end

####################################################
# 2. Count rows group by URI
####################################################

task :count_rows_group_by_uri do
  requires :download_log
  output target(:local_file, '/tmp/result.csv')
  run {
    counts = {}
    Zip::File.open(input.path) do |zip_file|
      zip_file.each do |entry|
        entry.get_input_stream.each do |line|
          values = LTSV.parse(line).first
          counts[values[:uri]] ||= 0
          counts[values[:uri]] += 1
        end
      end
    end
    output.open('w') do |o|
      counts.each do |k, v|
        o.puts "#{k},#{v}"
      end
    end
  }
end

####################################################
# Root Task
####################################################

task :main do
  requires :count_rows_group_by_uri
  run {
    log File.read(input.path)
  }
end
```

Save this code as `recipe1.rb`, then check this workflow.
Tumugi provides DAG (Directed Acyclic Graph) of workflow visualize feature.
`show` command can visualize DAG (Directed Acyclic Graph) of workflow like:

```sh
$ bundle exec tumugi show -f recipe1.rb -p day:2016-05-02 -o recipe1.png main
```

![recipe1_dag](../images/recipe1_dag.png)

Check visualized workflow and it's OK, you can run it.

```sh
$ bundle exec tumugi run -f recipe1.rb -p day:2016-05-02 main
```

Then you can get result like this:

```sh
I, [2016-05-24T01:09:49.243359 #59676]  INFO -- : Parameters: {"day"=>"2016-05-02"}
I, [2016-05-24T01:09:49.274092 #59676]  INFO -- : start: download_log
I, [2016-05-24T01:09:49.274243 #59676]  INFO -- : run: download_log
I, [2016-05-24T01:09:49.274368 #59676]  INFO -- : Execute command: wget https://tumugi.github.io/data/access_20160502.log.zip -O /tmp/access_20160502.log.zip -q
I, [2016-05-24T01:09:49.285433 #59676]  INFO -- : completed: download_log
I, [2016-05-24T01:09:49.285554 #59676]  INFO -- : start: count_group_by_uri
I, [2016-05-24T01:09:49.285668 #59676]  INFO -- : run: count_group_by_uri
I, [2016-05-24T01:09:50.807095 #59676]  INFO -- : completed: count_group_by_uri
I, [2016-05-24T01:09:50.807156 #59676]  INFO -- : start: main
I, [2016-05-24T01:09:50.807206 #59676]  INFO -- : run: main
I, [2016-05-24T01:09:50.813139 #59676]  INFO -- : /api/v1/messages,7150
/api/v1/textdata,7373
/api/v1/people,7377

I, [2016-05-24T01:09:50.813196 #59676]  INFO -- : completed: main
I, [2016-05-24T01:09:50.815432 #59676]  INFO -- : Result report:
+--------------------+--------------------+-------------------------------------------------------------------------------------------------------+-----------+
| Task               | Requires           | Parameters                                                                                            | State     |
+--------------------+--------------------+-------------------------------------------------------------------------------------------------------+-----------+
| main               | count_group_by_uri |                                                                                                       | completed |
| count_group_by_uri | download_log       |                                                                                                       | completed |
| download_log       |                    | host=https://tumugi.github.io                                                                         | completed |
|                    |                    | day=2016-05-02 00:00:00 +0900                                                                         |           |
|                    |                    | log_filename=access_20160502.log.zip                                                                  |           |
|                    |                    | command=wget https://tumugi.github.io/data/access_20160502.log.zip -O /tmp/access_20160502.log.zip -q |           |
|                    |                    | output_file=                                                                                          |           |
+--------------------+--------------------+-------------------------------------------------------------------------------------------------------+-----------+
```
