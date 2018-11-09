---
title: 大数据的"hello world" -word count词频统计 
date:  2018-11-08 13:30:00
tags:  big data program
comments: true
categories: 大数据
---

### 词频统计,每个单词在文本中出现的次数



#### Java 版本(单词计数)


1. 按行读取文本中的数据
```
 BufferedReader bufferedReader = new BufferedReader(new FileReader("F:/wordcount.txt"));
```



2. 对每行的数据进行切割
```
  String firstLine = bufferedReader.readLine();
        list.add(firstLine);
        while (firstLine != null)
        {
            String[] line = bufferedReader.readLine().split(" ");
            for (String word : line)
            {
                list.add(word);
            }
            firstLine = bufferedReader.readLine();
        }
```

3. 对数组中的单词进行计算
```
 for(String word:list){
            if(hashMap.containsKey(word)){
                hashMap.put(word,hashMap.get(word)+1);
            }else{
                hashMap.put(word,1);
            }
        }
```

#### Hadoop的MapReduce 
1. TextInputFormat ------> readline

2. map<key,value>{}
```
    //输入映射:按行读取,LongWritable(Long)代表的是偏移量,Text(String)则表示一行文本       java java java
    //输出映射:Text表示单个的单词,IntWritable表示出现的次数   [java,1] [java,1] [java,1]
    public static class WCMapper extends Mapper<LongWritable, Text, Text, IntWritable>
    {
        private Text keyOut = new Text();
        private IntWritable valueOut = new IntWritable();

        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException
        {
            String strLine = value.toString();
            String[] strs = strLine.split(" ");
            for (String str : strs)
            {
                keyOut.set(str);
                valueOut.set(1);
                //将输入结果写入到上下文中
                context.write(keyOut, valueOut);
            }
        }
    }
```
3. hadoop shuffle

4. reduce<key,value>{}
```
    //输入映射:map输出的结果经过shuffle阶段的混洗,Text IntWritable
    //输出映射:Text   IntWritable
    public static class WCReducer extends Reducer<Text, IntWritable, Text, IntWritable>
    {
        private IntWritable count = new IntWritable();

        @Override
        protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException
        {
            int sum = 0;
            for (IntWritable v : values)
            {
                sum += v.get();
            }
            count.set(sum);
            System.out.println(count.toString());
            //将reduce函数的处理结果写入上下文
            context.write(key, count);
        }
    }
```


#### Storm版本的 word count
1. Spout  输出:tuple(line)


2. SplitBolt  输入tuple(line)  输出word


3. wordcountBolt  输入word 输出
