Pyhton







##strptime

* time.strptime()
* datetime.datetime.strptime()

They both need to deal with locale env, much time will be cost.
If we want to transform variable need to be transformed from `string` type to `time` or `datetime` type, we’d better parse the string by ourself.
In my experiment, time reduce from 257ms to 2.3ms.



