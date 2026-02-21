Hanzo S3 Go SDK for Amazon S3 Compatible Cloud Storage [![Apache V2 License](https://img.shields.io/badge/license-Apache%20V2-blue.svg)](https://github.com/hanzos3/go-sdk/blob/main/LICENSE)
======================================================================================================================================================================================

The Hanzo S3 Go SDK provides straightforward APIs to access any Amazon S3 compatible object storage.

This Quickstart Guide covers how to install the SDK, connect to Hanzo S3, and create a sample file uploader. For a complete list of APIs and examples, see the [godoc documentation](https://pkg.go.dev/github.com/minio/minio-go/v7) or [Go Client API Reference](https://hanzo.space/docs/go/API.html).

These examples presume a working [Go development environment](https://golang.org/doc/install).

Download from Github
--------------------

From your project directory:

```sh
go get github.com/minio/minio-go/v7
```

Initialize a Client Object
---------------------------

The client requires the following parameters to connect to an Amazon S3 compatible object storage:

| Parameter         | Description                                                |
|-------------------|------------------------------------------------------------|
| `endpoint`        | URL to object storage service.                             |
| `_minio.Options_` | All the options such as credentials, custom transport etc. |

```go
package main

import (
	"log"

	"github.com/minio/minio-go/v7"
	"github.com/minio/minio-go/v7/pkg/credentials"
)

func main() {
	endpoint := "s3.hanzo.ai"
	accessKeyID := "YOUR-ACCESSKEYID"
	secretAccessKey := "YOUR-SECRETACCESSKEY"
	useSSL := true

	// Initialize Hanzo S3 client object.
	s3Client, err := minio.New(endpoint, &minio.Options{
		Creds:  credentials.NewStaticV4(accessKeyID, secretAccessKey, ""),
		Secure: useSSL,
	})
	if err != nil {
		log.Fatalln(err)
	}

	log.Printf("%#v\n", s3Client) // s3Client is now set up
}
```

Example - File Uploader
-----------------------

This sample code connects to an object storage server, creates a bucket, and uploads a file to the bucket. It uses the Hanzo S3 server at [https://s3.hanzo.ai](https://s3.hanzo.ai).

### FileUploader.go

This example does the following:

-	Connects to the Hanzo S3 server using the provided credentials.
-	Creates a bucket named `testbucket`.
-	Uploads a file named `testdata` from `/tmp`.

	```go
	// FileUploader.go Hanzo S3 example
	package main

	import (
		"context"
		"log"

		"github.com/minio/minio-go/v7"
		"github.com/minio/minio-go/v7/pkg/credentials"
	)

	func main() {
		ctx := context.Background()
		endpoint := "s3.hanzo.ai"
		accessKeyID := "YOUR-ACCESSKEYID"
		secretAccessKey := "YOUR-SECRETACCESSKEY"
		useSSL := true

		// Initialize Hanzo S3 client object.
		s3Client, err := minio.New(endpoint, &minio.Options{
			Creds:  credentials.NewStaticV4(accessKeyID, secretAccessKey, ""),
			Secure: useSSL,
		})
		if err != nil {
			log.Fatalln(err)
		}

		// Make a new bucket called testbucket.
		bucketName := "testbucket"
		location := "us-east-1"

		err = s3Client.MakeBucket(ctx, bucketName, minio.MakeBucketOptions{Region: location})
		if err != nil {
			// Check to see if we already own this bucket (which happens if you run this twice)
			exists, errBucketExists := s3Client.BucketExists(ctx, bucketName)
			if errBucketExists == nil && exists {
				log.Printf("We already own %s\n", bucketName)
			} else {
				log.Fatalln(err)
			}
		} else {
			log.Printf("Successfully created %s\n", bucketName)
		}

		// Upload the test file
		// Change the value of filePath if the file is in another location
		objectName := "testdata"
		filePath := "/tmp/testdata"
		contentType := "application/octet-stream"

		// Upload the test file with FPutObject
		info, err := s3Client.FPutObject(ctx, bucketName, objectName, filePath, minio.PutObjectOptions{ContentType: contentType})
		if err != nil {
			log.Fatalln(err)
		}

		log.Printf("Successfully uploaded %s of size %d\n", objectName, info.Size)
	}
	```

**1. Create a test file containing data:**

You can do this with `dd` on Linux or macOS systems:

```sh
dd if=/dev/urandom of=/tmp/testdata bs=2048 count=10
```

or `fsutil` on Windows:

```sh
fsutil file createnew "C:\Users\<username>\Desktop\sample.txt" 20480
```

**2. Run FileUploader with the following commands:**

```sh
go mod init example/FileUploader
go get github.com/minio/minio-go/v7
go get github.com/minio/minio-go/v7/pkg/credentials
go run FileUploader.go
```

The output resembles the following:

```sh
2024/01/01 14:27:55 Successfully created testbucket
2024/01/01 14:27:55 Successfully uploaded testdata of size 20480
```

API Reference
-------------

The full API Reference is available here.

-	[Complete API Reference](https://hanzo.space/docs/go/API.html)

### API Reference : Bucket Operations

-	[`MakeBucket`](https://hanzo.space/docs/go/API.html#MakeBucket)
-	[`ListBuckets`](https://hanzo.space/docs/go/API.html#ListBuckets)
-	[`BucketExists`](https://hanzo.space/docs/go/API.html#BucketExists)
-	[`RemoveBucket`](https://hanzo.space/docs/go/API.html#RemoveBucket)
-	[`ListObjects`](https://hanzo.space/docs/go/API.html#ListObjects)
-	[`ListIncompleteUploads`](https://hanzo.space/docs/go/API.html#ListIncompleteUploads)

### API Reference : Bucket policy Operations

-	[`SetBucketPolicy`](https://hanzo.space/docs/go/API.html#SetBucketPolicy)
-	[`GetBucketPolicy`](https://hanzo.space/docs/go/API.html#GetBucketPolicy)

### API Reference : Bucket notification Operations

-	[`SetBucketNotification`](https://hanzo.space/docs/go/API.html#SetBucketNotification)
-	[`GetBucketNotification`](https://hanzo.space/docs/go/API.html#GetBucketNotification)
-	[`RemoveAllBucketNotification`](https://hanzo.space/docs/go/API.html#RemoveAllBucketNotification)
-	[`ListenBucketNotification`](https://hanzo.space/docs/go/API.html#ListenBucketNotification) (Hanzo S3 Extension)
-	[`ListenNotification`](https://hanzo.space/docs/go/API.html#ListenNotification) (Hanzo S3 Extension)

### API Reference : File Object Operations

-	[`FPutObject`](https://hanzo.space/docs/go/API.html#FPutObject)
-	[`FGetObject`](https://hanzo.space/docs/go/API.html#FGetObject)

### API Reference : Object Operations

-	[`GetObject`](https://hanzo.space/docs/go/API.html#GetObject)
-	[`PutObject`](https://hanzo.space/docs/go/API.html#PutObject)
-	[`PutObjectStreaming`](https://hanzo.space/docs/go/API.html#PutObjectStreaming)
-	[`StatObject`](https://hanzo.space/docs/go/API.html#StatObject)
-	[`CopyObject`](https://hanzo.space/docs/go/API.html#CopyObject)
-	[`RemoveObject`](https://hanzo.space/docs/go/API.html#RemoveObject)
-	[`RemoveObjects`](https://hanzo.space/docs/go/API.html#RemoveObjects)
-	[`RemoveIncompleteUpload`](https://hanzo.space/docs/go/API.html#RemoveIncompleteUpload)
-	[`SelectObjectContent`](https://hanzo.space/docs/go/API.html#SelectObjectContent)

### API Reference : Presigned Operations

-	[`PresignedGetObject`](https://hanzo.space/docs/go/API.html#PresignedGetObject)
-	[`PresignedPutObject`](https://hanzo.space/docs/go/API.html#PresignedPutObject)
-	[`PresignedHeadObject`](https://hanzo.space/docs/go/API.html#PresignedHeadObject)
-	[`PresignedPostPolicy`](https://hanzo.space/docs/go/API.html#PresignedPostPolicy)

### API Reference : Client custom settings

-	[`SetAppInfo`](https://hanzo.space/docs/go/API.html#SetAppInfo)
-	[`TraceOn`](https://hanzo.space/docs/go/API.html#TraceOn)
-	[`TraceOff`](https://hanzo.space/docs/go/API.html#TraceOff)

Full Examples
-------------

### Full Examples : Bucket Operations

-	[makebucket.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/makebucket.go)
-	[listbuckets.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/listbuckets.go)
-	[bucketexists.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/bucketexists.go)
-	[removebucket.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/removebucket.go)
-	[listobjects.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/listobjects.go)
-	[listobjectsV2.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/listobjectsV2.go)
-	[listincompleteuploads.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/listincompleteuploads.go)

### Full Examples : Bucket policy Operations

-	[setbucketpolicy.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/setbucketpolicy.go)
-	[getbucketpolicy.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/getbucketpolicy.go)
-	[listbucketpolicies.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/listbucketpolicies.go)

### Full Examples : Bucket lifecycle Operations

-	[setbucketlifecycle.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/setbucketlifecycle.go)
-	[getbucketlifecycle.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/getbucketlifecycle.go)

### Full Examples : Bucket encryption Operations

-	[setbucketencryption.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/setbucketencryption.go)
-	[getbucketencryption.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/getbucketencryption.go)
-	[removebucketencryption.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/removebucketencryption.go)

### Full Examples : Bucket replication Operations

-	[setbucketreplication.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/setbucketreplication.go)
-	[getbucketreplication.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/getbucketreplication.go)
-	[removebucketreplication.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/removebucketreplication.go)

### Full Examples : Bucket notification Operations

-	[setbucketnotification.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/setbucketnotification.go)
-	[getbucketnotification.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/getbucketnotification.go)
-	[removeallbucketnotification.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/removeallbucketnotification.go)
-	[listenbucketnotification.go](https://github.com/hanzos3/go-sdk/blob/main/examples/minio/listenbucketnotification.go) (Hanzo S3 Extension)
-	[listennotification.go](https://github.com/hanzos3/go-sdk/blob/main/examples/minio/listen-notification.go) (Hanzo S3 Extension)

### Full Examples : File Object Operations

-	[fputobject.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/fputobject.go)
-	[fgetobject.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/fgetobject.go)

### Full Examples : Object Operations

-	[putobject.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/putobject.go)
-	[getobject.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/getobject.go)
-	[statobject.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/statobject.go)
-	[copyobject.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/copyobject.go)
-	[removeobject.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/removeobject.go)
-	[removeincompleteupload.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/removeincompleteupload.go)
-	[removeobjects.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/removeobjects.go)

### Full Examples : Encrypted Object Operations

-	[put-encrypted-object.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/put-encrypted-object.go)
-	[get-encrypted-object.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/get-encrypted-object.go)
-	[fput-encrypted-object.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/fputencrypted-object.go)

### Full Examples : Presigned Operations

-	[presignedgetobject.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/presignedgetobject.go)
-	[presignedputobject.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/presignedputobject.go)
-	[presignedheadobject.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/presignedheadobject.go)
-	[presignedpostpolicy.go](https://github.com/hanzos3/go-sdk/blob/main/examples/s3/presignedpostpolicy.go)

Explore Further
---------------

-	[Godoc Documentation](https://pkg.go.dev/github.com/minio/minio-go/v7)
-	[Complete Documentation](https://hanzo.space/docs)
-	[Hanzo S3 Go SDK API Reference](https://hanzo.space/docs/go/API.html)

Contribute
----------

[Contributors Guide](https://github.com/hanzos3/go-sdk/blob/main/CONTRIBUTING.md)

License
-------

This SDK is distributed under the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0), see [LICENSE](https://github.com/hanzos3/go-sdk/blob/main/LICENSE) and [NOTICE](https://github.com/hanzos3/go-sdk/blob/main/NOTICE) for more information.
