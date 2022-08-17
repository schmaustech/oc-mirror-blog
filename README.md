# **Mirroring OpenShift Registries The Easy Way**

<img src="mirror.jpg" style="width: 1000px;" border=0/>

OpenShift makes it easy to manage Kubernetes clusters that have unfettered internet access using well-known registries. However, preparation for deployment and maintenance of clusters reliant on locally hosted mirror registries can be challenging. The process of mirroring the registry content can be cumbersome with multiple steps, time consuming and sometimes down right frustrating.  However with the promise of the oc-mirror tool which was released as technical preview in OpenShift 4.10 a new dawn seamed on the horizon for mirroring activites. Now with the release of OpenShift 4.11 the oc-mirror tools has official gone GA and with adds additional feature functionality. 

## Use Cases We Are Addressing

* Simplifying the mirroring process
* Using a single tool and a single command for mirroring all types of content relevant to an OpenShift cluster
* Minimizing the amount of content downloaded
* Ensuring the right content is mirrored
* Ensuring the mirror stays updated over time

## New Features Included In GA

* Prune OpenShift and Operator images from the target registry
* Select image content based on version range selections
* OpenShift Update Service graph creation
* Option to prefer direct update paths and skip intermediate releases

## Existing Features from 4.10 Technical Preview

 * Provides a centralized method to mirror OpenShift Container Platform releases, Operators, helm charts, and other images.
 * Maintains update paths for OpenShift Container Platform and Operators.
 * Uses a declarative image set configuration file to include only the OpenShift Container Platform releases, Operators, and images required by cluster
 * Querying for published OpenShift channels and versions and Operator catalogs and versions

As I mentioned before oc-mirror was released as technical preview in OpenShift 4.10 and there is a preview blog which covers the basic features and usage of the tool [here](https://cloud.redhat.com/blog/how-oc-mirror-will-help-you-reduce-container-management-complexity).  With that stated this blog will focus on the new features to the 4.11 GA release that we highlighted above.

### Setting Up Initial Mirroring

Before I demonstrate some of the new features of oc-mirror let us first show a couple basic mirror examples of mirroring an OpenShift release and also an OpenShift operator.  First lets go ahead and create the OpenShift release imageset configuration.   In this example I am going to use 4.9.12 as the only release version I want to mirror:

~~~bash
$ cat << EOF > ~/imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  registry:
    imageURL: provisioning.schmaustech.com:5000/mirror/metadata
    skipTLS: false
mirror:
  platform:
    channels:
      - name: stable-4.9
        minVersion: 4.9.12
        maxVersion: 4.9.12
EOF
~~~
The above example stores the metadata oc-mirror relies on to figure out what it already mirrored in my target registry which makes oc-mirror more portable and independent of the host it is running on.

Now that we have the imageset created let us go ahead and run the oc-mirror command to mirror the image contents to our local registry of provisioning.schmaustech.com:5000:

~~~bash
$ oc mirror --config=imageset-configuration.yaml docker://provisioning.schmaustech.com:5000 --dest-skip-tls
Checking push permissions for provisioning.schmaustech.com:5000
Found: oc-mirror-workspace/src/publish
Found: oc-mirror-workspace/src/v2
Found: oc-mirror-workspace/src/charts
Found: oc-mirror-workspace/src/release-signatures
No metadata detected, creating new workspace
provisioning.schmaustech.com:5000/
  openshift/release
    blobs:
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:47aa3ed2034c4f27622b989b26c06087de17067268a19a1b3642a7e2686cd1a3 1.747KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5dad5185f24f8a37ff0ea88498affcfc48b0ffc1968dae33269dbfd467271c45 1.786KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:22f677655049d4c2e6cd9e49ca9ed20f34ac175ef0c82f5c5eabc79031c1c29a 1.832KiB
      (...)
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:99b8ec24066fc655b57a0dbd8702783b7a34230ed106d80a4e3c526a04865807 461MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:3b810e182d6c6693732816a47781515d8ee9eb8a610252eb5578e2dde1c666bb 480.7MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c331a0acfc68a1a55bfb70ba03240ad4027bcc34a50d76e703a34bdc9ead5448 1.029GiB
    manifests:
      sha256:00bfbe7800a9c803d13e666122a7b1f52bea6e53b0fb2352d8566d1e75759708 -> 4.9.12-x86_64-keepalived-ipfailover
      sha256:0213887c325d3967d122fe875b45c9259e2b8388db9dc4e0a25c0561414b8737 -> 4.9.12-x86_64-aws-ebs-csi-driver
      sha256:0305702b3b7208c339c5df376aaacf6992465e34490bc16c2d66e423039b2b06 -> 4.9.12-x86_64-insights-operator
      sha256:0310a7b9ec788788a2a6324475add611b5c5304511fe27e7444500fdefbc6ef0 -> 4.9.12-x86_64-ironic-static-ip-manager
      sha256:038452cc237d09fbbea348a454007760fb2d04e1e2439e931c505665c2c98071 -> 4.9.12-x86_64-haproxy-router
      ...
      sha256:f5628b30aa047fe32cba9308c70c581f7d9812f40a3e651a84f0532af184bfd2 -> 4.9.12-x86_64-machine-os-content
      sha256:f60434187e593c079d98e94b6ba69bce34e5daa5ac161e834827d594b070592b -> 4.9.12-x86_64-hyperkube
      sha256:f7ce7247375fa57b2be95733fb26a85751a5f41b28652e96612bea2ee6a25442 -> 4.9.12-x86_64-cluster-cloud-controller-manager-operator
      sha256:f83965a48127fcd265ef40ce6037df806980a088f8c83798be5b91e5cc09a706 -> 4.9.12-x86_64-ironic-machine-os-downloader
      sha256:fd41b7237cac235fead9bda6dc9bf5c6cbde163ebf9d9249f33065d5ceadded0 -> 4.9.12-x86_64-etcd
      sha256:fd7ce3da297b589c7b3c34f6dc820f4d71a51ec367424749e76fbfad06298456 -> 4.9.12-x86_64-baremetal-runtimecfg
      sha256:fe552892df8d59716997e8149a384ef65cade7610de3264652eb77e2d93575ed -> 4.9.12-x86_64-cluster-kube-apiserver-operator
  openshift/release-images
    blobs:
      quay.io/openshift-release-dev/ocp-release sha256:816fde8d07a680fad8d69eabf0d5d7d65289b631f0317a961928036453091a71 1.727KiB
      quay.io/openshift-release-dev/ocp-release sha256:47aa3ed2034c4f27622b989b26c06087de17067268a19a1b3642a7e2686cd1a3 1.747KiB
      quay.io/openshift-release-dev/ocp-release sha256:329a959bf930d1e3a71ad92ea2604143a09f8bfdcd7e40ffe34dbc7981417cfe 538.6KiB
      quay.io/openshift-release-dev/ocp-release sha256:f43eacb86576cfc775abc5d058b804eb2040e3e6dd57d232dd9b0af2c9c64d31 10.91MiB
      quay.io/openshift-release-dev/ocp-release sha256:24e8d2e218b03efbd1c2548e6c8c7fc5a216edb5f2fe7e6e83a49fca53d1018a 14.83MiB
      quay.io/openshift-release-dev/ocp-release sha256:c67baba1e9cbd1d8eeb0384784480534fe1ba2b96eca6d3d28ebbe18e50a0e56 21.32MiB
      quay.io/openshift-release-dev/ocp-release sha256:eac1b95df832dc9f172fd1f07e7cb50c1929b118a4249ddd02c6318a677b506a 79.44MiB
    manifests:
      sha256:dd71b3cd08ce1e859e0e740a585827c9caa1341819d1121d92879873a127f5e2 -> 4.9.12-x86_64
  stats: shared=5 unique=285 size=9.54GiB ratio=0.99

phase 0:
  provisioning.schmaustech.com:5000 openshift/release blobs=288 mounts=0 manifests=140 shared=5
phase 1:
  provisioning.schmaustech.com:5000 openshift/release-images blobs=7 mounts=5 manifests=1 shared=5

info: Planning completed in 27.34s
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:0eee8ab8157f509c4d7234cf7f27481e1a089c81ed860f678cf0ba6464ee1f1d 92.95MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:3c1274a35216f8237b6e8e7e2b4d9d67cb2e74d36e65a2751d5bfe4ffad1afe7 22.67MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:96a8fc5ce7b43f667984457b889a915a0c35b9096735d7774541cf949a913fee 16.12MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:1131776b7cc0daf252bbcf3f51e79ae2972e3542f8b29ec3ef88bd967d9ae12f 26.04MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:70c818a0cf5c5bc95d429d6410492ad9b5d7aab577ae4758be9ba6937b83117c 24.93MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:3091d852b55ed1d9ad20fcd602e71a12061270e29e3ee02e9c5d0f1fa435d738 29.97MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:122833c0cdcdfa996f53043709a8388d5c59a436ce6036eb266fe6de76bbdaea 30.87MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:7f7255527d260b403875e8180cff4ca9f09f893c42eaf2ed65cf09a7ef22cd2c 30.19MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:99b8ec24066fc655b57a0dbd8702783b7a34230ed106d80a4e3c526a04865807 461MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:dff4b01fa9d2f8bcdf80e406c77b4fee60068046c4ab75702cf2112973d4728e 29.61MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:28d47ea7720b0dd29867de093fb7fad50812c8f242743dffef7856e5a58e550a 5.034MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:b332c9645cde8561399e4187dddb72c4c5afc9ff8e4a18bba42c5ea429d241a0 61.78MiB
...
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:9dca186a78952d3ba3f9a164d41ca3fbb13006c266744eaae5315351aded142a 31.82MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:af733ea2d56b8e8789ecca0727a73b570e40a8c4ad20dd583fafa93375053a75 6.993MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:0988f9bb863a676036d4daf95023d0874f5a596be3597fd609fce3c41e0c50f4 27.26MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:44b174cbaec38dbeb1b584e1e859dd9fe3121200bd2e4e986d8445e45390d7d5 31.12MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:55006bb1219d58b16f56b61022987eabdc0c7ed3c58668dc777b02f75594cff8 47.29MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:28a02fd1a277f9b5339f958c69fc2c6fd87d38201d8dc01e678bd27b6dfa0b3c 30.21MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:7e9557b6b185c2f7f7742ca4bac4c683f3964cdbebb80ad9bc09e912c02523ce 34.6MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:da471f9ac607dd48718ba466684786676a36a848109b6e590248516ca4a2961b 5.915MiB
sha256:43150eb3f35005a1c04fa4ed6fef3c8cd3199991658a88530d28cd1a32a5597a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-aws-cloud-controller-manager
sha256:f2a6bffb17480fb2caa5a52512efa348a09b960d23782d02ec8733df948dbff6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-driver-nfs
sha256:6e2a44337dbe5bf89d6f0f16124df38bb86f71c866a9ae38badb450e58e7f42c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-operator-registry
sha256:e5c16190ac98e446c7cf90cba8e14e996f81021ea4a38abd17b87c13b30bd6f8 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-oauth-apiserver
sha256:fd41b7237cac235fead9bda6dc9bf5c6cbde163ebf9d9249f33065d5ceadded0 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-etcd
sha256:0fdd27a12ee71d1268ef2e7c4cfe8dbdd3a86e3010f77db3f2e530b928fa2a42 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-node-driver-registrar
sha256:a928705d99ccf81cfb7c0ef07a515f67a488d7052a2d7ba34ddabaa140295d78 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-grafana
...
sha256:426f99588fbce292824dba75372675b83daea64a1cf5d321fb5e4182fc43867e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cli
sha256:9f56d95ed665c1442fb4ce0b6415dbecf17729372527c46570aee5c372282a1e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openstack-cinder-csi-driver
sha256:db28495dcf72f26368357ed71b745c3c7b5e2a40c787bc8a76440f16872a0dbc provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-csi-snapshot-controller-operator
sha256:e03cefa188318d283eb39b799950a3540d0cfe14becf8995d391dfff5dd0369a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-network-tools
sha256:5c4039e2846f73e3703f63a2aa52a27a85d8c3c9c3a076e05e4aac33301a5715 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-autoscaler
sha256:aaf0854244f356ccb3f1cd70356362ac5ef7a9f227e08128740306430fd75497 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-coredns
mounted: provisioning.schmaustech.com:5000/openshift/release-images sha256:24e8d2e218b03efbd1c2548e6c8c7fc5a216edb5f2fe7e6e83a49fca53d1018a 14.83MiB
mounted: provisioning.schmaustech.com:5000/openshift/release-images sha256:c67baba1e9cbd1d8eeb0384784480534fe1ba2b96eca6d3d28ebbe18e50a0e56 21.32MiB
mounted: provisioning.schmaustech.com:5000/openshift/release-images sha256:f43eacb86576cfc775abc5d058b804eb2040e3e6dd57d232dd9b0af2c9c64d31 10.91MiB
mounted: provisioning.schmaustech.com:5000/openshift/release-images sha256:eac1b95df832dc9f172fd1f07e7cb50c1929b118a4249ddd02c6318a677b506a 79.44MiB
uploading: provisioning.schmaustech.com:5000/openshift/release-images sha256:329a959bf930d1e3a71ad92ea2604143a09f8bfdcd7e40ffe34dbc7981417cfe 538.6KiB
sha256:dd71b3cd08ce1e859e0e740a585827c9caa1341819d1121d92879873a127f5e2 provisioning.schmaustech.com:5000/openshift/release-images:4.9.12-x86_64
info: Mirroring completed in 15m3.28s (11.34MB/s)
Writing image mapping to oc-mirror-workspace/results-1660659095/mapping.txt
Writing ICSP manifests to oc-mirror-workspace/results-1660659095
~~~

We can see from the (abbreviated) output that the images for 4.9.12 were mirrored and oc-mirror created a output directory where we can find our imageContentSourcePolicy(ICSP) file:

~~~bash
$ ls -l oc-mirror-workspace/results-1660659095
total 36
drwxrwxr-x. 2 bschmaus bschmaus     6 Aug 16 08:55 charts
-rwxrwxr-x. 1 bschmaus bschmaus   401 Aug 16 09:11 imageContentSourcePolicy.yaml
-rw-rw-r--. 1 bschmaus bschmaus 29131 Aug 16 09:11 mapping.txt
drwxrwxr-x. 2 bschmaus bschmaus    52 Aug 16 08:55 release-signatures
~~~

This file is needed to make a running disconnected cluster use my own registry to retrieve images instead of trying to reach registry.redhat.io or quay.io.  And here is what the contents of the imageContentSourcePolicy.yaml file look like:

~~~bash
$ cat oc-mirror-workspace/results-1660659095/imageContentSourcePolicy.yaml
---
apiVersion: operator.openshift.io/v1alpha1
kind: ImageContentSourcePolicy
metadata:
  name: release-0
spec:
  repositoryDigestMirrors:
  - mirrors:
    - provisioning.schmaustech.com:5000/openshift/release
    source: quay.io/openshift-release-dev/ocp-v4.0-art-dev
  - mirrors:
    - provisioning.schmaustech.com:5000/openshift/release-images
    source: quay.io/openshift-release-dev/ocp-release
~~~

If you were to install a new disconnected cluster you would use the above information to populate the `imageContentSource` section of the `install-config.yaml` file the OpenShift installer relies on:

~~~yaml
apiVersion: v1
baseDomain: example.com
metadata:
  name: cluster
...
imageContentSources:
- mirrors:
  - provisioning.schmaustech.com:5000/openshift/release
  source: quay.io/openshift-release-dev/ocp-v4.0-art-dev
- mirrors:
  - provisioning.schmaustech.com:5000/openshift/release-images
  source: quay.io/openshift-release-dev/ocp-release
~~~

These settings cause the same `ImageContentSourcePolicies` to be created on a new cluster as you would when applying the `imageContentSourcePolicy.yaml` oc-mirror generated to a running cluster.

Now lets show another mirroring example for an operator.  In this case I just randomly picked elasticsearch as my operator.  Let us go ahead and create the imageset configuration file for the operator:

~~~bash
$ cat << EOF > ~/elasticsearch-operator-imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  registry:
    imageURL: provisioning.schmaustech.com:5000/mirror/metadata2
    skipTLS: false
mirror:
  operators:
    - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.10
      packages:
        - name: elasticsearch-operator
EOF
~~~

With the operator imageset file created we can now mirror the operator.   Normally one would probably want to have both there OpenShift release and operator in the same imageset but I wanted to break them out here for demonstration purposes.  Note, that I am also using another location (`metadata2`) to store the metadata, otherwise `oc-mirror` would try to interpret this second mirror command as an update and subsequently delete the OpenShift 4.9.12 release from my registry since I haven't specified it in my configuration file. More on that new pruning behavior later!

~~~bash
$ oc mirror --config=elasticsearch-operator-imageset-configuration.yaml docker://provisioning.schmaustech.com:5000 --dest-skip-tls
Checking push permissions for provisioning.schmaustech.com:5000
Creating directory: oc-mirror-workspace/src/publish
Creating directory: oc-mirror-workspace/src/v2
Creating directory: oc-mirror-workspace/src/charts
Creating directory: oc-mirror-workspace/src/release-signatures
No metadata detected, creating new workspace
WARN[0088] DEPRECATION NOTICE:
Sqlite-based catalogs and their related subcommands are deprecated. Support for
them will be removed in a future release. Please migrate your catalog workflows
to the new file-based catalog format. 
wrote mirroring manifests to oc-mirror-workspace/operators.1660659521/manifests-redhat-operator-index

To upload local images to a registry, run:

	oc adm catalog mirror file://redhat/redhat-operator-index:v4.10 REGISTRY/REPOSITORY
provisioning.schmaustech.com:5000/
  openshift-logging/elasticsearch-operator-bundle
    blobs:
      registry.redhat.io/openshift-logging/elasticsearch-operator-bundle sha256:4f4fb700ef54461cfa02571ae0db9a0dc1e0cdb5577484a6d75e68dc38e8acc1 32B
      registry.redhat.io/openshift-logging/elasticsearch-operator-bundle sha256:813d94d47b0041c4da11bdff39c7a386c28837025392bbd4afc451d1154f56cf 3.659KiB
      registry.redhat.io/openshift-logging/elasticsearch-operator-bundle sha256:c80990bee129333d1e40baf7aead2ab51c3aa53b0605f3ca7766e754052aca73 3.666KiB
      registry.redhat.io/openshift-logging/elasticsearch-operator-bundle sha256:792f1ae03ee18123e789de8fb0e2a2b278d14bf53b5bbaeef052180ed923a08d 3.668KiB
      registry.redhat.io/openshift-logging/elasticsearch-operator-bundle sha256:e6e3fb5e057657094df27d82b26c880247b6ae62ced3debd2f560ab74d1ffb45 12.46KiB
      registry.redhat.io/openshift-logging/elasticsearch-operator-bundle sha256:2931b3f39467f8d9699238cc37cd2ef7b43651a0458b3f715465ec71e19ca536 13.13KiB
      registry.redhat.io/openshift-logging/elasticsearch-operator-bundle sha256:12f03c3d7d189b8b2dc8efdbfdda279edf6b766963d0653efd7143f33e3a8630 13.88KiB
    manifests:
      sha256:7af1bcccd4219e6f0678bd0570e65e00e6924d0c7013a081a8036168badba724 -> 52791cb
      sha256:a909c412c3f41c780d193e55611561b89f42e140f02dd2057f9877104beea263 -> 999cca6f
      sha256:c45340a9bdd7b544b9410d1c52fa6e9b444571d506aa05d6849281df2014ef1c -> 98515fd6
  openshift-logging/elasticsearch-proxy-rhel8
    blobs:
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:028bdc977650c08fcf7a2bb4a7abefaead71ff8a84a55ed5940b6dbc7e466045 1.738KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:9f6eca53e5bceb7b88b4c078cc5d939e61d13301d8bdccb6b68eeea4d3879f3e 1.75KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:18ea5ce2a404500d5dfadf661ec0566faf7380e56b05ed37735b93844ed5faaa 1.753KiB
      (...)
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:e0444ce29df7b754434fd4557ec395c5a1759e4af71c3615a81471e0c7edeadc 72.84MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:0c673eb68f88b60abc0cba5ef8ddb9c256eaf627bfd49eb7e09a2369bb2e5db0 74.73MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:7472fa517fd188919c3ff6ec27806dd20311bb57e1e34a7f3998734688fa7b42 81.13MiB
    manifests:
      sha256:4229507d8b89b1a24f16477661b41a4a7a6925d90fed348dd609978297fe88c4
      sha256:5830aaf028a6a94ade5bb40840180911dd80ef0dc4eb93080cf8bde3476cd585
      sha256:59c4a4b20ad42a32f09c60fada67a9f20c237e8dce8ece678dced5c37a87b65b
      sha256:5ed92d7ac40e29491d237353b27237b38824eff3fe983628f199ff4ab6431ca1
      sha256:81436241c1042cff2523bfcc73d050700efa107b3fb41c0475ae18b4a3b7b43c
      sha256:8f2b1b2b15cab8a10b70a16e0af15dd73f0cf6932b16aac07c872beab229392d
      sha256:90cffb969ef49252a28fc5542eca6ad4dae64d4145a59643342b3409d34250c2
      sha256:9375a864efbb3011eb4b9e1754b338d8875a0cc70fc46996dec07946d4c9a2ae
      sha256:99bddf1517cd5c323b34fd4690c59ed3592f812f959a43981df1cc78bed27cc9
      sha256:b95246b7c3d0f619c92595918f9959d4d4540f492184f40353ecac5788370d3b
      sha256:bbe43427c7cbd4007ff673afbae66070c4ed0302d82068ab987a996076d68d79
      sha256:dda0674e21d5372ca54a504727e065ff2700e2b9d4a2d7c32aa0021ffd317d8c
      sha256:f0bee519bb4f20aa1b7f4a1ca01db0d6a068bbcc0fa07416c242c0acffc83422
      sha256:4229507d8b89b1a24f16477661b41a4a7a6925d90fed348dd609978297fe88c4 -> 95da220c
      sha256:8f2b1b2b15cab8a10b70a16e0af15dd73f0cf6932b16aac07c872beab229392d -> aea12e2c
      sha256:bbe43427c7cbd4007ff673afbae66070c4ed0302d82068ab987a996076d68d79 -> 546637da
  openshift-logging/elasticsearch-rhel8-operator
    blobs:
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:028bdc977650c08fcf7a2bb4a7abefaead71ff8a84a55ed5940b6dbc7e466045 1.738KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:9f6eca53e5bceb7b88b4c078cc5d939e61d13301d8bdccb6b68eeea4d3879f3e 1.75KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:18ea5ce2a404500d5dfadf661ec0566faf7380e56b05ed37735b93844ed5faaa 1.753KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:d851aca55b090483dcda2af05dbf048848399647d96dd3d4089f92d35f827db7 1.753KiB
  
  ...
  
  stats: shared=20 unique=173 size=4.997GiB ratio=0.88

phase 0:
  provisioning.schmaustech.com:5000 openshift-logging/elasticsearch-operator-bundle blobs=7  mounts=0 manifests=3  shared=0
  provisioning.schmaustech.com:5000 openshift4/ose-oauth-proxy                      blobs=24 mounts=0 manifests=10 shared=12
  provisioning.schmaustech.com:5000 openshift-logging/elasticsearch6-rhel8          blobs=28 mounts=0 manifests=16 shared=8
phase 1:
  provisioning.schmaustech.com:5000 openshift4/ose-kube-rbac-proxy                 blobs=54 mounts=12 manifests=16 shared=12
  provisioning.schmaustech.com:5000 openshift-logging/elasticsearch-rhel8-operator blobs=28 mounts=8  manifests=16 shared=8
  provisioning.schmaustech.com:5000 openshift-logging/logging-curator5-rhel8       blobs=40 mounts=8  manifests=16 shared=8
  provisioning.schmaustech.com:5000 openshift-logging/elasticsearch-proxy-rhel8    blobs=28 mounts=8  manifests=16 shared=8
  provisioning.schmaustech.com:5000 openshift-logging/kibana6-rhel8                blobs=28 mounts=8  manifests=16 shared=8

info: Planning completed in 5.8s
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:cadc283046514317dd4ecd1438530437d99cc92bf73080fb9e12e76751108de0 76.69MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:63dcfccb8725e6cc489621e7e4103def0d6302cba4de38027bd3276b300be5ba 84.18MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:2a7c17a4ad7579bc5fb4bc709db63d7852fbdc4fd195629fabd5d2e5ca7718dd 74.14MiB

...

Rendering catalog image "provisioning.schmaustech.com:5000/redhat/redhat-operator-index:v4.10" with file-based catalog 
Writing image mapping to oc-mirror-workspace/results-1660660213/mapping.txt
Writing CatalogSource manifests to oc-mirror-workspace/results-1660660213
Writing ICSP manifests to oc-mirror-workspace/results-1660660213
~~~

We can see from the above (abbreviated) output the images needed for the elasticsearch operator were mirrored and as part of the operation.  This included all the images that the ElasticSearch operator needs to run and also those to run the various ElasticSearch application components.  If the ElasticSearch operator would have dependencies on other OLM operators, those would be have been mirrored too! 

### Prefer Direct Update Paths and Skip Intermediate Releases

With the basics of mirroring out of the way lets explore one of the new features of oc-mirror.  Prefer direct path updates or shortest path to an update is a feature that enables one to download the least amount of images for an upgrade path.   In this example we currently have a OpenShift cluster that is at 4.9.12 and we want to bring it up to 4.10.22.  To generate the mirror for this we need to mirror the 4.10.22 release and also change the channel, in this case stable-4.10. For updates to new minor versions, OpenShift always has the versions of the previous minor release present in the channel.  This allows us to keep 4.9.12 as the minVersion and then set the a maxVersion of 4.10.22, which is our target.  By default oc-mirror will mirror all intermediate patch releases between 4.9.12 and 4.10.22, because it assumes you have more than one disconnected cluster possibly running on some of these versions. In oc-mirror 4.11 you can now set the shortestPath option to true, to mirror just the releases that are absolutely needed.

~~~bash
$ cat << EOF > ~/shortest-upgrade-imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  registry:
    imageURL: provisioning.schmaustech.com:5000/mirror/metadata
    skipTLS: false
mirror:
  platform:
    channels:
    - name: stable-4.10
      minVersion: 4.9.12
      maxVersion: 4.10.22
      shortestPath: true
EOF
~~~

Now that we have created the imageset lets go ahead and run the mirror command to see what happens.  In this example of the mirror command I am going to go ahead and use the --dry-run option which will go through the motions of showing us which images are going to get mirrored but will not actually mirror any data to my local registry.

~~~bash
$ oc mirror --config=shortest-upgrade-imageset-configuration.yaml docker://provisioning.schmaustech.com:5000 --dest-skip-tls --dry-run
Checking push permissions for provisioning.schmaustech.com:5000
Creating directory: oc-mirror-workspace/src/publish
Creating directory: oc-mirror-workspace/src/v2
Creating directory: oc-mirror-workspace/src/charts
Creating directory: oc-mirror-workspace/src/release-signatures
provisioning.schmaustech.com:5000/
  openshift/release
    blobs:
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:39382676eb30fabb7a0616b064e142f6ef58d45216a9124e9358d14b12dedd65 1.428KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:47aa3ed2034c4f27622b989b26c06087de17067268a19a1b3642a7e2686cd1a3 1.747KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5dad5185f24f8a37ff0ea88498affcfc48b0ffc1968dae33269dbfd467271c45 1.786KiB
      (...)
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c331a0acfc68a1a55bfb70ba03240ad4027bcc34a50d76e703a34bdc9ead5448 1.029GiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:e831cb81011a33a650c12f03d528a8f2953e93b2e87dd21b99ba7ffbb8c20fcf 1.03GiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4a2977094d3b99ccd555aa043343ee51c971979bf011b021a0ff247d7a2f0dae 1.04GiB
    manifests:
      sha256:000d3c52c0de9af1b455b8d9ed9fb814aef0e074327fe95df4716a091af81109 -> 4.9.42-x86_64-cluster-baremetal-operator
      sha256:00bfbe7800a9c803d13e666122a7b1f52bea6e53b0fb2352d8566d1e75759708 -> 4.9.12-x86_64-keepalived-ipfailover
      sha256:0213887c325d3967d122fe875b45c9259e2b8388db9dc4e0a25c0561414b8737 -> 4.9.12-x86_64-aws-ebs-csi-driver
      sha256:0305702b3b7208c339c5df376aaacf6992465e34490bc16c2d66e423039b2b06 -> 4.9.12-x86_64-insights-operator
      sha256:0310a7b9ec788788a2a6324475add611b5c5304511fe27e7444500fdefbc6ef0 -> 4.9.12-x86_64-ironic-static-ip-manager
      sha256:037a178d5e046fdae2d51a7e758713adcb57348471fde203e0bd3af4c6f7b77b -> 4.10.22-x86_64-thanos
      sha256:038452cc237d09fbbea348a454007760fb2d04e1e2439e931c505665c2c98071 -> 4.9.12-x86_64-haproxy-router
      sha256:03a06499c3efae948535eb61c340efb8511d3ac45db1ca9fccfe5515e49a70ac -> 4.10.22-x86_64-must-gather
      sha256:03cbd8048e00835d29ced43ddb4548e979e51dda727239cfdf027c9ef47339cf -> 4.9.12-x86_64-egress-router-cni
      sha256:0461810622791c98ab3ad77c541d9a866d3e14eaa18ce673ca5c1cb2733894e4 -> 4.10.22-x86_64-haproxy-router
      sha256:0503429fe988d46d901cd8c625c5ca3e27d0eddd151cd6ee2af346c6397fbffe -> 4.9.12-x86_64-baremetal-machine-controllers
      sha256:051c76b923d9826bd577aeb1e176a4096a9af47e9a7c1819158282a5a417170b -> 4.9.12-x86_64-machine-config-operator
      sha256:06ca4e45c6ec3aba104a066905322e35b44c8a2c293d9f2821c83ed2cb743633 -> 4.9.12-x86_64-prometheus-config-reloader
      sha256:0735d3d8e6d50ad3934e512dcf51717b161c1e3de4f79b278465c3dbc3473b93 -> 4.10.22-x86_64-baremetal-runtimecfg
      sha256:078da3dec4ec9825f45712cb4b5500d61097ef813cef446f8c5b56617d7740b0 -> 4.10.22-x86_64-csi-driver-shared-resource
      sha256:07c709f51f7383720c2acca830b3441c188ab0f077616d075a5a6bd9cbd1a2e4 -> 4.9.12-x86_64-cluster-storage-operator
      sha256:094ba20f45a142d52227b5559f17adb3cb8e100433880af8f3aa49a25c8a53b7 -> 4.9.42-x86_64-cluster-dns-operator
      sha256:0a456b7ba3eb1dc739c78e400fec57f382221f3ebf33554732b53e3d8dc8712e -> 4.10.22-x86_64-azure-cloud-node-manager
      sha256:0aaf300c8a5ca54774a067f257c20728a6a90f76bc981ea0c97eff458a8980e0 -> 4.9.42-x86_64-cluster-storage-operator
      sha256:0b8a09ab3c370f7ef89319f3ff66cc346bbfc1cc48b58c2d40ef7d61b33a349c -> 4.9.12-x86_64-multus-route-override-cni
      sha256:0ba72e9a498eff6b1e377564f5766cc1cb5e371d107ce6e81de73b8ea66d1c9d -> 4.10.22-x86_64-cluster-kube-apiserver-operator
      sha256:0bb893c699435046404e5c96a6375c0aa4e2e8bfe78a466af42ab3afb75060f2 -> 4.10.22-x86_64-hypershift
      sha256:0c12ce723d41af21546a6ac538f336861d307882dfc703cf675ec8b10ba46a6e -> 4.10.22-x86_64-machine-os-images
      sha256:0c2c8ae9c06cac2db313a4ae394a4eab22f611b071ed1e612e86944709e120be -> 4.9.42-x86_64-telemeter
      sha256:0c9fc10c8afe200d865520cdb076bac1e7404f783f98e349905736f4ef36df24 -> 4.10.22-x86_64-aws-machine-controllers
      sha256:0cfd1727486ae652c5f566055499f6d4b3d5d6d73da6dd8c75a45f88a621905f -> 4.10.22-x86_64-kuryr-controller
      sha256:0d20224a81633274071ae8fbf9c5f013cb386858a4f16cf46f53ef8508a11584 -> 4.10.22-x86_64-kube-storage-version-migrator
      sha256:0d741e590eee37fd9708609dde99c68f0b8fba9d0d5cee172227037b7446e3c7 -> 4.9.12-x86_64-ironic-ipa-downloader
      sha256:0db352b5240ca2b75b53c754e9013fdeb36d118d8d0ac2bb410d716e428ff73e -> 4.9.42-x86_64-cluster-samples-operator
      sha256:0db9f75f74f8114fd0f3be3ad34ca5c6f0dc892fc3586d1c92b31213b268447a -> 4.9.42-x86_64-ironic-ipa-downloader
      sha256:0dfdfce02f420c81d6d707dbad3132898a84deec949084618af8af6f3a602481 -> 4.9.42-x86_64-baremetal-runtimecfg
      sha256:0e0a6328ea7449e2b3ecaa057f59f98cb49e2c71b60ea0d9a4f3d358ef472f36 -> 4.9.12-x86_64-ironic-inspector
      sha256:0f00662ef8bfd9cf36b7f5a7bf5321d71eb57c1a91aaf8b770b0f0b3fd931550 -> 4.10.22-x86_64-cluster-ingress-operator
      sha256:0fdd27a12ee71d1268ef2e7c4cfe8dbdd3a86e3010f77db3f2e530b928fa2a42 -> 4.9.12-x86_64-csi-node-driver-registrar
      sha256:1019fb094294660c8da4741ac820e818b2baf4d43813feb627cfe90c0b674ba2 -> 4.10.22-x86_64-machine-api-operator
      sha256:10cdfbbe2ef60d136948cfa9ac72d8e23d1e08b52a6e0bf0f6e0cab8e3b55403 -> 4.9.42-x86_64-jenkins-agent-base
      sha256:1141f74833abe8de6384022fb7a800171640a40464a82669421517f5f7d2a128 -> 4.9.12-x86_64-csi-external-snapshotter
      sha256:119ce8d95beaef7fcc420fb1acc6d311a1a6309a371a1ccc427b64a7f2d1e371 -> 4.9.12-x86_64-csi-external-provisioner
      sha256:119f8096655464ede18a1be74531251323d6a0d50773b8d58dabc07fa64c0304 -> 4.10.22-x86_64-docker-builder
      sha256:11a4f413212bddf9034d92a561acf66a816f2601656fbbde3e35b6f83ff72881 -> 4.10.22-x86_64-ovn-kubernetes
      sha256:1267dd9b35b81041888212be03415b2fab37d1ac9e0fb4d9ddcf60c72f7a99ad -> 4.9.12-x86_64-container-networking-plugins
      sha256:132250626db1a76bb49a23a5593db1a9ba8cc237003c6f8d3781ac7cb268b2f5 -> 4.10.22-x86_64-cluster-dns-operator
      sha256:13a775771eb0fbb24216f5cbaeab394426671bf5d7a5aa23268607e51b59ecbd -> 4.9.12-x86_64-jenkins-agent-base
      sha256:13bb02aabde612c09fe136b0ca02487cb5f48b9ddf871afbe0d4f2b4b1b66b3a -> 4.9.12-x86_64-csi-external-resizer
      sha256:146b25bd783a4ef26de48ba3d984a6c9799edbacb0cf2895b26d924fe0982d0d -> 4.10.22-x86_64-cluster-storage-operator
      sha256:1520c47a8e05a0895b15d34a6464fb7b3308c21ef8e920831e4285ae0eacabb1 -> 4.9.42-x86_64-ironic
      sha256:154b10c8fd2ae2d3a5a63fa2fffb9bebe8fcbaae776aa96d8456cbb983744d00 -> 4.9.42-x86_64-cluster-autoscaler
      sha256:154fa9cbd70c6afee25fe4b27011ee07a0657393e5ef3fee03a20e9c783a8e03 -> 4.9.42-x86_64-cluster-kube-scheduler-operator
      sha256:170535928175c08be2ae36c03229944cc875bd6900b4edd54f883738f631b87f -> 4.9.42-x86_64-jenkins-agent-maven
      
      ...
      
    manifests:
      sha256:21f0bbf81d5328a00b6b44a94e70765eb20c4df67a89721a72048baec2c99770 -> 4.9.42-x86_64
      sha256:62c995079672535662ee94ef2358ee6b0e700475c38f6502ca2d3d13d9d7de5b -> 4.10.22-x86_64
      sha256:dd71b3cd08ce1e859e0e740a585827c9caa1341819d1121d92879873a127f5e2 -> 4.9.12-x86_64
  stats: shared=13 unique=895 size=30.94GiB ratio=0.99

phase 0:
  provisioning.schmaustech.com:5000 openshift/release-images blobs=19 mounts=0 manifests=3 shared=13
phase 1:
  provisioning.schmaustech.com:5000 openshift/release blobs=902 mounts=13 manifests=441 shared=13

info: Planning completed in 1m27.51s
info: Dry run complete
Writing image mapping to oc-mirror-workspace/mapping.txt
Writing image pruning plan to oc-mirror-workspace/pruning-plan.json
~~~

The output got shortened to save space, but we can see from our direct update path run that we needed to obtain images for 4.9.42 since there was no direct path from 4.9.12 to 4.10.22.  However oc mirror ensures it brings in all the images required without the cluster administrator needing to do all the guess work!

Tip: if you want to know upfront if there are mandatory intermediate releases or if there is a direct upgrade path, you can use this awesome tool on the Red Hat Customer Portal: https://access.redhat.com/labs/ocpupgradegraph/update_path?channel=stable-4.10&arch=x86_64&is_show_hot_fix=true&current_ocp_version=4.9.12&target_ocp_version=4.10.22

<img width="1146" alt="image" src="https://user-images.githubusercontent.com/12664117/185098880-8a7476db-863e-4ea1-9c2b-e8fa80faf056.png">

It will yield the update graph including any required intermediate releases and the exact commands to perform the update.  It even shows you what other releases are in a particular OpenShift channel!

### Select Image Content Based on Version Range

Another new feature of oc mirror is the ability to select a range of version images for a OpenShift or operator release.   In this example we will demonstrate how to mirror all the images between the release of 4.10.10 to 4.10.22.  First we need to go ahead and construct our imageset configuration and set the minVersion to 4.10.10 and the maxVersion to 4.10.22:

~~~bash
$ cat << EOF > ~/version-range-imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  registry:
    imageURL: provisioning.schmaustech.com:5000/mirror/metadata
    skipTLS: false
mirror:
  platform:
    channels:
      - name: stable-4.10
        minVersion: 4.10.10
        maxVersion: 4.10.22
EOF
~~~

With the imageset configuration file created lets go ahead and run the oc mirror command and see what happens.  Again I am using the --dry-run option to avoid actually mirroring the images into my local registry but still getting a preview of what would be mirrored if I did it without.

~~~bash
$ oc mirror --config=version-range-imageset-configuration.yaml docker://provisioning.schmaustech.com:5000 --dest-skip-tls --dry-run
Checking push permissions for provisioning.schmaustech.com:5000
Creating directory: oc-mirror-workspace/src/publish
Creating directory: oc-mirror-workspace/src/v2
Creating directory: oc-mirror-workspace/src/charts
Creating directory: oc-mirror-workspace/src/release-signatures
provisioning.schmaustech.com:5000/
  openshift/release
    blobs:
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:39382676eb30fabb7a0616b064e142f6ef58d45216a9124e9358d14b12dedd65 1.428KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5aeea3b4a4e4d17d0941b4d4cdec6921becac2c969971962a1666cef4b226512 1.491KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:374b4456b24c0bbc53899837c9582ab1b76b2bcc03b71f1dd58ea6b243d1daef 1.805KiB
      (...)
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4dcecf28bdcaffe322a8ab76eb916a28f21487937fa9c0676ed5fb3999cb9260 1.04GiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:bfa42d88434f5ede92e85e79660573dc6b3ffc555150c2c06beb1503021afdd9 1.04GiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4a2977094d3b99ccd555aa043343ee51c971979bf011b021a0ff247d7a2f0dae 1.04GiB
    manifests:
      sha256:002be9b4001d090ab4ed3a9c8950be14f6bde64df1178d9eef86f017c08980c7 -> 4.10.20-x86_64-cluster-kube-controller-manager-operator
      sha256:00eb90593a05987ee61ebe7278469470bd071e76ed1ef96669e23bd940c5af62 -> 4.10.16-x86_64-vsphere-cloud-controller-manager
      sha256:0146b20629175d9a1ab399e7a6fc140b93f84a4ef379777eba58cd2dc5daba9f -> 4.10.16-x86_64-thanos
      
      ...
      
    manifests:
      sha256:0dc1a4b4d9ea7954987f63e506474a4f0dc55e5f1ea5c1f6f1179e2c09eaffda -> 4.10.11-x86_64
      sha256:195de2a5ef3af1083620a62a45ea61ac1233ffa27bbce7b30609a69775aeca19 -> 4.10.18-x86_64
      sha256:39efe13ef67cb4449f5e6cdd8a26c83c07c6a2ce5d235dfbc3ba58c64418fcf3 -> 4.10.10-x86_64
      sha256:420ee7160d4970304ae97a1b0a77d9bd52af1fd97c597d7cb5d5a2c0d0b72dda -> 4.10.21-x86_64
      sha256:4f516616baed3cf84585e753359f7ef2153ae139c2e80e0191902fbd073c4143 -> 4.10.13-x86_64
      sha256:62c995079672535662ee94ef2358ee6b0e700475c38f6502ca2d3d13d9d7de5b -> 4.10.22-x86_64
      sha256:87d800b3f7c657ed6f18c920f7c925df91b000805366bee068de3625807abd33 -> 4.10.17-x86_64
      sha256:a546cd80eae8f94ea0779091e978a09ad47ea94f0769b153763881edb2f5056e -> 4.10.16-x86_64
      sha256:b89ada9261a1b257012469e90d7d4839d0d2f99654f5ce76394fa3f06522b600 -> 4.10.20-x86_64
      sha256:ddcb70ce04a01ce487c0f4ad769e9e36a10c8c832a34307c1b1eb8e03a5b7ddb -> 4.10.15-x86_64
      sha256:e6aa643f494f64eefc8a6ad92b64156290bccd7e5cda56b8809f3a67ee2a53bb -> 4.10.14-x86_64
      sha256:f77f4f75c1e1a4ddd0a0355f298a834db3473fd9ca473235013e9419d1df16db -> 4.10.12-x86_64
  stats: shared=20 unique=2011 size=87.07GiB ratio=1.00

phase 0:
  provisioning.schmaustech.com:5000 openshift/release-images blobs=44 mounts=0 manifests=12 shared=20
phase 1:
  provisioning.schmaustech.com:5000 openshift/release blobs=2007 mounts=20 manifests=984 shared=20

info: Planning completed in 3m16.6s
info: Dry run complete
Writing image mapping to oc-mirror-workspace/mapping.txt
Writing image pruning plan to oc-mirror-workspace/pruning-plan.json
~~~

We can see from our version range run that we would have ended up mirroring images for all version between 4.10.10 to 4.10.22 which is the equivalent of ~87GB of data!  I am really appreciating that dry run option in this example.

Version ranges also work with operators, here is an example:

~~~yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
mirror:
  operators:
    - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.11
      packages:
        - name: rhacs-operator
          channels:
            - name: latest
              minVersion: '3.67.0'
              maxVersion: '3.67.2'
~~~

For this to work you need to know the operators by their package name and also which channels and which version exist.  Fortunately oc-mirror helps you with that too:

~~~bash
$ oc mirror list operators --catalog registry.redhat.io/redhat/redhat-operator-index:v4.11
NAME                                          DISPLAY NAME                                             DEFAULT CHANNEL

...
rhacs-operator                                Advanced Cluster Security for Kubernetes                 latest
...
~~~

And to determine the release versions and channels:

~~~bash
$ oc mirror list operators --package rhacs-operator --catalog registry.redhat.io/redhat/redhat-operator-index:v4.11
NAME            DISPLAY NAME                              DEFAULT CHANNEL
rhacs-operator  Advanced Cluster Security for Kubernetes  latest

PACKAGE         CHANNEL     HEAD
rhacs-operator  latest      rhacs-operator.v3.71.0
rhacs-operator  rhacs-3.62  rhacs-operator.v3.62.1
rhacs-operator  rhacs-3.64  rhacs-operator.v3.64.2
rhacs-operator  rhacs-3.65  rhacs-operator.v3.65.1
rhacs-operator  rhacs-3.66  rhacs-operator.v3.66.1
rhacs-operator  rhacs-3.67  rhacs-operator.v3.67.2
rhacs-operator  rhacs-3.68  rhacs-operator.v3.68.2
rhacs-operator  rhacs-3.69  rhacs-operator.v3.69.2
rhacs-operator  rhacs-3.70  rhacs-operator.v3.70.1
rhacs-operator  rhacs-3.71  rhacs-operator.v3.71.0
~~~

### Always mirroring the newest content

Restricting the version range of OpenShift releases and Operators to be mirrored allows to be very selective about what is available in your disconnected clusters and safe storage space.  However, with this approach you need to manually discover new release version and manually adjust the imageset configuration file.  In a less restricted variant of this you can omit the `maxVersion` property which will cause oc-mirror to automatically determine the newest / highest version of OpenShift or Operator in a channel.

This way you can keep running oc-mirror in some form of cron job and get newer releases automatically:

~~~yaml
---
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  registry:
    imageURL: provisioning.schmaustech.com:5000/mirror/metadata
    skipTLS: false
mirror:
  platform:
    channels:
      - name: stable-4.11
        minVersion: 4.11.0
        shortestPath: true
  operators:
    - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.11
      packages:
        - name: rhacs-operator
          minversion: '3.71.0'
          shortestPath: true
~~~

In the above configuration oc-mirror is instructed to mirror from the 4.11.0 OpenShift release onwards as well as start to mirror Red Hat Advanced Cluster Security 3.71.0.  Each time it is executed it determien the newest release of each and mirror that. Because `shortestPath` is set to `true` it will skip any intermediate release unless it is required to make it whatever the latest version is.

### Pruning Images

Another great feature added was pruning images that ensures the older content in the mirrored registry gets purged especially if it will not be needed anymore.  Pruning works by either entirely removing explicitly listed release or catalogs from the imageset config file or adjust the version range.  Take the following example:

~~~yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  registry:
    imageURL: provisioning.schmaustech.com:5000/mirror/metadata
    skipTLS: false
mirror:
  operators:
    - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.11
      packages:
        - name: rhacs-operator
          channels:
            - name: latest
              minVersion: '3.67.2'
              maxVersion: '3.71.0'
~~~

If this was mirrored into your registry via oc-mirror you can decrease the registry storage consumption by removing releases you are not running anymore.  Let's say the oldest version of Red Hat Advanced Cluster Security you are running is now `3.70.0` you can adjust the image set configuration by increasing the `minVersion` to that level:

~~~yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  registry:
    imageURL: provisioning.schmaustech.com:5000/mirror/metadata
mirror:
  operators:
    - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.11
      packages:
        - name: rhacs-operator
          channels:
            - name: latest
              minVersion: '3.70.0'
              maxVersion: '3.71.0'
~~~

This would cause oc-mirror to delete any older release than `3.70.0` of said operator from the registry.  For this to work it is important to use the same metadata storage config across different invocations of `oc-mirror`.  This would also work if we just had `minVersion` but no `maxVersion`.  This way you can combine getting the latest and greatest while deleteing content that is no longer required.

To see it in action I will demonstrate it with with a simpler example where oc-mirror is going to mirror the Universal Base Image (ubi).   First lets create our imageset configuration file for the ubi7 image:

~~~bash
$ cat << EOF > ~/ubi-imageset-config.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  registry:
    imageURL: provisioning.schmaustech.com:5000/mirror/metadata
    skipTLS: false
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
  registry:
    imageURL: provisioning.schmaustech.com:5000/mirror/metadata
    skipTLS: false
mirror:
  additionalimages:
    - name: registry.redhat.io/ubi8/ubi:latest
EOF
~~~

And now lets run our oc-mirror command again and see what happens this time:

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

As we can see on the second run we mirrored the ubi8 image which replaced the ubi7 image. The mirror operation noticed this (based on the metadata stored in the registry) and then proceeded to prune the ubi7 image from the registry.   If we had wanted to keep both images on our registry we would have needed to specify both in our imageset configuration file.

Pruning always happens when you remove content from the image set configuration file, either implictly by adjusting version ranges or explicitly by removing entire releases, channels, catalogs or images. In the current version of oc-mirror pruning is only triggered when there was also new content found to be mirrored, however this will be something that can explicitly be triggered in a future version.

### OpenShift Update Service Graph Creation

The OpenShift Update Service (OSUS) provides over-the-air updates to OpenShift Container Platform, including Red Hat Enterprise Linux CoreOS (RHCOS). It provides a graph, or diagram, that contains the vertices of component Operators and the edges that connect them. The edges in the graph show which versions you can safely update to. The vertices are update payloads that specify the intended state of the managed cluster components.  With OSUS you can therefore have the same ease-of-use and graphical experience to update the cluster as a connected cluster would have that gets the graph data directly from Red Hat.

With oc-mirror we can also mirror the graph data to our disconnected registry which enables our disconnected clusters to still show the visual of what versions we can update to.  In this example we will take the original 4.9.12 imageset file we created and just add the graph: true option.

~~~bash
$ cat << EOF > ~/imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  registry:
    imageURL: provisioning.schmaustech.com:5000/mirror/metadata
    skipTLS: false
mirror:
  platform:
    channels:
      - name: stable-4.9
        minVersion: 4.9.12
        maxVersion: 4.9.12
    graph: true
EOF
~~~

Now with the updated imageset we can run the oc mirror command again.  Can we catch watch changed in the output?

~~~bash
$ oc mirror --config=imageset-configuration.yaml docker://provisioning.schmaustech.com:5000 --dest-skip-tls
Checking push permissions for provisioning.schmaustech.com:5000
Creating directory: oc-mirror-workspace/src/publish
Creating directory: oc-mirror-workspace/src/v2
Creating directory: oc-mirror-workspace/src/charts
Creating directory: oc-mirror-workspace/src/release-signatures
Adding graph data
provisioning.schmaustech.com:5000/
  openshift/release
    blobs:
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:47aa3ed2034c4f27622b989b26c06087de17067268a19a1b3642a7e2686cd1a3 1.747KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5dad5185f24f8a37ff0ea88498affcfc48b0ffc1968dae33269dbfd467271c45 1.786KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:22f677655049d4c2e6cd9e49ca9ed20f34ac175ef0c82f5c5eabc79031c1c29a 1.832KiB
      (...)
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:99b8ec24066fc655b57a0dbd8702783b7a34230ed106d80a4e3c526a04865807 461MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:3b810e182d6c6693732816a47781515d8ee9eb8a610252eb5578e2dde1c666bb 480.7MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c331a0acfc68a1a55bfb70ba03240ad4027bcc34a50d76e703a34bdc9ead5448 1.029GiB
    manifests:
      sha256:00bfbe7800a9c803d13e666122a7b1f52bea6e53b0fb2352d8566d1e75759708 -> 4.9.12-x86_64-keepalived-ipfailover
      sha256:0213887c325d3967d122fe875b45c9259e2b8388db9dc4e0a25c0561414b8737 -> 4.9.12-x86_64-aws-ebs-csi-driver
      
      ...
      
Writing image mapping to oc-mirror-workspace/results-1660664346/mapping.txt
Writing UpdateService manifests to oc-mirror-workspace/results-1660664346
Writing ICSP manifests to oc-mirror-workspace/results-1660664346
~~~

With our imageset run complete you may have noticed one additional item was shown in the output.  Did we see the following:

~~~bash
Adding graph data
~~~

This tells us that along with the images being mirrored the graph data for OSUS was also mirrored into our local registry.  But oc-mirror did more for us as well.   If we look in the ICSP manifest directory we will see something else:

~~~bash
$ ls -l oc-mirror-workspace/results-1660664346
total 40
drwxrwxr-x. 2 bschmaus bschmaus     6 Aug 16 10:37 charts
-rwxrwxr-x. 1 bschmaus bschmaus   642 Aug 16 10:39 imageContentSourcePolicy.yaml
-rw-rw-r--. 1 bschmaus bschmaus 29415 Aug 16 10:39 mapping.txt
drwxrwxr-x. 2 bschmaus bschmaus    52 Aug 16 10:37 release-signatures
-rwxrwxr-x. 1 bschmaus bschmaus   351 Aug 16 10:39 updateService.yaml
~~~

There is a new file called `updateService.yaml` along with our `imageContentSourcePolicy.yaml`.   Lets take a look at whats inside:

~~~bash
$ cat oc-mirror-workspace/results-1660664346/updateService.yaml
apiVersion: updateservice.operator.openshift.io/v1
kind: UpdateService
metadata:
  name: update-service-oc-mirror
spec:
  graphDataImage: provisioning.schmaustech.com:5000/openshift/graph-image@sha256:dc4221e8fa732873152ca822ed110d4c425ad664f2882b315bedc504780d6e1d
  releases: provisioning.schmaustech.com:5000/openshift/release-images
  replicas: 2
~~~

This is the `UpdateService` custom resource that the OSUS operator uses in order to create a local instance of the update graph server that connected clusters normally talk to via api.openshift.com. It is configuring to find the graph data in our local registry.  Its just another example of oc-mirror and its capabilities in action.  In an automation script that regularly executes of oc-mirror you can apply `updateService.yaml` and `imageContentSourcePolicy.yaml` conveniently to all your disconnected clusters so that they get the latest graph data and the latest offline access configuration, or push it into a central git repository to distribute it via Red Hat Advanced Cluster Management as a policy.

Hopefully these example gave a good idea on how the oc mirror plugin works and how it simplifies the mirroring process for disconnected environments.  I personally found it a lot more user friendly then the old style of mirroring content specifically when it comes to dealing with operators.  
