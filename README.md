# **Mirroring Registries The Easy Way**

<img src="mirror.jpg" style="width: 1000px;" border=0/>

OpenShift makes it easy to manage Kubernetes clusters that have unfettered internet access using well-known registries. However, preparation for deployment and maintenance of clusters reliant on locally hosted mirror registries can be challenging. The process of mirroring the registry content can be cumbersome with multiple steps, time consuming and sometimes down right frustrating.  However with the promise of the oc-mirror tool which was released as technical preview in OpenShift 4.10 a new dawn seamed on the horizon for mirroring activites. Now with the release of OpenShift 4.11 the oc-mirror tools has official gone GA and with adds additional feature functionality. 

## Use Cases We Are Addressing

* Minimizing the amount of content downloaded
* Simplifying the mirroring process
* Ensuring the right content is mirrored
* Better overall user experience with a single command to manage images

## New Features Included In GA

* Prune OpenShift and Operator images
* Select image content based on version range selections
* OSUS graph creation
* Option to prefer direct update paths and skip intermediate releases

## Existing Features from 4.10 Technical Preview

 * Provides a centralized method to mirror OpenShift Container Platform releases, Operators, helm charts, and other images.
 * Maintains update paths for OpenShift Container Platform and Operators.
 * Uses a declarative image set configuration file to include only the OpenShift Container Platform releases, Operators, and images required by cluster

As I mentioned before oc-mirror was released as technical preview in OpenShift 4.10 and there is a preview blog which covers the basic features and usage of the tool [here](https://cloud.redhat.com/blog/how-oc-mirror-will-help-you-reduce-container-management-complexity).  With that stated this blog will focus on the new features to the 4.11 GA release that we highlighted above.

### Setting Up Initial Mirroring

Before I demonstrate some of the new features of oc-mirror let us first show a few basic mirror examples of mirroring an OpenShift release and also an OpenShift operator.  First lets go ahead and create the OpenShift release imageset configuration.   In this example I am going to use 4.9.12 as the only release version I want to mirror:

~~~bash
$ cat << EOF > ~/imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
mirror:
  platform:
    channels:
      - name: stable-4.9
        minVersion: 4.9.12
        maxVersion: 4.9.12
EOF
~~~

Now that we have the file create let us go ahead and run the oc mirror command to mirror the contents to our local registry of provisioning.schmaustech.com:5000:

~~~bash

~~~

### Prefer Direct Update Paths and Skip Intermediate Releases

~~~bash
$ cat << EOF > ~/shortest-upgrade-imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
mirror:
  platform:
    channels:
    - name: stable-4.10
      minVersion: 4.9.37
      maxVersion: 4.10.22
      shortestPath: true
EOF
~~~

### Select Image Content Based on Version Range

~~~bash
$ cat << EOF > ~/version-range-imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  local:
    path: metadata
mirror:
  platform:
    channels:
      - name: stable-4.9
        minVersion: 4.9.13
        maxVersion: 4.9.26
EOF
~~~

### Pruning Images

Pruning images a feature that ensures the older content in the mirrored registry gets purged especially if it will not be needed anymore.  In this example we will demonstrate it with the Universal Base Image (ubi) mirror.   First lets create our imageset configuration file for the ubi7 image:

~~~bash
$ cat << EOF > ~/ubi-imageset-config.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  local:
    path: metadata
mirror:
  additionalimages:
    - name: registry.redhat.io/ubi7/ubi:latest
EOF
~~~

Next lets go ahead and run the mirror operation with the configuration file we just created:

~~~bash
$ oc mirror --config=ubi-imageset-config.yaml docker://provisioning.schmaustech.com:5000 --dest-skip-tls
Checking push permissions for provisioning.schmaustech.com:5000
Found: oc-mirror-workspace/src/publish
Found: oc-mirror-workspace/src/v2
Found: oc-mirror-workspace/src/charts
Found: oc-mirror-workspace/src/release-signatures
No metadata detected, creating new workspace
provisioning.schmaustech.com:5000/
  ubi7/ubi
    blobs:
      registry.redhat.io/ubi7/ubi sha256:1c42cd59ba3e0759f1177deb1ff3e4151c7815d732060c46addb2ba1f37d0fb8 1.773KiB
      registry.redhat.io/ubi7/ubi sha256:32af1cd3663f4f1e6db4197602e00ca87fd37df2e4d54b947672d9507aae20de 1.787KiB
      registry.redhat.io/ubi7/ubi sha256:b07823692aebceca7b046a2f38303bed9c73f948674e72591e385c99f9067f04 1.792KiB
      registry.redhat.io/ubi7/ubi sha256:6c7184f511d71e9f1946d16bf6b0c37bfe86ce58714ce20123aaef31f10173d5 4.171KiB
      registry.redhat.io/ubi7/ubi sha256:5b5117b21ba141575b094c7eceb8ce15a8c88de56620162a0b8137afe17cccd4 4.173KiB
      registry.redhat.io/ubi7/ubi sha256:54706f2e539f30b2e9bf6781a10f1219b70b3568905d23de5d3520078079ca46 4.185KiB
      registry.redhat.io/ubi7/ubi sha256:e5746dd3575a92053f5871f0c54fdc950a16d792b816f1b798e2ebbd4956b793 73MiB
      registry.redhat.io/ubi7/ubi sha256:627370aaccb9c13ecb9c18d8986b3a022afcf1753c8ec2143afd36d0416fc60b 73.34MiB
      registry.redhat.io/ubi7/ubi sha256:2388c12220d9730ceab1ad18f702c2790a58087a613d11df5ee02df23273f784 76.94MiB
    manifests:
      sha256:339ca735e05529c9f1991ce7a12fc81aebfe242a42fccde152e9c0715557b896
      sha256:360f063fb2a417f2fbb1ac46a8ab74b00e3361a936e179c9895c03d06425546f
      sha256:bf29e70507e4147279cc43b06069df30b860b593adef9ac00bcb261f97a9b1d6
      sha256:d9f7746715fea2d770e23a34e97b36a44e2bd2dac9c95d26829d6d5fa836bd99
      sha256:bf29e70507e4147279cc43b06069df30b860b593adef9ac00bcb261f97a9b1d6 -> latest
  stats: shared=0 unique=9 size=223.3MiB ratio=1.00

phase 0:
  provisioning.schmaustech.com:5000 ubi7/ubi blobs=9 mounts=0 manifests=5 shared=0

info: Planning completed in 1.24s
uploading: provisioning.schmaustech.com:5000/ubi7/ubi sha256:e5746dd3575a92053f5871f0c54fdc950a16d792b816f1b798e2ebbd4956b793 73MiB
uploading: provisioning.schmaustech.com:5000/ubi7/ubi sha256:2388c12220d9730ceab1ad18f702c2790a58087a613d11df5ee02df23273f784 76.94MiB
uploading: provisioning.schmaustech.com:5000/ubi7/ubi sha256:627370aaccb9c13ecb9c18d8986b3a022afcf1753c8ec2143afd36d0416fc60b 73.34MiB
sha256:339ca735e05529c9f1991ce7a12fc81aebfe242a42fccde152e9c0715557b896 provisioning.schmaustech.com:5000/ubi7/ubi
sha256:360f063fb2a417f2fbb1ac46a8ab74b00e3361a936e179c9895c03d06425546f provisioning.schmaustech.com:5000/ubi7/ubi
sha256:d9f7746715fea2d770e23a34e97b36a44e2bd2dac9c95d26829d6d5fa836bd99 provisioning.schmaustech.com:5000/ubi7/ubi
sha256:bf29e70507e4147279cc43b06069df30b860b593adef9ac00bcb261f97a9b1d6 provisioning.schmaustech.com:5000/ubi7/ubi:latest
info: Mirroring completed in 21.94s (10.67MB/s)
Writing image mapping to oc-mirror-workspace/results-1660174421/mapping.txt
Writing ICSP manifests to oc-mirror-workspace/results-1660174421
~~~

With the ubi7 image mirrored now we can now update our imageset configuration file and set the image to ubi8:

~~~bash
$ cat << EOF > ~/ubi-imageset-config.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  local:
    path: metadata
mirror:
  additionalimages:
    - name: registry.redhat.io/ubi8/ubi:latest
EOF
~~~

And now lets run our oc-mirror command again:

~~~bash
$ oc mirror --config=ubi-imageset-config.yaml docker://provisioning.schmaustech.com:5000 --dest-skip-tls
Checking push permissions for provisioning.schmaustech.com:5000
Creating directory: oc-mirror-workspace/src/publish
Creating directory: oc-mirror-workspace/src/v2
Creating directory: oc-mirror-workspace/src/charts
Creating directory: oc-mirror-workspace/src/release-signatures
provisioning.schmaustech.com:5000/
  ubi8/ubi
    blobs:
      registry.redhat.io/ubi8/ubi sha256:7e3624512448126fd29504b9af9bc034538918c54f0988fb08c03ff7a3a9a4cb 1.747KiB
      registry.redhat.io/ubi8/ubi sha256:1c716916a225fab7df9407f9190793c5757af63a875810145426432ec461250c 1.748KiB
      registry.redhat.io/ubi8/ubi sha256:01ab2c1195006b264cb8a77d0756617e33944292964dcbd65b4eb5bab57eb22f 1.752KiB
      registry.redhat.io/ubi8/ubi sha256:847f634e7f1e9901731ab73fc8bae8971d44de50c0bbe89acd830b08a475db13 1.753KiB
      registry.redhat.io/ubi8/ubi sha256:066fa0d6d5ac443e9f672920f40ede88ea4c69934f4d792e21cb89a61a76cb58 4.26KiB
      registry.redhat.io/ubi8/ubi sha256:4891eb50ccc7df2e2a4ed7b8887e782b34bd253a473d28648fd0632d312eb83c 4.262KiB
      registry.redhat.io/ubi8/ubi sha256:f754849582f5232784db3791c3a1092482fbec296274a00a0bdee25e3e20c1c4 4.264KiB
      registry.redhat.io/ubi8/ubi sha256:6acdcdbcf1a1be6435c4756ca7c4637b651dd95da205d7319f0d2495230fa2d5 4.275KiB
      registry.redhat.io/ubi8/ubi sha256:5e70be1586c73537515c1c2e1441ca2f90c489b814b2fee24c3ac929a2f01853 72.03MiB
      registry.redhat.io/ubi8/ubi sha256:e46e89129c9d273e7b57a9b719d06e3f9e0469a35cdfcc7ea4bda6b7a69cf965 72.95MiB
      registry.redhat.io/ubi8/ubi sha256:db0f4cd412505c5cc2f31cf3c65db80f84d8656c4bfa9ef627a6f532c0459fc4 74.73MiB
      registry.redhat.io/ubi8/ubi sha256:d6050ae37df36bd42b8d5ae50c9ec8016ec8191cd44d03ff5b67a2e513b16f8f 81MiB
    manifests:
      sha256:08e221b041a95e6840b208c618ae56c27e3429c3dad637ece01c9b471cc8fac6
      sha256:48ba6276b423eafd63c7c54949cbc98b47165e5660329b7bae9632f6c592b258
      sha256:6edca3916b34d10481e4d24d14ebe6ebc6db517bec1b2db6ae2d7d47c2ecfaee
      sha256:bc2a3d7583a955f1c2f9753b5c01ebca12287c5f68a8ce7e85b2c7fd64c145d3
      sha256:c2931a594cab69b4a2e912fd5d9bbc7e3542e47c341974fad6e407c8d91e76c3
      sha256:6edca3916b34d10481e4d24d14ebe6ebc6db517bec1b2db6ae2d7d47c2ecfaee -> latest
  stats: shared=0 unique=12 size=300.7MiB ratio=1.00

phase 0:
  provisioning.schmaustech.com:5000 ubi8/ubi blobs=12 mounts=0 manifests=6 shared=0

info: Planning completed in 730ms
uploading: provisioning.schmaustech.com:5000/ubi8/ubi sha256:e46e89129c9d273e7b57a9b719d06e3f9e0469a35cdfcc7ea4bda6b7a69cf965 72.95MiB
uploading: provisioning.schmaustech.com:5000/ubi8/ubi sha256:d6050ae37df36bd42b8d5ae50c9ec8016ec8191cd44d03ff5b67a2e513b16f8f 81MiB
uploading: provisioning.schmaustech.com:5000/ubi8/ubi sha256:5e70be1586c73537515c1c2e1441ca2f90c489b814b2fee24c3ac929a2f01853 72.03MiB
uploading: provisioning.schmaustech.com:5000/ubi8/ubi sha256:db0f4cd412505c5cc2f31cf3c65db80f84d8656c4bfa9ef627a6f532c0459fc4 74.73MiB
sha256:c2931a594cab69b4a2e912fd5d9bbc7e3542e47c341974fad6e407c8d91e76c3 provisioning.schmaustech.com:5000/ubi8/ubi
sha256:08e221b041a95e6840b208c618ae56c27e3429c3dad637ece01c9b471cc8fac6 provisioning.schmaustech.com:5000/ubi8/ubi
sha256:48ba6276b423eafd63c7c54949cbc98b47165e5660329b7bae9632f6c592b258 provisioning.schmaustech.com:5000/ubi8/ubi
sha256:bc2a3d7583a955f1c2f9753b5c01ebca12287c5f68a8ce7e85b2c7fd64c145d3 provisioning.schmaustech.com:5000/ubi8/ubi
sha256:6edca3916b34d10481e4d24d14ebe6ebc6db517bec1b2db6ae2d7d47c2ecfaee provisioning.schmaustech.com:5000/ubi8/ubi:latest
info: Mirroring completed in 28.92s (10.9MB/s)
Pruning 4 manifest(s) from repository ubi7/ubi
Deleting manifest sha256:339ca735e05529c9f1991ce7a12fc81aebfe242a42fccde152e9c0715557b896 from repo ubi7/ubi
Deleting manifest sha256:360f063fb2a417f2fbb1ac46a8ab74b00e3361a936e179c9895c03d06425546f from repo ubi7/ubi
Deleting manifest sha256:bf29e70507e4147279cc43b06069df30b860b593adef9ac00bcb261f97a9b1d6 from repo ubi7/ubi
Deleting manifest sha256:d9f7746715fea2d770e23a34e97b36a44e2bd2dac9c95d26829d6d5fa836bd99 from repo ubi7/ubi
Writing image mapping to oc-mirror-workspace/results-1660175306/mapping.txt
Writing ICSP manifests to oc-mirror-workspace/results-1660175306
~~~

As we can see on the second run we uploaded the ubi8 image and then proceeded to delete the ubi7 image from the registry thus pruning it.   If we had wanted to keep both images on our registry we would have needed to specify both in our imageset configuration file.

### OSUS Graph Creation

~~~bash

~~~
