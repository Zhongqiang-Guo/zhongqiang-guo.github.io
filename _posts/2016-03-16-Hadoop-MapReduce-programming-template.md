---
layout: post
title: Hadoop MapReduce程序的模板框架
categories: [Hadoop]
---


<!-- writing below -->

###简要说明
这里放了两个Hadoop MapReduce 程序的模板框架，主要包括内容：

* 一些基本的包import语句
* 继承`Mapper`基类
* `map()`方法头
* 继承`Reducer`基类
* `reduce()`方法头
* 作业job的配置项
* 其他

###TODO
写MR程序时，程序员需要实现的有：

* 相应的`map()`函数
* 相应的`reduce()`函数

###MapReduce程序模板1：

```
/*
 * MapReduce程序模板，一些必要的语句
 * 写MR程序时，复制该文件，修改类名，实现相应的`map()`、`reduce()`函数等 
 */

import java.io.IOException; 
import java.util.StringTokenizer; // 分隔字符串
import org.apache.hadoop.conf.Configured; 
import org.apache.hadoop.fs.Path; 
import org.apache.hadoop.io.IntWritable; // 相当于int类型
import org.apache.hadoop.io.LongWritable; // 相当于long类型
import org.apache.hadoop.io.Text; // 相当于String类型
import org.apache.hadoop.mapreduce.Job; 
import org.apache.hadoop.mapreduce.Mapper; 
import org.apache.hadoop.mapreduce.Reducer; 
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat; 
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat; 
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat; 
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat; 
import org.apache.hadoop.util.Tool; 
import org.apache.hadoop.util.ToolRunner; 

public class HadoopMRTemplate extends Configured implements Tool{
    
	public static class MapTemplate extends Mapper<LongWritable, Text, Text, IntWritable> { 
		// TODO: some preprocessing operations before map() function
		
		public void map(LongWritable   key, Text value, Context context) 
				throws IOException, InterruptedException {
			// map函数中参数key是偏移量，value是每一行的内容
			// TODO: implements map() function
            
        } // map( )
     } // class MapTemplate


    public static class ReduceTemplate extends Reducer<Text, IntWritable, Text, IntWritable> { //实现reduce函数
        // TODO: some preprocessing operations before reduce() function
    	
    	public void reduce(Text key, Iterable<IntWritable> values, Context context)
        	throws IOException, InterruptedException {
    		// TODO: implements reduce() function
            
            
        } // reduce( )        
    } // class ReduceTemplate
    
    public int run(String[] args) throws Exception {
        Job job = new Job(getConf()); 
        
        job.setJobName("HadoopMRTemplate"); // 作业名
        job.setOutputKeyClass(Text.class); // 类名.class生成class对象
        job.setOutputValueClass(IntWritable.class);
        job.setMapperClass(MapTemplate.class); 
        job.setReducerClass(ReduceTemplate.class); 
        job.setInputFormatClass(TextInputFormat.class); 
        job.setOutputFormatClass(TextOutputFormat.class); 
        
        FileInputFormat.setInputPaths(job, new Path(args[0])); // 作业的输入路径
        FileOutputFormat.setOutputPath(job, new Path(args[1])); // 作业的输出路径
         
        return (job.waitForCompletion(true)? 0 : 1); 
    } //run()
    
    public static void main(String[] args) throws Exception { // 调用ToolRunner.run( )
        int exitCode = ToolRunner.run(new HadoopMRTemplate(), args); 
        System.exit(exitCode); 
    } //main()
} // class HadoopMRTemplate
```

###MapReduce程序模板2：

```
/*
 * MapReduce程序模板，一些必要的语句
 * 写MR程序时，复制该文件，修改类名，实现相应的map、reduce函数等 
 */

import java.io.IOException;
import java.util.StringTokenizer; // 分隔字符串

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable; // 相当于int类型
import org.apache.hadoop.io.Text; // 相当于String类型
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class HadoopMRTemplate2 {

  public static class TokenizerMapper 
       extends Mapper<Object, Text, Text, IntWritable>{
        // TODO: some preprocessing operations before map() function
      
    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
    	// map函数中参数key是偏移量，value是每一行的内容
    	// TODO: implements map() function
      
    } //map()
  } // class TokenizerMapper
  
  public static class IntSumReducer 
       extends Reducer<Text,IntWritable,Text,IntWritable> {
	// TODO: some preprocessing operations before map() function

    public void reduce(Text key, Iterable<IntWritable> values, 
                       Context context
                       ) throws IOException, InterruptedException {
    	// TODO: implements reduce() function
      
    } // reduce()
  } // class IntSumReducer

  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration(); 
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount <in> <out>");
      System.exit(2);
    }
    Job job = new Job(conf, "HadoopMRTemplate2"); // 作业名
    
    job.setJarByClass(HadoopMRTemplate2.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    
    FileInputFormat.addInputPath(job, new Path(otherArgs[0])); // 作业的输入路径
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1])); // 作业的输出路径
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  } //main()
} // class HadoopMRTempalte2

```
