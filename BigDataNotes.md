# Big Data: An Adventure

## NoSQL databases are the antithesis to Relational Database Normalization.

- Structure the data in the quickest way of querying.
- Weigh the likelyhood of change


## Hadoop

Start hadoop:

`/usr/local/Cellar/hadoop/2.4.0/sbin/start-all.sh`

Copy a file into hadoop:

`hadoop fs -put ../input/ncdc/sample.txt /user/blischalk/input`

`hadoop dfs -copyFromLocal /home/hadoop/dft dft`

Run the jar on hadoop:

`hadoop jar target/ch02-3.0.jar MaxTemperature input/sample.txt output`

## Run map reduce job in ruby:

Map:

	#!/usr/bin/env ruby

	STDIN.each_line do |line|
      val = line
      year, temp, q = val[15,4], val[87,5], val[92,1]
      puts "#{year}\t#{temp}" if (temp != "+9999" && q =~ /[01459]/)
    end
    
Reduce:

	#!/usr/bin/env ruby

	last_key, max_val = nil, -1000000
	STDIN.each_line do |line|
	  key, val = line.split("\t")
	  if last_key && last_key != key
        puts "#{last_key}\t#{max_val}"
        last_key, max_val = key, val.to_i
      else
        last_key, max_val = key, [max_val, val.to_i].max
      end
    end
    puts "#{last_key}\t#{max_val}" if last_key

Command Line:

	hadoop jar /usr/local/Cellar/hadoop/2.4.0/libexec/share/hadoop/tools/lib/hadoop-streaming-2.4.0.jar \
	-input /user/blischalk/input/sample.txt \
	-output myOutput -mapper "max_temperature_map.rb | sort | max_temperature_reduce.rb" \
	-reducer max_temperature_reduce.rb \
	-file max_temperature_map.rb \
	-file max_temperature_reduce.rb
	
## Elasticsearch

### Best For:
- Searching a large number of product descriptions for the best match for a specific phrase (like "chef's knife") and returning the best results
- Breaking down the various departments where "chef's knife" appears (Faceting)
- Searching text for words that sound like "season"
- Auto-completing a search box based on partially typed words based on previously issued searches while accounting for mis-spellings
- Storing a large quantity of semi-structured JSON data in a distributed fashion with a specified level of redundancy across a cluster of machines.


### Bad For:
- Calculating how many items are left in the inventory
- Figuring out the sum of all line-items on all the invoices sent out in a given month
- Executing two operations transactionally with rollback support
- Creating records that are guaranteed to be unique across multiple given terms, for instance a phone number and extension