### Add logging to file functionality in github.com/prometheus/common/log
This is an improvement for github.com/prometheus/common/log package where it lets you write logs to a file.

I liked prometheus's wrapper for logging and brought it to my code. I downloaded commit 61f87aac8082fa8c3c5655c7608d7478d46ac2ad. This commit's NewLogger function looks like

````
func NewLogger(w io.Writer) Logger {
	l := logrus.New()
	l.Out = w
	return logger{entry: logrus.NewEntry(l)}
}
````

But this implementation writes log to console only/stdout. See issue #82 raising the same thing.

So, I raised issue #104 describing my problem. And then while working on it, I found solution for my problem. This repository has changed the NewLogger to accept no parameter:

````
func NewLogger() Logger {
	l := logrus.New()
	return logger{entry: logrus.NewEntry(l)}
}
````
and added a new `SetOutput` function for the same purpose.

````
func SetOutput(w io.Writer) {
	origLogger.Out = w
}
````

### Usage
To use this package and log to a file, add the following lines in your package:
````
f, err := os.OpenFile("logs.log", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0666)
if err != nil {
	log.Fatal(err)
}
defer f.Close()
log.NewLogger()
log.SetOutput(f)
````
