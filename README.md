# Geodesity Protocol Buffers

## Introduction
Geodesity can use a binary communication mechanism for efficient message transport between servers
and remote devices.  Google's Protocol Buffers provide a well-known method for such transport.
More information about protocol buffers is found at <https://developers.google.com/protocol-buffers/>.

## Generation
To generate the proto files, first compile the C++ version of `protoc`: 
* <https://github.com/google/protobuf> and
* <https://github.com/google/protobuf/blob/master/src/README.md>

There are special instructions for Mac; they work perfectly.

### Python Generation
Python is natively supported in the `protoc` compiler, so it is simply:

```
protoc --python_out=./ geodesity.proto
```

### Java Generation
Java is natively supported in the `protoc` compiler, so it is simply:

```
protoc --java_out=./ geodesity.proto
```

The generated files are buried in a traditional Java folder structure such as `io/geodesity/proto`.

### Go Generation
Go isn't natively supported, so you'll need to grab the latest package from Google.  Also, your environment
variables will need to be set properly as well for golang.  An example for Mac is:

```
go get -u github.com/golang/protobuf/protoc-gen-go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$PATH
protoc --go_out=./ geodesity.proto
```

This will generate a `geodesity.pb.go` file for use in your golang application.

### Swift Generation
Swift is not directly supported by Google's Protobufs, but Applis is almost finished with a plugin.
The plugin source can be downloaded from <https://github.com/apple/swift-protobuf> and compiled via
the instructions within the included README. The Swift generated files included in this Geodesity repo
were compiled with the 0.9.904 tag.

```
git clone https://github.com/apple/swift-protobuf.git
cd swift-protobuf
git checkout tags/0.9.904
swift build -c release -Xswiftc -static-stdlib
# Now copy the executable into a common path so protoc can find it
cp .build/release/protoc-gen-swift /usr/local/bin/
```

Then, to build the Geodesity protobuf file, in the Geodesity repo:

```
protoc -swift_out=./ geodesity.proto
```

Complilation requires Xcode with Swift and can therefore only be performed on a Mac.
You may receive a `WARNING: untested version of protoc ...`.  Hopefully this will go away once Apple
releases a 1.0RC of the plugin.

## Usage
To use the Geodesity protobufs, simply include the appropriate pre-generated language file in your application
and exercise the models accordingly. You'll need to make sure and have the protobuf classes installed 
for some languages, like Python.

### Python Usage
For Python, you'll need to install the protobuf library from google.  To do this, setup your virtual
environment and use pip to install:

```
virtualenv ./env
source ./env/bin/activate
pip install protobuf
```

Then, using the `python` interpreter you can:
 
```
>>> import geodesity_pb2 as gpb
>>> event = gpb.Event(id="1234",type=1)
>>> event.timestamp.GetCurrentTime()
>>> event.position.longitude = -82.3
>>> event.position.latitude = 32.4
>>> event
id: "1234"
type: EVENT_HEARTBEAT
timestamp {
  seconds: 1505775773
  nanos: 595492000
}
position {
  longitude: -82.3
  latitude: 32.4
}
```

Serialization is performed by:

```
>>> input = event.SerializeToString()
>>> input
'\n\x041234\x10\x01\x1a\x0c\x08\x9d\xa1\x81\xce\x05\x10\xa0\xf9\xf9\x9b\x02\xaa\x06\x0c\xed\x12\x9a\x99\xa4\xc2\xf5\x12\x9a\x99\x01B'
>>> event2 = gpb.Event()
>>> event2.ParseFromString(input)
>>> event2
id: "1234"
type: EVENT_HEARTBEAT
timestamp {
  seconds: 1505775773
  nanos: 595492000
}
position {
  longitude: -82.3000030518
  latitude: 32.4000015259
}
```

## Examples
TODO: Add examples.
