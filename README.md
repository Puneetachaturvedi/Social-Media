# Social-Media
Analyze Dataset from Stack Exchange
Big Data Hadoop Project submission on Social Media
Question – 1 Top 10 most commonly used tags in this data set
Solution – 1
Pig Script –
questions_data = LOAD  '/user/ebc86e37a5c7b529c82d77dbb24392/VinayT/Datafiles/answers.txt' USING PigStorage('_') AS (c1:chararray,c2:chararray,c3:chararray,c4:chararray,tags:chararray,c6:chararray,c7:chararray,c8:chararray,c9:chararray,c10:chararray,c11:chararray);
tags_data = FOREACH questions_data GENERATE tags;
tags_seperated = FOREACH tags_data GENERATE TOKENIZE(tags);
tags = FOREACH tags_seperated GENERATE FLATTEN($0) AS tag;
tags_group = GROUP tags BY tag;
tags_count = FOREACH tags_group GENERATE group, COUNT(tags) as cnt;
tags_sorted = ORDER tags_count BY cnt DESC;
top10_tags = LIMIT tags_sorted 10;
DUMP top10_tags;
Output –
 

Question – 2 Average time to answer questions
Solution – 2
Pig Script –
questions_data = LOAD  '/user/ebc86e37a5c7b529c82d77dbb24392/VinayT/Datafiles/answers.txt' USING PigStorage('_') AS (qid:chararray,c2:chararray,c3:chararray,c4:chararray,c5:chararray,c6:chararray,c7:chararray,c8:chararray,c9:chararray,c10:chararray,at:long);
group_qid = GROUP questions_data BY qid;
avg_qtime = FOREACH group_qid GENERATE group, questions_data.qid as qid, AVG(questions_data.at) as avg;
avg_qtime1 = FOREACH avg_qtime GENERATE qid, ToDate((long)avg*1000) as avg_ans_time;
DUMP avg_qtime1;
Output  –
 

Question – 3 Number of questions which got answered within 1 hour
Solution – 3
Pig Script –
questions_data = LOAD  '/user/ebc86e37a5c7b529c82d77dbb24392/VinayT/Datafiles/answers.txt' USING PigStorage('_') AS (qid:chararray,c2:chararray,c3:chararray,c4:chararray,c5:chararray,c6:chararray,c7:chararray,c8:chararray,c9:chararray,c10:chararray,at:long);
qid_at = FOREACH questions_data GENERATE qid as q, ToDate((long)at*1000) as time;
qid_gethour = FOREACH qid_at GENERATE q as q, time as time, GetHour(time) as hour;
qid_hourless1 = FILTER qid_gethour by hour <= 1;
DUMP qid_hourless1;
Output –
 


Question – 4 Tags of questions which got answered within 1 hour
Solution – 4
Pig Script –
questions_data = LOAD  '/user/ebc86e37a5c7b529c82d77dbb24392/VinayT/Datafiles/answers.txt' USING PigStorage('_') AS (qid:chararray,c2:chararray,c3:chararray,c4:chararray,tags:chararray,c6:chararray,c7:chararray,c8:chararray,c9:chararray,c10:chararray,at:chararray);
tags_data_qid_time = FOREACH questions_data GENERATE tags, qid as q, ToDate((long)at*1000) as time;
tags_seperated_with_hour = FOREACH tags_data_qid_time GENERATE TOKENIZE(tags), q as q, GetHour(time) as hour;
tags_flatten = FOREACH tags_seperated_with_hour GENERATE FLATTEN($0) AS tag, q as q, hour as hour;
tags_lessthanonehour = FILTER tags_flatten by hour <= 1;
tags_order = ORDER tags_lessthanonehour by tag;
DUMP tags_order;
Output –
 





