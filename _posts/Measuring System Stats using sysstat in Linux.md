### Download sysstat

```sudo apt-get install sysstat```

Enable Data Collecting

```
sudo vi /etc/default/sysstat
change ENABLED="false" to ENABLED="true"
save the file

```

```
sudo service sysstat restart

```
Now systat is collecting data.

The logs are usually here
```
/var/log/sysstat
```
When we run load test on a system we can ask sar to record the system stats

```
sar -o system_stats 5 20

```

We record system stats every 5 seconds for 20 iterations and write it to a file
system_stats.

We can now use this file recorded and plot it graphically

#### Plotting

First we convert the recording to text since it has been written in binary
format.

```
sar -A -f system_stats > system_stats.txt
chmod 755 system_stats.txt

```

For plotting use the following web tool

[sar chart](https://sarchart.dotsuresh.com/#)

Upload the system_stats.txt file here and it will generate the System Report.


There are many terms in the report. The below blog explains some of the
parameters.

[SAR Results](http://g33kinfo.com/info/archives/6647)
