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

Before I demonstrate some of the new features of oc-mirror let us first show a couple basic mirror examples of mirroring an OpenShift release and also an OpenShift operator.  First lets go ahead and create the OpenShift release imageset configuration.   In this example I am going to use 4.9.12 as the only release version I want to mirror:

~~~bash
$ cat << EOF > ~/imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  local:
    path: metadata
mirror:
  platform:
    channels:
      - name: stable-4.9
        minVersion: 4.9.12
        maxVersion: 4.9.12
EOF
~~~

Now that we have the imageset created let us go ahead and run the oc mirror command to mirror the image contents to our local registry of provisioning.schmaustech.com:5000:

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
      sha256:03cbd8048e00835d29ced43ddb4548e979e51dda727239cfdf027c9ef47339cf -> 4.9.12-x86_64-egress-router-cni
      sha256:0503429fe988d46d901cd8c625c5ca3e27d0eddd151cd6ee2af346c6397fbffe -> 4.9.12-x86_64-baremetal-machine-controllers
      sha256:051c76b923d9826bd577aeb1e176a4096a9af47e9a7c1819158282a5a417170b -> 4.9.12-x86_64-machine-config-operator
      sha256:06ca4e45c6ec3aba104a066905322e35b44c8a2c293d9f2821c83ed2cb743633 -> 4.9.12-x86_64-prometheus-config-reloader
      sha256:07c709f51f7383720c2acca830b3441c188ab0f077616d075a5a6bd9cbd1a2e4 -> 4.9.12-x86_64-cluster-storage-operator
      sha256:0b8a09ab3c370f7ef89319f3ff66cc346bbfc1cc48b58c2d40ef7d61b33a349c -> 4.9.12-x86_64-multus-route-override-cni
      sha256:0d741e590eee37fd9708609dde99c68f0b8fba9d0d5cee172227037b7446e3c7 -> 4.9.12-x86_64-ironic-ipa-downloader
      sha256:0e0a6328ea7449e2b3ecaa057f59f98cb49e2c71b60ea0d9a4f3d358ef472f36 -> 4.9.12-x86_64-ironic-inspector
      sha256:0fdd27a12ee71d1268ef2e7c4cfe8dbdd3a86e3010f77db3f2e530b928fa2a42 -> 4.9.12-x86_64-csi-node-driver-registrar
      sha256:1141f74833abe8de6384022fb7a800171640a40464a82669421517f5f7d2a128 -> 4.9.12-x86_64-csi-external-snapshotter
      sha256:119ce8d95beaef7fcc420fb1acc6d311a1a6309a371a1ccc427b64a7f2d1e371 -> 4.9.12-x86_64-csi-external-provisioner
      sha256:1267dd9b35b81041888212be03415b2fab37d1ac9e0fb4d9ddcf60c72f7a99ad -> 4.9.12-x86_64-container-networking-plugins
      sha256:13a775771eb0fbb24216f5cbaeab394426671bf5d7a5aa23268607e51b59ecbd -> 4.9.12-x86_64-jenkins-agent-base
      sha256:13bb02aabde612c09fe136b0ca02487cb5f48b9ddf871afbe0d4f2b4b1b66b3a -> 4.9.12-x86_64-csi-external-resizer
      sha256:183b7a1d2b4d3600a7251eda82f90ff45d4b5324b3bfdba6a091aa735fcff4be -> 4.9.12-x86_64-cluster-version-operator
      sha256:18740b88ca23a49a1cbbf3be74bf96ff536e961bb11235d4539cffccda7101ed -> 4.9.12-x86_64-kuryr-cni
      sha256:1938512df87a22d0a3c4f9d7aafa9557d7268f3511b52f9494946bb7478bc2da -> 4.9.12-x86_64-ovirt-machine-controllers
      sha256:19ac6ad2cac43fd4134dfed13168ac0761a014ec55d277eb4d1259caac757b04 -> 4.9.12-x86_64-cloud-credential-operator
      sha256:1bbe2e9e053350f5af04d80e9a088fb6cc5ca7898b8c1dfdeb5269a4befffa60 -> 4.9.12-x86_64-cluster-node-tuning-operator
      sha256:214b7e2b9bece6260311a2dc28bfc12c379e37186c09582221ec1fe724a995d1 -> 4.9.12-x86_64-kuryr-controller
      sha256:22e31d53a7c6a92176d5a183fd213fff4e2e68c343ccf6cca9c7fc1363e34836 -> 4.9.12-x86_64-multus-cni
      sha256:23244e61eaee6a986e1ac26def05a6eeefa3150b9e740787d8ec0be765b6c8ec -> 4.9.12-x86_64-tools
      sha256:235f6b6a1c9c41dbf3b8867bdb25da59c5e3a15af93b61fb13b4b5f41814d954 -> 4.9.12-x86_64-cluster-policy-controller
      sha256:23921ee4c51c3fc5b32598d89233353855b335cb1d3592bd4e14cda9b23aa0e3 -> 4.9.12-x86_64-cluster-bootstrap
      sha256:251f6fcf7b079421f5ed480db37715ce1fc35e4bbbf859b3ffc449b84d8fd246 -> 4.9.12-x86_64-baremetal-operator
      sha256:279a3393fbb0c344b410de5aed4702ae41c49382a5c271c5a6d915defa72c47a -> 4.9.12-x86_64-jenkins-agent-nodejs
      sha256:292d0fbd28892d4952e0cd914cb4b1eca7b5b31dad3ff26abbbfe3ee57ea24e6 -> 4.9.12-x86_64-cli-artifacts
      sha256:2ddae06a7ca8031611b8ec5a63050195a5b747c66289b8ad810bc8da73aa58d4 -> 4.9.12-x86_64-ironic-hardware-inventory-recorder
      sha256:2fc2c9d9fae3070c00239ea3d0d1d9fa7477c99c296b17b2fc352794c535912e -> 4.9.12-x86_64-network-metrics-daemon
      sha256:2ff95508332a75a0b0b50be84416297781946d0feb687df36c8082baff564857 -> 4.9.12-x86_64-prometheus-alertmanager
      sha256:33001ffab5cba9aaff19e475e0118ea158c99797bf6f3e210832f129ac34d71c -> 4.9.12-x86_64-cluster-kube-storage-version-migrator-operator
      sha256:3730bafc9c8a397887d4213b220afffe11cb6835ea5754e60a0cfe967efbdaf5 -> 4.9.12-x86_64-prometheus
      sha256:37e092de518988e67fb60bc5eaaa365058b6be6a1877613af5d12e2c4a2ec129 -> 4.9.12-x86_64-oauth-server
      sha256:3e4dd326434d115808c7bff14d8a38806c6757d928f2359737496fe6a32b7b4e -> 4.9.12-x86_64-cluster-kube-controller-manager-operator
      sha256:40302e29af4c6e3e9a6e73544a2fd4239da691b0afde937e76d078a642d30817 -> 4.9.12-x86_64-service-ca-operator
      sha256:406384d7815dc29c85c903318b1a98a2f3bd65b440a9344b71d6ddfa3e98c287 -> 4.9.12-x86_64-ovirt-csi-driver-operator
      sha256:426f99588fbce292824dba75372675b83daea64a1cf5d321fb5e4182fc43867e -> 4.9.12-x86_64-cli
      sha256:42eb156593fbc94fa12eaa5938c8335d500e222be370475ee19a57ad7f7f42a8 -> 4.9.12-x86_64-mdns-publisher
      sha256:43150eb3f35005a1c04fa4ed6fef3c8cd3199991658a88530d28cd1a32a5597a -> 4.9.12-x86_64-aws-cloud-controller-manager
      sha256:4446e75f22adf0e091bb337da46174ba7e82b394da5b04a2dc9c60b749c7cd02 -> 4.9.12-x86_64-vsphere-problem-detector
      sha256:46d7b63794f6ceec9fbcc63bbf170aff5ee3152ccb24ba51b8167ac59a30ca6a -> 4.9.12-x86_64-cluster-etcd-operator
      sha256:4b14ffcea52f9eb5952546634cb26bfb1d523a4dd81382021c71673fed91efa2 -> 4.9.12-x86_64-operator-lifecycle-manager
      sha256:4b56346c5c3c09eee6d8d37b6796f1e4f15e5a8de53b2afc6a4d3e6fb780ee24 -> 4.9.12-x86_64-csi-driver-manila
      sha256:4b979717b90adb7f1758e00403495f09a24e53bc537dbd2eb69398b83e3d8819 -> 4.9.12-x86_64-kube-state-metrics
      sha256:4f4021f6a725ee1bc3c393535742b720ee2fc5ffc978849a2b67fc437debc283 -> 4.9.12-x86_64-prometheus-node-exporter
      sha256:50e0b3eced37efe7c94f746e0260d4750144962ef16ab334b93c08b3635f946c -> 4.9.12-x86_64-csi-snapshot-controller
      sha256:550304f5ac4929e68b97108da732b8271997dee09db91a2d4fb55d3b143ad7eb -> 4.9.12-x86_64-aws-machine-controllers
      sha256:5982ecdb04297b4c546da927abbbb9bdaf772737e794218c20b43a5d4ede9d22 -> 4.9.12-x86_64-k8s-prometheus-adapter
      sha256:5a947cf105f932656f89e0a31973fec637a756ea0a66bea80fb623c37a91e7e0 -> 4.9.12-x86_64-installer
      sha256:5c4039e2846f73e3703f63a2aa52a27a85d8c3c9c3a076e05e4aac33301a5715 -> 4.9.12-x86_64-cluster-autoscaler
      sha256:5e33f9d095952866b9743cc8268fb740cce6d93439f00ce333a2de1e5974837e -> 4.9.12-x86_64-kube-rbac-proxy
      sha256:63149966fd4008c90409477bd30490445d8f11dd484009fdcbd665237d1e85a9 -> 4.9.12-x86_64-multus-admission-controller
      sha256:644b470e3cc0e417c8766bdecaa4944ded16267a542fc93d477b364a6905fbe7 -> 4.9.12-x86_64-csi-driver-manila-operator
      sha256:65f83e849f9cb76bad217bceee4e34a30d062e17a2a1a80237462d26d98e8870 -> 4.9.12-x86_64-gcp-machine-controllers
      sha256:66996ef32742e67e768985c759f248b5af8b503403c9844e535f3a1ee4c24c07 -> 4.9.12-x86_64-openstack-cinder-csi-driver-operator
      sha256:66bf4dc3d0a3f0559c48d7dc7b9b45af7c90e5b6be704421362645d225c09cf8 -> 4.9.12-x86_64-azure-cloud-controller-manager
      sha256:67cb844c3e059ef41d746d6132ff1a3cf4240bf959191fbf31a680461b95397f -> 4.9.12-x86_64-vsphere-csi-driver-syncer
      sha256:6a78c524aab5bc95c671811b2c76d59a6c2d394c8f9ba3f2a92bc05a780c783a -> 4.9.12-x86_64-cluster-image-registry-operator
      sha256:6e2a44337dbe5bf89d6f0f16124df38bb86f71c866a9ae38badb450e58e7f42c -> 4.9.12-x86_64-operator-registry
      sha256:6e68ba361e31be5dd1b3ecd0fe5318b337b1915d5db2d8b635cb6f8bb8ee7f97 -> 4.9.12-x86_64-machine-api-operator
      sha256:71a76b1a54d9b9ccba384c353db5777f4a3b37fff2c7027225bb534d09565615 -> 4.9.12-x86_64-openshift-apiserver
      sha256:72a75a1eecdfe8e42703fcde0bb63c1f5f256dd21dba92bc36edf54e9f2a845a -> 4.9.12-x86_64-cluster-kube-scheduler-operator
      sha256:72d072f33452fc8c5fb0751ac3743688cc4d44959654bb0747dad000218f6f05 -> 4.9.12-x86_64-azure-machine-controllers
      sha256:79c8e38ac956a6be5ba8dfbefc10e30ad0b5d3229ea8951fd9745b57fa5c6a90 -> 4.9.12-x86_64-cluster-autoscaler-operator
      sha256:7b90fbeebf473c2e85acc23bd116c3d09750377f32a800ae09caeae101d34957 -> 4.9.12-x86_64-vsphere-csi-driver-operator
      sha256:7bc4f0c55d6e8d92cb429337759824f8535e56a172e75b8c82959a4f4f270ab4 -> 4.9.12-x86_64-azure-disk-csi-driver
      sha256:7d78a787b5655292d4440942ae018ad5d1c985881c4e9d95d887d4f8450c7899 -> 4.9.12-x86_64-csi-livenessprobe
      sha256:7f734c4f3a69377a667c97ef00642accf60f2f0020ab89531eea3d650f1a89ca -> 4.9.12-x86_64-ovirt-csi-driver
      sha256:810097f053d1859d516ab784d975d41ae435ee91f5eaa7c90a02e643620c18fb -> 4.9.12-x86_64-cluster-network-operator
      sha256:82158554ee3c838c24ac3d894d5c92a30f9e6705e6e17520115a5edd62a2bf75 -> 4.9.12-x86_64-ovn-kubernetes
      sha256:82bfc9ea845d7948c362061afa266d7e3dc1465cec041371e99cb1bcb5056036 -> 4.9.12-x86_64-deployer
      sha256:83bc064c1957b18d231d0befd2bf14bcbadb3ebe20ff3b21dc4b23f048aec547 -> 4.9.12-x86_64-gcp-pd-csi-driver-operator
      sha256:8bbd804da57f3f961ce8cf8057ac7a946166df966bad4bca3295eeb16eadde5b -> 4.9.12-x86_64-libvirt-machine-controllers
      sha256:8ce4be01db73a940acf798ea8102dc06f340f3afec470a3af72abfe130114de9 -> 4.9.12-x86_64-prometheus-operator
      sha256:94145128ec7805e478fada43a8a8ade8054fb50b56c97f159fe9da28a56851c7 -> 4.9.12-x86_64-telemeter
      sha256:948def25e5c80edcc357064d740d78b97bb8fa4f940f13d0b501714c7edf57a1 -> 4.9.12-x86_64-console
      sha256:94926d57aaefa23376615302a14a96d4bca45dd6c3184a8595042b0ae847eb1b -> 4.9.12-x86_64-kube-storage-version-migrator
      sha256:961b30e0ab62cf04d000c1eb8330e62ff2daf4545ee094d3c9acd4c443e3c5d2 -> 4.9.12-x86_64-docker-builder
      sha256:96487fbdd3efaf1ef9c8e34bd17470095daa3dd01bf9c5c42e5c67ee99b21bbc -> 4.9.12-x86_64-aws-ebs-csi-driver-operator
      sha256:9716b0f99225f7aa9680a8f07fc02c3a8233b010acd332d7c4651d5e0177de3f -> 4.9.12-x86_64-cluster-monitoring-operator
      sha256:980d27d0eb312864f4dacf8f125c7a2ec6d507817045517ebfb80ea25601e1f6 -> 4.9.12-x86_64-cluster-config-operator
      sha256:9df074e84d628156a71c314708d4cc003e020d3e4948926e30654b5eb0c2a958 -> 4.9.12-x86_64-tests
      sha256:9e2d384be6ebe5fc9c089ab3ed1e1f082c180aed520ee4ff306e46fa56c12728 -> 4.9.12-x86_64-baremetal-installer
      sha256:9f56d95ed665c1442fb4ce0b6415dbecf17729372527c46570aee5c372282a1e -> 4.9.12-x86_64-openstack-cinder-csi-driver
      sha256:a4b8af157bd962a43aa8d44707743a6d743b70faeb72548c5a91869a50ae6b5e -> 4.9.12-x86_64-thanos
      sha256:a4e51611597318e86a82389b481f49a0978d5deb403f705b14346161c0b62104 -> 4.9.12-x86_64-console-operator
      sha256:a6322f222f98adc1585ff8777c73140a56ac3cdcd8a6949309884b79496bcbb6 -> 4.9.12-x86_64-sdn
      sha256:a928705d99ccf81cfb7c0ef07a515f67a488d7052a2d7ba34ddabaa140295d78 -> 4.9.12-x86_64-grafana
      sha256:aaaec3417207330eaeb203f459bdd819c27bac1956f1d9f0684f42dc5a3807e6 -> 4.9.12-x86_64-cluster-update-keys
      sha256:aaf0854244f356ccb3f1cd70356362ac5ef7a9f227e08128740306430fd75497 -> 4.9.12-x86_64-coredns
      sha256:ad7e0cca53e098a448f9d5df9ad5410515ee1ed3c83dbbe8ab72fc8ae73b986c -> 4.9.12-x86_64-kube-proxy
      sha256:b98f4e019aa3cae1ac333a8241bafdbe52caccfcdcd7f640d1a7410dd33dd788 -> 4.9.12-x86_64-multus-whereabouts-ipam-cni
      sha256:bb0a5b9d4d3d5c774d58e1f2f11abd260ea1b47e79c64047f753d04927b5c202 -> 4.9.12-x86_64-ironic
      sha256:bb5f32d6a5294d211e7016a3aeefc1a02dd8ce3773747bcead61ab40f3a13366 -> 4.9.12-x86_64-operator-marketplace
      sha256:bc6efcc92aa2dde7d917eb2922e3eb19eeebcd4e8f19a499fc536fc2e45183ec -> 4.9.12-x86_64-cluster-dns-operator
      sha256:bc7227d80dc8f416eabc2bf1d326fa41ec9ab849b2a06291ef98d25a83f5ccb6 -> 4.9.12-x86_64-gcp-pd-csi-driver
      sha256:bc820dec82b685d589a6d71fea17baf28d7404d0351d7d91876476c4c2932c0c -> 4.9.12-x86_64-cluster-samples-operator
      sha256:bd81d21f0593cab25f321b000e8e18f5ff50fb741289261874fa332407b9da05 -> 4.9.12-x86_64-vsphere-csi-driver
      sha256:c017f8b24e9d9a913456373c69dec63e3315ddae052ac6ad9cee25f856abe502 -> 4.9.12-x86_64-docker-registry
      sha256:c10376143bb2fc51f72619cb7d45c8905d7c5a10b0150e1a1f65c54a90fff1bc -> 4.9.12-x86_64-csi-external-attacher
      sha256:c56c86d030185bda241514593970e80f75ae75afd9bc6288388944bc2a1dfb1f -> 4.9.12-x86_64-pod
      sha256:c5c4823cce587db60358f78f2a324a6def77930b0fa392a2939e04041b5a4a6b -> 4.9.12-x86_64-openstack-machine-controllers
      sha256:c6124ae70fd1f19ada3d5930ffc912dd5a3fb09dcffabc6425e9e327a61528fa -> 4.9.12-x86_64-prom-label-proxy
      sha256:ca8dc8a6e1a672565fb1db1bdf0bc49d1878bc9621b15b293334937d42a34972 -> 4.9.12-x86_64-openstack-cloud-controller-manager
      sha256:cc04ac8cc0ebd5ac0b7bd69e27a5d3958d311b61e3f78ed896acc2445d5e1ba7 -> 4.9.12-x86_64-azure-cloud-node-manager
      sha256:ccd64e8b56711ced7558d8e593ae32867cb2178ea73078f47d20bc7e2e56119b -> 4.9.12-x86_64-aws-pod-identity-webhook
      sha256:cd13cdf851def78178b6c1072910fcbceabdb121b888cf82377469fea75a0bf3 -> 4.9.12-x86_64-azure-disk-csi-driver-operator
      sha256:ce897bc72101dacc82aa593974fa0d8a421a43227b540fbcf1e303ffb1d3f1ea -> 4.9.12-x86_64-driver-toolkit
      sha256:cf33109e11798f970fa181f7ab08be92301b245dc2b7d17226f38b4146d585cd -> 4.9.12-x86_64-openshift-state-metrics
      sha256:cff70e2271d5bcc26426cf3f22f3439219677e6175fcaa00c2e5a5da9379d5d6 -> 4.9.12-x86_64-jenkins-agent-maven
      sha256:d622ce62d93f99c3f5e4ffdcafc19ee3c31f82630185b882c7bd4d332fc435cd -> 4.9.12-x86_64-multus-networkpolicy
      sha256:d74f9409782832026f98e261089bad47fa50be5282c2775dbe521f5fda0e0ee4 -> 4.9.12-x86_64-jenkins
      sha256:d8c857820a05e64b271622508038330f411f08d87cedd16a3ac13095ed920b4b -> 4.9.12-x86_64-installer-artifacts
      sha256:db28495dcf72f26368357ed71b745c3c7b5e2a40c787bc8a76440f16872a0dbc -> 4.9.12-x86_64-cluster-csi-snapshot-controller-operator
      sha256:df22255abc474a2b61b323925f76126fa2f8c99affb1c48c2a0eb16c4b4a1056 -> 4.9.12-x86_64-cluster-ingress-operator
      sha256:e03cefa188318d283eb39b799950a3540d0cfe14becf8995d391dfff5dd0369a -> 4.9.12-x86_64-network-tools
      sha256:e146605c1b75917d26c07268b361134aeda68983b2e2b060c202420b8267aa45 -> 4.9.12-x86_64-oauth-proxy
      sha256:e2998b64e69d5a903a76e5f47f1f9aba9b4f5db13f0dbe265c85121317c1cd33 -> 4.9.12-x86_64-configmap-reloader
      sha256:e5c16190ac98e446c7cf90cba8e14e996f81021ea4a38abd17b87c13b30bd6f8 -> 4.9.12-x86_64-oauth-apiserver
      sha256:e79fb8754d7c01a60c1763fe39104343783a6498bf10254b4206eae532c1679e -> 4.9.12-x86_64-cluster-openshift-controller-manager-operator
      sha256:e80f8718cc38cb025680205d890d4f622f975297bc437cf52ae1fbce0e89bf1c -> 4.9.12-x86_64-cluster-authentication-operator
      sha256:eae57963e5746607da7ce8070275abd301ab420ce767706d5b36972f64a0e450 -> 4.9.12-x86_64-must-gather
      sha256:edc8e8eafe3f73633f3ffe4a393e21f4495d518e9f044f127a3616315cdd78b4 -> 4.9.12-x86_64-openshift-controller-manager
      sha256:ee96cf6a6fc395bb5432ba2322c3fa740832ebe490211e53b401f35cbe615636 -> 4.9.12-x86_64-csi-snapshot-validation-webhook
      sha256:efbc54ce84c0b4dd7fe7b3e6fa2e6cd488b57d9c6fe7d055ef6395ade51b5906 -> 4.9.12-x86_64-cluster-openshift-apiserver-operator
      sha256:f0d490ad627c935e07384b942859a938a8445e66486450919537d5bac8f92f62 -> 4.9.12-x86_64-cluster-baremetal-operator
      sha256:f22b23274a3f220805ef2b83502d9af379b6841dfbd593d526f072acd6c780dd -> 4.9.12-x86_64-cluster-machine-approver
      sha256:f2a6bffb17480fb2caa5a52512efa348a09b960d23782d02ec8733df948dbff6 -> 4.9.12-x86_64-csi-driver-nfs
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
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:d3c48a7c60a44c138beb6caf24c081f4c24e5698b10d8024a18e9a912cbad7b1 9.185MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:e3b97e7499e547573709b41ebe392718bd8a5d9720e5bef46ced707e23054b8a 31.87MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:a6effd552debbb9b8f8d3ca0c9327c5c96f6e5f89b17c58ac5487e4f88ffae06 25.81MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:961d94212659ea340ae5a8de1a33c2b9b9739b01ae398818fe0d61bb9a149433 4.802MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:2ca1cf936896af116a210e77e6f498c38ca1c87a37fa7356edf217c823b5f996 27.21MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:804abaa69f500750187b402000f4334a90dfe7be4b71c56c1150516d75a9fb16 17.16MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:7effbb56ea6ac2de02e51cc725b5db0aee8ac7c9e731c590b77d471f5d6b05ca 29.56MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:181ff758e9d7e038c3778b2f6c287055af01a6dc9b5cd5f1895dfdf4dda0151e 152.6MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:bd856d4e2df814456905a7addcdb073c8e6b1168fed3771d51ab8aa7008c0b38 25.29MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:64b33fcb67935dc72e3655d43ed47521271c1769db6543d60393961a44db6ec4 14.15MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:88b11fc9fe314375a4fe59d4cd9a9193d08e3b7bb61782dfa4180680ebef1c46 20.09MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:1659aa99fadee664637ab731d9799a558c578b9662c57bf865f8773d3f84c295 91.57MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:bc859789b7d44f07fe0beb389effbf1bac3f9a6bc6f00bb5af3dabcaa5773089 124.4MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:3a6e00b8ea27fcd20d489ee3a3141e9c804510b6dc37851462bace5aef21b586 374.5KiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:3bc2b1a1d6ad14c37395b0ecff7597e086cd82aab0da308bf81166f00e96e066 86.48MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:c054ab6c0de2e5ae8a76236b3129ab8ca2b2c52164c46b6b78dc785c5586d9bc 32.52MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:d6c329dfb7dfd7b4b4b92a4e06403b8026893c472691b505b031859a801f0760 98.93MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:24e8d2e218b03efbd1c2548e6c8c7fc5a216edb5f2fe7e6e83a49fca53d1018a 14.83MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:15f3fa57e65d16ed976bea80fc7c8aed0e20f8af3069af85a36fee5cd0dbe8ae 29.78MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:f38f48ea5fe454322983465a54c5f20afe077092a11098e3fac3afbe88b2d54a 28.44MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:eb5e55c4ee159cd075c2a983e0d8f2eaec5a26937cb96198afab484d08514ce2 132.6MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:9e3d58e52d931408991ea9dc68a24d3bbf5588ac2aef3b394877f2c3f30f83fa 40.88MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:405fd37d00114f374ca6704f372a72fd55eaa56ee564d9db36ad637d3d8407ea 7.585MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:f43eacb86576cfc775abc5d058b804eb2040e3e6dd57d232dd9b0af2c9c64d31 10.91MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:c3de55fc203c511e333741faaee84b6517805d5c27c13932fecef228e51bbe66 103.8MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:86cb77f62affe2b91d2ddff91410d783bebc062ebb0700b2f58a35b13209d069 39.35MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:1034c6f35c4eeb97642b5077d2710b80764b2d97810e1b709e6455bb8e164219 28.26MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:d5cf8b84525c9e9f9882d20fee8279ac7bcb095b4a20bfcf7645568bacd4bdbd 29.59MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:a2720d25185cc14165349ec1e30fab1023a419ef28367131eee8f78e77f13711 18.92MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:ae7747797362fa33af97c562b481038bf06796fcad589f216d1e72dd24d4e3b0 28.25MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:d55b5908424f75afade301b39b04da9380352d16942f02bcdb48a226430a0fe4 25.39MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:c2032b437f3c7ddfd6772a1dd6b1c61c9e5112f036397c440857a67b6410f7c2 25.05MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:21cc75ba870c93c3f211ca8d7d4a4feae1299a85e851370a048da0769b00ba93 91.16MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:8c4766afd687f820e1022f4fcbda3bb80aafe25dd2dcd602950744cf658174fd 34.89MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:2c2701d400bba41b506ca073ed4c31f05ede23ff6c0b593be732cab74995617d 33.48MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:8f17910097341fabcd49222bb30a5dccc971a6f8213ee59f77e3f706fae500ea 29.56MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:c4d797b450eea478878adc15410a4499670377c6c6b940dfbf7168e9404b0bcb 90.54MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:76830d3e13e55784d4e5cfb1a4a11b78047d55816984e927fea0be4952c9cb53 11.45MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:f5591c88a126680a5459344f6f2e67713dc034cb96a58e6e0b11dd319f69c15e 25.62MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:dd921d73823c8ffe10faac26ac98a806000032fc0ca2b792dec1d00c9d9390d8 37.69MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:3fb5a8b38a1d2e950a57608106bedb9e0f43c84b03c5ae2bf979fbe6546934a0 43.34MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:f6cb5e01b9160f3baa5821a57b2a95e542e46a04a3e0fa426764bc20734686d6 45MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:54b906177a4d2b0618aedc9a81602136205bb4c9d7d9095b4837aefea18b795c 18.02MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:99febc066ce59eeee9a0af22e978211306d1d845e5e341fdb681fc2b9276ad15 9.534MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:71bcd49f86cd22699a4b211757cb6f80bbc0e818857724dd706368fe0b59ace6 21.6MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:c1211fac6ad2fc86066bfc3294ef23c957ca8e37953dc4c3c07fb112a153d6a8 30.25MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:fe5dc203b1d8cbc03e537a6927ba2bdd90ccabb2605396e93eb4bd63cf693ece 118.1MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:3b4ace74afa170ecebb0dc6116beae7b9476c72751a1d07d3dde17cd53f7bc4c 17.44MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:dea9e8cef0a45950376056981f29edc990600bb6ee823d7575f93880abdc6802 25.35MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:eeafc8c7f83959bb1f80b0bd51f4854602d5e5ec39cf0b4cc4bab77b78b93184 29.15MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:16eea94743037d8c322a890d0cd529d083b28ff4f266e3d237962dcbdda700f1 130MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:84bda9df689df65c1ec8cd37c8ce13c8639ad80da66116e17170d2d0f5ca8e8c 29.68MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:fa4e8e96f9912234c4251c398716789a2b4c507cc35bbe2c05faf2478548af51 86.39MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:4c1b6ddc54a209e07632e6a2dedbb4f25d4eb3b6f3597f410d7ea97c71256f5c 431.1MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:949342418efc491e9e1d3836dd5a8c4ea8bfe3804de9d13bf5a919a8b0d9b5da 38.72MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:e45b640651ec29810b1b6c80bc5fbe41ca627257a9b875bdddfe196ae21226d5 31.35MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:6a6e6defe8c5ac973230a8b0675825b08b1f4a0433ee0ab63fbc011ed96d9085 186.4MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:5788b7adfcea841704d4535e19a3993a6e69163cc15d74843b195965a90394ab 29.71MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:f2c0dd9442146750ad68a5c6dadc58f57a5c33ed3092e47ffdac96ba4c720fbb 60.97MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:3b810e182d6c6693732816a47781515d8ee9eb8a610252eb5578e2dde1c666bb 480.7MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:494ae1847e4a4b92194f4d671452abf443971e1e7042cf36d835f896a60476cd 29.63MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:7c7945ce90493a58f102c766b284a8d127ce1c92d5acd0834deda4830a2c69f6 28.83MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:fb9a7c404864ab3cd0d646b95c45595801023671074fa089b5ffa4e97da76047 254.4MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:97231ef0121ebd6dee8221a8a6bf6a35214931b90b84d060d468da406f8c8c0b 13.57MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:89219d8ed23382a9a7e509334773168d5239ba94ba65ce6ce0b11263ca3cddf9 399.5MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:75cf9943196266b8ca3decb117fc15d028188f8b06394bed9fff8a29b602c4eb 32.77MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:8bf8bc78d689926ba04c3686d18c5fc1677a8fe135e0a2ce77640d67faba6857 9.386MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:697b43e78cbbb6d6c21909ee564810d59ea9120ee704bf3dfe98c54ac44cf9ac 55.48MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:4c4b1b44eece5abd517ef2484e063965fe69a307cff5d71e206fbe2b267bcdf7 151MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:1455608b65152f6cb87418862117776ee5bc6433a5bf3f529391997fdabf976b 138.1MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:21d09198a26e9fbdce25ee36b2163783f6a131faf8841053fae55541ac569afa 9.22MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:d0dcc01a5b3643463e486c160e23fe07c87c400dbf0956cf1970bc2a78d66b9f 29.3MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:487bb488b5f1a0b6a1f10ae974c1f0a41cfa9e2e5c48e77ebc690e19aecdb5ec 13.21MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:98b97d2851af2ea0a6cce7907eaf336ca293d3f4b6fde828af15a3ac76e5d9f1 435.8MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:0d5a05ee7e100781f2cac5fbfae2169912b43426f21cc4fde6655540df3f91fb 17.87MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:ac56bdc7f9934acede05653e9e01e73e961c31818b522c0732ad35350bb3a82b 81.67MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:76834f57a119f4d370a120bc54ad376d2e846cbb1458b4fafd7981585717f895 19.99MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:a048f6705d3b7c5b982e961681217ca551688ecad9115486e6d97f8629269b3f 6.042MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:9b405cb9eaf61b7c6f0edd309502994c027362a937b4cdfc026bb7221cdbebc6 44.97MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:6df6462667e06155de3c18d25b7ece40cba6e033d7669182f1d768141d24489a 21.11MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:1510e1321086e5f3b5b1cbc590cfaf8fbf280c3419327baf3a13e37efeceb1f4 11.85MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:eac1b95df832dc9f172fd1f07e7cb50c1929b118a4249ddd02c6318a677b506a 79.44MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:802b0aadb7eda0706c515d197469be532f5f01df44b468c7892a7f1e5e9ea0a3 112.4MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:4879a344e5603fe8e4431825a448491bb7b7e4a7f39d09fdb15ecbc813e7183a 29.84MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:5598bb0e774186f39433724abfbf054789ec61b1c87cab5cecbc84b2580be0d4 29.61MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:a2d54da2eff27f0ca47a9f1974616e07c6439965bb1463c1ed1349aa80b0b537 275.1MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:c95fd2c6c33924ad9356c54d5e76d751d42e513f7e5a55c060beb881485d9367 45.86MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:d00febf470ea1ec9ea912dc293a7eb8a9db93e5144e901d39002eec946f3799e 38.55MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:b230f550b80daed3062f18bfc5d3096d950e92cbbbbd4400857f911fb67a8694 422.2MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:c67baba1e9cbd1d8eeb0384784480534fe1ba2b96eca6d3d28ebbe18e50a0e56 21.32MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:b69a79a83f8d53971681286e098a77439424457a3ef4a37c68d03df086e4e38e 108.3MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:e45eeb718043804cd10f0049223e076844484edbf045db7ffb37c9ac523a9943 68.26MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:cac26e82c2fc50c1a4ef9b7d94bd1c9a5f87a5aefcb8416dda0f7dd8acd3a956 93.69MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:0e39912dd5610986643adc8ca48fd4e1a397d6b9150ba311df28f5be0ed520fb 18.63MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:c331a0acfc68a1a55bfb70ba03240ad4027bcc34a50d76e703a34bdc9ead5448 1.029GiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:8040e99619ff2ad3b90dd7097213255f6460acd582a9d65f32a5388f23ba5189 2.716MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:2247ef326cd6c317992818df6edeb9bcd4dc938e8b587885dcb35e40e222fcb2 24.78MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:4f57d0b5804e7850b608b24b21d0a8ec6fd6a78e7db02b5eb3a3974f7f4760b9 129.6MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:e0689321defa2640dae332b26eacfab33fbc58e049c55cf019fd292ddad7f667 21.33MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:468832300b755cc7ca9fa58d8f60af9b189c8dffb4f0b4e6609dbb7a94fe0ebf 8.893MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:9347df20b6ae99c198e8a8bcb3bb2927237c75f8a21d4ab6279011152b8607d2 41.51MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:74ce203b8fc1cd42ba1ffa3d96596701be3e274745562bdcc5552f10abeb3889 50.28MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:52b6d912784a850bbeef9b296cf2ecd3e973cd930ae7576c2ab01af91e44085d 39.11MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:6810d0b32afa026e527f754ab376a5a6f8b6cf6ee34233e2dbdb4ddd71a62f6c 13.44MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:10e9ae65b69ca820edc5a7f7b0f45c692ff247cc78c7a7890c5033abc048f629 17.29MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:af9e7f82057be11519f70d1c772eba2b53f065e6e3074693b7ae7636870276b3 19.7MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:e4c859ae5a10f95d281d436cef542d499f725808a06a36581af67cd53aadca8c 120.7MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:d723cbc52baae92894f0cf7e151303129c074b04a37210f4e85edd8c29538afe 24.65MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:47fcbd58764cbacb47957b17b6eda0407b23aba7fa416fae8b2598c4e98be2ba 25.98MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:c2b12b53ad82ad320f26ad52dcfa836d5de9e7aaef2e0d4ceeb46005d54298e4 29.64MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:0efdf0a242aef64b44ac17b5dab9ac19f6dd66eed0e27622bcda6666836ed32c 9.476MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:e82e7d95c2f4ed17c1646f52f41fabebfc29a9eaefda14e1c9a1e3550c393ed0 18.18MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:ccfc9d78a4fdf29533d57e550fb6a4453c74f26e6aa2ba0e7d046f2b01cdd2f2 18.1MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:5c344f65db64fcbb2aed622e596acb8866a03890b41aceabc2bd1b2795a5a2cf 19.89MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:e279974983f64e83d636f72df3fb32ef3baeeba96e09357581426c332ce2446f 38.36MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:ff3c954207c8bbb1ccb4960c24debfc4d86b57cf88dfe1bf04f6e57fd01b9e05 105.2MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:d18ddc13857eb2c8629afa84910a91e31925070b2873a886b621acd0305a3a5c 41.15MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:fd233661273c64fee89e4fd343231748352d96ba03197320fb173e0416265dec 14.86MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:d5f9207186fb5e4cc1f05052086b3627afc4bdc9de13dd10990456fb5a85ff2f 21.1MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:b3f9c609e51e53e8c8ff67e48c5dea2e91a07fd95ff2a28ec0f074c5c24eadf3 49.21MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:2506b5459443372cb9e84376dc89aca56281bbf94f788e26ae717e5d8838dd3d 24.7MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:e63a491468ee6dfb49dac24cd7d4ee23f79c42c46a1f227ab3ca12008b879f05 3.44MiB
uploading: provisioning.schmaustech.com:5000/openshift/release sha256:70f8470ac6953d5a8fab1c1269c8bcf159e37cca2d3969ec71473adfe4b568d4 14.61MiB
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
sha256:bc7227d80dc8f416eabc2bf1d326fa41ec9ab849b2a06291ef98d25a83f5ccb6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-gcp-pd-csi-driver
sha256:f5628b30aa047fe32cba9308c70c581f7d9812f40a3e651a84f0532af184bfd2 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-machine-os-content
sha256:07c709f51f7383720c2acca830b3441c188ab0f077616d075a5a6bd9cbd1a2e4 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-storage-operator
sha256:7d78a787b5655292d4440942ae018ad5d1c985881c4e9d95d887d4f8450c7899 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-livenessprobe
sha256:d622ce62d93f99c3f5e4ffdcafc19ee3c31f82630185b882c7bd4d332fc435cd provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-multus-networkpolicy
sha256:0310a7b9ec788788a2a6324475add611b5c5304511fe27e7444500fdefbc6ef0 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic-static-ip-manager
sha256:72d072f33452fc8c5fb0751ac3743688cc4d44959654bb0747dad000218f6f05 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-azure-machine-controllers
sha256:eae57963e5746607da7ce8070275abd301ab420ce767706d5b36972f64a0e450 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-must-gather
sha256:1bbe2e9e053350f5af04d80e9a088fb6cc5ca7898b8c1dfdeb5269a4befffa60 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-node-tuning-operator
sha256:0305702b3b7208c339c5df376aaacf6992465e34490bc16c2d66e423039b2b06 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-insights-operator
sha256:1141f74833abe8de6384022fb7a800171640a40464a82669421517f5f7d2a128 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-external-snapshotter
sha256:67cb844c3e059ef41d746d6132ff1a3cf4240bf959191fbf31a680461b95397f provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-vsphere-csi-driver-syncer
sha256:cd13cdf851def78178b6c1072910fcbceabdb121b888cf82377469fea75a0bf3 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-azure-disk-csi-driver-operator
sha256:efbc54ce84c0b4dd7fe7b3e6fa2e6cd488b57d9c6fe7d055ef6395ade51b5906 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-openshift-apiserver-operator
sha256:c10376143bb2fc51f72619cb7d45c8905d7c5a10b0150e1a1f65c54a90fff1bc provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-external-attacher
sha256:5e33f9d095952866b9743cc8268fb740cce6d93439f00ce333a2de1e5974837e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kube-rbac-proxy
sha256:df22255abc474a2b61b323925f76126fa2f8c99affb1c48c2a0eb16c4b4a1056 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-ingress-operator
sha256:0e0a6328ea7449e2b3ecaa057f59f98cb49e2c71b60ea0d9a4f3d358ef472f36 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic-inspector
sha256:2fc2c9d9fae3070c00239ea3d0d1d9fa7477c99c296b17b2fc352794c535912e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-network-metrics-daemon
sha256:e2998b64e69d5a903a76e5f47f1f9aba9b4f5db13f0dbe265c85121317c1cd33 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-configmap-reloader
sha256:d74f9409782832026f98e261089bad47fa50be5282c2775dbe521f5fda0e0ee4 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-jenkins
sha256:7f734c4f3a69377a667c97ef00642accf60f2f0020ab89531eea3d650f1a89ca provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ovirt-csi-driver
sha256:6a78c524aab5bc95c671811b2c76d59a6c2d394c8f9ba3f2a92bc05a780c783a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-image-registry-operator
sha256:cf33109e11798f970fa181f7ab08be92301b245dc2b7d17226f38b4146d585cd provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openshift-state-metrics
sha256:0213887c325d3967d122fe875b45c9259e2b8388db9dc4e0a25c0561414b8737 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-aws-ebs-csi-driver
sha256:f60434187e593c079d98e94b6ba69bce34e5daa5ac161e834827d594b070592b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-hyperkube
sha256:a6322f222f98adc1585ff8777c73140a56ac3cdcd8a6949309884b79496bcbb6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-sdn
sha256:3730bafc9c8a397887d4213b220afffe11cb6835ea5754e60a0cfe967efbdaf5 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prometheus
sha256:c017f8b24e9d9a913456373c69dec63e3315ddae052ac6ad9cee25f856abe502 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-docker-registry
sha256:051c76b923d9826bd577aeb1e176a4096a9af47e9a7c1819158282a5a417170b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-machine-config-operator
sha256:0503429fe988d46d901cd8c625c5ca3e27d0eddd151cd6ee2af346c6397fbffe provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-baremetal-machine-controllers
sha256:94145128ec7805e478fada43a8a8ade8054fb50b56c97f159fe9da28a56851c7 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-telemeter
sha256:c5c4823cce587db60358f78f2a324a6def77930b0fa392a2939e04041b5a4a6b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openstack-machine-controllers
sha256:119ce8d95beaef7fcc420fb1acc6d311a1a6309a371a1ccc427b64a7f2d1e371 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-external-provisioner
sha256:ccd64e8b56711ced7558d8e593ae32867cb2178ea73078f47d20bc7e2e56119b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-aws-pod-identity-webhook
sha256:0b8a09ab3c370f7ef89319f3ff66cc346bbfc1cc48b58c2d40ef7d61b33a349c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-multus-route-override-cni
sha256:f7ce7247375fa57b2be95733fb26a85751a5f41b28652e96612bea2ee6a25442 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-cloud-controller-manager-operator
sha256:9716b0f99225f7aa9680a8f07fc02c3a8233b010acd332d7c4651d5e0177de3f provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-monitoring-operator
sha256:fe552892df8d59716997e8149a384ef65cade7610de3264652eb77e2d93575ed provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-kube-apiserver-operator
sha256:82bfc9ea845d7948c362061afa266d7e3dc1465cec041371e99cb1bcb5056036 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-deployer
sha256:f83965a48127fcd265ef40ce6037df806980a088f8c83798be5b91e5cc09a706 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic-machine-os-downloader
sha256:66996ef32742e67e768985c759f248b5af8b503403c9844e535f3a1ee4c24c07 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openstack-cinder-csi-driver-operator
sha256:235f6b6a1c9c41dbf3b8867bdb25da59c5e3a15af93b61fb13b4b5f41814d954 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-policy-controller
sha256:06ca4e45c6ec3aba104a066905322e35b44c8a2c293d9f2821c83ed2cb743633 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prometheus-config-reloader
sha256:d8c857820a05e64b271622508038330f411f08d87cedd16a3ac13095ed920b4b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-installer-artifacts
sha256:183b7a1d2b4d3600a7251eda82f90ff45d4b5324b3bfdba6a091aa735fcff4be provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-version-operator
sha256:2ff95508332a75a0b0b50be84416297781946d0feb687df36c8082baff564857 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prometheus-alertmanager
sha256:5982ecdb04297b4c546da927abbbb9bdaf772737e794218c20b43a5d4ede9d22 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-k8s-prometheus-adapter
sha256:4f4021f6a725ee1bc3c393535742b720ee2fc5ffc978849a2b67fc437debc283 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prometheus-node-exporter
sha256:e146605c1b75917d26c07268b361134aeda68983b2e2b060c202420b8267aa45 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-oauth-proxy
sha256:8bbd804da57f3f961ce8cf8057ac7a946166df966bad4bca3295eeb16eadde5b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-libvirt-machine-controllers
sha256:71a76b1a54d9b9ccba384c353db5777f4a3b37fff2c7027225bb534d09565615 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openshift-apiserver
sha256:6e68ba361e31be5dd1b3ecd0fe5318b337b1915d5db2d8b635cb6f8bb8ee7f97 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-machine-api-operator
sha256:bb0a5b9d4d3d5c774d58e1f2f11abd260ea1b47e79c64047f753d04927b5c202 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic
sha256:ad7e0cca53e098a448f9d5df9ad5410515ee1ed3c83dbbe8ab72fc8ae73b986c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kube-proxy
sha256:13a775771eb0fbb24216f5cbaeab394426671bf5d7a5aa23268607e51b59ecbd provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-jenkins-agent-base
sha256:292d0fbd28892d4952e0cd914cb4b1eca7b5b31dad3ff26abbbfe3ee57ea24e6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cli-artifacts
sha256:72a75a1eecdfe8e42703fcde0bb63c1f5f256dd21dba92bc36edf54e9f2a845a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-kube-scheduler-operator
sha256:82158554ee3c838c24ac3d894d5c92a30f9e6705e6e17520115a5edd62a2bf75 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ovn-kubernetes
sha256:4446e75f22adf0e091bb337da46174ba7e82b394da5b04a2dc9c60b749c7cd02 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-vsphere-problem-detector
sha256:7bc4f0c55d6e8d92cb429337759824f8535e56a172e75b8c82959a4f4f270ab4 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-azure-disk-csi-driver
sha256:961b30e0ab62cf04d000c1eb8330e62ff2daf4545ee094d3c9acd4c443e3c5d2 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-docker-builder
sha256:42eb156593fbc94fa12eaa5938c8335d500e222be370475ee19a57ad7f7f42a8 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-mdns-publisher
sha256:4b979717b90adb7f1758e00403495f09a24e53bc537dbd2eb69398b83e3d8819 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kube-state-metrics
sha256:50e0b3eced37efe7c94f746e0260d4750144962ef16ab334b93c08b3635f946c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-snapshot-controller
sha256:1938512df87a22d0a3c4f9d7aafa9557d7268f3511b52f9494946bb7478bc2da provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ovirt-machine-controllers
sha256:810097f053d1859d516ab784d975d41ae435ee91f5eaa7c90a02e643620c18fb provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-network-operator
sha256:00bfbe7800a9c803d13e666122a7b1f52bea6e53b0fb2352d8566d1e75759708 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-keepalived-ipfailover
sha256:ca8dc8a6e1a672565fb1db1bdf0bc49d1878bc9621b15b293334937d42a34972 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openstack-cloud-controller-manager
sha256:65f83e849f9cb76bad217bceee4e34a30d062e17a2a1a80237462d26d98e8870 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-gcp-machine-controllers
sha256:980d27d0eb312864f4dacf8f125c7a2ec6d507817045517ebfb80ea25601e1f6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-config-operator
sha256:33001ffab5cba9aaff19e475e0118ea158c99797bf6f3e210832f129ac34d71c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-kube-storage-version-migrator-operator
sha256:c56c86d030185bda241514593970e80f75ae75afd9bc6288388944bc2a1dfb1f provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-pod
sha256:948def25e5c80edcc357064d740d78b97bb8fa4f940f13d0b501714c7edf57a1 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-console
sha256:f0d490ad627c935e07384b942859a938a8445e66486450919537d5bac8f92f62 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-baremetal-operator
sha256:ce897bc72101dacc82aa593974fa0d8a421a43227b540fbcf1e303ffb1d3f1ea provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-driver-toolkit
sha256:279a3393fbb0c344b410de5aed4702ae41c49382a5c271c5a6d915defa72c47a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-jenkins-agent-nodejs
sha256:66bf4dc3d0a3f0559c48d7dc7b9b45af7c90e5b6be704421362645d225c09cf8 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-azure-cloud-controller-manager
sha256:19ac6ad2cac43fd4134dfed13168ac0761a014ec55d277eb4d1259caac757b04 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cloud-credential-operator
sha256:e79fb8754d7c01a60c1763fe39104343783a6498bf10254b4206eae532c1679e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-openshift-controller-manager-operator
sha256:94926d57aaefa23376615302a14a96d4bca45dd6c3184a8595042b0ae847eb1b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kube-storage-version-migrator
sha256:ee96cf6a6fc395bb5432ba2322c3fa740832ebe490211e53b401f35cbe615636 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-snapshot-validation-webhook
sha256:b98f4e019aa3cae1ac333a8241bafdbe52caccfcdcd7f640d1a7410dd33dd788 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-multus-whereabouts-ipam-cni
sha256:1267dd9b35b81041888212be03415b2fab37d1ac9e0fb4d9ddcf60c72f7a99ad provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-container-networking-plugins
sha256:fd7ce3da297b589c7b3c34f6dc820f4d71a51ec367424749e76fbfad06298456 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-baremetal-runtimecfg
sha256:03cbd8048e00835d29ced43ddb4548e979e51dda727239cfdf027c9ef47339cf provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-egress-router-cni
sha256:22e31d53a7c6a92176d5a183fd213fff4e2e68c343ccf6cca9c7fc1363e34836 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-multus-cni
sha256:f22b23274a3f220805ef2b83502d9af379b6841dfbd593d526f072acd6c780dd provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-machine-approver
sha256:83bc064c1957b18d231d0befd2bf14bcbadb3ebe20ff3b21dc4b23f048aec547 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-gcp-pd-csi-driver-operator
sha256:18740b88ca23a49a1cbbf3be74bf96ff536e961bb11235d4539cffccda7101ed provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kuryr-cni
sha256:214b7e2b9bece6260311a2dc28bfc12c379e37186c09582221ec1fe724a995d1 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kuryr-controller
sha256:cc04ac8cc0ebd5ac0b7bd69e27a5d3958d311b61e3f78ed896acc2445d5e1ba7 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-azure-cloud-node-manager
sha256:644b470e3cc0e417c8766bdecaa4944ded16267a542fc93d477b364a6905fbe7 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-driver-manila-operator
sha256:c6124ae70fd1f19ada3d5930ffc912dd5a3fb09dcffabc6425e9e327a61528fa provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prom-label-proxy
sha256:4b14ffcea52f9eb5952546634cb26bfb1d523a4dd81382021c71673fed91efa2 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-operator-lifecycle-manager
sha256:79c8e38ac956a6be5ba8dfbefc10e30ad0b5d3229ea8951fd9745b57fa5c6a90 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-autoscaler-operator
sha256:63149966fd4008c90409477bd30490445d8f11dd484009fdcbd665237d1e85a9 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-multus-admission-controller
sha256:13bb02aabde612c09fe136b0ca02487cb5f48b9ddf871afbe0d4f2b4b1b66b3a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-external-resizer
sha256:37e092de518988e67fb60bc5eaaa365058b6be6a1877613af5d12e2c4a2ec129 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-oauth-server
sha256:9df074e84d628156a71c314708d4cc003e020d3e4948926e30654b5eb0c2a958 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-tests
sha256:a4e51611597318e86a82389b481f49a0978d5deb403f705b14346161c0b62104 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-console-operator
sha256:bb5f32d6a5294d211e7016a3aeefc1a02dd8ce3773747bcead61ab40f3a13366 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-operator-marketplace
sha256:40302e29af4c6e3e9a6e73544a2fd4239da691b0afde937e76d078a642d30817 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-service-ca-operator
sha256:038452cc237d09fbbea348a454007760fb2d04e1e2439e931c505665c2c98071 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-haproxy-router
sha256:96487fbdd3efaf1ef9c8e34bd17470095daa3dd01bf9c5c42e5c67ee99b21bbc provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-aws-ebs-csi-driver-operator
sha256:bd81d21f0593cab25f321b000e8e18f5ff50fb741289261874fa332407b9da05 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-vsphere-csi-driver
sha256:edc8e8eafe3f73633f3ffe4a393e21f4495d518e9f044f127a3616315cdd78b4 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openshift-controller-manager
sha256:23921ee4c51c3fc5b32598d89233353855b335cb1d3592bd4e14cda9b23aa0e3 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-bootstrap
sha256:251f6fcf7b079421f5ed480db37715ce1fc35e4bbbf859b3ffc449b84d8fd246 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-baremetal-operator
sha256:bc820dec82b685d589a6d71fea17baf28d7404d0351d7d91876476c4c2932c0c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-samples-operator
sha256:bc6efcc92aa2dde7d917eb2922e3eb19eeebcd4e8f19a499fc536fc2e45183ec provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-dns-operator
sha256:5a947cf105f932656f89e0a31973fec637a756ea0a66bea80fb623c37a91e7e0 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-installer
sha256:550304f5ac4929e68b97108da732b8271997dee09db91a2d4fb55d3b143ad7eb provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-aws-machine-controllers
sha256:a4b8af157bd962a43aa8d44707743a6d743b70faeb72548c5a91869a50ae6b5e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-thanos
sha256:e80f8718cc38cb025680205d890d4f622f975297bc437cf52ae1fbce0e89bf1c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-authentication-operator
sha256:3e4dd326434d115808c7bff14d8a38806c6757d928f2359737496fe6a32b7b4e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-kube-controller-manager-operator
sha256:cff70e2271d5bcc26426cf3f22f3439219677e6175fcaa00c2e5a5da9379d5d6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-jenkins-agent-maven
sha256:9e2d384be6ebe5fc9c089ab3ed1e1f082c180aed520ee4ff306e46fa56c12728 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-baremetal-installer
sha256:aaaec3417207330eaeb203f459bdd819c27bac1956f1d9f0684f42dc5a3807e6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-update-keys
sha256:23244e61eaee6a986e1ac26def05a6eeefa3150b9e740787d8ec0be765b6c8ec provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-tools
sha256:8ce4be01db73a940acf798ea8102dc06f340f3afec470a3af72abfe130114de9 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prometheus-operator
sha256:7b90fbeebf473c2e85acc23bd116c3d09750377f32a800ae09caeae101d34957 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-vsphere-csi-driver-operator
sha256:2ddae06a7ca8031611b8ec5a63050195a5b747c66289b8ad810bc8da73aa58d4 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic-hardware-inventory-recorder
sha256:46d7b63794f6ceec9fbcc63bbf170aff5ee3152ccb24ba51b8167ac59a30ca6a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-etcd-operator
sha256:0d741e590eee37fd9708609dde99c68f0b8fba9d0d5cee172227037b7446e3c7 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic-ipa-downloader
sha256:406384d7815dc29c85c903318b1a98a2f3bd65b440a9344b71d6ddfa3e98c287 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ovirt-csi-driver-operator
sha256:4b56346c5c3c09eee6d8d37b6796f1e4f15e5a8de53b2afc6a4d3e6fb780ee24 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-driver-manila
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

We can see from the output that the images for 4.9.12 were mirrored and oc mirror created a output directory where we can find our imageContentSourcePolicy(ICSP) file:

~~~bash
$ ls -l oc-mirror-workspace/results-1660659095
total 36
drwxrwxr-x. 2 bschmaus bschmaus     6 Aug 16 08:55 charts
-rwxrwxr-x. 1 bschmaus bschmaus   401 Aug 16 09:11 imageContentSourcePolicy.yaml
-rw-rw-r--. 1 bschmaus bschmaus 29131 Aug 16 09:11 mapping.txt
drwxrwxr-x. 2 bschmaus bschmaus    52 Aug 16 08:55 release-signatures
~~~

And here is what the contents of the imageContentSourcePolicy.yaml file look like:

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

Now lets show another mirroring example for an operator.  In this case I just randomly picked elasticsearch as my operator.  Let us go ahead and create the imageset configuration file for the operator:

~~~bash
$ cat << EOF > ~/elasticsearch-operator-imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  local:
    path: metadata
mirror:
  operators:
    - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.10
      packages:
        - name: elasticsearch-operator
EOF
~~~

With the operator imageset file created we can now mirror the operator.   Normally one would probably want to have both there OpenShift release and operator in the same imageset but I wanted to break them out here for demonstration purposes.

~~~bash
$ oc mirror --config=elasticsearch-operator-imageset-configuration.yaml docker://provisioning.schmaustech.com:5000 --dest-skip-tls
Checking push permissions for provisioning.schmaustech.com:5000
Creating directory: oc-mirror-workspace/src/publish
Creating directory: oc-mirror-workspace/src/v2
Creating directory: oc-mirror-workspace/src/charts
Creating directory: oc-mirror-workspace/src/release-signatures
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
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:9274ef1af8feb639c6fa2d89bc8ce81a458a99e766fd57c6fcf0ae5bde3d5ad5 4.867KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:1b22c87ebb3558c962415912f6a745433084150636cf700ec173be6e8a3003d3 4.869KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:204b27cdab080a66b6d43a093b6995b63773f913b094612d1f5f158a0181b79c 4.871KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:3394126ca288b1385a582e679aa7158346a211160c085336c97a367ec72412ec 4.871KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:ca630c079b88d57837ef1b41da36f946ab66a1ddf068df885a5b13aa3e5cec79 4.871KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:d7b63476773adac3b2c7036bbfa90e53c1b44b5750b5600cbff20ede2cde607a 4.871KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:5aeabeb8bf8e5d841345eef75d49d7d5e34d622da7d994dc2dea25fb8dda138a 4.873KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:0a45fdb65c417c2573e937e66c17e93ccad7fe118da646a5b97ed8c89c5e0527 4.882KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:7aae894edcb1ca7497e742194fee008dbb82237546c917c59797ab5fa67e003d 4.884KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:802d1e432f0adb8df75394aad6960fea717c1c48dd6a405f8dae02aa5ec15a5f 4.884KiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:7c9d5c29c097ad4d27db3724ef111af9c58078fecb740c18ea9970fabd01d34b 15.84MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:d79db737310b836ed8074695d801a5ff6a82c6cba60b302fdfea50a883921bf1 17MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:b4173d05a32d6fb3368cf44b57886adcde807ba7b471e6ed2cbe7f5687c13435 17.46MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:b02c54fa38a684229c0ade3eb5eea8fa4a30d67912a52cd043dcf55384e1b5c8 18.26MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:6997a911916b353da05a0b7a1351170bb927c13f6f0dc3d080d7e149f2e3a3a0 18.47MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:1df48d2eae98da38c1789e39d4766c1cae09c9c38107d9c89949c12f3efa290e 19.09MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:0f713f2e5c06198b19c74201ea100622e5e12e69895b3c0dba3e283bfbb8e87e 19.77MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:1f1fa47b3cc066feefb22630ab9b3ef776ca8b1d1736f71c0767259f54713928 19.91MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:594dca45124d8d7bbc1b46fb96940ff5f60d20882c3ea2593fa5e68e0d417e9c 20.34MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:c1d4d222568b0b98997645cda3c807a4ebb28b42ef841afc4f7c3c43553c25c5 20.49MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:744db8e918c5c882f601488dcdbe3c4e9fa01913fe57119975eb5b16a18f6250 71.97MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:e0444ce29df7b754434fd4557ec395c5a1759e4af71c3615a81471e0c7edeadc 72.84MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:0c673eb68f88b60abc0cba5ef8ddb9c256eaf627bfd49eb7e09a2369bb2e5db0 74.73MiB
      registry.redhat.io/openshift-logging/elasticsearch-rhel8-operator sha256:7472fa517fd188919c3ff6ec27806dd20311bb57e1e34a7f3998734688fa7b42 81.13MiB
    manifests:
      sha256:04c57db2d67b32fa53f021ea003704226517e493b6cc655aba02aab0d23323a1
      sha256:0ca3b10eeed3148fdde0cc79e379e6cf5b66fb8df18ea810cf58838caaf92f70
      sha256:0d0bd7b54472b47a65480f5b5832b773c5c79161eedbc15cbf80a8615f7d7e47
      sha256:0fd6cac32b8d10b0eaeecdd38c50df577067bd9ef27f20a8d03dba053e3aab04
      sha256:150f56a69ff535982d1af00896a1b0f951cbb75666f9d139811a79b29e86b04b
      sha256:19580a0c859f4970c09131b11f1717a417bf0fce987b0d0d0ec14aa0ae41123a
      sha256:2305040b28a6443a794956b3d854abe7e05756228055967f24689f191ef7596f
      sha256:32072064d66d79c1464a4f36bdba832e7ece25ed0c011b6e4a17d132408767aa
      sha256:42f31b5843a05f5813fbd277f6304fe9e3f170fe516b35a0b1f93a0be8d936e1
      sha256:594b6b4c6224276ee07c5b1953ba71bc8ba1eb0536d244d6cd64c0c0e971fa31
      sha256:5bf6c8da6f400c6ca0ed3a0294f614f45bdc2477759708723d77d15cfab52e34
      sha256:a834960d45d27966bb4f866b87014c02b80c463cf0298fecb2d943b51200d2dd
      sha256:c0fb2fe7c385dcac5e5e5cd587ab5ef0b27c6cb821a62a0927a80bc2e9b7c96b
      sha256:04c57db2d67b32fa53f021ea003704226517e493b6cc655aba02aab0d23323a1 -> 269efb57
      sha256:0fd6cac32b8d10b0eaeecdd38c50df577067bd9ef27f20a8d03dba053e3aab04 -> 2a020a6
      sha256:32072064d66d79c1464a4f36bdba832e7ece25ed0c011b6e4a17d132408767aa -> 3b9c8a03
  openshift-logging/elasticsearch6-rhel8
    blobs:
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:028bdc977650c08fcf7a2bb4a7abefaead71ff8a84a55ed5940b6dbc7e466045 1.738KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:9f6eca53e5bceb7b88b4c078cc5d939e61d13301d8bdccb6b68eeea4d3879f3e 1.75KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:18ea5ce2a404500d5dfadf661ec0566faf7380e56b05ed37735b93844ed5faaa 1.753KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:d851aca55b090483dcda2af05dbf048848399647d96dd3d4089f92d35f827db7 1.753KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:91b345fa63275be3f0923e4e44b1484d80be07b29e8615a6f1c8d98750ff805a 5.184KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:680d586ff9861f86dbf95612f95ca7917cfb1a35184c00e155b22b3a011b31c9 5.186KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:ed0905b935a31bbbe0028e7067491e244d20584a748699cfa613511c16d676d0 5.196KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:76b5d0886390deba920aef23f5019cf0f27806e8bdc03da5cd35dfdf8cc584ca 5.213KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:5ff1da96e70e895fea3989b2eea08d5cf655e7dd9ae17a50353771e43d26d75a 5.215KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:29b451605f52172a7fbfd56d3e8697abbc7523c040dcc18badcdaf5dabdf7d06 5.217KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:3e2b4a9faae355a5d6427aa2f33d0bae4154ff66201d126044e44489825db5e5 5.217KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:c1aabd6fea279b010a8cb1584b67837ddac44f9763b55f3e52a108586236961d 5.219KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:08cec5c62a42972af51188c10f9e55b81b7afb4a28c337a2deabc0793d8fb396 5.229KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:1cc17be930536c6f822c4791961d7313ad8a93b9b03c9bbf6e2b858b969d03ba 5.229KiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:744db8e918c5c882f601488dcdbe3c4e9fa01913fe57119975eb5b16a18f6250 71.97MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:e0444ce29df7b754434fd4557ec395c5a1759e4af71c3615a81471e0c7edeadc 72.84MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:0c673eb68f88b60abc0cba5ef8ddb9c256eaf627bfd49eb7e09a2369bb2e5db0 74.73MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:7472fa517fd188919c3ff6ec27806dd20311bb57e1e34a7f3998734688fa7b42 81.13MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:0426bc76cfcec7ac4a57b924ff9f4f16c778cac6f901740935c8d92292be394b 127.2MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:4d756d1efa8d4216c9087a9e8d7e2dfd5866a825c7d88f6cbedc9ef75cdee4a0 127.2MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:00beaece7ea33c31ff371482a939906501d2eba4bd5e59d49efb12e6421499f0 129.4MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:a0babb4e67c401ee5c81c2f6d19d835e2e445bb12f47c1c035a99f14dd301392 130.5MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:42f1bdba73509157bc04b7a2a873c7f34ed0684871c2cba80bfda9ddb2c1bb88 130.5MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:ef2eedd126d250887935c4eef88d8e0d12f7bcdc2f4a21784111eab0065e6a31 132.7MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:143b441c3f6dc0876008ff7487f29da9833302594a12b128fc995ba914b2a5eb 133.9MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:3f293dc69570341094547d1e5841c52e3e5f672807d667a3d6d955b3bc04465b 134.8MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:1986cf014b8e11aa06d3d8f65bebefd005377d08a13061c25ec32c2f2d97901c 134.8MiB
      registry.redhat.io/openshift-logging/elasticsearch6-rhel8 sha256:479a285eea48eaef0ff2f0ff86e43deedeccf984de9edcfcd24469e41a706642 137MiB
    manifests:
      sha256:3a5c08cba4458bfa6eadb89b1021b4c1d079d4dffcf8696e6805aac998134927
      sha256:4fad94662f25baf49926835945125fd98146ab22c1bb6137e4ed3941f1ca9216
      sha256:6654f0fb5a4cc17a7b202c8348ee1a73ec95b2176d07562e4219554ebe9f174f
      sha256:79ed4a8e586776a7d6f0c2d1a18e5d3323504d0dc7a5878cdf032957babe5a5a
      sha256:849379f0269df32524a3faeb5c3d57bf75494159d34395b50eb9452611310054
      sha256:8abca8585eff035f45616a265500f2ce06ef4e442f13875b3c21211652bf7f7f
      sha256:9ccdc664571e8253482b1742b3752f8ad97f3a3fe6be3cd8a9daa85d11c10598
      sha256:b73fb8e9127df35d8a7ae199320b11d27e0cce6e962f91cfc17293d50e1aca55
      sha256:c0584e6b83f02d37cd70d1a67dd7dd954c5fb4f387fa9908b01c065801ab5cda
      sha256:c2073dba02ece384f46c096cc7d381eada238a44761ff6db9c539a5ecebfbecc
      sha256:c6c240f1572a3bad0e5ed98da599ec8cf807bba46a5ad321648e4edd133a16fe
      sha256:cfa7ab769658065d18573d619a814e084dc78e5e037d82c4bbcabf91b4da620b
      sha256:dcd19739325a15140f8bbfe20f384b404552a382f02d353233470f59af9d5ee9
      sha256:6654f0fb5a4cc17a7b202c8348ee1a73ec95b2176d07562e4219554ebe9f174f -> 3094b4ef
      sha256:c2073dba02ece384f46c096cc7d381eada238a44761ff6db9c539a5ecebfbecc -> df628929
      sha256:dcd19739325a15140f8bbfe20f384b404552a382f02d353233470f59af9d5ee9 -> ec4de832
  openshift-logging/kibana6-rhel8
    blobs:
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:028bdc977650c08fcf7a2bb4a7abefaead71ff8a84a55ed5940b6dbc7e466045 1.738KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:9f6eca53e5bceb7b88b4c078cc5d939e61d13301d8bdccb6b68eeea4d3879f3e 1.75KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:18ea5ce2a404500d5dfadf661ec0566faf7380e56b05ed37735b93844ed5faaa 1.753KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:d851aca55b090483dcda2af05dbf048848399647d96dd3d4089f92d35f827db7 1.753KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:d83abe0bf84e68fd00b0d52bd69f27052215c6905b03e8173aa6796ec50bca63 4.942KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:08e1b8a4b86e01d9bb6f2fa72d90fe9a90945cb720ca7623f3384bc7cf5012b4 4.944KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:69308c99c73851e3ed1295e1ecb4253e36bf9e712baa993b7086510106982bb3 4.944KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:7ab56a1384b5d08b61a2d969bd2e76678f7b771b5fda9deb12e77c6fe7d83fb9 4.944KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:8bee28c3f1462dc983d8ab5bf72725b5ae1ab4b34c1664e7f72d4200811a8f04 4.946KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:d36c94e09fabc65d2c94b142287bef1ad609b4e1d92d82ad9921eaa115fe8221 4.946KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:d4fc7f3d8c4d085918aa831b2f914292e78a79a9bb17cd9c338a245bba9b416f 4.946KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:33a07b6706c17b660019e08495a2e93449d502a196792752c5770abeaca207ec 4.957KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:3435b81ae00e06fef828c17774abb71adb5f6b90eb6171294b5ccc0f8d33343e 4.957KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:f6eadda5edb427dcb8318029c2604cb94fc4c48b1a7b80af04adcb0e4e43aa13 4.957KiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:744db8e918c5c882f601488dcdbe3c4e9fa01913fe57119975eb5b16a18f6250 71.97MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:e0444ce29df7b754434fd4557ec395c5a1759e4af71c3615a81471e0c7edeadc 72.84MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:0c673eb68f88b60abc0cba5ef8ddb9c256eaf627bfd49eb7e09a2369bb2e5db0 74.73MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:7472fa517fd188919c3ff6ec27806dd20311bb57e1e34a7f3998734688fa7b42 81.13MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:285aca8b4565e008e8274c6ff136f49ff5abcd91c71c47b10861f978a65987f3 106.8MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:02198392e06307f51ca3bc77e199c9e040af1fca1efa5bcda2a353f529ad82f5 107.1MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:ae7d948d4fe3dd8bbb1c9cd9d0302c8b9ff250bbb23e5f6a41e517d02419517e 107.1MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:a1228ff2a3593e6da7b42f70f4802a35c5d6ce19177e5932d06727630590ab70 107.1MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:0d34ba994a88f4ea03d58b93aff34d8d914d7d5d1f58288341f8f4ba863daa8a 108.2MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:7a3a16f1a7833a8b0669de670a8bcf7618e92a6ea7703bdf1050f09666e8e831 108.2MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:7c81be05d73326343912066d334f6b63f5e5c6a5f255096e6bf238e80ef5c4de 108.2MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:464be667dc24005da32089e22dbb15427626aa52294d8879e10b2e25fed0fe24 110.4MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:7bce98aa4e08dd538229d5a73c4b3a46bdff3cb8d12f928b68fc254798811331 110.4MiB
      registry.redhat.io/openshift-logging/kibana6-rhel8 sha256:13966aee5eabd568c53761e7b565a699b6306dceb1f8dc37fbe233ed32bb01df 110.4MiB
    manifests:
      sha256:20b07dc6e878d6b446f53382d46d619b642f5770b5557185430ce3f1027e9e58
      sha256:46752e10e726c84be876c1dfd565fba4815054afd71db84af16a854346930a8f
      sha256:4c4777c63d906f332d658183f76cc0f7c1eefbfa58adfa6b7b584ab710323ac7
      sha256:64adeb26441159a1d2c3624fdb2a8b76f642f48d1171ce46bbfee7767839c77f
      sha256:64cb1964e1ece3ca7ff609dfe59c974208095200ae79a95517646aef7330f279
      sha256:7edf2fbe72e88de3b48ee40b9c960b5b567c27d1902745f7f6d8a5836d89432b
      sha256:7ef542a41916c19fae8c3500afc4b575809d0091001709b8df8ef107df046aa7
      sha256:a2f2573009880db9e2e21e68c90bbf0d32192774e0bee60d85a9c1117cc096a5
      sha256:a7863257f2bd010979de552d78252195f077b17ff28af140f066102fa0893afb
      sha256:b7562f5d22d8860bca1fd0d4dd7610e84153f111aeced03b7c6646495f3ee5a5
      sha256:ece1828d3b83d7d080c14061017a0954454d6eaa1b64cbbe9eeed51d3fbaaa59
      sha256:f64d3b646c1c0ad13cac02c0e6eab377eccf040c33e896fa5d0c4be90d1f85d4
      sha256:fcc486c24f642b390d7c77765a33c6fc656719e0ab1345f18f186d6c15f7c12c
      sha256:46752e10e726c84be876c1dfd565fba4815054afd71db84af16a854346930a8f -> 303c8651
      sha256:a7863257f2bd010979de552d78252195f077b17ff28af140f066102fa0893afb -> 54a9b3fb
      sha256:fcc486c24f642b390d7c77765a33c6fc656719e0ab1345f18f186d6c15f7c12c -> 8d9ad1df
  openshift-logging/logging-curator5-rhel8
    blobs:
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:028bdc977650c08fcf7a2bb4a7abefaead71ff8a84a55ed5940b6dbc7e466045 1.738KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:9f6eca53e5bceb7b88b4c078cc5d939e61d13301d8bdccb6b68eeea4d3879f3e 1.75KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:18ea5ce2a404500d5dfadf661ec0566faf7380e56b05ed37735b93844ed5faaa 1.753KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:d851aca55b090483dcda2af05dbf048848399647d96dd3d4089f92d35f827db7 1.753KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:87f2574ef01157208dde2107fa02ad2f2f4ef4bc99787246ba90b4fdc34b9864 8.711KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:7f70e2ae7ae0cad4dcaf44157b436b7d46280d41065e645b009e4b2d022e4949 8.712KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:dec3a205365c434159397e9fd205e2d06fe6c92f3500ad25970270c2124aa2c0 8.712KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:fe6cf584261df0257564f78560135e84c2aaf4e43c7c9b3c1cf159813370ff71 8.712KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:bb20745801252c4c42b5913da8211b7e9660a5d1d906f21d6867d55f2adfc843 8.715KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:d18c92306b4e88259ef35b716ce28f643ffe21b0b00e7f7cce12fd5902604276 8.715KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:e647ce8115f7923b3199fb58599c5dab2529e7908a22e301768178bfeb8e697d 8.715KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:9b2866411fdd12501121fd6504d9827fe0ba78086e8c606eedc03fff55f1012a 8.719KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:659c0888e2d0852b0f2cc8816adab2186d903ad33e356cf75b3a3268dbc5b174 8.721KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:e4e702a12c4fa2228d5d7e29ee4a8e863d0072466ebb333a14f6b21f118bfbb9 8.721KiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:7fc90ec5910cc61ec9ef343b45afa4686f5fce8d315e035e715173a644e594f9 9.841MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:a08701cfb02c70f5a591f61b704143efc204eea4741b001dfaadf1dc1e418abd 9.863MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:976a6ec80cde45dfeae673daa8dbc621636ede774c75b83baa54f7156fff32f3 9.863MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:646863affa911f2c19ab3171d96c762f6771b56abc1bbb59fe37618d25b9074e 9.864MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:a8c993030847eb9dd7df137c4c1870668567f3ee08c23a0ce6aaae850a73dae0 9.868MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:a91c5a52790333e1e3745df0653ca99a97fcd2e8e62afe4554e5e073539a8632 9.869MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:7c111161f1c7d02527fdd542b9453a099b83c39431d260981e58cdaf9b9b2122 9.869MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:3a42a12285a14c214f7dc65ce11f7d824720bd957c37799819e4d5fab9bfbeb9 9.87MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:d279a707031eec5e0f9b724a7cd01341b9462eeaa0440f3fc72315523ba7f263 9.87MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:f120a28ad0333fad7128621e7e926d5f031fb8ce7524c48eaacbe50d2d2659d0 9.87MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:a1acc2fd53cd4b8369b226d144c216d25d9c1a380745768f496afbc3f48a1298 16.34MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:1ec3039ee2083ddda0023477b8b6ce4953997b4ca67abdeae1be577be165745a 16.37MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:c37fd7de0840b4031b29e532b9c694c59a63983ae93162a2e6476882cd075b21 16.53MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:c54fd38fa8e85b2fb449666efa98198e9664b52a6f3602c0a4a8667186f12b99 16.94MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:9e25df444adda9ace9a6b12845bc467f66ce28210e0e861345b09dfb1a4934f4 54.74MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:e7b19f7fb76205a1eeff179c33d6dd5b42f54cbd78a31da78b4de061b7efee56 57.73MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:dd2de847c8709912f53afe9b923c4162d8d952acf40c71aa5cb558ee8b51069a 59.27MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:37824726d5db14cd27b31672cc85b982a3cbb241d267997ac14d88305d7314cd 63.02MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:744db8e918c5c882f601488dcdbe3c4e9fa01913fe57119975eb5b16a18f6250 71.97MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:e0444ce29df7b754434fd4557ec395c5a1759e4af71c3615a81471e0c7edeadc 72.84MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:0c673eb68f88b60abc0cba5ef8ddb9c256eaf627bfd49eb7e09a2369bb2e5db0 74.73MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:7472fa517fd188919c3ff6ec27806dd20311bb57e1e34a7f3998734688fa7b42 81.13MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:cae34425c559694a7a6f072c2486b4b9353f567f1591ab9460e507232f7eccce 129MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:7147b3e78d611407487bf504aca3c53a567ce827a9bc60213a018f8743315da6 129.7MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:bf105214519e48fd5c21e598563e367f6f3b7c30996d1745a99428752c0ad1ae 135MiB
      registry.redhat.io/openshift-logging/logging-curator5-rhel8 sha256:937bf243b00d655660ed34f27c7d3b34d35b6773132db86e176584f6706cdfcf 142MiB
    manifests:
      sha256:0bc94a614ac031c45530e18b702fa102a349f40c24a59b16d536f8199d3b04ff
      sha256:119265db56646a10f10947f14ed12e5b409ff8c5cc3bdc3ef9d3f3d3364b82f6
      sha256:1cbe10ebc4c421a53bf9a652596efe5510a8591bf9cf1336729fa8bef9a8963c
      sha256:363822fd64a18478e51fd30c6851172c4554cff467059e61d643e8d048fe4cec
      sha256:435d314d38947a8cc87880957f09ce8dfd6269cbcc4d4474ae1a128ddf93f04d
      sha256:753aa77878a0081118266cf12fc3715723b623633d5eaafb658d8ff0bcec6bf2
      sha256:83ba24d948341f4b768fbff7212f6109c75b784229d184a2556078e036873025
      sha256:8968f9ce2e74fdc5d8256c5a5c3f34ae6db8f6f86de0149fd66168168df54015
      sha256:bd257297e0fc1269905c4211b4650e5863d13b8700b2a981b2a6c58f216e47a2
      sha256:c137a9b5a58a7e74526ed6285fe30eafbaaf34cf8f88c76ec607df955cbc9519
      sha256:cd473c718cf65172a4cfa37c8fe9cbe7ab020bfa0cad3793dd9a51b8e6cb2c9e
      sha256:d1b1fc1c4e215ab53266800aff65e6b83278ab9add1a416e0a991f111b8af47b
      sha256:f7893725ed892239b1896ecfe3e0bb8152a70881e277c0def2df1b5e18f96c56
      sha256:119265db56646a10f10947f14ed12e5b409ff8c5cc3bdc3ef9d3f3d3364b82f6 -> 85ee8fab
      sha256:c137a9b5a58a7e74526ed6285fe30eafbaaf34cf8f88c76ec607df955cbc9519 -> 8d0ec9fc
      sha256:f7893725ed892239b1896ecfe3e0bb8152a70881e277c0def2df1b5e18f96c56 -> d74c6e27
  openshift4/ose-kube-rbac-proxy
    blobs:
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:9ab4182c2bfadf02e03bf6f92a366cd8d9e0e760abe30727538b54491201ac8c 1.42KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:fd464176d200a96c67f45dd10129481cbb797d604395b3240f80fcef996f5f4c 1.425KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:46369f78796f28a4e4bbb2298a753af43af7834c418dc5fb4c717d580ae6e52e 1.426KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:9029d856fd03ae63d1c975795fff5265f3947ef5313d14f413e4d0fac74da551 1.426KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:f0696e2e6e881a72eb463f3df90a18bf158d0fa295714420091fe2f5beb2074d 1.426KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:39382676eb30fabb7a0616b064e142f6ef58d45216a9124e9358d14b12dedd65 1.428KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:5e6920d5affe7a85fb62fffa4ff5285701d12f0a5777bbd21f32c0e789dd748a 1.428KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:8038dd80caa254828a7f9c85460c13c827cb61aa57fee5b9e78d92f4aa1cb54e 5.726KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:317fbde8e2b2589d99e3fd103de1321fb1563cbbdae738a3d421f48666c43b27 5.728KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:8ab804b87d90ec7c4f329ac45b62127ff96e1eb72b0e370551fc4dae70209613 5.728KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:a8f919bce518dc160221102b0f4d5cbbe8979b8cc2c38f71fce142f2ea2f9771 5.728KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:7768b5ddf996bf2d0fa298c64c6f9f2f1140a21402e1984723ca5ca8c2848693 5.729KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:aaf0ccd15df72e56b62078c6c515f5d084ee87794ec26c6e84922d9f4e21fd38 5.729KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:fe0efddaa353cb24a45beeee62716a52155d3615e645989db146298c61ef03ab 5.729KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:9d629822904b3ae7a78c71e4009accf5d370816f5308368d047675bf2e809f9f 5.74KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:c576071dfc1d3cef705848f15fe98ad395d0a3219893c35eaa51caded8d75d0d 5.74KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:38657d45a12863cf6115becd6a4b0b47a904a7a9f7cbbccc1950a3caa4f27689 5.741KiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:94362a98952b7d06aedd23c64c69357c9b7f886a90adac4839b081ea83443a31 10.43MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:726acff89796815b6fbc76ae2a0551949d8677d5345c3cf63c9d75fcbbf88eb6 10.43MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:f07762ea04d5aa2f4b0a423333f905143583e1a2a9830374340f31521729030d 10.54MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:28ef59df406a7f72420d94c9f2b55ed3aee000869b7a5bb7e61c838d62cf2c02 10.54MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:1afb83553d635bd3d1a362b6fdafb636cebb4e2164a5733532dc6b29aa2e023d 10.62MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:4b4fa837bd5769603e4f6524184e06b9a8e06d8d1e25af8cc39696d2262cb206 10.71MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:eb0703b204cfbf670dca262a9e9d04922a33ef195fc851fae7b536e0b68abf55 10.77MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:acc3c976ac75521c922cf4e11572dd3f6631d19df2fcbbac798d9ceb3507d45c 11.14MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:16534c517e67808030b585c03f3d5f460f41b2726739e5f7dbcd5b3e9d1c882a 11.15MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:987221a77e1f5540207d25a0e3c243c2225320c630e71b9433f5838c807086c9 11.33MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:4bd4969436a7a239bb362488cb70bfd6a50d0f25d17d9b4411b31fa66a5cb856 15.1MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:16dc99702748c331dcd1ff87b1df36ebd7d0a2cff27d37eaa4bfe68a58d01603 15.2MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:bd80971c62b783b388d270e07eb5298b439a726538ebe36bf2fccfacd45589b8 15.56MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:e0f53eb44a8ddfd6aeba8ea92b21b2ea3d2db6ff4e81385132f91a9d98017bd4 15.64MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:46d6f57c81ac369ef4ad71ae4078d6c2026e7872fdd6955017ca819d18072842 15.88MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:cca77bf9c3025e3609a6ae14da5f7eeb59db83317eaf67c7c4b85fe91438a571 16.11MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:f93e08b972e7b9051432d5ad44fee4efd261461622806342a03b79571ec0fbaa 16.11MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:b2ae9e688c0e4641170062bb34e71929b7f8f3cda81d71798d567cd54574c084 16.29MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:70975922084bc50a4fa67e0baa43b0e4f9ffacfcefa9f588ee901c62ee846feb 16.38MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:9da0528d44b30a4f4ad041489ae9d79087bda0ac37dccde7e3e63497bf7205be 16.5MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:53e613e6bf26018f653141907c9c3b6c24274e8480d955512132be683d320352 17.39MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:4380a10564e7f0ffe0fa397eee2ad8f07ad164370dc9943c03f9139bf8323ffe 17.46MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:dbeaa7bd632ba9223468f17fe8e01f3d5b84ef4fa41fd77f4c6f128eee13e29e 17.54MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:5f062d5160af5e12a94aa7f300bb96ead3155b82ae18928810312a8077dc521c 17.54MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:688638faf322bed6ab14884ec00b9cb9624c5e73024bc9eca8b57993e18d74b6 17.68MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:a5a2378416fe56b92ebf6bf8d37e1fa8515404a833245c639dc3c344641cdc5a 17.68MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:ef6f7e1fe13e66a0aa9bf1184733fdc596e1218c90c8441f767808a8dbd7a54e 18.62MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:36b768e39a244076e3f135d060185a4166ed80df8382a3f20f20a1eca674b491 18.78MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:471e5250377d59b10b42f0486deabc76453d3a22dc20cce154dadb4cc0e35e50 19.32MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:bddc7336dae7e298911491149215db035310f9754c77d2c0f478fa0c1945d556 20.36MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:2a7c17a4ad7579bc5fb4bc709db63d7852fbdc4fd195629fabd5d2e5ca7718dd 74.14MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:cadc283046514317dd4ecd1438530437d99cc92bf73080fb9e12e76751108de0 76.69MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:4e164a8c5ac173c1683ca572e23b00962ef8ddd2db486762980f8d48117e4f8d 76.79MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:57fce60a81bf84e6b61ce2b64643c9e4c61da77869b95e0641360b42429c0cff 78.52MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:237bfbffb5f297018ef21e92b8fede75d3ca63e2154236331ef2b2a9dd818a02 79.51MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:63dcfccb8725e6cc489621e7e4103def0d6302cba4de38027bd3276b300be5ba 84.18MiB
      registry.redhat.io/openshift4/ose-kube-rbac-proxy sha256:b3bfb78b869da2ea75182a04179bb0cb2a737a34201dd9cd42f3ec128352223d 87MiB
    manifests:
      sha256:036c78fea970d6bbe45aa1097a8ed701c72ce1117404450dd7e717a1bedd2b2a
      sha256:0a50f810f74d571d7371a80074027f2eb428f61424400ac070743442f47e4272
      sha256:22a058039a594a12029865d21061c1cd1e22c7ef23538f52fc2c9671995c689c
      sha256:45ed97c7f8252fa3566b3c5f68cc30bd0b3b9182a483f11ed3d32e55658ce27a
      sha256:4c2a45ac44848e9defb8ac4224ae517df83a3fa3e94349fb4593cb38dfafc585
      sha256:543dbcf59475f67ec1fb2c29e8c88ad067e35cda75835924719a2619e0348619
      sha256:552036784912510925e9315ea5061bfb4a6f31335fcc162b80177b7da26363ab
      sha256:61a164ad711478ac603242338f7b3c55986319479878b092be128581a823598e
      sha256:66159ce8eadcd6697e653bddcac81a38bb9c060f3fb27fcc8f07a352870a4428
      sha256:cc9680f70f4d1e1c6be9d98966e01caee9a3ab3523656ce52b4fbc423c39d7fe
      sha256:d4c60531f59953f399c6a2b0f8239128cdc2640f72f2d1e9ac4d6add618f9f01
      sha256:fd73a0644b87fd24ede361dd4f3cc82a1a5cd48235a63c2413779de5a9644cb2
      sha256:ff4441d4553634913a48ab282b1baa70cbcaca4bc81d3dd805408304b230f098
      sha256:036c78fea970d6bbe45aa1097a8ed701c72ce1117404450dd7e717a1bedd2b2a -> 48439f7c
      sha256:61a164ad711478ac603242338f7b3c55986319479878b092be128581a823598e -> cbadfac7
      sha256:d4c60531f59953f399c6a2b0f8239128cdc2640f72f2d1e9ac4d6add618f9f01 -> 17f68ea3
  openshift4/ose-oauth-proxy
    blobs:
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:fd464176d200a96c67f45dd10129481cbb797d604395b3240f80fcef996f5f4c 1.425KiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:46369f78796f28a4e4bbb2298a753af43af7834c418dc5fb4c717d580ae6e52e 1.426KiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:9029d856fd03ae63d1c975795fff5265f3947ef5313d14f413e4d0fac74da551 1.426KiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:9ec91ffaabb327dd30dfa71541d5912995bc09400e2465b395295d5efc638f9a 5.687KiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:bcdecfc07dd782bbcbde95f034b1b1bcf5efdb872b014dbcbb6515f0c6d07168 5.687KiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:465b5dd6647407fd16c0f7cb49e1b5e964c39992942bdbddf3b75c5df956cc02 5.688KiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:cd3711dbe45db1ca5cd72561bacd47a8b383e9e787ec6c6992e5ba306051377c 5.688KiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:6d667592a37c4f03deedf75a8f68f1851d6479182c658508334760b32276fdfc 5.699KiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:cffa0cd8a333044bd40710017d1f309b381dc4653334c32d5b8e768cae32c790 5.7KiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:4bd4969436a7a239bb362488cb70bfd6a50d0f25d17d9b4411b31fa66a5cb856 15.1MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:16dc99702748c331dcd1ff87b1df36ebd7d0a2cff27d37eaa4bfe68a58d01603 15.2MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:403a086c21cd22fa40589f0c7c8462969f5e1dae7ec856cd1c809858e280352d 15.22MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:064eac4957329ab3acca247a6214e0ff0d2391d5cab1d2b6dd31ea0f8e7052b3 15.22MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:bd80971c62b783b388d270e07eb5298b439a726538ebe36bf2fccfacd45589b8 15.56MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:e0f53eb44a8ddfd6aeba8ea92b21b2ea3d2db6ff4e81385132f91a9d98017bd4 15.64MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:70975922084bc50a4fa67e0baa43b0e4f9ffacfcefa9f588ee901c62ee846feb 16.38MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:9da0528d44b30a4f4ad041489ae9d79087bda0ac37dccde7e3e63497bf7205be 16.5MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:5d9f50f76e4c60256b71c344507171ae1a9481333f68a518967b69d0efe0af1e 17.53MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:4bc3a06a7c9b9b2b36a12d0aea1ea2a7ee018ceff8dba853f5363f339fe0456d 17.53MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:d99e4c3b8dd4d00167ad47149935782462eca169fb4737c42e619933287c4bad 17.69MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:3570693f539ccda240df92eaae03d86ce7466a0addc2872bc4c0b4d42660adb6 17.69MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:2a7c17a4ad7579bc5fb4bc709db63d7852fbdc4fd195629fabd5d2e5ca7718dd 74.14MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:cadc283046514317dd4ecd1438530437d99cc92bf73080fb9e12e76751108de0 76.69MiB
      registry.redhat.io/openshift4/ose-oauth-proxy sha256:63dcfccb8725e6cc489621e7e4103def0d6302cba4de38027bd3276b300be5ba 84.18MiB
    manifests:
      sha256:08b3eb667df212d8983ba777920536f2d3b9bd363c2cb718780907fd13aafe13
      sha256:12757e265f9b0a04939e9f7e2708867c2cc20b0b474b3a11e02edfa8b002d616
      sha256:20081ac90a5a3b548669d7dcea7e5bd45da7c44dd7279a2ca5baf765d1214e70
      sha256:2e80a063a29ef4917ec42d4465185ab0ab5b821fc2335c12d8064ab2dedcec1e
      sha256:6c01bead972377b810fe92313354687f2032ea36666f74a31063566b28239455
      sha256:85ce36395a59bc42746f7002fae89e677e6ecaaef4db5c351f4eac57f5d381b3
      sha256:96810c770f75057b8bba6b78a5ba414c8289315d8af1a387f681ae5e4fb7d6dd
      sha256:eadbaa6bb2f6fe639c8ffe5608dd1cd7769fb7d814f2cda92e4d664b0eabd1c3
      sha256:12757e265f9b0a04939e9f7e2708867c2cc20b0b474b3a11e02edfa8b002d616 -> c5bd1342
      sha256:96810c770f75057b8bba6b78a5ba414c8289315d8af1a387f681ae5e4fb7d6dd -> 808a65b8
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
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:e0f53eb44a8ddfd6aeba8ea92b21b2ea3d2db6ff4e81385132f91a9d98017bd4 15.64MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:064eac4957329ab3acca247a6214e0ff0d2391d5cab1d2b6dd31ea0f8e7052b3 15.22MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:d99e4c3b8dd4d00167ad47149935782462eca169fb4737c42e619933287c4bad 17.69MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:5d9f50f76e4c60256b71c344507171ae1a9481333f68a518967b69d0efe0af1e 17.53MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:bd80971c62b783b388d270e07eb5298b439a726538ebe36bf2fccfacd45589b8 15.56MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:9da0528d44b30a4f4ad041489ae9d79087bda0ac37dccde7e3e63497bf7205be 16.5MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:403a086c21cd22fa40589f0c7c8462969f5e1dae7ec856cd1c809858e280352d 15.22MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:16dc99702748c331dcd1ff87b1df36ebd7d0a2cff27d37eaa4bfe68a58d01603 15.2MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:3570693f539ccda240df92eaae03d86ce7466a0addc2872bc4c0b4d42660adb6 17.69MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:70975922084bc50a4fa67e0baa43b0e4f9ffacfcefa9f588ee901c62ee846feb 16.38MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:4bd4969436a7a239bb362488cb70bfd6a50d0f25d17d9b4411b31fa66a5cb856 15.1MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:143b441c3f6dc0876008ff7487f29da9833302594a12b128fc995ba914b2a5eb 133.9MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy sha256:4bc3a06a7c9b9b2b36a12d0aea1ea2a7ee018ceff8dba853f5363f339fe0456d 17.53MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:ef2eedd126d250887935c4eef88d8e0d12f7bcdc2f4a21784111eab0065e6a31 132.7MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:a0babb4e67c401ee5c81c2f6d19d835e2e445bb12f47c1c035a99f14dd301392 130.5MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:479a285eea48eaef0ff2f0ff86e43deedeccf984de9edcfcd24469e41a706642 137MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:7472fa517fd188919c3ff6ec27806dd20311bb57e1e34a7f3998734688fa7b42 81.13MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:0c673eb68f88b60abc0cba5ef8ddb9c256eaf627bfd49eb7e09a2369bb2e5db0 74.73MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:3f293dc69570341094547d1e5841c52e3e5f672807d667a3d6d955b3bc04465b 134.8MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:0426bc76cfcec7ac4a57b924ff9f4f16c778cac6f901740935c8d92292be394b 127.2MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:e0444ce29df7b754434fd4557ec395c5a1759e4af71c3615a81471e0c7edeadc 72.84MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:4d756d1efa8d4216c9087a9e8d7e2dfd5866a825c7d88f6cbedc9ef75cdee4a0 127.2MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:00beaece7ea33c31ff371482a939906501d2eba4bd5e59d49efb12e6421499f0 129.4MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:1986cf014b8e11aa06d3d8f65bebefd005377d08a13061c25ec32c2f2d97901c 134.8MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:744db8e918c5c882f601488dcdbe3c4e9fa01913fe57119975eb5b16a18f6250 71.97MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8 sha256:42f1bdba73509157bc04b7a2a873c7f34ed0684871c2cba80bfda9ddb2c1bb88 130.5MiB
sha256:a909c412c3f41c780d193e55611561b89f42e140f02dd2057f9877104beea263 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-operator-bundle:999cca6f
sha256:7af1bcccd4219e6f0678bd0570e65e00e6924d0c7013a081a8036168badba724 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-operator-bundle:52791cb
sha256:c45340a9bdd7b544b9410d1c52fa6e9b444571d506aa05d6849281df2014ef1c provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-operator-bundle:98515fd6
sha256:08b3eb667df212d8983ba777920536f2d3b9bd363c2cb718780907fd13aafe13 provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy
sha256:2e80a063a29ef4917ec42d4465185ab0ab5b821fc2335c12d8064ab2dedcec1e provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy
sha256:eadbaa6bb2f6fe639c8ffe5608dd1cd7769fb7d814f2cda92e4d664b0eabd1c3 provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy
sha256:6c01bead972377b810fe92313354687f2032ea36666f74a31063566b28239455 provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy
sha256:85ce36395a59bc42746f7002fae89e677e6ecaaef4db5c351f4eac57f5d381b3 provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy
sha256:20081ac90a5a3b548669d7dcea7e5bd45da7c44dd7279a2ca5baf765d1214e70 provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy
sha256:12757e265f9b0a04939e9f7e2708867c2cc20b0b474b3a11e02edfa8b002d616 provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy:c5bd1342
sha256:96810c770f75057b8bba6b78a5ba414c8289315d8af1a387f681ae5e4fb7d6dd provisioning.schmaustech.com:5000/openshift4/ose-oauth-proxy:808a65b8
sha256:c6c240f1572a3bad0e5ed98da599ec8cf807bba46a5ad321648e4edd133a16fe provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8
sha256:849379f0269df32524a3faeb5c3d57bf75494159d34395b50eb9452611310054 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8
sha256:b73fb8e9127df35d8a7ae199320b11d27e0cce6e962f91cfc17293d50e1aca55 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8
sha256:79ed4a8e586776a7d6f0c2d1a18e5d3323504d0dc7a5878cdf032957babe5a5a provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8
sha256:9ccdc664571e8253482b1742b3752f8ad97f3a3fe6be3cd8a9daa85d11c10598 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8
sha256:8abca8585eff035f45616a265500f2ce06ef4e442f13875b3c21211652bf7f7f provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8
sha256:3a5c08cba4458bfa6eadb89b1021b4c1d079d4dffcf8696e6805aac998134927 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8
sha256:c0584e6b83f02d37cd70d1a67dd7dd954c5fb4f387fa9908b01c065801ab5cda provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8
sha256:cfa7ab769658065d18573d619a814e084dc78e5e037d82c4bbcabf91b4da620b provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8
sha256:4fad94662f25baf49926835945125fd98146ab22c1bb6137e4ed3941f1ca9216 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8
sha256:6654f0fb5a4cc17a7b202c8348ee1a73ec95b2176d07562e4219554ebe9f174f provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8:3094b4ef
sha256:dcd19739325a15140f8bbfe20f384b404552a382f02d353233470f59af9d5ee9 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8:ec4de832
sha256:c2073dba02ece384f46c096cc7d381eada238a44761ff6db9c539a5ecebfbecc provisioning.schmaustech.com:5000/openshift-logging/elasticsearch6-rhel8:df628929
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:57cb0cfdadd782c86e4d5cf07335fb64154b959c0d8900496be3735ad2e85138 18.15MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:6c425223dd97cc703b8fb2fa0f8904fca09eec06f048ab7dc34ed1e818cf8184 18.15MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:e0444ce29df7b754434fd4557ec395c5a1759e4af71c3615a81471e0c7edeadc 72.84MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:0c673eb68f88b60abc0cba5ef8ddb9c256eaf627bfd49eb7e09a2369bb2e5db0 74.73MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:744db8e918c5c882f601488dcdbe3c4e9fa01913fe57119975eb5b16a18f6250 71.97MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:71c93b2a36fbe0bdfc96b6cec8a9da250fea7d28a66fd41fc6db5851e2b59c49 19.72MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:885b6d3571ee46af651aea440f9d599e088df7400c0151f4ecf73076d40d4b19 18.18MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:85709ddc9cfc3cec7043cd31ebfa53f97f705c50e841e051c6b65df7a8fa680e 19.52MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:01b3f5c9eb1b336aa106b3d114a9f8b2a5a3be18a79b4379e6398863d926d45e 15.59MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:bf91b897e3a40da210ed4c96dc8cc3c4f073c68b99f3921a32e7b2966430e80b 19.72MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:7472fa517fd188919c3ff6ec27806dd20311bb57e1e34a7f3998734688fa7b42 81.13MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:09cf259d81dce3fa62b7ea83a617358370be170970d0632c2f408f63119d6d8f 18.43MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:9a004564f0149b5a45c0f5e118f5a27174a93f245695956b87bfdd33a22a0b7d 19.52MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:7472fa517fd188919c3ff6ec27806dd20311bb57e1e34a7f3998734688fa7b42 81.13MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:0c673eb68f88b60abc0cba5ef8ddb9c256eaf627bfd49eb7e09a2369bb2e5db0 74.73MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:e0444ce29df7b754434fd4557ec395c5a1759e4af71c3615a81471e0c7edeadc 72.84MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:7c9d5c29c097ad4d27db3724ef111af9c58078fecb740c18ea9970fabd01d34b 15.84MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:c1d4d222568b0b98997645cda3c807a4ebb28b42ef841afc4f7c3c43553c25c5 20.49MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:1f1fa47b3cc066feefb22630ab9b3ef776ca8b1d1736f71c0767259f54713928 19.91MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:b02c54fa38a684229c0ade3eb5eea8fa4a30d67912a52cd043dcf55384e1b5c8 18.26MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:0f713f2e5c06198b19c74201ea100622e5e12e69895b3c0dba3e283bfbb8e87e 19.77MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:744db8e918c5c882f601488dcdbe3c4e9fa01913fe57119975eb5b16a18f6250 71.97MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:6997a911916b353da05a0b7a1351170bb927c13f6f0dc3d080d7e149f2e3a3a0 18.47MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:b4173d05a32d6fb3368cf44b57886adcde807ba7b471e6ed2cbe7f5687c13435 17.46MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:1df48d2eae98da38c1789e39d4766c1cae09c9c38107d9c89949c12f3efa290e 19.09MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:594dca45124d8d7bbc1b46fb96940ff5f60d20882c3ea2593fa5e68e0d417e9c 20.34MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator sha256:d79db737310b836ed8074695d801a5ff6a82c6cba60b302fdfea50a883921bf1 17MiB
mounted: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:2a7c17a4ad7579bc5fb4bc709db63d7852fbdc4fd195629fabd5d2e5ca7718dd 74.14MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:726acff89796815b6fbc76ae2a0551949d8677d5345c3cf63c9d75fcbbf88eb6 10.43MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8 sha256:71c87f12ca0e35fd76a6f713512e8c85f660683abab8c1fbf3806ee34920c46c 18.42MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:f07762ea04d5aa2f4b0a423333f905143583e1a2a9830374340f31521729030d 10.54MiB
mounted: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:63dcfccb8725e6cc489621e7e4103def0d6302cba4de38027bd3276b300be5ba 84.18MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:5f062d5160af5e12a94aa7f300bb96ead3155b82ae18928810312a8077dc521c 17.54MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:1afb83553d635bd3d1a362b6fdafb636cebb4e2164a5733532dc6b29aa2e023d 10.62MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:57fce60a81bf84e6b61ce2b64643c9e4c61da77869b95e0641360b42429c0cff 78.52MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:53e613e6bf26018f653141907c9c3b6c24274e8480d955512132be683d320352 17.39MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:ef6f7e1fe13e66a0aa9bf1184733fdc596e1218c90c8441f767808a8dbd7a54e 18.62MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:16534c517e67808030b585c03f3d5f460f41b2726739e5f7dbcd5b3e9d1c882a 11.15MiB
mounted: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:bd80971c62b783b388d270e07eb5298b439a726538ebe36bf2fccfacd45589b8 15.56MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:471e5250377d59b10b42f0486deabc76453d3a22dc20cce154dadb4cc0e35e50 19.32MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:4b4fa837bd5769603e4f6524184e06b9a8e06d8d1e25af8cc39696d2262cb206 10.71MiB
mounted: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:e0f53eb44a8ddfd6aeba8ea92b21b2ea3d2db6ff4e81385132f91a9d98017bd4 15.64MiB
mounted: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:16dc99702748c331dcd1ff87b1df36ebd7d0a2cff27d37eaa4bfe68a58d01603 15.2MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:f93e08b972e7b9051432d5ad44fee4efd261461622806342a03b79571ec0fbaa 16.11MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:b2ae9e688c0e4641170062bb34e71929b7f8f3cda81d71798d567cd54574c084 16.29MiB
mounted: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:70975922084bc50a4fa67e0baa43b0e4f9ffacfcefa9f588ee901c62ee846feb 16.38MiB
mounted: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:4bd4969436a7a239bb362488cb70bfd6a50d0f25d17d9b4411b31fa66a5cb856 15.1MiB
mounted: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:cadc283046514317dd4ecd1438530437d99cc92bf73080fb9e12e76751108de0 76.69MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:cca77bf9c3025e3609a6ae14da5f7eeb59db83317eaf67c7c4b85fe91438a571 16.11MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:36b768e39a244076e3f135d060185a4166ed80df8382a3f20f20a1eca674b491 18.78MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:bddc7336dae7e298911491149215db035310f9754c77d2c0f478fa0c1945d556 20.36MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:28ef59df406a7f72420d94c9f2b55ed3aee000869b7a5bb7e61c838d62cf2c02 10.54MiB
mounted: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:9da0528d44b30a4f4ad041489ae9d79087bda0ac37dccde7e3e63497bf7205be 16.5MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:acc3c976ac75521c922cf4e11572dd3f6631d19df2fcbbac798d9ceb3507d45c 11.14MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:eb0703b204cfbf670dca262a9e9d04922a33ef195fc851fae7b536e0b68abf55 10.77MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:46d6f57c81ac369ef4ad71ae4078d6c2026e7872fdd6955017ca819d18072842 15.88MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:dbeaa7bd632ba9223468f17fe8e01f3d5b84ef4fa41fd77f4c6f128eee13e29e 17.54MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:4e164a8c5ac173c1683ca572e23b00962ef8ddd2db486762980f8d48117e4f8d 76.79MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:b3bfb78b869da2ea75182a04179bb0cb2a737a34201dd9cd42f3ec128352223d 87MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:a5a2378416fe56b92ebf6bf8d37e1fa8515404a833245c639dc3c344641cdc5a 17.68MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:94362a98952b7d06aedd23c64c69357c9b7f886a90adac4839b081ea83443a31 10.43MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:4380a10564e7f0ffe0fa397eee2ad8f07ad164370dc9943c03f9139bf8323ffe 17.46MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:237bfbffb5f297018ef21e92b8fede75d3ca63e2154236331ef2b2a9dd818a02 79.51MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:987221a77e1f5540207d25a0e3c243c2225320c630e71b9433f5838c807086c9 11.33MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:c37fd7de0840b4031b29e532b9c694c59a63983ae93162a2e6476882cd075b21 16.53MiB
uploading: provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy sha256:688638faf322bed6ab14884ec00b9cb9624c5e73024bc9eca8b57993e18d74b6 17.68MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:a1acc2fd53cd4b8369b226d144c216d25d9c1a380745768f496afbc3f48a1298 16.34MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:c54fd38fa8e85b2fb449666efa98198e9664b52a6f3602c0a4a8667186f12b99 16.94MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:937bf243b00d655660ed34f27c7d3b34d35b6773132db86e176584f6706cdfcf 142MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:37824726d5db14cd27b31672cc85b982a3cbb241d267997ac14d88305d7314cd 63.02MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:646863affa911f2c19ab3171d96c762f6771b56abc1bbb59fe37618d25b9074e 9.864MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:dd2de847c8709912f53afe9b923c4162d8d952acf40c71aa5cb558ee8b51069a 59.27MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:d279a707031eec5e0f9b724a7cd01341b9462eeaa0440f3fc72315523ba7f263 9.87MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:744db8e918c5c882f601488dcdbe3c4e9fa01913fe57119975eb5b16a18f6250 71.97MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:e0444ce29df7b754434fd4557ec395c5a1759e4af71c3615a81471e0c7edeadc 72.84MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:7c111161f1c7d02527fdd542b9453a099b83c39431d260981e58cdaf9b9b2122 9.869MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:f120a28ad0333fad7128621e7e926d5f031fb8ce7524c48eaacbe50d2d2659d0 9.87MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:e7b19f7fb76205a1eeff179c33d6dd5b42f54cbd78a31da78b4de061b7efee56 57.73MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:0c673eb68f88b60abc0cba5ef8ddb9c256eaf627bfd49eb7e09a2369bb2e5db0 74.73MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:7472fa517fd188919c3ff6ec27806dd20311bb57e1e34a7f3998734688fa7b42 81.13MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:7147b3e78d611407487bf504aca3c53a567ce827a9bc60213a018f8743315da6 129.7MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:9e25df444adda9ace9a6b12845bc467f66ce28210e0e861345b09dfb1a4934f4 54.74MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:7fc90ec5910cc61ec9ef343b45afa4686f5fce8d315e035e715173a644e594f9 9.841MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:976a6ec80cde45dfeae673daa8dbc621636ede774c75b83baa54f7156fff32f3 9.863MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:a08701cfb02c70f5a591f61b704143efc204eea4741b001dfaadf1dc1e418abd 9.863MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:bf105214519e48fd5c21e598563e367f6f3b7c30996d1745a99428752c0ad1ae 135MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:a91c5a52790333e1e3745df0653ca99a97fcd2e8e62afe4554e5e073539a8632 9.869MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:1ec3039ee2083ddda0023477b8b6ce4953997b4ca67abdeae1be577be165745a 16.37MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:cae34425c559694a7a6f072c2486b4b9353f567f1591ab9460e507232f7eccce 129MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:3a42a12285a14c214f7dc65ce11f7d824720bd957c37799819e4d5fab9bfbeb9 9.87MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8 sha256:a8c993030847eb9dd7df137c4c1870668567f3ee08c23a0ce6aaae850a73dae0 9.868MiB
sha256:9375a864efbb3011eb4b9e1754b338d8875a0cc70fc46996dec07946d4c9a2ae provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8
sha256:f0bee519bb4f20aa1b7f4a1ca01db0d6a068bbcc0fa07416c242c0acffc83422 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8
sha256:dda0674e21d5372ca54a504727e065ff2700e2b9d4a2d7c32aa0021ffd317d8c provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8
sha256:b95246b7c3d0f619c92595918f9959d4d4540f492184f40353ecac5788370d3b provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8
sha256:90cffb969ef49252a28fc5542eca6ad4dae64d4145a59643342b3409d34250c2 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8
sha256:5ed92d7ac40e29491d237353b27237b38824eff3fe983628f199ff4ab6431ca1 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8
sha256:99bddf1517cd5c323b34fd4690c59ed3592f812f959a43981df1cc78bed27cc9 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8
sha256:81436241c1042cff2523bfcc73d050700efa107b3fb41c0475ae18b4a3b7b43c provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8
sha256:59c4a4b20ad42a32f09c60fada67a9f20c237e8dce8ece678dced5c37a87b65b provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8
sha256:5830aaf028a6a94ade5bb40840180911dd80ef0dc4eb93080cf8bde3476cd585 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8
sha256:2305040b28a6443a794956b3d854abe7e05756228055967f24689f191ef7596f provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator
sha256:42f31b5843a05f5813fbd277f6304fe9e3f170fe516b35a0b1f93a0be8d936e1 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator
sha256:19580a0c859f4970c09131b11f1717a417bf0fce987b0d0d0ec14aa0ae41123a provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator
sha256:150f56a69ff535982d1af00896a1b0f951cbb75666f9d139811a79b29e86b04b provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator
sha256:c0fb2fe7c385dcac5e5e5cd587ab5ef0b27c6cb821a62a0927a80bc2e9b7c96b provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator
sha256:0ca3b10eeed3148fdde0cc79e379e6cf5b66fb8df18ea810cf58838caaf92f70 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator
sha256:594b6b4c6224276ee07c5b1953ba71bc8ba1eb0536d244d6cd64c0c0e971fa31 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator
sha256:5bf6c8da6f400c6ca0ed3a0294f614f45bdc2477759708723d77d15cfab52e34 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator
sha256:0d0bd7b54472b47a65480f5b5832b773c5c79161eedbc15cbf80a8615f7d7e47 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator
sha256:a834960d45d27966bb4f866b87014c02b80c463cf0298fecb2d943b51200d2dd provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator
sha256:45ed97c7f8252fa3566b3c5f68cc30bd0b3b9182a483f11ed3d32e55658ce27a provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy
sha256:0a50f810f74d571d7371a80074027f2eb428f61424400ac070743442f47e4272 provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy
sha256:22a058039a594a12029865d21061c1cd1e22c7ef23538f52fc2c9671995c689c provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy
sha256:4c2a45ac44848e9defb8ac4224ae517df83a3fa3e94349fb4593cb38dfafc585 provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy
sha256:66159ce8eadcd6697e653bddcac81a38bb9c060f3fb27fcc8f07a352870a4428 provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy
sha256:cc9680f70f4d1e1c6be9d98966e01caee9a3ab3523656ce52b4fbc423c39d7fe provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy
sha256:fd73a0644b87fd24ede361dd4f3cc82a1a5cd48235a63c2413779de5a9644cb2 provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy
sha256:552036784912510925e9315ea5061bfb4a6f31335fcc162b80177b7da26363ab provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy
sha256:ff4441d4553634913a48ab282b1baa70cbcaca4bc81d3dd805408304b230f098 provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy
sha256:543dbcf59475f67ec1fb2c29e8c88ad067e35cda75835924719a2619e0348619 provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy
sha256:bbe43427c7cbd4007ff673afbae66070c4ed0302d82068ab987a996076d68d79 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8:546637da
sha256:4229507d8b89b1a24f16477661b41a4a7a6925d90fed348dd609978297fe88c4 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8:95da220c
sha256:8f2b1b2b15cab8a10b70a16e0af15dd73f0cf6932b16aac07c872beab229392d provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-proxy-rhel8:aea12e2c
sha256:0fd6cac32b8d10b0eaeecdd38c50df577067bd9ef27f20a8d03dba053e3aab04 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator:2a020a6
sha256:32072064d66d79c1464a4f36bdba832e7ece25ed0c011b6e4a17d132408767aa provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator:3b9c8a03
sha256:04c57db2d67b32fa53f021ea003704226517e493b6cc655aba02aab0d23323a1 provisioning.schmaustech.com:5000/openshift-logging/elasticsearch-rhel8-operator:269efb57
sha256:036c78fea970d6bbe45aa1097a8ed701c72ce1117404450dd7e717a1bedd2b2a provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy:48439f7c
sha256:d4c60531f59953f399c6a2b0f8239128cdc2640f72f2d1e9ac4d6add618f9f01 provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy:17f68ea3
sha256:61a164ad711478ac603242338f7b3c55986319479878b092be128581a823598e provisioning.schmaustech.com:5000/openshift4/ose-kube-rbac-proxy:cbadfac7
uploading: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:02198392e06307f51ca3bc77e199c9e040af1fca1efa5bcda2a353f529ad82f5 107.1MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:13966aee5eabd568c53761e7b565a699b6306dceb1f8dc37fbe233ed32bb01df 110.4MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:285aca8b4565e008e8274c6ff136f49ff5abcd91c71c47b10861f978a65987f3 106.8MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:7bce98aa4e08dd538229d5a73c4b3a46bdff3cb8d12f928b68fc254798811331 110.4MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:0c673eb68f88b60abc0cba5ef8ddb9c256eaf627bfd49eb7e09a2369bb2e5db0 74.73MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:a1228ff2a3593e6da7b42f70f4802a35c5d6ce19177e5932d06727630590ab70 107.1MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:744db8e918c5c882f601488dcdbe3c4e9fa01913fe57119975eb5b16a18f6250 71.97MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:ae7d948d4fe3dd8bbb1c9cd9d0302c8b9ff250bbb23e5f6a41e517d02419517e 107.1MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:e0444ce29df7b754434fd4557ec395c5a1759e4af71c3615a81471e0c7edeadc 72.84MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:464be667dc24005da32089e22dbb15427626aa52294d8879e10b2e25fed0fe24 110.4MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:7c81be05d73326343912066d334f6b63f5e5c6a5f255096e6bf238e80ef5c4de 108.2MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:7a3a16f1a7833a8b0669de670a8bcf7618e92a6ea7703bdf1050f09666e8e831 108.2MiB
mounted: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:7472fa517fd188919c3ff6ec27806dd20311bb57e1e34a7f3998734688fa7b42 81.13MiB
uploading: provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8 sha256:0d34ba994a88f4ea03d58b93aff34d8d914d7d5d1f58288341f8f4ba863daa8a 108.2MiB
sha256:83ba24d948341f4b768fbff7212f6109c75b784229d184a2556078e036873025 provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8
sha256:d1b1fc1c4e215ab53266800aff65e6b83278ab9add1a416e0a991f111b8af47b provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8
sha256:1cbe10ebc4c421a53bf9a652596efe5510a8591bf9cf1336729fa8bef9a8963c provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8
sha256:435d314d38947a8cc87880957f09ce8dfd6269cbcc4d4474ae1a128ddf93f04d provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8
sha256:753aa77878a0081118266cf12fc3715723b623633d5eaafb658d8ff0bcec6bf2 provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8
sha256:bd257297e0fc1269905c4211b4650e5863d13b8700b2a981b2a6c58f216e47a2 provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8
sha256:363822fd64a18478e51fd30c6851172c4554cff467059e61d643e8d048fe4cec provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8
sha256:cd473c718cf65172a4cfa37c8fe9cbe7ab020bfa0cad3793dd9a51b8e6cb2c9e provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8
sha256:8968f9ce2e74fdc5d8256c5a5c3f34ae6db8f6f86de0149fd66168168df54015 provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8
sha256:0bc94a614ac031c45530e18b702fa102a349f40c24a59b16d536f8199d3b04ff provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8
sha256:c137a9b5a58a7e74526ed6285fe30eafbaaf34cf8f88c76ec607df955cbc9519 provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8:8d0ec9fc
sha256:f7893725ed892239b1896ecfe3e0bb8152a70881e277c0def2df1b5e18f96c56 provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8:d74c6e27
sha256:119265db56646a10f10947f14ed12e5b409ff8c5cc3bdc3ef9d3f3d3364b82f6 provisioning.schmaustech.com:5000/openshift-logging/logging-curator5-rhel8:85ee8fab
sha256:b7562f5d22d8860bca1fd0d4dd7610e84153f111aeced03b7c6646495f3ee5a5 provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8
sha256:a2f2573009880db9e2e21e68c90bbf0d32192774e0bee60d85a9c1117cc096a5 provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8
sha256:7ef542a41916c19fae8c3500afc4b575809d0091001709b8df8ef107df046aa7 provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8
sha256:ece1828d3b83d7d080c14061017a0954454d6eaa1b64cbbe9eeed51d3fbaaa59 provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8
sha256:64cb1964e1ece3ca7ff609dfe59c974208095200ae79a95517646aef7330f279 provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8
sha256:f64d3b646c1c0ad13cac02c0e6eab377eccf040c33e896fa5d0c4be90d1f85d4 provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8
sha256:4c4777c63d906f332d658183f76cc0f7c1eefbfa58adfa6b7b584ab710323ac7 provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8
sha256:7edf2fbe72e88de3b48ee40b9c960b5b567c27d1902745f7f6d8a5836d89432b provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8
sha256:64adeb26441159a1d2c3624fdb2a8b76f642f48d1171ce46bbfee7767839c77f provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8
sha256:20b07dc6e878d6b446f53382d46d619b642f5770b5557185430ce3f1027e9e58 provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8
sha256:46752e10e726c84be876c1dfd565fba4815054afd71db84af16a854346930a8f provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8:303c8651
sha256:a7863257f2bd010979de552d78252195f077b17ff28af140f066102fa0893afb provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8:54a9b3fb
sha256:fcc486c24f642b390d7c77765a33c6fc656719e0ab1345f18f186d6c15f7c12c provisioning.schmaustech.com:5000/openshift-logging/kibana6-rhel8:8d9ad1df
info: Mirroring completed in 7m47.25s (11.48MB/s)
Pruning 140 manifest(s) from repository openshift/release
Pruning 1 manifest(s) from repository openshift/release-images
Deleting manifest sha256:00bfbe7800a9c803d13e666122a7b1f52bea6e53b0fb2352d8566d1e75759708 from repo openshift/release
Deleting manifest sha256:dd71b3cd08ce1e859e0e740a585827c9caa1341819d1121d92879873a127f5e2 from repo openshift/release-images
Deleting manifest sha256:0213887c325d3967d122fe875b45c9259e2b8388db9dc4e0a25c0561414b8737 from repo openshift/release
Deleting manifest sha256:0305702b3b7208c339c5df376aaacf6992465e34490bc16c2d66e423039b2b06 from repo openshift/release
Deleting manifest sha256:0310a7b9ec788788a2a6324475add611b5c5304511fe27e7444500fdefbc6ef0 from repo openshift/release
Deleting manifest sha256:038452cc237d09fbbea348a454007760fb2d04e1e2439e931c505665c2c98071 from repo openshift/release
Deleting manifest sha256:03cbd8048e00835d29ced43ddb4548e979e51dda727239cfdf027c9ef47339cf from repo openshift/release
Deleting manifest sha256:0503429fe988d46d901cd8c625c5ca3e27d0eddd151cd6ee2af346c6397fbffe from repo openshift/release
Deleting manifest sha256:051c76b923d9826bd577aeb1e176a4096a9af47e9a7c1819158282a5a417170b from repo openshift/release
Deleting manifest sha256:06ca4e45c6ec3aba104a066905322e35b44c8a2c293d9f2821c83ed2cb743633 from repo openshift/release
Deleting manifest sha256:07c709f51f7383720c2acca830b3441c188ab0f077616d075a5a6bd9cbd1a2e4 from repo openshift/release
Deleting manifest sha256:0b8a09ab3c370f7ef89319f3ff66cc346bbfc1cc48b58c2d40ef7d61b33a349c from repo openshift/release
Deleting manifest sha256:0d741e590eee37fd9708609dde99c68f0b8fba9d0d5cee172227037b7446e3c7 from repo openshift/release
Deleting manifest sha256:0e0a6328ea7449e2b3ecaa057f59f98cb49e2c71b60ea0d9a4f3d358ef472f36 from repo openshift/release
Deleting manifest sha256:0fdd27a12ee71d1268ef2e7c4cfe8dbdd3a86e3010f77db3f2e530b928fa2a42 from repo openshift/release
Deleting manifest sha256:1141f74833abe8de6384022fb7a800171640a40464a82669421517f5f7d2a128 from repo openshift/release
Deleting manifest sha256:119ce8d95beaef7fcc420fb1acc6d311a1a6309a371a1ccc427b64a7f2d1e371 from repo openshift/release
Deleting manifest sha256:1267dd9b35b81041888212be03415b2fab37d1ac9e0fb4d9ddcf60c72f7a99ad from repo openshift/release
Deleting manifest sha256:13a775771eb0fbb24216f5cbaeab394426671bf5d7a5aa23268607e51b59ecbd from repo openshift/release
Deleting manifest sha256:13bb02aabde612c09fe136b0ca02487cb5f48b9ddf871afbe0d4f2b4b1b66b3a from repo openshift/release
Deleting manifest sha256:183b7a1d2b4d3600a7251eda82f90ff45d4b5324b3bfdba6a091aa735fcff4be from repo openshift/release
Deleting manifest sha256:18740b88ca23a49a1cbbf3be74bf96ff536e961bb11235d4539cffccda7101ed from repo openshift/release
Deleting manifest sha256:1938512df87a22d0a3c4f9d7aafa9557d7268f3511b52f9494946bb7478bc2da from repo openshift/release
Deleting manifest sha256:19ac6ad2cac43fd4134dfed13168ac0761a014ec55d277eb4d1259caac757b04 from repo openshift/release
Deleting manifest sha256:1bbe2e9e053350f5af04d80e9a088fb6cc5ca7898b8c1dfdeb5269a4befffa60 from repo openshift/release
Deleting manifest sha256:214b7e2b9bece6260311a2dc28bfc12c379e37186c09582221ec1fe724a995d1 from repo openshift/release
Deleting manifest sha256:22e31d53a7c6a92176d5a183fd213fff4e2e68c343ccf6cca9c7fc1363e34836 from repo openshift/release
Deleting manifest sha256:23244e61eaee6a986e1ac26def05a6eeefa3150b9e740787d8ec0be765b6c8ec from repo openshift/release
Deleting manifest sha256:235f6b6a1c9c41dbf3b8867bdb25da59c5e3a15af93b61fb13b4b5f41814d954 from repo openshift/release
Deleting manifest sha256:23921ee4c51c3fc5b32598d89233353855b335cb1d3592bd4e14cda9b23aa0e3 from repo openshift/release
Deleting manifest sha256:251f6fcf7b079421f5ed480db37715ce1fc35e4bbbf859b3ffc449b84d8fd246 from repo openshift/release
Deleting manifest sha256:279a3393fbb0c344b410de5aed4702ae41c49382a5c271c5a6d915defa72c47a from repo openshift/release
Deleting manifest sha256:292d0fbd28892d4952e0cd914cb4b1eca7b5b31dad3ff26abbbfe3ee57ea24e6 from repo openshift/release
Deleting manifest sha256:2ddae06a7ca8031611b8ec5a63050195a5b747c66289b8ad810bc8da73aa58d4 from repo openshift/release
Deleting manifest sha256:2fc2c9d9fae3070c00239ea3d0d1d9fa7477c99c296b17b2fc352794c535912e from repo openshift/release
Deleting manifest sha256:2ff95508332a75a0b0b50be84416297781946d0feb687df36c8082baff564857 from repo openshift/release
Deleting manifest sha256:33001ffab5cba9aaff19e475e0118ea158c99797bf6f3e210832f129ac34d71c from repo openshift/release
Deleting manifest sha256:3730bafc9c8a397887d4213b220afffe11cb6835ea5754e60a0cfe967efbdaf5 from repo openshift/release
Deleting manifest sha256:37e092de518988e67fb60bc5eaaa365058b6be6a1877613af5d12e2c4a2ec129 from repo openshift/release
Deleting manifest sha256:3e4dd326434d115808c7bff14d8a38806c6757d928f2359737496fe6a32b7b4e from repo openshift/release
Deleting manifest sha256:40302e29af4c6e3e9a6e73544a2fd4239da691b0afde937e76d078a642d30817 from repo openshift/release
Deleting manifest sha256:406384d7815dc29c85c903318b1a98a2f3bd65b440a9344b71d6ddfa3e98c287 from repo openshift/release
Deleting manifest sha256:426f99588fbce292824dba75372675b83daea64a1cf5d321fb5e4182fc43867e from repo openshift/release
Deleting manifest sha256:42eb156593fbc94fa12eaa5938c8335d500e222be370475ee19a57ad7f7f42a8 from repo openshift/release
Deleting manifest sha256:43150eb3f35005a1c04fa4ed6fef3c8cd3199991658a88530d28cd1a32a5597a from repo openshift/release
Deleting manifest sha256:4446e75f22adf0e091bb337da46174ba7e82b394da5b04a2dc9c60b749c7cd02 from repo openshift/release
Deleting manifest sha256:46d7b63794f6ceec9fbcc63bbf170aff5ee3152ccb24ba51b8167ac59a30ca6a from repo openshift/release
Deleting manifest sha256:4b14ffcea52f9eb5952546634cb26bfb1d523a4dd81382021c71673fed91efa2 from repo openshift/release
Deleting manifest sha256:4b56346c5c3c09eee6d8d37b6796f1e4f15e5a8de53b2afc6a4d3e6fb780ee24 from repo openshift/release
Deleting manifest sha256:4b979717b90adb7f1758e00403495f09a24e53bc537dbd2eb69398b83e3d8819 from repo openshift/release
Deleting manifest sha256:4f4021f6a725ee1bc3c393535742b720ee2fc5ffc978849a2b67fc437debc283 from repo openshift/release
Deleting manifest sha256:50e0b3eced37efe7c94f746e0260d4750144962ef16ab334b93c08b3635f946c from repo openshift/release
Deleting manifest sha256:550304f5ac4929e68b97108da732b8271997dee09db91a2d4fb55d3b143ad7eb from repo openshift/release
Deleting manifest sha256:5982ecdb04297b4c546da927abbbb9bdaf772737e794218c20b43a5d4ede9d22 from repo openshift/release
Deleting manifest sha256:5a947cf105f932656f89e0a31973fec637a756ea0a66bea80fb623c37a91e7e0 from repo openshift/release
Deleting manifest sha256:5c4039e2846f73e3703f63a2aa52a27a85d8c3c9c3a076e05e4aac33301a5715 from repo openshift/release
Deleting manifest sha256:5e33f9d095952866b9743cc8268fb740cce6d93439f00ce333a2de1e5974837e from repo openshift/release
Deleting manifest sha256:63149966fd4008c90409477bd30490445d8f11dd484009fdcbd665237d1e85a9 from repo openshift/release
Deleting manifest sha256:644b470e3cc0e417c8766bdecaa4944ded16267a542fc93d477b364a6905fbe7 from repo openshift/release
Deleting manifest sha256:65f83e849f9cb76bad217bceee4e34a30d062e17a2a1a80237462d26d98e8870 from repo openshift/release
Deleting manifest sha256:66996ef32742e67e768985c759f248b5af8b503403c9844e535f3a1ee4c24c07 from repo openshift/release
Deleting manifest sha256:66bf4dc3d0a3f0559c48d7dc7b9b45af7c90e5b6be704421362645d225c09cf8 from repo openshift/release
Deleting manifest sha256:67cb844c3e059ef41d746d6132ff1a3cf4240bf959191fbf31a680461b95397f from repo openshift/release
Deleting manifest sha256:6a78c524aab5bc95c671811b2c76d59a6c2d394c8f9ba3f2a92bc05a780c783a from repo openshift/release
Deleting manifest sha256:6e2a44337dbe5bf89d6f0f16124df38bb86f71c866a9ae38badb450e58e7f42c from repo openshift/release
Deleting manifest sha256:6e68ba361e31be5dd1b3ecd0fe5318b337b1915d5db2d8b635cb6f8bb8ee7f97 from repo openshift/release
Deleting manifest sha256:71a76b1a54d9b9ccba384c353db5777f4a3b37fff2c7027225bb534d09565615 from repo openshift/release
Deleting manifest sha256:72a75a1eecdfe8e42703fcde0bb63c1f5f256dd21dba92bc36edf54e9f2a845a from repo openshift/release
Deleting manifest sha256:72d072f33452fc8c5fb0751ac3743688cc4d44959654bb0747dad000218f6f05 from repo openshift/release
Deleting manifest sha256:79c8e38ac956a6be5ba8dfbefc10e30ad0b5d3229ea8951fd9745b57fa5c6a90 from repo openshift/release
Deleting manifest sha256:7b90fbeebf473c2e85acc23bd116c3d09750377f32a800ae09caeae101d34957 from repo openshift/release
Deleting manifest sha256:7bc4f0c55d6e8d92cb429337759824f8535e56a172e75b8c82959a4f4f270ab4 from repo openshift/release
Deleting manifest sha256:7d78a787b5655292d4440942ae018ad5d1c985881c4e9d95d887d4f8450c7899 from repo openshift/release
Deleting manifest sha256:7f734c4f3a69377a667c97ef00642accf60f2f0020ab89531eea3d650f1a89ca from repo openshift/release
Deleting manifest sha256:810097f053d1859d516ab784d975d41ae435ee91f5eaa7c90a02e643620c18fb from repo openshift/release
Deleting manifest sha256:82158554ee3c838c24ac3d894d5c92a30f9e6705e6e17520115a5edd62a2bf75 from repo openshift/release
Deleting manifest sha256:82bfc9ea845d7948c362061afa266d7e3dc1465cec041371e99cb1bcb5056036 from repo openshift/release
Deleting manifest sha256:83bc064c1957b18d231d0befd2bf14bcbadb3ebe20ff3b21dc4b23f048aec547 from repo openshift/release
Deleting manifest sha256:8bbd804da57f3f961ce8cf8057ac7a946166df966bad4bca3295eeb16eadde5b from repo openshift/release
Deleting manifest sha256:8ce4be01db73a940acf798ea8102dc06f340f3afec470a3af72abfe130114de9 from repo openshift/release
Deleting manifest sha256:94145128ec7805e478fada43a8a8ade8054fb50b56c97f159fe9da28a56851c7 from repo openshift/release
Deleting manifest sha256:948def25e5c80edcc357064d740d78b97bb8fa4f940f13d0b501714c7edf57a1 from repo openshift/release
Deleting manifest sha256:94926d57aaefa23376615302a14a96d4bca45dd6c3184a8595042b0ae847eb1b from repo openshift/release
Deleting manifest sha256:961b30e0ab62cf04d000c1eb8330e62ff2daf4545ee094d3c9acd4c443e3c5d2 from repo openshift/release
Deleting manifest sha256:96487fbdd3efaf1ef9c8e34bd17470095daa3dd01bf9c5c42e5c67ee99b21bbc from repo openshift/release
Deleting manifest sha256:9716b0f99225f7aa9680a8f07fc02c3a8233b010acd332d7c4651d5e0177de3f from repo openshift/release
Deleting manifest sha256:980d27d0eb312864f4dacf8f125c7a2ec6d507817045517ebfb80ea25601e1f6 from repo openshift/release
Deleting manifest sha256:9df074e84d628156a71c314708d4cc003e020d3e4948926e30654b5eb0c2a958 from repo openshift/release
Deleting manifest sha256:9e2d384be6ebe5fc9c089ab3ed1e1f082c180aed520ee4ff306e46fa56c12728 from repo openshift/release
Deleting manifest sha256:9f56d95ed665c1442fb4ce0b6415dbecf17729372527c46570aee5c372282a1e from repo openshift/release
Deleting manifest sha256:a4b8af157bd962a43aa8d44707743a6d743b70faeb72548c5a91869a50ae6b5e from repo openshift/release
Deleting manifest sha256:a4e51611597318e86a82389b481f49a0978d5deb403f705b14346161c0b62104 from repo openshift/release
Deleting manifest sha256:a6322f222f98adc1585ff8777c73140a56ac3cdcd8a6949309884b79496bcbb6 from repo openshift/release
Deleting manifest sha256:a928705d99ccf81cfb7c0ef07a515f67a488d7052a2d7ba34ddabaa140295d78 from repo openshift/release
Deleting manifest sha256:aaaec3417207330eaeb203f459bdd819c27bac1956f1d9f0684f42dc5a3807e6 from repo openshift/release
Deleting manifest sha256:aaf0854244f356ccb3f1cd70356362ac5ef7a9f227e08128740306430fd75497 from repo openshift/release
Deleting manifest sha256:ad7e0cca53e098a448f9d5df9ad5410515ee1ed3c83dbbe8ab72fc8ae73b986c from repo openshift/release
Deleting manifest sha256:b98f4e019aa3cae1ac333a8241bafdbe52caccfcdcd7f640d1a7410dd33dd788 from repo openshift/release
Deleting manifest sha256:bb0a5b9d4d3d5c774d58e1f2f11abd260ea1b47e79c64047f753d04927b5c202 from repo openshift/release
Deleting manifest sha256:bb5f32d6a5294d211e7016a3aeefc1a02dd8ce3773747bcead61ab40f3a13366 from repo openshift/release
Deleting manifest sha256:bc6efcc92aa2dde7d917eb2922e3eb19eeebcd4e8f19a499fc536fc2e45183ec from repo openshift/release
Deleting manifest sha256:bc7227d80dc8f416eabc2bf1d326fa41ec9ab849b2a06291ef98d25a83f5ccb6 from repo openshift/release
Deleting manifest sha256:bc820dec82b685d589a6d71fea17baf28d7404d0351d7d91876476c4c2932c0c from repo openshift/release
Deleting manifest sha256:bd81d21f0593cab25f321b000e8e18f5ff50fb741289261874fa332407b9da05 from repo openshift/release
Deleting manifest sha256:c017f8b24e9d9a913456373c69dec63e3315ddae052ac6ad9cee25f856abe502 from repo openshift/release
Deleting manifest sha256:c10376143bb2fc51f72619cb7d45c8905d7c5a10b0150e1a1f65c54a90fff1bc from repo openshift/release
Deleting manifest sha256:c56c86d030185bda241514593970e80f75ae75afd9bc6288388944bc2a1dfb1f from repo openshift/release
Deleting manifest sha256:c5c4823cce587db60358f78f2a324a6def77930b0fa392a2939e04041b5a4a6b from repo openshift/release
Deleting manifest sha256:c6124ae70fd1f19ada3d5930ffc912dd5a3fb09dcffabc6425e9e327a61528fa from repo openshift/release
Deleting manifest sha256:ca8dc8a6e1a672565fb1db1bdf0bc49d1878bc9621b15b293334937d42a34972 from repo openshift/release
Deleting manifest sha256:cc04ac8cc0ebd5ac0b7bd69e27a5d3958d311b61e3f78ed896acc2445d5e1ba7 from repo openshift/release
Deleting manifest sha256:ccd64e8b56711ced7558d8e593ae32867cb2178ea73078f47d20bc7e2e56119b from repo openshift/release
Deleting manifest sha256:cd13cdf851def78178b6c1072910fcbceabdb121b888cf82377469fea75a0bf3 from repo openshift/release
Deleting manifest sha256:ce897bc72101dacc82aa593974fa0d8a421a43227b540fbcf1e303ffb1d3f1ea from repo openshift/release
Deleting manifest sha256:cf33109e11798f970fa181f7ab08be92301b245dc2b7d17226f38b4146d585cd from repo openshift/release
Deleting manifest sha256:cff70e2271d5bcc26426cf3f22f3439219677e6175fcaa00c2e5a5da9379d5d6 from repo openshift/release
Deleting manifest sha256:d622ce62d93f99c3f5e4ffdcafc19ee3c31f82630185b882c7bd4d332fc435cd from repo openshift/release
Deleting manifest sha256:d74f9409782832026f98e261089bad47fa50be5282c2775dbe521f5fda0e0ee4 from repo openshift/release
Deleting manifest sha256:d8c857820a05e64b271622508038330f411f08d87cedd16a3ac13095ed920b4b from repo openshift/release
Deleting manifest sha256:db28495dcf72f26368357ed71b745c3c7b5e2a40c787bc8a76440f16872a0dbc from repo openshift/release
Deleting manifest sha256:df22255abc474a2b61b323925f76126fa2f8c99affb1c48c2a0eb16c4b4a1056 from repo openshift/release
Deleting manifest sha256:e03cefa188318d283eb39b799950a3540d0cfe14becf8995d391dfff5dd0369a from repo openshift/release
Deleting manifest sha256:e146605c1b75917d26c07268b361134aeda68983b2e2b060c202420b8267aa45 from repo openshift/release
Deleting manifest sha256:e2998b64e69d5a903a76e5f47f1f9aba9b4f5db13f0dbe265c85121317c1cd33 from repo openshift/release
Deleting manifest sha256:e5c16190ac98e446c7cf90cba8e14e996f81021ea4a38abd17b87c13b30bd6f8 from repo openshift/release
Deleting manifest sha256:e79fb8754d7c01a60c1763fe39104343783a6498bf10254b4206eae532c1679e from repo openshift/release
Deleting manifest sha256:e80f8718cc38cb025680205d890d4f622f975297bc437cf52ae1fbce0e89bf1c from repo openshift/release
Deleting manifest sha256:eae57963e5746607da7ce8070275abd301ab420ce767706d5b36972f64a0e450 from repo openshift/release
Deleting manifest sha256:edc8e8eafe3f73633f3ffe4a393e21f4495d518e9f044f127a3616315cdd78b4 from repo openshift/release
Deleting manifest sha256:ee96cf6a6fc395bb5432ba2322c3fa740832ebe490211e53b401f35cbe615636 from repo openshift/release
Deleting manifest sha256:efbc54ce84c0b4dd7fe7b3e6fa2e6cd488b57d9c6fe7d055ef6395ade51b5906 from repo openshift/release
Deleting manifest sha256:f0d490ad627c935e07384b942859a938a8445e66486450919537d5bac8f92f62 from repo openshift/release
Deleting manifest sha256:f22b23274a3f220805ef2b83502d9af379b6841dfbd593d526f072acd6c780dd from repo openshift/release
Deleting manifest sha256:f2a6bffb17480fb2caa5a52512efa348a09b960d23782d02ec8733df948dbff6 from repo openshift/release
Deleting manifest sha256:f5628b30aa047fe32cba9308c70c581f7d9812f40a3e651a84f0532af184bfd2 from repo openshift/release
Deleting manifest sha256:f60434187e593c079d98e94b6ba69bce34e5daa5ac161e834827d594b070592b from repo openshift/release
Deleting manifest sha256:f7ce7247375fa57b2be95733fb26a85751a5f41b28652e96612bea2ee6a25442 from repo openshift/release
Deleting manifest sha256:f83965a48127fcd265ef40ce6037df806980a088f8c83798be5b91e5cc09a706 from repo openshift/release
Deleting manifest sha256:fd41b7237cac235fead9bda6dc9bf5c6cbde163ebf9d9249f33065d5ceadded0 from repo openshift/release
Deleting manifest sha256:fd7ce3da297b589c7b3c34f6dc820f4d71a51ec367424749e76fbfad06298456 from repo openshift/release
Deleting manifest sha256:fe552892df8d59716997e8149a384ef65cade7610de3264652eb77e2d93575ed from repo openshift/release
Rendering catalog image "provisioning.schmaustech.com:5000/redhat/redhat-operator-index:v4.10" with file-based catalog 
Writing image mapping to oc-mirror-workspace/results-1660660213/mapping.txt
Writing CatalogSource manifests to oc-mirror-workspace/results-1660660213
Writing ICSP manifests to oc-mirror-workspace/results-1660660213
~~~

We can see from the above output the images needed for the elasticsearch operator were mirrored and as part of the operation there were also a few manifests deleting in the process.

### Prefer Direct Update Paths and Skip Intermediate Releases

With the basics of mirroring out of the way lets explore one of the new features of oc mirror.  Prefer direct path updates or shortest path to an update is a feature that enables one to download the least amount of images for an upgrade path.   In this example we currently have a OpenShift cluster that is at 4.9.12 and we want to bring it up to 4.10.22.  To generate the imageset for this we need to define the channel as the version we want to get to, in this case stable-4.10 and then we specify a minVersion of our starting point which is 4.9.12 and then a maxVersion of 4.10.22 and add in the shortestPath option set to true.

~~~bash
$ cat << EOF > ~/shortest-upgrade-imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  local:
    path: metadata
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
      sha256:172c3384fae890209db5795e988d68f9d947c0573800099a002478203fe7c71b -> 4.10.22-x86_64-csi-external-snapshotter
      sha256:1738d7f06906e7af4cda09ebff1e1538a35b834889bbe6ae61012b0dc5d8bb33 -> 4.9.42-x86_64-etcd
      sha256:183b7a1d2b4d3600a7251eda82f90ff45d4b5324b3bfdba6a091aa735fcff4be -> 4.9.12-x86_64-cluster-version-operator
      sha256:18536c0098dc131be45c747ae3ee26268fe3e8debc1ffa593e5cd804ad5eeda4 -> 4.9.42-x86_64-csi-driver-nfs
      sha256:18740b88ca23a49a1cbbf3be74bf96ff536e961bb11235d4539cffccda7101ed -> 4.9.12-x86_64-kuryr-cni
      sha256:18ff4e641ed452fdd1da95bac8001772e4a0c05ffe5a5a904f7428790cc8c2e5 -> 4.9.42-x86_64-azure-cloud-node-manager
      sha256:1938512df87a22d0a3c4f9d7aafa9557d7268f3511b52f9494946bb7478bc2da -> 4.9.12-x86_64-ovirt-machine-controllers
      sha256:19ac6ad2cac43fd4134dfed13168ac0761a014ec55d277eb4d1259caac757b04 -> 4.9.12-x86_64-cloud-credential-operator
      sha256:1a8e482f7ff3b64cddfc75a0c1475a7aa5818109899d8d47f430563b426b0283 -> 4.9.42-x86_64-network-tools
      sha256:1adf9086599199ac6690575839cc4ebfda8ec2e1011c4501c99c3127c4a50b16 -> 4.9.42-x86_64-aws-ebs-csi-driver
      sha256:1b5f6a51eccc5c3f56389f3f423858f31c27ea7c6dc25d139196e9852d7b8d2d -> 4.10.22-x86_64-cluster-openshift-apiserver-operator
      sha256:1bbe2e9e053350f5af04d80e9a088fb6cc5ca7898b8c1dfdeb5269a4befffa60 -> 4.9.12-x86_64-cluster-node-tuning-operator
      sha256:1da490b51d2ead698ff172b8165c85a80e568d0fca8b650b3f70159a0772a1e6 -> 4.9.42-x86_64-kube-proxy
      sha256:214b7e2b9bece6260311a2dc28bfc12c379e37186c09582221ec1fe724a995d1 -> 4.9.12-x86_64-kuryr-controller
      sha256:21dfdc485fd9d901e4e6a9cb235267b8bb8e8ae3b4e77ff28b554efa0be7b3cc -> 4.10.22-x86_64-openstack-machine-controllers
      sha256:22e31d53a7c6a92176d5a183fd213fff4e2e68c343ccf6cca9c7fc1363e34836 -> 4.9.12-x86_64-multus-cni
      sha256:23244e61eaee6a986e1ac26def05a6eeefa3150b9e740787d8ec0be765b6c8ec -> 4.9.12-x86_64-tools
      sha256:235f6b6a1c9c41dbf3b8867bdb25da59c5e3a15af93b61fb13b4b5f41814d954 -> 4.9.12-x86_64-cluster-policy-controller
      sha256:23921ee4c51c3fc5b32598d89233353855b335cb1d3592bd4e14cda9b23aa0e3 -> 4.9.12-x86_64-cluster-bootstrap
      sha256:23e6126f81f2cfedbe7b1fe251bcbad4c2365ed7ee12576b67fc56fcf587863a -> 4.10.22-x86_64-aws-ebs-csi-driver
      sha256:2416437d8e05a2b87e52f705920c7759637cda53862343f7e8abd940e726d555 -> 4.9.42-x86_64-thanos
      sha256:251e26f57143f8862b25bbe62717c52df6e0f88dcc6cacd995f7aa377defe490 -> 4.9.42-x86_64-driver-toolkit
      sha256:251f6fcf7b079421f5ed480db37715ce1fc35e4bbbf859b3ffc449b84d8fd246 -> 4.9.12-x86_64-baremetal-operator
      sha256:253b4a7e89bee87c58d26ce52504750bddeb4b7a4bb8e5d6d9ffcad04880e288 -> 4.10.22-x86_64-openshift-apiserver
      sha256:2597f77e2a8757e8ff6e15eb5a7ab65113aa44f0382b9199c0f2553daac38487 -> 4.10.22-x86_64-baremetal-operator
      sha256:25a2301d546119b4dd68a21607c3cd79e85f56a675cf3ca1b77a53469e351464 -> 4.10.22-x86_64-cluster-samples-operator
      sha256:2692a98a6505e477113d815186214d6d5c15c0b5b83ef6d54a5fe11d49183704 -> 4.10.22-x86_64-installer-artifacts
      sha256:2698c26d141d45a1efbebdf019140d4e1e59711d6c90e0e7265e93167f1a6bc8 -> 4.9.42-x86_64-ovirt-machine-controllers
      sha256:2782057e05351081a8bc0f237736327eb5ae66fe8bcec14dec4a7613f366460b -> 4.10.22-x86_64-csi-driver-shared-resource-operator
      sha256:2787d50587694a64c146026cf9f764f8526599589504d9a43bbaa9d4bff796ae -> 4.10.22-x86_64-keepalived-ipfailover
      sha256:2799e3dd44e025ae0894f6c1f1933ce58bc0e6ce40fe356b801df2f604bdbdaa -> 4.9.42-x86_64-tools
      sha256:279a3393fbb0c344b410de5aed4702ae41c49382a5c271c5a6d915defa72c47a -> 4.9.12-x86_64-jenkins-agent-nodejs
      sha256:28ba84f0f4ca93e172e14f5d1dd726079cfbfe44e75dc527656818a6cebdcdaf -> 4.9.42-x86_64-cluster-openshift-apiserver-operator
      sha256:292d0fbd28892d4952e0cd914cb4b1eca7b5b31dad3ff26abbbfe3ee57ea24e6 -> 4.9.12-x86_64-cli-artifacts
      sha256:29b9bfab6b470eb758de7ca3a3cf11014acea8987cc81cb45baa5672eba339d8 -> 4.9.42-x86_64-vsphere-csi-driver-syncer
      sha256:2a04576d52958bcfda335d82768846f8869dcb8da004b4b6139ff664fa1b9389 -> 4.10.22-x86_64-gcp-machine-controllers
      sha256:2bd2b75e1e224c4c647285d552658e35462f27e72a0aff54e56191c29e887ea4 -> 4.9.42-x86_64-openstack-cloud-controller-manager
      sha256:2bfff62bcf1421f77b344d5425db116637409ea5e4d731db385e7d0b10e17315 -> 4.9.42-x86_64-ironic-inspector
      sha256:2dc0b7379974b02072e46acb69078174ca6c52b5041bee5431d22b3a48fc5a23 -> 4.9.42-x86_64-libvirt-machine-controllers
      sha256:2ddae06a7ca8031611b8ec5a63050195a5b747c66289b8ad810bc8da73aa58d4 -> 4.9.12-x86_64-ironic-hardware-inventory-recorder
      sha256:2fc2c9d9fae3070c00239ea3d0d1d9fa7477c99c296b17b2fc352794c535912e -> 4.9.12-x86_64-network-metrics-daemon
      sha256:2ff95508332a75a0b0b50be84416297781946d0feb687df36c8082baff564857 -> 4.9.12-x86_64-prometheus-alertmanager
      sha256:311880a014f3e61f2f11345bd63885d114c9324fcfc7096933e159da8bc2a8a7 -> 4.9.42-x86_64-aws-pod-identity-webhook
      sha256:316fbd6dd377ffd1e83a647ce9b610017d7b1ea007552ca1d0224ec576d08e58 -> 4.10.22-x86_64-operator-registry
      sha256:33001ffab5cba9aaff19e475e0118ea158c99797bf6f3e210832f129ac34d71c -> 4.9.12-x86_64-cluster-kube-storage-version-migrator-operator
      sha256:3353f58c8ad5b5a1a1e7e4a9acf815b39de690e673ad09bc2c56f1ca682b8a1b -> 4.10.22-x86_64-powervs-machine-controllers
      sha256:3555186adf6d0d79b9edf02935eb49ae8f61084c5117ff3e2066556caaf7ca6f -> 4.10.22-x86_64-baremetal-machine-controllers
      sha256:36b41684d64f1f0ff932f994d8d37b96c91c19f1b725b97bbb31b5faa3656eb5 -> 4.10.22-x86_64-prometheus
      sha256:3727b82ddb1569db2d1302a32dfc08cc331ce3bd0455ba62c2f51d5c67aa43cc -> 4.9.42-x86_64-prometheus-config-reloader
      sha256:3730bafc9c8a397887d4213b220afffe11cb6835ea5754e60a0cfe967efbdaf5 -> 4.9.12-x86_64-prometheus
      sha256:379cf019a7a78452596263ceea1adf98714dd8a985f09531c029560526a6b15c -> 4.10.22-x86_64-csi-external-attacher
      sha256:37e092de518988e67fb60bc5eaaa365058b6be6a1877613af5d12e2c4a2ec129 -> 4.9.12-x86_64-oauth-server
      sha256:39321117eb9d93b64cb7a20e04dae962558d8cfbc09a27beb2d209c467783300 -> 4.9.42-x86_64-csi-driver-manila-operator
      sha256:394f7d2a9c57088142575bdcd3257647666017e05888ed00966b921eb877ad43 -> 4.9.42-x86_64-cloud-credential-operator
      sha256:398becea0e9d87e94ee42d6a5e3c2afc2fbfbfab11394a61c03957f69ad600d1 -> 4.10.22-x86_64-cluster-openshift-controller-manager-operator
      sha256:3b43158218bc54a73c810aa46e312043a9873de2096a35dea3ebe180cf6f1219 -> 4.9.42-x86_64-azure-machine-controllers
      sha256:3c1ed18541701e1dc2e579498593e197dc6dedebc1ecd8f5971ad92ce5ea2ded -> 4.10.22-x86_64-vsphere-csi-driver-syncer
      sha256:3d7baec9b3c2420262ab20c53ba51e5e6aed8272173fa7726a86becda0045be7 -> 4.9.42-x86_64-kube-state-metrics
      sha256:3e014c76462408e7a8ffbf4000dc726e533efab4ab56b71a4d62aeb401b9478d -> 4.9.42-x86_64-csi-node-driver-registrar
      sha256:3e4dd326434d115808c7bff14d8a38806c6757d928f2359737496fe6a32b7b4e -> 4.9.12-x86_64-cluster-kube-controller-manager-operator
      sha256:3fa5ec3c07257003575c63f218236b76eb0a3175fd206eb9c6abb32b925b15c4 -> 4.10.22-x86_64-cluster-authentication-operator
      sha256:3ff865b0ea29f4e39c57454089553072cc4a8139742ae5fc28b348566237a15e -> 4.9.42-x86_64-aws-ebs-csi-driver-operator
      sha256:40302e29af4c6e3e9a6e73544a2fd4239da691b0afde937e76d078a642d30817 -> 4.9.12-x86_64-service-ca-operator
      sha256:406384d7815dc29c85c903318b1a98a2f3bd65b440a9344b71d6ddfa3e98c287 -> 4.9.12-x86_64-ovirt-csi-driver-operator
      sha256:408df31ea13f536abf7e25500e0127695659f13078633b302d7fccfdb29dbb6e -> 4.10.22-x86_64-csi-external-resizer
      sha256:41015da925f8818de1ce275e691def3736785cdd089b296335f50a96ee617656 -> 4.9.42-x86_64-kuryr-controller
      sha256:426f99588fbce292824dba75372675b83daea64a1cf5d321fb5e4182fc43867e -> 4.9.12-x86_64-cli
      sha256:42eb156593fbc94fa12eaa5938c8335d500e222be370475ee19a57ad7f7f42a8 -> 4.9.12-x86_64-mdns-publisher
      sha256:43150eb3f35005a1c04fa4ed6fef3c8cd3199991658a88530d28cd1a32a5597a -> 4.9.12-x86_64-aws-cloud-controller-manager
      sha256:4446e75f22adf0e091bb337da46174ba7e82b394da5b04a2dc9c60b749c7cd02 -> 4.9.12-x86_64-vsphere-problem-detector
      sha256:44fca8b4f5efd9e4d5980dfaf4eacaac48e2eeda34f4cf79508199379df3a811 -> 4.10.22-x86_64-network-tools
      sha256:463108ebaa4ca29cd7f569229c62bec1797932a33fb03d21d81aff733c0ead06 -> 4.9.42-x86_64-docker-builder
      sha256:46d7b63794f6ceec9fbcc63bbf170aff5ee3152ccb24ba51b8167ac59a30ca6a -> 4.9.12-x86_64-cluster-etcd-operator
      sha256:47148ba7fa9873fa73dd41dc4cf1b0106f7fe6a6396c5ab18c790f5f99bbc479 -> 4.9.42-x86_64-grafana
      sha256:487e545cc51baea2b87b5b7f21cf4beb610ff81baf367f3db90601e06b4a9314 -> 4.10.22-x86_64-kuryr-cni
      sha256:4961daab3005237fdc1283eb091882b6a0b589ef21faac2ad92b4927ada26296 -> 4.10.22-x86_64-cluster-network-operator
      sha256:4b14ffcea52f9eb5952546634cb26bfb1d523a4dd81382021c71673fed91efa2 -> 4.9.12-x86_64-operator-lifecycle-manager
      sha256:4b56346c5c3c09eee6d8d37b6796f1e4f15e5a8de53b2afc6a4d3e6fb780ee24 -> 4.9.12-x86_64-csi-driver-manila
      sha256:4b979717b90adb7f1758e00403495f09a24e53bc537dbd2eb69398b83e3d8819 -> 4.9.12-x86_64-kube-state-metrics
      sha256:4c6a585c8a4f3944ab010bb89ab4fc89cc87ba9ce5428a463cfd68c9a5649f48 -> 4.10.22-x86_64-coredns
      sha256:4d6235bff40e083e019159a980dc4120d550b558cff2b942c67938cdbfd2f229 -> 4.10.22-x86_64-machine-os-content
      sha256:4dba958914fb7ecd4f656cdeb8522ac300d639766e6a4f50ed6155fa2ca4f93f -> 4.10.22-x86_64-jenkins-agent-base
      sha256:4f4021f6a725ee1bc3c393535742b720ee2fc5ffc978849a2b67fc437debc283 -> 4.9.12-x86_64-prometheus-node-exporter
      sha256:5085b404248def7058806ad61102dad09a02208777bf15fd4e275c435bd6031a -> 4.10.22-x86_64-oauth-server
      sha256:50e0b3eced37efe7c94f746e0260d4750144962ef16ab334b93c08b3635f946c -> 4.9.12-x86_64-csi-snapshot-controller
      sha256:513942eaefd7af0308ba02b9406f7ae297eb24dcec02352a5d8a82ac7e77a12a -> 4.9.42-x86_64-gcp-machine-controllers
      sha256:5186c0764c9a29f87be66cf3e7cd41734490a91d7fcc15821c072b9dc996d2cf -> 4.9.42-x86_64-container-networking-plugins
      sha256:51f2735e19fd2b371259344c71999b663e335026564450820cb542ac4a72f229 -> 4.9.42-x86_64-mdns-publisher
      sha256:5337e31027996dc21f87b80255fceaf4d57e29453f139a1007b59e36dd60ca6f -> 4.9.42-x86_64-cluster-cloud-controller-manager-operator
      sha256:53501ab17ffbc07722ee37dea9a4b88d9e52c0a8d3117510ceb6073ea38c535f -> 4.10.22-x86_64-csi-snapshot-validation-webhook
      sha256:53757499f08e902462edb7e55b0a6c52d1ed54e495defe309f87493c1b35981d -> 4.10.22-x86_64-cluster-version-operator
      sha256:543dbcf59475f67ec1fb2c29e8c88ad067e35cda75835924719a2619e0348619 -> 4.9.42-x86_64-kube-rbac-proxy
      sha256:550304f5ac4929e68b97108da732b8271997dee09db91a2d4fb55d3b143ad7eb -> 4.9.12-x86_64-aws-machine-controllers
      sha256:56a1dcfe82cd3aa9ec44bdcc77df93163faf48bb5d1a38d4f023d48a34c8cab6 -> 4.10.22-x86_64-cluster-node-tuning-operator
      sha256:56a44e0af6a2bf9ee10c53fdcce4aea413e3376269d72db6d4dc966e3ac4ca4f -> 4.9.42-x86_64-k8s-prometheus-adapter
      sha256:57566ceabcb75dea3f46c47f05f5615cac1dfd27df86511ca7d7c5937b2f91d8 -> 4.9.42-x86_64-aws-machine-controllers
      sha256:57c07728c6c522e476fb56b6a75fea063f8d5b71b7ed90e16f95799c6e70fdc7 -> 4.10.22-x86_64-cluster-etcd-operator
      sha256:5876036a0b15e58566fd0c9cde4c67b4c0d1dcdc56ee743da690dd0b89579d90 -> 4.10.22-x86_64-openstack-machine-api-provider
      sha256:5978c108971458d12f6817c4ca35c26e5269bdde3d30cc02868871020d87b77e -> 4.9.42-x86_64-multus-admission-controller
      sha256:5982ecdb04297b4c546da927abbbb9bdaf772737e794218c20b43a5d4ede9d22 -> 4.9.12-x86_64-k8s-prometheus-adapter
      sha256:59db09bc04d2e7439ffbf51f7cd9ba9b9cd9f27961ad4f455e2fd017747da170 -> 4.10.22-x86_64-jenkins-agent-maven
      sha256:5a947cf105f932656f89e0a31973fec637a756ea0a66bea80fb623c37a91e7e0 -> 4.9.12-x86_64-installer
      sha256:5c4039e2846f73e3703f63a2aa52a27a85d8c3c9c3a076e05e4aac33301a5715 -> 4.9.12-x86_64-cluster-autoscaler
      sha256:5c58858e1e9cc46a44e569dc1cf4cc770a1fea4407b7004848655139f353fca6 -> 4.9.42-x86_64-cli-artifacts
      sha256:5d769932729c5b197e0e16865190f0b58a02310e87ce69ce1d4efa6bcfd1d2df -> 4.9.42-x86_64-kube-storage-version-migrator
      sha256:5ddbfeeebb4766d887060f549371b43bb2a948c314ad680a76028878c69aa5b8 -> 4.9.42-x86_64-cluster-config-operator
      sha256:5e33f9d095952866b9743cc8268fb740cce6d93439f00ce333a2de1e5974837e -> 4.9.12-x86_64-kube-rbac-proxy
      sha256:5e900c1991f7c9390c01bf32218263ec5539ff0942f35fff880173d9916827c1 -> 4.9.42-x86_64-machine-config-operator
      sha256:5f19d8c0210061111f218922bd3e7fbc168a0dec297d8a7a9dabc62a424f9cf5 -> 4.9.42-x86_64-oauth-proxy
      sha256:5f3dc4af08738c4ddf7ace3fcbea0d5f8d10321414fe2681f3a056ee55c8e969 -> 4.10.22-x86_64-cluster-monitoring-operator
      sha256:5f55a0c0979ef0961fef7f267d2ce56c3f05f1dae8d8dac8811f68cf2811abbd -> 4.10.22-x86_64-ovirt-machine-controllers
      sha256:60053c28babe9ef993c1cfd95edc98ecb8a51bef3d0b1ff2dc837cf73eec8e4a -> 4.9.42-x86_64-deployer
      sha256:60e75fbfcb2b40ec07e20a05fbf833d051da3ecb520327f834754bc5d3093573 -> 4.9.42-x86_64-kuryr-cni
      sha256:6119a71fbf48dc417da82b53e2f7fcc39b72689b0cd79377433239ba6d755f79 -> 4.10.22-x86_64-cli
      sha256:6144ef2fd62755010372cc403356fa4dd3254c7fa3bb7b6c56901eeb76d6e570 -> 4.9.42-x86_64-hyperkube
      sha256:61c10d04162b4e97d0493b0a7567810204c06e22b6115ac8190c7aa7c11cbd8e -> 4.9.42-x86_64-operator-lifecycle-manager
      sha256:629b4771d02bedec74330d1e7ab8f1a166a15811a92bc4f5739515df5471139e -> 4.10.22-x86_64-network-metrics-daemon
      sha256:63149966fd4008c90409477bd30490445d8f11dd484009fdcbd665237d1e85a9 -> 4.9.12-x86_64-multus-admission-controller
      sha256:644b470e3cc0e417c8766bdecaa4944ded16267a542fc93d477b364a6905fbe7 -> 4.9.12-x86_64-csi-driver-manila-operator
      sha256:645d0885041eec1a0660def5a541f785f545f2056b642b38bc352519fc3a0a1a -> 4.10.22-x86_64-prometheus-config-reloader
      sha256:647c51744df244d04a0d288b022278152ed4d91ec23788fb331d0e1b3abc0115 -> 4.9.42-x86_64-openstack-machine-controllers
      sha256:65e6a5433b03f5be3e11798c4b6874ecc30bc81535712c240d9aa282e00228e5 -> 4.10.22-x86_64-service-ca-operator
      sha256:65f83e849f9cb76bad217bceee4e34a30d062e17a2a1a80237462d26d98e8870 -> 4.9.12-x86_64-gcp-machine-controllers
      sha256:66996ef32742e67e768985c759f248b5af8b503403c9844e535f3a1ee4c24c07 -> 4.9.12-x86_64-openstack-cinder-csi-driver-operator
      sha256:66bf4dc3d0a3f0559c48d7dc7b9b45af7c90e5b6be704421362645d225c09cf8 -> 4.9.12-x86_64-azure-cloud-controller-manager
      sha256:67cb844c3e059ef41d746d6132ff1a3cf4240bf959191fbf31a680461b95397f -> 4.9.12-x86_64-vsphere-csi-driver-syncer
      sha256:6885e79c6ddd2c593b39a13630c75f1af6351199940973b24f93e9b5e795cede -> 4.10.22-x86_64-gcp-pd-csi-driver
      sha256:68fc721338cfa4de2f37b117f45dacd22f763171ae69ade6beb1c8c44a4f91ac -> 4.9.42-x86_64-azure-disk-csi-driver-operator
      sha256:6a78c524aab5bc95c671811b2c76d59a6c2d394c8f9ba3f2a92bc05a780c783a -> 4.9.12-x86_64-cluster-image-registry-operator
      sha256:6c7f41cd2202ff4a687baff9133a1ccba8e77311b96cef2a781831353db90be4 -> 4.10.22-x86_64-kube-rbac-proxy
      sha256:6e2a44337dbe5bf89d6f0f16124df38bb86f71c866a9ae38badb450e58e7f42c -> 4.9.12-x86_64-operator-registry
      sha256:6e68ba361e31be5dd1b3ecd0fe5318b337b1915d5db2d8b635cb6f8bb8ee7f97 -> 4.9.12-x86_64-machine-api-operator
      sha256:6eb2d82ed0bd65de1ecca019b2483b01ade5954b79d53c342449f8fcbe77044a -> 4.10.22-x86_64-cluster-policy-controller
      sha256:6ec37f69340d4095be09158eef93fcfb30697728538630b54860781280eb6623 -> 4.9.42-x86_64-cluster-kube-storage-version-migrator-operator
      sha256:703dcc99ea8b944b99849eb90762ca5ed7469240dd9a7fcfb6b61086da81d670 -> 4.10.22-x86_64-deployer
      sha256:7126d7321ba855bf24aa4e7e03ac92c5ecacc19ad567ab6fbd3b3e2b2b991e4f -> 4.10.22-x86_64-telemeter
      sha256:716d6ccc60f834876eb7f9b3e5e65300e8847a292ac95261cd5d939e2733d25b -> 4.9.42-x86_64-service-ca-operator
      sha256:717c4a3cdc726abfbb1037a2a62fa3566e8575a05a328001e39e14d9677f5d8a -> 4.10.22-x86_64-docker-registry
      sha256:71a76b1a54d9b9ccba384c353db5777f4a3b37fff2c7027225bb534d09565615 -> 4.9.12-x86_64-openshift-apiserver
      sha256:72929100430eecec345ef9b3b6697dd55fc3c85a3d1dd8d2a2343d46664c0805 -> 4.9.42-x86_64-prometheus-alertmanager
      sha256:72a75a1eecdfe8e42703fcde0bb63c1f5f256dd21dba92bc36edf54e9f2a845a -> 4.9.12-x86_64-cluster-kube-scheduler-operator
      sha256:72d072f33452fc8c5fb0751ac3743688cc4d44959654bb0747dad000218f6f05 -> 4.9.12-x86_64-azure-machine-controllers
      sha256:7325ff1fa72f33b5c9cc5227aee8727e4f1a6202fcc6896ffac7eb1ee486a344 -> 4.9.42-x86_64-baremetal-operator
      sha256:7411cfa7110c6ee546c74ff111fa32311cb4b43e335c7f68be5d39491c3ddafc -> 4.9.42-x86_64-cluster-policy-controller
      sha256:748fbfabc432acef10b37e564688ba5e0fe934361ecb1913e40fa4d34cdd6bd3 -> 4.10.22-x86_64-egress-router-cni
      sha256:7509bf36391bae3c2c0c6f2619bf57615bd9f417ce8befbfde0fa320eba7c67f -> 4.9.42-x86_64-cluster-kube-controller-manager-operator
      sha256:7680ef83f71e2e5d646d8fb067dc6409b83efe178c3c9a44f9ec40a8477e937a -> 4.10.22-x86_64-multus-whereabouts-ipam-cni
      sha256:772edcafbacf208e6094b25f0ce924f5e3c9651a611dd28e8493f1ce274d9874 -> 4.10.22-x86_64-ibmcloud-machine-controllers
      sha256:777111fc99844b71b012b69c6f67cf73a60c76be351943240a9169fcf00c45fa -> 4.10.22-x86_64-configmap-reloader
      sha256:78f03ca61a0d1fb66388b6fe4729f395a23fb032ed145612e115abab7b5ac7d2 -> 4.9.42-x86_64-openstack-cinder-csi-driver
      sha256:79c8e38ac956a6be5ba8dfbefc10e30ad0b5d3229ea8951fd9745b57fa5c6a90 -> 4.9.12-x86_64-cluster-autoscaler-operator
      sha256:79cfdf0364d05a02840622bf801f80c5832ddad8afd820b323f95b3f96b57354 -> 4.10.22-x86_64-cluster-image-registry-operator
      sha256:7a0ad0b0acbcdef440367c985a2e095a10de23e2f22b0527c5b6b78022755f40 -> 4.10.22-x86_64-installer
      sha256:7a3376e6677d457d54e90d9e4b0b34f26ed4e406065c378005a9408674506527 -> 4.10.22-x86_64-cloud-credential-operator
      sha256:7af24fa22ecda762a762a0afe1791032f799777fa2153448f11ec9803a220cdd -> 4.9.42-x86_64-jenkins
      sha256:7b2c006c283e3e427f9e613bdc5449e0c8b1fd4c09d20845601719edb7ab8b9e -> 4.10.22-x86_64-azure-disk-csi-driver
      sha256:7b90fbeebf473c2e85acc23bd116c3d09750377f32a800ae09caeae101d34957 -> 4.9.12-x86_64-vsphere-csi-driver-operator
      sha256:7bc4f0c55d6e8d92cb429337759824f8535e56a172e75b8c82959a4f4f270ab4 -> 4.9.12-x86_64-azure-disk-csi-driver
      sha256:7bc7260f7921fbd5a9aebee70ba0fbb822f0e0247b04ac64fe13c7af8d941c90 -> 4.10.22-x86_64-cluster-machine-approver
      sha256:7c330b88fb2d0bf06b02892745c7fc3096b6325a49c854f3a7d80a87048f6401 -> 4.10.22-x86_64-alibaba-cloud-csi-driver
      sha256:7cb1aa390a4ce1b9c733f2aa508b463fe96de104aac245c1d068ec3f113d8b0b -> 4.10.22-x86_64-prometheus-node-exporter
      sha256:7d22ba343e3a9ded6578f4633d986e10da058ed66065934f99e1da7540e0b085 -> 4.10.22-x86_64-vsphere-csi-driver-operator
      sha256:7d78a787b5655292d4440942ae018ad5d1c985881c4e9d95d887d4f8450c7899 -> 4.9.12-x86_64-csi-livenessprobe
      sha256:7dd4fca1f03f65c7837e3b3420d1df10ff612ac36f5f1e8ecbec0d6fc0c49659 -> 4.10.22-x86_64-grafana
      sha256:7f734c4f3a69377a667c97ef00642accf60f2f0020ab89531eea3d650f1a89ca -> 4.9.12-x86_64-ovirt-csi-driver
      sha256:8093764baed315a2e2147f7fe8666c39bb9ed1a075f1afafa8d77e3a2fc435af -> 4.9.42-x86_64-insights-operator
      sha256:80e193b9e36e7262afedbaf1d6608d542eb1614187a12d3720c506531ac57ca2 -> 4.10.22-x86_64-openstack-cloud-controller-manager
      sha256:810097f053d1859d516ab784d975d41ae435ee91f5eaa7c90a02e643620c18fb -> 4.9.12-x86_64-cluster-network-operator
      sha256:810f6f325a635003b9a3503aa4d080063698e9478637ccbf7de4ef99b4b4787c -> 4.9.42-x86_64-sdn
      sha256:812af35a109e30a2bd2a084d220894fc14b59bab2f58d28b616d3796fba809b4 -> 4.10.22-x86_64-console-operator
      sha256:82158554ee3c838c24ac3d894d5c92a30f9e6705e6e17520115a5edd62a2bf75 -> 4.9.12-x86_64-ovn-kubernetes
      sha256:82bfc9ea845d7948c362061afa266d7e3dc1465cec041371e99cb1bcb5056036 -> 4.9.12-x86_64-deployer
      sha256:82db40fc70d17ceaefc83ecab75a134b5bfb44deffb343d2868a2f17c5b82302 -> 4.10.22-x86_64-azure-disk-csi-driver-operator
      sha256:83937cc370e3861482659c58814a865641a17f0b6427d28aa40118d7da10ea98 -> 4.9.42-x86_64-vsphere-csi-driver
      sha256:83bc064c1957b18d231d0befd2bf14bcbadb3ebe20ff3b21dc4b23f048aec547 -> 4.9.12-x86_64-gcp-pd-csi-driver-operator
      sha256:83fc8253611fc117513c9b5381d1849b3fe57d24300618cfc7def83fdc7fdb88 -> 4.10.22-x86_64-openshift-controller-manager
      sha256:841a035526ab33d076bc785dc1c430bde77d773765f2ef2e4a33452e01d26ab3 -> 4.10.22-x86_64-cluster-config-operator
      sha256:849f33260d8cdd4720630ed86c46beb9ca76cbe83982fe04073090f69adf0616 -> 4.10.22-x86_64-ibm-vpc-block-csi-driver
      sha256:84be7ff78010f7a6127ee6dcc13082fff11afb502e18ebe18e258fb3f5ece84a -> 4.10.22-x86_64-console
      sha256:84c0418d83452f9815d6c2a9dbde03f59c087b203ad507fbeccc832936aa3eab -> 4.10.22-x86_64-oc-mirror
      sha256:84edddb4322cfe50d8545c41014a8f5595a4260a20d9a269e144909c5949582b -> 4.10.22-x86_64-driver-toolkit
      sha256:850073672505d78f5868f0c90956e0117bea48d1493ad801be77ca657677baaa -> 4.9.42-x86_64-openshift-state-metrics
      sha256:8524c9458dd9517ae6d72b447c8c822ee3960b0bf660c6388d046b0ed8f3704f -> 4.10.22-x86_64-csi-livenessprobe
      sha256:85fe6f78cd27adec4c597d06e6f681f807d5582b1c7cd121de26351f3ccf1e7f -> 4.10.22-x86_64-azure-file-csi-driver-operator
      sha256:8669914f068501bdb755e484418b49dff9bc6eeb9bb07e07bf7e2e261a7496b1 -> 4.10.22-x86_64-csi-driver-manila
      sha256:86a84e949d6d3890844e7a66a23d6cf4e2290c1b26c380fe57d4ee11e6f8f60c -> 4.10.22-x86_64-ironic-agent
      sha256:86ff76fbc90d77b16c42c0022c72d5b71102ad52e186a654af7a3cd1c8b4dda1 -> 4.10.22-x86_64-ovirt-csi-driver-operator
      sha256:87a166dd8f21d8ba612ee0f49dc8839cb222f77156dcb67ed82e1c3ed13127cb -> 4.10.22-x86_64-container-networking-plugins
      sha256:8886eb8d0f279a077eb667c59488cae3154a8e4f92be25e92df8590e7d9e62df -> 4.10.22-x86_64-csi-node-driver-registrar
      sha256:89ac84fc71b4eab3168fae7f5509996e4414938f63a2097f9ff96d700ef4e366 -> 4.10.22-x86_64-csi-driver-manila-operator
      sha256:89dadf5373c933540b4e039a615448c42d0ce0f21329c05ccb62bc43319a8c63 -> 4.9.42-x86_64-prometheus
      sha256:8b2d4b65d1eed177da19d92889a7258e9c01bb68f2ec678e5402163961f3a61d -> 4.10.22-x86_64-kube-state-metrics
      sha256:8b53960d0d4a9b265271c5ad42427cd3db289dc783187827646e45d58e53f35e -> 4.9.42-x86_64-gcp-pd-csi-driver-operator
      sha256:8b6857a6b10bff8e941a8c51ddc2f22a821f42fef25e4ed9a7024b1de97a2000 -> 4.10.22-x86_64-multus-cni
      sha256:8b75cbb25d16e48afd464eeaa6676ec6a181a1b5db455c57f5f466bf4c7bd319 -> 4.10.22-x86_64-pod
      sha256:8b9a92276f84629472c0c9ab379d1e7da6a4a1638d4d2291acacbdb1dfaeda3e -> 4.10.22-x86_64-gcp-cloud-controller-manager
      sha256:8bbd804da57f3f961ce8cf8057ac7a946166df966bad4bca3295eeb16eadde5b -> 4.9.12-x86_64-libvirt-machine-controllers
      sha256:8c00e6df3b34edfdf8c3bbf4d23b39fa91de238d322f657dc4d8341f3db77e70 -> 4.10.22-x86_64-aws-cloud-controller-manager
      sha256:8ce4be01db73a940acf798ea8102dc06f340f3afec470a3af72abfe130114de9 -> 4.9.12-x86_64-prometheus-operator
      sha256:8d5baeeb8f14ef270bc38fc19d0372bc6ef2af96ebdcc6a1f7b136c08f891a70 -> 4.9.42-x86_64-multus-route-override-cni
      sha256:8de1995bd51c96068885d7b6f02a92423672f5450060af4d39cab662313a2069 -> 4.9.42-x86_64-prometheus-operator
      sha256:8dfd080452dc184ea0687070d9cd1f00702557c91ec64e503da2e0924c7dddec -> 4.9.42-x86_64-csi-external-attacher
      sha256:8e243e867e7ff031336cc93b9925861d29ec2dd7d1ef6ccea6b5b64b304421c3 -> 4.9.42-x86_64-pod
      sha256:8e8ccc0bc92245b24afc762c98b24ecbf24a9b28a7d4fe497de790e761633411 -> 4.10.22-x86_64-k8s-prometheus-adapter
      sha256:9053faaf26e77ee41b159cc91af3bfdc9e76c5d707e5b4675b462d1be45af267 -> 4.10.22-x86_64-prom-label-proxy
      sha256:91181c9e54a69e0f377710947d601624b086e51d70a4989e20b201479e0ea23d -> 4.9.42-x86_64-cluster-csi-snapshot-controller-operator
      sha256:920de3b037df8e9649a4a0e50f60507a9a202fc9de12f3a7b95748ca89829684 -> 4.10.22-x86_64-vsphere-cloud-controller-manager
      sha256:9226a6ecf2937c5a4f97d9f64f4e837b1e70fb2daa0f934697a273123c602bbb -> 4.9.42-x86_64-installer
      sha256:9226cfb2134fcff2b646be72f46f7b7dd2ebef64f1375906e8e44859332f2d0d -> 4.9.42-x86_64-cluster-network-operator
      sha256:938310aeedba130b66204b062b7d86d562bdff2d6767fd643176cea3754e0d37 -> 4.10.22-x86_64-vsphere-problem-detector
      sha256:94145128ec7805e478fada43a8a8ade8054fb50b56c97f159fe9da28a56851c7 -> 4.9.12-x86_64-telemeter
      sha256:948def25e5c80edcc357064d740d78b97bb8fa4f940f13d0b501714c7edf57a1 -> 4.9.12-x86_64-console
      sha256:94926d57aaefa23376615302a14a96d4bca45dd6c3184a8595042b0ae847eb1b -> 4.9.12-x86_64-kube-storage-version-migrator
      sha256:95acdaf15eecdf12d3ec5bbef5df9d6231d425f96272133fc2a8cd2a610c76a6 -> 4.9.42-x86_64-cluster-bootstrap
      sha256:961b30e0ab62cf04d000c1eb8330e62ff2daf4545ee094d3c9acd4c443e3c5d2 -> 4.9.12-x86_64-docker-builder
      sha256:96487fbdd3efaf1ef9c8e34bd17470095daa3dd01bf9c5c42e5c67ee99b21bbc -> 4.9.12-x86_64-aws-ebs-csi-driver-operator
      sha256:966597f3bca51e0bc7f33b5892bf929c37081b8fe3812e55d82e353d6603aa6f -> 4.10.22-x86_64-kube-proxy
      sha256:96a0a30df8f3282fef2a206b6af6b35df455128f654345ee6321bd8462defc28 -> 4.10.22-x86_64-openshift-state-metrics
      sha256:9716b0f99225f7aa9680a8f07fc02c3a8233b010acd332d7c4651d5e0177de3f -> 4.9.12-x86_64-cluster-monitoring-operator
      sha256:97253518289360f50c3de3be8bf508fadf50a18935e7990d2bdde658080fdb9d -> 4.10.22-x86_64-aws-pod-identity-webhook
      sha256:978956809bd9364261b44492ad80e9bf5e8edca14d2e151df084ac9517acc9fa -> 4.10.22-x86_64-aws-ebs-csi-driver-operator
      sha256:980d27d0eb312864f4dacf8f125c7a2ec6d507817045517ebfb80ea25601e1f6 -> 4.9.12-x86_64-cluster-config-operator
      sha256:9957600c01e00a5ff2655e06432828c001690ea3b71455ceaf6cc72b5f97f09d -> 4.9.42-x86_64-must-gather
      sha256:9a84061655f394751c2fabce9b06cf34e62ab2c26b09bebeefb4fec0dade8608 -> 4.9.42-x86_64-cluster-node-tuning-operator
      sha256:9b7d51b5c05960e9d3a89535038a622a419bfb12ba3742e1a075c898515f543f -> 4.9.42-x86_64-prom-label-proxy
      sha256:9be5534ac9273aed86607cd583bb8beb6bca0a03c4c49a29024f79a7fbf0966e -> 4.9.42-x86_64-cluster-etcd-operator
      sha256:9df074e84d628156a71c314708d4cc003e020d3e4948926e30654b5eb0c2a958 -> 4.9.12-x86_64-tests
      sha256:9e2d384be6ebe5fc9c089ab3ed1e1f082c180aed520ee4ff306e46fa56c12728 -> 4.9.12-x86_64-baremetal-installer
      sha256:9f56d95ed665c1442fb4ce0b6415dbecf17729372527c46570aee5c372282a1e -> 4.9.12-x86_64-openstack-cinder-csi-driver
      sha256:a061f390054dfb0869ef862f9cd6160af4e6c2cae2214629fd4145ad68692d82 -> 4.10.22-x86_64-prometheus-alertmanager
      sha256:a0fb9e3fb93589d705b19eb89fc91684da73ac452044444d8aeb93cb6ef84268 -> 4.10.22-x86_64-ironic-hardware-inventory-recorder
      sha256:a28ead61f61b63d332b7e341f25f714527995045cef5a32581fcc42f614fb23a -> 4.9.42-x86_64-multus-networkpolicy
      sha256:a2dec8e4f0a0d3b36c6fdff11e7514f5a19246389742085a658980e416fd3ad0 -> 4.9.42-x86_64-ovn-kubernetes
      sha256:a3f4748f93cda0314bd9428454330e7faae81fec59d6efafdb89b4e6dafaef5f -> 4.9.42-x86_64-baremetal-installer
      sha256:a4901af87fb7ffe48587e7ea262396ed49c2a0b0fa0b7234457b0c665d472436 -> 4.9.42-x86_64-csi-snapshot-controller
      sha256:a4934b12e3698a384826bae660ebfb84a30f68e0519fee949ebeb4f4074f26c6 -> 4.10.22-x86_64-alibaba-disk-csi-driver-operator
      sha256:a4b8af157bd962a43aa8d44707743a6d743b70faeb72548c5a91869a50ae6b5e -> 4.9.12-x86_64-thanos
      sha256:a4e51611597318e86a82389b481f49a0978d5deb403f705b14346161c0b62104 -> 4.9.12-x86_64-console-operator
      sha256:a52fdffb6362ee6ca2b52f66281d41add5ead8cc11d35407c2f621dc7ce5ba4d -> 4.9.42-x86_64-cluster-version-operator
      sha256:a6322f222f98adc1585ff8777c73140a56ac3cdcd8a6949309884b79496bcbb6 -> 4.9.12-x86_64-sdn
      sha256:a65aa029e51a7fe7b597bac47310290cff8eba6fdf053dd33cb286e99406fd62 -> 4.9.42-x86_64-console-operator
      sha256:a85d093e161ccaf88e4f28085be64bfe2ed88b4beee7492dae02b776fabb53ac -> 4.10.22-x86_64-cluster-update-keys
      sha256:a928705d99ccf81cfb7c0ef07a515f67a488d7052a2d7ba34ddabaa140295d78 -> 4.9.12-x86_64-grafana
      sha256:a9743100d96d1f764933474c7389ef89220e6ed53c006ab055c0721dc19c7f1f -> 4.9.42-x86_64-multus-whereabouts-ipam-cni
      sha256:aa1556eaf6dae00fb8bb34c495faecaeebb9e374176f8e01ca46de025e63f2f8 -> 4.10.22-x86_64-azure-file-csi-driver
      sha256:aaaec3417207330eaeb203f459bdd819c27bac1956f1d9f0684f42dc5a3807e6 -> 4.9.12-x86_64-cluster-update-keys
      sha256:aaf0854244f356ccb3f1cd70356362ac5ef7a9f227e08128740306430fd75497 -> 4.9.12-x86_64-coredns
      sha256:ab592552bb6f276206acfe2d930a375a6469773613a0c7337a4f318c167cdf45 -> 4.10.22-x86_64-azure-cloud-controller-manager
      sha256:ac9c793e65afe8cc6f2829ae8c463c8e7333c4a0117096944a9a80564fa2bb6d -> 4.10.22-x86_64-openstack-cinder-csi-driver-operator
      sha256:ace4bc2c5421ffc6362853723567bce6858b1c757b8b71511320990adee1b02b -> 4.10.22-x86_64-azure-machine-controllers
      sha256:ad7e0cca53e098a448f9d5df9ad5410515ee1ed3c83dbbe8ab72fc8ae73b986c -> 4.9.12-x86_64-kube-proxy
      sha256:afb89b5f8a7cac640f0d349f213922f25cce9237dfd8ae8ad5c43dc6ee6785f1 -> 4.9.42-x86_64-console
      sha256:b03e939e5111cc9f796f9077355101911df3e97d43185dd56c40ba6f0b3dfea4 -> 4.9.42-x86_64-prometheus-node-exporter
      sha256:b134ad08c091eb3e66cb528b7805551fb5194e2efbb4121423465957a7399357 -> 4.9.42-x86_64-cluster-openshift-controller-manager-operator
      sha256:b3c16fcd503c6284ac809dbbf3a15f8a8c7f391483fcb73b1443b617afd46e13 -> 4.9.42-x86_64-openshift-apiserver
      sha256:b3d4cac3b44228c42bd17ce31a85ce246349d169801107d2ac2184d44ef1f387 -> 4.9.42-x86_64-egress-router-cni
      sha256:b3f241d18f0bacf3129b12775b4269fc22a6c4173d0862fcf96bf9f408effa57 -> 4.10.22-x86_64-ibm-vpc-node-label-updater
      sha256:b479b31b5689e31e6285c2b7e3235d82c0742acb40cbd2027de702893930e596 -> 4.9.42-x86_64-ironic-machine-os-downloader
      sha256:b5009dd56c6f3b5563d3b6f0134725e2535aed9082953cfc718bfb42443d0a6a -> 4.9.42-x86_64-haproxy-router
      sha256:b52ed323365b45ceef82fc63e0e6723de825c6b1345f4234cba07ecd28aeeb24 -> 4.10.22-x86_64-image-customization-controller
      sha256:b5bcc1882b9afe3fd26f3e68da2cf1b72cf7f31850dc634f0d38c1515d8c4092 -> 4.10.22-x86_64-multus-admission-controller
      sha256:b7356398de214f471cc2ff18b0b679887504693ebf339a242bc0d2a5235c6a23 -> 4.9.42-x86_64-baremetal-machine-controllers
      sha256:b85dffdb9f64182cf2f318e3d73d5a476d87ad36cee3fff6af99cd2f77421502 -> 4.9.42-x86_64-azure-cloud-controller-manager
      sha256:b8ac372155423a0cd13cf01beda810868f557942da2fb4f95975f95c4a2cd300 -> 4.9.42-x86_64-oauth-apiserver
      sha256:b91889b113e8dfef3993f4635ab3f7176f338f181ac32c15d7b21de30bcb4a60 -> 4.9.42-x86_64-operator-marketplace
      sha256:b954745858c6b14b1d58c178d9bd1c444577d2e56ab9f93bca2c29f4f41a63fd -> 4.9.42-x86_64-ironic-static-ip-manager
      sha256:b98f4e019aa3cae1ac333a8241bafdbe52caccfcdcd7f640d1a7410dd33dd788 -> 4.9.12-x86_64-multus-whereabouts-ipam-cni
      sha256:ba0fedee256543851cf903f0732f84fe1aaee5a022c7bdf463821c3891ae02ec -> 4.9.42-x86_64-vsphere-csi-driver-operator
      sha256:ba88a2ea6c14472b499d9e0092456cbd07ee79a5378ec3cb24c4749600eace41 -> 4.10.22-x86_64-oauth-apiserver
      sha256:ba93e06839b0c0cdcd9a29e515ed588d41eb71fd422dd42dfe92906bfab88fe8 -> 4.10.22-x86_64-operator-marketplace
      sha256:bb0a5b9d4d3d5c774d58e1f2f11abd260ea1b47e79c64047f753d04927b5c202 -> 4.9.12-x86_64-ironic
      sha256:bb336a7d3c3da934aab9ec31035f0ec0e27446b1c3e8a83ca3566914e5a9f2c0 -> 4.10.22-x86_64-hyperkube
      sha256:bb5f32d6a5294d211e7016a3aeefc1a02dd8ce3773747bcead61ab40f3a13366 -> 4.9.12-x86_64-operator-marketplace
      sha256:bc6efcc92aa2dde7d917eb2922e3eb19eeebcd4e8f19a499fc536fc2e45183ec -> 4.9.12-x86_64-cluster-dns-operator
      sha256:bc7227d80dc8f416eabc2bf1d326fa41ec9ab849b2a06291ef98d25a83f5ccb6 -> 4.9.12-x86_64-gcp-pd-csi-driver
      sha256:bc820dec82b685d589a6d71fea17baf28d7404d0351d7d91876476c4c2932c0c -> 4.9.12-x86_64-cluster-samples-operator
      sha256:bd81d21f0593cab25f321b000e8e18f5ff50fb741289261874fa332407b9da05 -> 4.9.12-x86_64-vsphere-csi-driver
      sha256:bd96471a5aca8cd6f980585ae783fb17438566f61d183447ebb85b369776b8b0 -> 4.9.42-x86_64-cluster-monitoring-operator
      sha256:bda0266de7f68d6e368d15895f16e2201ed7a777fcf5479ead4a2ca0038754b4 -> 4.10.22-x86_64-cluster-kube-scheduler-operator
      sha256:be0d46f5a50a9d4770947e8c8eea773893a7e39b9a0629badbe86f1d3f00a594 -> 4.10.22-x86_64-cli-artifacts
      sha256:bf3bd27408bca5f21e350e55a2cb54808cfb84ec9f3b62f6e3d25a398dec0e0b -> 4.9.42-x86_64-oauth-server
      sha256:bf425701c275c871a68e5f0d06dd34954148e15c5c78e7889cbf24c621c189c3 -> 4.9.42-x86_64-gcp-pd-csi-driver
      sha256:bf7dc8b573d8776f8e3ca0bb4605b6c2d35f2c52dc5badbf261c6f717e949893 -> 4.10.22-x86_64-ironic-machine-os-downloader
      sha256:bfa4aecd5a8457874c11742b7c73256ab1d9967a65b2c58216757a99068baa02 -> 4.9.42-x86_64-cluster-update-keys
      sha256:c017f8b24e9d9a913456373c69dec63e3315ddae052ac6ad9cee25f856abe502 -> 4.9.12-x86_64-docker-registry
      sha256:c10376143bb2fc51f72619cb7d45c8905d7c5a10b0150e1a1f65c54a90fff1bc -> 4.9.12-x86_64-csi-external-attacher
      sha256:c112ea4839c6540d2e95cae47a757d4effe43f03cbf26850dccf34496bd4ac66 -> 4.10.22-x86_64-ironic
      sha256:c2f4dcf7620e0b0a22f73502a5368448a3338496e90cd690baac766cb866b011 -> 4.9.42-x86_64-csi-external-snapshotter
      sha256:c4240dae68a407256b481e84c9931c88fcdef9ad79003a35bfb26120328e61f4 -> 4.10.22-x86_64-ovirt-csi-driver
      sha256:c43036d5bb262560b97134548b85cc8e1a6ad9b2477f6985ee146e4e215d1c6c -> 4.10.22-x86_64-multus-route-override-cni
      sha256:c56703279601e5cbf332776fab5950ecd43687cf1af005aa0a098e587b772079 -> 4.9.42-x86_64-ironic-hardware-inventory-recorder
      sha256:c56c86d030185bda241514593970e80f75ae75afd9bc6288388944bc2a1dfb1f -> 4.9.12-x86_64-pod
      sha256:c57688fd97bbe93178bad3b924dc678547e7dc86bf397a13aa1ccc79b4cc76e8 -> 4.9.42-x86_64-cluster-autoscaler-operator
      sha256:c5c4823cce587db60358f78f2a324a6def77930b0fa392a2939e04041b5a4a6b -> 4.9.12-x86_64-openstack-machine-controllers
      sha256:c6124ae70fd1f19ada3d5930ffc912dd5a3fb09dcffabc6425e9e327a61528fa -> 4.9.12-x86_64-prom-label-proxy
      sha256:c8734ce1c987467de7cb429a8442c8e1507778e207d77f8c260be48adcf4fc52 -> 4.9.42-x86_64-configmap-reloader
      sha256:c8795322b3b89da466ed9d08ded4d7a94de43fa04ddb3aefdc0b43d19cc452e5 -> 4.9.42-x86_64-openstack-cinder-csi-driver-operator
      sha256:c9a873c826b5dc5ed8257b0c2e9e5c5a6e6630a519abe27a59cdba88b99ec0a7 -> 4.10.22-x86_64-tests
      sha256:ca8dc8a6e1a672565fb1db1bdf0bc49d1878bc9621b15b293334937d42a34972 -> 4.9.12-x86_64-openstack-cloud-controller-manager
      sha256:ca9d9b978607282661d147ad6690a70f98dc745b7b034e29d2c0a1274a13e93f -> 4.9.42-x86_64-machine-os-content
      sha256:cb2dd344296dcf6140d637d849f443faa8e80436b9ac515835f8f19367505f8c -> 4.10.22-x86_64-gcp-pd-csi-driver-operator
      sha256:cb786b2e448e5cfd290e801c9ddcb0c389ac7e7f16b79b77ab8f8ce8a1e3249d -> 4.10.22-x86_64-cluster-bootstrap
      sha256:cb8f3566d3fb7e380dfff6a9c291ed60d7c700477377e77b154c266e7c4a66ad -> 4.10.22-x86_64-vsphere-csi-driver
      sha256:cc04ac8cc0ebd5ac0b7bd69e27a5d3958d311b61e3f78ed896acc2445d5e1ba7 -> 4.9.12-x86_64-azure-cloud-node-manager
      sha256:cc24ea8dcc994d6ce2bd6bb8b3b25e18f099c0448fbbdbb5565048608f086152 -> 4.10.22-x86_64-ibm-vpc-block-csi-driver-operator
      sha256:cc83500a51c225da8f897a506be19c8271bdc7839b3d1e47d9f870d00025edb6 -> 4.10.22-x86_64-cluster-kube-controller-manager-operator
      sha256:ccd64e8b56711ced7558d8e593ae32867cb2178ea73078f47d20bc7e2e56119b -> 4.9.12-x86_64-aws-pod-identity-webhook
      sha256:cd13cdf851def78178b6c1072910fcbceabdb121b888cf82377469fea75a0bf3 -> 4.9.12-x86_64-azure-disk-csi-driver-operator
      sha256:ce0c29a0a607a6b5ac3ea392e8b29da156b662e11ca601a40689ba7da4af691b -> 4.10.22-x86_64-cluster-cloud-controller-manager-operator
      sha256:ce897bc72101dacc82aa593974fa0d8a421a43227b540fbcf1e303ffb1d3f1ea -> 4.9.12-x86_64-driver-toolkit
      sha256:cec118cd293db02e090c7f5cd6b502d994e2b5cf052c847029fe4ab1b86f1878 -> 4.9.42-x86_64-csi-external-provisioner
      sha256:cf15d98a8b135e45cca22b8ebc4a41d32e0b42bd3b539cc5d296dfd62ce5f5f5 -> 4.9.42-x86_64-csi-external-resizer
      sha256:cf33109e11798f970fa181f7ab08be92301b245dc2b7d17226f38b4146d585cd -> 4.9.12-x86_64-openshift-state-metrics
      sha256:cff70e2271d5bcc26426cf3f22f3439219677e6175fcaa00c2e5a5da9379d5d6 -> 4.9.12-x86_64-jenkins-agent-maven
      sha256:d0e506b786984b64baccb7f778b9d66428157e66ca516c14e9363399049c2b47 -> 4.10.22-x86_64-csi-snapshot-controller
      sha256:d202489a2cc4374300e632e9f3bf565dcb73d6f69b61c78e34f85920bc597405 -> 4.9.42-x86_64-cluster-image-registry-operator
      sha256:d2988ce4a7a8cfeeefab8456435531faafe3804d3ad0b78822fb1eb0e95fcbad -> 4.10.22-x86_64-csi-external-provisioner
      sha256:d2a634f7198daba84dd4268893e8e5cfd10386095d5332c56f1032f1aa547b3b -> 4.10.22-x86_64-cluster-baremetal-operator
      sha256:d2b9b2429079d03176528b11656bffc34c6d2576ec3d31b8f56c06a47ad3dd58 -> 4.9.42-x86_64-coredns
      sha256:d2f400620b99bee4f7aa646df29ccefbba99111aae48a0b8b55579e12c7e9641 -> 4.10.22-x86_64-cluster-autoscaler-operator
      sha256:d4305fe75a7676708f245043b63b53510e584b48d61a9a56733db54145d8431a -> 4.10.22-x86_64-sdn
      sha256:d4d26977c8e6c77a7f0e0d7876262e0e54cc7b0627ee591493d8e1f53f5514db -> 4.9.42-x86_64-operator-registry
      sha256:d622ce62d93f99c3f5e4ffdcafc19ee3c31f82630185b882c7bd4d332fc435cd -> 4.9.12-x86_64-multus-networkpolicy
      sha256:d74f9409782832026f98e261089bad47fa50be5282c2775dbe521f5fda0e0ee4 -> 4.9.12-x86_64-jenkins
      sha256:d843c1fdbd21e62cfc7fd373882e520a7e456394befed335cce8df8472416b4a -> 4.9.42-x86_64-openshift-controller-manager
      sha256:d8c857820a05e64b271622508038330f411f08d87cedd16a3ac13095ed920b4b -> 4.9.12-x86_64-installer-artifacts
      sha256:d9162a0b7217d6eca0692a4ca91543bf6e706fe58cb16dcd53d92e6f0d45b31d -> 4.9.42-x86_64-ovirt-csi-driver
      sha256:d9ccf587dbd1d1a96297827e6f53476774843366d6787a0001f0e3b12691c5dc -> 4.10.22-x86_64-jenkins
      sha256:db28495dcf72f26368357ed71b745c3c7b5e2a40c787bc8a76440f16872a0dbc -> 4.9.12-x86_64-cluster-csi-snapshot-controller-operator
      sha256:dcb61fe5e82c0d01b90155f20b0b6ce37446867ca0d005b727fc073f86ed1354 -> 4.10.22-x86_64-oauth-proxy
      sha256:ddd29a22d824dd1ed3623656f53f608909a23c53d99a1fec10df39f1f30f8e3a -> 4.10.22-x86_64-etcd
      sha256:df22255abc474a2b61b323925f76126fa2f8c99affb1c48c2a0eb16c4b4a1056 -> 4.9.12-x86_64-cluster-ingress-operator
      sha256:df2ca7f54a8d34f568a92c077c56ed8983fffc51146b55964971c1f970288aaa -> 4.10.22-x86_64-alibaba-cloud-controller-manager
      sha256:e03cefa188318d283eb39b799950a3540d0cfe14becf8995d391dfff5dd0369a -> 4.9.12-x86_64-network-tools
      sha256:e146605c1b75917d26c07268b361134aeda68983b2e2b060c202420b8267aa45 -> 4.9.12-x86_64-oauth-proxy
      sha256:e1b1018913bd4e01b96f381257546c0cb4131d8e26b29c2f61fb2514ad6bfce1 -> 4.9.42-x86_64-keepalived-ipfailover
      sha256:e2998b64e69d5a903a76e5f47f1f9aba9b4f5db13f0dbe265c85121317c1cd33 -> 4.9.12-x86_64-configmap-reloader
      sha256:e2e760084e09a987a5a4d132c16f2ff6c1d5f3abf85ac7161a8c160abf96e25f -> 4.9.42-x86_64-multus-cni
      sha256:e322662d8fa936c134d449b5f56b60038b0e21c04971011739cbf72b18c490bf -> 4.10.22-x86_64-csi-driver-nfs
      sha256:e390815e5f117498778a9d356140730815a8d84ebab970311ff43aeb97d869fa -> 4.9.42-x86_64-cluster-ingress-operator
      sha256:e4667f1e6479ac0448331aaa0a61d6f98708af7325b44723baf4e4f34e93f52c -> 4.9.42-x86_64-installer-artifacts
      sha256:e4b0cd94ba98e9705237b5ba996b5b404bb23ae0c652d6d65043ee96b3924090 -> 4.9.42-x86_64-cluster-kube-apiserver-operator
      sha256:e50762aea53d92240ba22686c6a6507306167493576e827491fd042c34f316b9 -> 4.9.42-x86_64-azure-disk-csi-driver
      sha256:e5079431099699f7c155d52f2a40d0434917c02d60285edc9745c7567ffd6690 -> 4.9.42-x86_64-cluster-machine-approver
      sha256:e5c16190ac98e446c7cf90cba8e14e996f81021ea4a38abd17b87c13b30bd6f8 -> 4.9.12-x86_64-oauth-apiserver
      sha256:e5f05125fe17708fea8b2005712a747b80b349a61e53d77b23c29042dd6959bf -> 4.9.42-x86_64-aws-cloud-controller-manager
      sha256:e6e98f8f447fed3e27ceb7e83886295e037f6ebea7e604808cf9917f46c617e0 -> 4.10.22-x86_64-multus-networkpolicy
      sha256:e71ad2038c8772cfaafb2da7e17d2865e189a20dea5d659ce4a185ea997fec9d -> 4.9.42-x86_64-csi-livenessprobe
      sha256:e79fb8754d7c01a60c1763fe39104343783a6498bf10254b4206eae532c1679e -> 4.9.12-x86_64-cluster-openshift-controller-manager-operator
      sha256:e7af1c34ad4bb702126b2102653a5e490cceb9b7ba3e81656d66d3edad1451f7 -> 4.9.42-x86_64-machine-api-operator
      sha256:e7b7f8f79ff7f81b8e923fa83a2a7fe6b31a08256923ab4b16b8731e6e762eca -> 4.10.22-x86_64-cluster-kube-storage-version-migrator-operator
      sha256:e7f6e1b25302e21ebc1b163b69dfe33fe28dd7c336ac0bdd122bff000b7b285d -> 4.9.42-x86_64-tests
      sha256:e80f8718cc38cb025680205d890d4f622f975297bc437cf52ae1fbce0e89bf1c -> 4.9.12-x86_64-cluster-authentication-operator
      sha256:e8bbefd4ae99d6f668723488774f357234af37fdb817ece7e36fd779ab46073a -> 4.10.22-x86_64-network-interface-bond-cni
      sha256:e8dc92312868e94d808e36b972a8202d43f3ab3d3ed4088ed3e96f0445f02a37 -> 4.10.22-x86_64-prometheus-operator
      sha256:e98105dd0ee81c9c5da6772504ffe610850af371075421f16fd2389b37c69a70 -> 4.10.22-x86_64-machine-config-operator
      sha256:ea1f013e3a6fca4d1d7777c455f0e34be49bebd2cb1ccac16b140d49196916e3 -> 4.10.22-x86_64-alibaba-machine-controllers
      sha256:eae57963e5746607da7ce8070275abd301ab420ce767706d5b36972f64a0e450 -> 4.9.12-x86_64-must-gather
      sha256:ece0fdfc6a4ee72f6fe21e5100fac0276986aab5b380cc7c657177a1389509b5 -> 4.10.22-x86_64-cluster-autoscaler
      sha256:ed52f6768fec23920a69e831f516bd016a358a193527f852b9813ddb7a49cae7 -> 4.10.22-x86_64-operator-lifecycle-manager
      sha256:edc8e8eafe3f73633f3ffe4a393e21f4495d518e9f044f127a3616315cdd78b4 -> 4.9.12-x86_64-openshift-controller-manager
      sha256:ee352bdfb32857610269eba37dd08beb68927c15cd6e95bfdfbdedf8aad7b7cc -> 4.10.22-x86_64-baremetal-installer
      sha256:ee96cf6a6fc395bb5432ba2322c3fa740832ebe490211e53b401f35cbe615636 -> 4.9.12-x86_64-csi-snapshot-validation-webhook
      sha256:eed0f48ed00838fb9c5879bc6b6e5481ea14bf14ea1116985ce4bbecc2be53aa -> 4.10.22-x86_64-libvirt-machine-controllers
      sha256:ef451837878b3a5e415517f7496a25bc879c8aaa6753cdcf3447c686942a8cee -> 4.9.42-x86_64-ovirt-csi-driver-operator
      sha256:efbc54ce84c0b4dd7fe7b3e6fa2e6cd488b57d9c6fe7d055ef6395ade51b5906 -> 4.9.12-x86_64-cluster-openshift-apiserver-operator
      sha256:f03dff25b769ad2cbddb5c9fbb95e82e7787da947fb9ac44672cd114cb45b4d6 -> 4.9.42-x86_64-cli
      sha256:f0a7604ca20bc69231b3ed108ed50d73cb44d1a3479ad6834d6a264ed9ef3efb -> 4.9.42-x86_64-network-metrics-daemon
      sha256:f0d490ad627c935e07384b942859a938a8445e66486450919537d5bac8f92f62 -> 4.9.12-x86_64-cluster-baremetal-operator
      sha256:f22b23274a3f220805ef2b83502d9af379b6841dfbd593d526f072acd6c780dd -> 4.9.12-x86_64-cluster-machine-approver
      sha256:f246b25e46a057732232b3f6fc09149dfe31d97a81d300af51de808c3f699241 -> 4.9.42-x86_64-vsphere-problem-detector
      sha256:f2947771e88e71ad3835b0f63af3362b5e5c083c3114ad8f1f6b4914544884da -> 4.9.42-x86_64-cluster-authentication-operator
      sha256:f2a6bffb17480fb2caa5a52512efa348a09b960d23782d02ec8733df948dbff6 -> 4.9.12-x86_64-csi-driver-nfs
      sha256:f52a840ce398e4c852b000a9dd14044785d27a6461aa6d871bbfc8570e3659a1 -> 4.10.22-x86_64-openstack-cinder-csi-driver
      sha256:f544ba90a7bd85c8d203a10d2817ae2444c669e682cdd1641cc9bb0337fd374b -> 4.10.22-x86_64-jenkins-agent-nodejs
      sha256:f5628b30aa047fe32cba9308c70c581f7d9812f40a3e651a84f0532af184bfd2 -> 4.9.12-x86_64-machine-os-content
      sha256:f5b0f920ca63c5b5978629deed3495e48e87bdba427957ea6947e7da784873b8 -> 4.10.22-x86_64-insights-operator
      sha256:f5c20ea0c5df3636194a02aff2226c0a8285438277fd28c7ab6f98cc215e5be3 -> 4.10.22-x86_64-ibm-cloud-controller-manager
      sha256:f5ee144b56bbdd408ea1728146969830ebbabc2ed73362abd6940f8246ca1913 -> 4.9.42-x86_64-docker-registry
      sha256:f60434187e593c079d98e94b6ba69bce34e5daa5ac161e834827d594b070592b -> 4.9.12-x86_64-hyperkube
      sha256:f6081de5f3484be277edcb8e1c1d89ed9075db733cda09c3858f8bca20f5b268 -> 4.10.22-x86_64-tools
      sha256:f7ce7247375fa57b2be95733fb26a85751a5f41b28652e96612bea2ee6a25442 -> 4.9.12-x86_64-cluster-cloud-controller-manager-operator
      sha256:f83965a48127fcd265ef40ce6037df806980a088f8c83798be5b91e5cc09a706 -> 4.9.12-x86_64-ironic-machine-os-downloader
      sha256:f8c700e3ea6f2ffcc73dc201ca0e5ab452cba7dd1c5fcec880ae8528ebd1be95 -> 4.9.42-x86_64-csi-driver-manila
      sha256:f95284c3ee005b9a93ce02a703a03269b41d48688b6f8b1503bcdb40e13b296c -> 4.10.22-x86_64-cluster-csi-snapshot-controller-operator
      sha256:f98859069312ca0316543002f8709cdde044097f28f6b3177d658cda648a802c -> 4.9.42-x86_64-jenkins-agent-nodejs
      sha256:fa83f24b892664b7060e777ac5cd4f4cdac234678f12981edf04df3e5da4c7b2 -> 4.10.22-x86_64-ironic-static-ip-manager
      sha256:faefa3ed5c8c520b2a0baf84faaba92a5cdd0fdd2b3324ae1eb6ae802341fd7a -> 4.9.42-x86_64-csi-snapshot-validation-webhook
      sha256:fd41b7237cac235fead9bda6dc9bf5c6cbde163ebf9d9249f33065d5ceadded0 -> 4.9.12-x86_64-etcd
      sha256:fd59b3faf37bddb363bc72d328273a7a35e1fd358bae5a799ed2595ed2e0e5b3 -> 4.10.22-x86_64-cloud-network-config-controller
      sha256:fd7ce3da297b589c7b3c34f6dc820f4d71a51ec367424749e76fbfad06298456 -> 4.9.12-x86_64-baremetal-runtimecfg
      sha256:fe552892df8d59716997e8149a384ef65cade7610de3264652eb77e2d93575ed -> 4.9.12-x86_64-cluster-kube-apiserver-operator
  openshift/release-images
    blobs:
      quay.io/openshift-release-dev/ocp-release sha256:39382676eb30fabb7a0616b064e142f6ef58d45216a9124e9358d14b12dedd65 1.428KiB
      quay.io/openshift-release-dev/ocp-release sha256:3863346e5ccdc8e47d55d947cab9e0fd5bb8a210f3ee9501ed06f5d01fd77f4a 1.727KiB
      quay.io/openshift-release-dev/ocp-release sha256:816fde8d07a680fad8d69eabf0d5d7d65289b631f0317a961928036453091a71 1.727KiB
      quay.io/openshift-release-dev/ocp-release sha256:c43c79bdb8cf4f07566518d6b573bc5128ebc11681eaaf8966bab448104f0cbf 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:47aa3ed2034c4f27622b989b26c06087de17067268a19a1b3642a7e2686cd1a3 1.747KiB
      quay.io/openshift-release-dev/ocp-release sha256:329a959bf930d1e3a71ad92ea2604143a09f8bfdcd7e40ffe34dbc7981417cfe 538.6KiB
      quay.io/openshift-release-dev/ocp-release sha256:48ce158c46e0757be1d00d352150b85f7671b6269f0f2ab496ea39a98c532311 539.7KiB
      quay.io/openshift-release-dev/ocp-release sha256:a3ab98453804dd8a53e7bbb2558251789098d0f86102c27a9fb8049c7390d862 599.1KiB
      quay.io/openshift-release-dev/ocp-release sha256:4b4fa837bd5769603e4f6524184e06b9a8e06d8d1e25af8cc39696d2262cb206 10.71MiB
      quay.io/openshift-release-dev/ocp-release sha256:7c20bc76d0477296afb6317a3b9d2bb1b2ddcaafdb0fcf62676bb7e3c8928ce3 10.71MiB
      quay.io/openshift-release-dev/ocp-release sha256:f43eacb86576cfc775abc5d058b804eb2040e3e6dd57d232dd9b0af2c9c64d31 10.91MiB
      quay.io/openshift-release-dev/ocp-release sha256:24e8d2e218b03efbd1c2548e6c8c7fc5a216edb5f2fe7e6e83a49fca53d1018a 14.83MiB
      quay.io/openshift-release-dev/ocp-release sha256:471e5250377d59b10b42f0486deabc76453d3a22dc20cce154dadb4cc0e35e50 19.32MiB
      quay.io/openshift-release-dev/ocp-release sha256:2a83e9b81ea230196be6c07ac8246eb4f1bb4afd084ae925d2d5590c503591bd 19.35MiB
      quay.io/openshift-release-dev/ocp-release sha256:c67baba1e9cbd1d8eeb0384784480534fe1ba2b96eca6d3d28ebbe18e50a0e56 21.32MiB
      quay.io/openshift-release-dev/ocp-release sha256:369ff90116ede90357fe8f1943f4bec663e50f467f671787316d098c9af1e37f 21.35MiB
      quay.io/openshift-release-dev/ocp-release sha256:5ee8b551f095ac4a2098148853845251c136cf3262320ea6d18a1feda9c7740c 22.76MiB
      quay.io/openshift-release-dev/ocp-release sha256:eac1b95df832dc9f172fd1f07e7cb50c1929b118a4249ddd02c6318a677b506a 79.44MiB
      quay.io/openshift-release-dev/ocp-release sha256:237bfbffb5f297018ef21e92b8fede75d3ca63e2154236331ef2b2a9dd818a02 79.51MiB
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

We can see from our direct update path run that we needed to obtain images for 4.9.42 since there was no direct path from 4.9.12 to 4.10.22.  However oc mirror ensures it brings in all the images required without the cluster administrator needing to do all the guess work!

### Select Image Content Based on Version Range

Another new feature of oc mirror is the ability to select a range of version images for a OpenShift or operator release.   In this example we will demonstrate how to mirror all the images between the release of 4.10.10 to 4.10.22.  First we need to go ahead and construct our imageset configuration and set the minVersion to 4.10.10 and the maxVersion to 4.10.22:

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
      sha256:017bf5043f350cce18ff046a0c878bcee8d91e961c10b5a08f436632399db40d -> 4.10.12-x86_64-cluster-kube-storage-version-migrator-operator
      sha256:0193e46b358de8c92f0f807a117af9f2a93eaa6d2899b53add400535bce1d01f -> 4.10.20-x86_64-aws-pod-identity-webhook
      sha256:01ecc050e21f4b432758602cd89a9b59594c274a54f7882aa5dbffe393f58329 -> 4.10.16-x86_64-vsphere-csi-driver
      sha256:01f01a95141f1af1de1124904f646b9b46abd9860654ec974b5e62cba27e8237 -> 4.10.11-x86_64-installer-artifacts
      sha256:0256c31195c09e27484813362c292ec6e14769ccf2430b154ce591895ce642b4 -> 4.10.16-x86_64-baremetal-runtimecfg
      sha256:02e7cc9b6a1039a247f2524a09db6fbcd00418f0f77fac590a8bc1ff24eb8047 -> 4.10.20-x86_64-openstack-machine-api-provider
      sha256:037a178d5e046fdae2d51a7e758713adcb57348471fde203e0bd3af4c6f7b77b -> 4.10.22-x86_64-thanos
      sha256:0385ba8462d670623a2de31cca2efda36000d378be2f815262d8d91b701c43b5 -> 4.10.20-x86_64-deployer
      sha256:03a06499c3efae948535eb61c340efb8511d3ac45db1ca9fccfe5515e49a70ac -> 4.10.22-x86_64-must-gather
      sha256:03e72bf492d23a80a45857ae36223e4203d48412a763ee5ee4e35db6304d4629 -> 4.10.20-x86_64-oauth-server
      sha256:0461810622791c98ab3ad77c541d9a866d3e14eaa18ce673ca5c1cb2733894e4 -> 4.10.22-x86_64-haproxy-router
      sha256:04c5cfcb4700850a3d58767c37fcf6abf8838b7d7ccb0778004f2d164a7acb97 -> 4.10.20-x86_64-oauth-proxy
      sha256:0531d6c3b51b67eb9ea7f670069e5edc4d3826e3c48797b9d108fec3712075cd -> 4.10.20-x86_64-grafana
      sha256:054fbaac8295dada04effbbef8d8793dc0e52f71183b5d76a7396ff362180f41 -> 4.10.11-x86_64-ironic
      sha256:057d7bd5e99cd23de88ea89cfae8cde1718e38ce2a7a1fa262954bab913cdb87 -> 4.10.12-x86_64-ovn-kubernetes
      sha256:06043f3ce603c130d77bc8f2508476b204d8bc9e03e04808b00d913d2e44190c -> 4.10.12-x86_64-csi-external-resizer
      sha256:060ded5b7815eb6ac09668db63940152eb4c39ecfa618626ec48c544b09f522a -> 4.10.16-x86_64-csi-external-provisioner
      sha256:064098766cae67cee370e0ef4374908b540017f2d54148bb8be5414fdc47990e -> 4.10.20-x86_64-cluster-cloud-controller-manager-operator
      sha256:0661d0560654e7e2d1761e883ffdd6c482c8c8f37e60608bb59c44fa81a3f0bb -> 4.10.20-x86_64-driver-toolkit
      sha256:0694873d36244589d44a4f522b1587a333ef33f93acb45866501cba4cdb94b02 -> 4.10.11-x86_64-tests
      sha256:069f9a81269e7277e65f0921d33609aa684249f2396cd27f787f8329cdf67178 -> 4.10.20-x86_64-prometheus
      sha256:072a89d9f480abc729e0086cc689801b7ead887638818220c22e00a6a03ff1e7 -> 4.10.12-x86_64-csi-driver-shared-resource
      sha256:072fb9c220fc34ef1675783f5d982395f79c057963a7a6d9703b3f13b6a3e2ac -> 4.10.10-x86_64-operator-registry
      sha256:0735d3d8e6d50ad3934e512dcf51717b161c1e3de4f79b278465c3dbc3473b93 -> 4.10.22-x86_64-baremetal-runtimecfg
      sha256:078da3dec4ec9825f45712cb4b5500d61097ef813cef446f8c5b56617d7740b0 -> 4.10.22-x86_64-csi-driver-shared-resource
      sha256:07a5a6dcf2f33ad3dc70f720db37ef040379e92f5a4bdceea145e06bec011e51 -> 4.10.18-x86_64-cluster-image-registry-operator
      sha256:07bf06e605c2c43b50d1e24214fb6f80365de03e846e0bdf16e36cff228f7241 -> 4.10.20-x86_64-ibmcloud-machine-controllers
      sha256:0801fe7e79eee48497e5522f05da270a29daf200e3625522020b76dc31fedd94 -> 4.10.16-x86_64-deployer
      sha256:08416d9a3a81a18c7298cb0279b2dcf737b6ede3e58d25cdc826e086d7e2d308 -> 4.10.16-x86_64-console-operator
      sha256:0863df852b7cdf865467ebb31656863093d2b032d0467d20d2471f2bc349ace1 -> 4.10.11-x86_64-openshift-apiserver
      sha256:08bdcbea4a53ea0b15703faaec9769bc596a66654d6ab5d1f4ce76c6733caa0f -> 4.10.15-x86_64-jenkins-agent-maven
      sha256:08cc9d978d89b07ec37f9ae28922e48784a5d579e269db68a4dfc7d54e63cc14 -> 4.10.16-x86_64-must-gather
      sha256:08e8b4004edaeeb125ced09ab2c4cd6d690afaf3a86309c91a994dec8e3ccbf3 -> 4.10.12-x86_64-kube-rbac-proxy
      sha256:09d9214dfeaeb682c963d74c662bc7abc2c58304d80e4df15278f28ad0a1906c -> 4.10.20-x86_64-openstack-cinder-csi-driver
      sha256:0a456b7ba3eb1dc739c78e400fec57f382221f3ebf33554732b53e3d8dc8712e -> 4.10.22-x86_64-azure-cloud-node-manager
      sha256:0a55709b668fbc4f673758d1f11cdab8e8254ae27e09492b643391a5b3ded042 -> 4.10.16-x86_64-operator-marketplace
      sha256:0ab11abc531bc21a8ada5bf8f8474a93e8541ded14a9bc291521cf428f6efa0d -> 4.10.20-x86_64-gcp-machine-controllers
      sha256:0af23b602b648117b6276bbc49725daa707cf4b913d74a47bd25ab2743e560b7 -> 4.10.20-x86_64-ironic
      sha256:0b5a047416960a9eb9ff61442b0a435c256710c8a3eb8c7a0ae75faff5aba507 -> 4.10.18-x86_64-jenkins
      sha256:0b92c887bd6ec6a8cf80213438d24b13662fb9421a9654ecc2e018db4a764864 -> 4.10.15-x86_64-machine-os-images
      sha256:0ba72e9a498eff6b1e377564f5766cc1cb5e371d107ce6e81de73b8ea66d1c9d -> 4.10.22-x86_64-cluster-kube-apiserver-operator
      sha256:0bb893c699435046404e5c96a6375c0aa4e2e8bfe78a466af42ab3afb75060f2 -> 4.10.22-x86_64-hypershift
      sha256:0be8ad7cac4b778eb67994b2a72fbfe2f381a994f68f45ae2d862ac63dce285d -> 4.10.16-x86_64-prometheus-operator
      sha256:0c12ce723d41af21546a6ac538f336861d307882dfc703cf675ec8b10ba46a6e -> 4.10.22-x86_64-machine-os-images
      sha256:0c236e79ca60cdb66d14f89a2e89d88c15c4f152add83a8db6565bfa9da1c989 -> 4.10.20-x86_64-ovn-kubernetes
      sha256:0c460e32a623ca6a92343071cb29d502e7906bea4edd977e621380bcedfd9474 -> 4.10.20-x86_64-cluster-update-keys
      sha256:0c6954594a4615f902916678f2a200c44378e8bb623f542c15521ecb25570d26 -> 4.10.12-x86_64-insights-operator
      sha256:0c9fc10c8afe200d865520cdb076bac1e7404f783f98e349905736f4ef36df24 -> 4.10.22-x86_64-aws-machine-controllers
      sha256:0cd88007c4f1a1703943a95175177a3f5b8de7ad2b5de6d32a20fa8587b6dffb -> 4.10.16-x86_64-azure-cloud-controller-manager
      sha256:0ce80c58699259350a00ea97b4afa58fe06b6044bb45a187c2c8fe5786d59f80 -> 4.10.11-x86_64-deployer
      sha256:0cfd1727486ae652c5f566055499f6d4b3d5d6d73da6dd8c75a45f88a621905f -> 4.10.22-x86_64-kuryr-controller
      sha256:0d05e1a2f10e099389a3b0f7f9d2b92b898567aab2c2cd6c0cc897e5533a3ceb -> 4.10.11-x86_64-openstack-machine-api-provider
      sha256:0d0b9f77957664b81472ca2db475e511a14addc8e11681ec7a607f765b09a328 -> 4.10.15-x86_64-cluster-network-operator
      sha256:0d1faaee8b8fd6d251885150ea93a5d17077ffc6993bbdb469aa56f65273982a -> 4.10.18-x86_64-cluster-cloud-controller-manager-operator
      sha256:0d20224a81633274071ae8fbf9c5f013cb386858a4f16cf46f53ef8508a11584 -> 4.10.22-x86_64-kube-storage-version-migrator
      sha256:0d94f19d4012b22604dea71832897acda8e597703b73b50626a0478f89da047a -> 4.10.16-x86_64-ibm-vpc-node-label-updater
      sha256:0dcd9c8f6bb34a24b02521a80c1ec1fc5656ebb4b0f02617b48417ee6e054feb -> 4.10.11-x86_64-multus-route-override-cni
      sha256:0e4aafcf2e6cd8527c058719864314db53bec0c862ea7ca8ddff2bfb59de534f -> 4.10.17-x86_64-cluster-version-operator
      sha256:0e720cb9b6b36b1e750b65c78ed7331f9e02ec10c1bcbe36823ff9199e0eaea5 -> 4.10.12-x86_64-multus-cni
      sha256:0e7ff29f85b7cc38ea1cf90eedb95e096cdef6c0930d823c9a95e2fbe77880af -> 4.10.11-x86_64-csi-driver-nfs
      sha256:0ec4e9eea3829fc91e836d2976349549aab358473aa163c684a6b3a9c7d64975 -> 4.10.20-x86_64-prometheus-config-reloader
      sha256:0ecb9883be2e5a679df27a033c12253efb29d4a20038ec6fea453c0d17e877dc -> 4.10.11-x86_64-prometheus
      sha256:0f00662ef8bfd9cf36b7f5a7bf5321d71eb57c1a91aaf8b770b0f0b3fd931550 -> 4.10.22-x86_64-cluster-ingress-operator
      sha256:100618eec9d9f3a9286431da3dde82c98e829a604c2b4e4aaf4658c45ec9bd9d -> 4.10.20-x86_64-configmap-reloader
      sha256:100d1f76dad34eeeb627b273d9ef43127e0bb59944d7e95db519a55cb4bdbf78 -> 4.10.20-x86_64-cluster-policy-controller
      sha256:1019fb094294660c8da4741ac820e818b2baf4d43813feb627cfe90c0b674ba2 -> 4.10.22-x86_64-machine-api-operator
      sha256:103c8062698b9232c6531eabf7596a54f652312b63d754191d3c15467452b171 -> 4.10.11-x86_64-container-networking-plugins
      sha256:10ad1ec4ff792842b11161f1c47eff9562f8b387a9c9a179f0549ae65d6ba75c -> 4.10.12-x86_64-ironic-static-ip-manager
      sha256:119f8096655464ede18a1be74531251323d6a0d50773b8d58dabc07fa64c0304 -> 4.10.22-x86_64-docker-builder
      sha256:11a130899dc8be114981db5bc7791f46c881699ecfc0d487a17968cd0eb6354e -> 4.10.16-x86_64-azure-cloud-node-manager
      sha256:11a4f413212bddf9034d92a561acf66a816f2601656fbbde3e35b6f83ff72881 -> 4.10.22-x86_64-ovn-kubernetes
      sha256:11be8781b7256e2417323a641d7f5c48f5dd926daa7b9edaad392a259e496d56 -> 4.10.12-x86_64-openstack-machine-controllers
      sha256:11d03e12451b549eab21f314cf0f0e6618eaa6059762e12c9c98b0af6f774eaa -> 4.10.16-x86_64-csi-node-driver-registrar
      sha256:11e1db78dc74fc7ad769ed3c829b713bdcf92e2d76f9de9b2c4d2923380801c2 -> 4.10.20-x86_64-csi-driver-nfs
      sha256:11e6a690df2e8d22c3ed0a0e9ef58557293ca135ab01fd38123d0d1e6e3f4c38 -> 4.10.11-x86_64-azure-file-csi-driver
      sha256:1292e8576bbebeace6f6bcd3273b93cc0c20fe748271a46c93406ab0191d92b8 -> 4.10.20-x86_64-openshift-apiserver
      sha256:12c7e063009d1009db227124625984ec39e33c0821b690449d39702acad5c2c8 -> 4.10.12-x86_64-pod
      sha256:12dafdbd608ea803ca9ad7053f8cffa5837b751d84bf9d22e8c390249d9b7810 -> 4.10.12-x86_64-cluster-version-operator
      sha256:1307a548f1f790c65356c78a98aba86762d42fc011bc08373df9fdeeea14d8d8 -> 4.10.12-x86_64-console-operator
      sha256:132250626db1a76bb49a23a5593db1a9ba8cc237003c6f8d3781ac7cb268b2f5 -> 4.10.22-x86_64-cluster-dns-operator
      sha256:134d32160bebee23aac42e499c5106c0cb57086e083291c2c59868cfdf78a0ec -> 4.10.20-x86_64-azure-disk-csi-driver
      sha256:139aadb1611f22601b2b17e7599c8b7c1d98c4f1b7a404a87a576f06e25a09e6 -> 4.10.20-x86_64-keepalived-ipfailover
      sha256:13edb142d399747c4e4f83613160400b9d6ffa95e779ad0898d0856459d1432a -> 4.10.11-x86_64-baremetal-operator
      sha256:146b25bd783a4ef26de48ba3d984a6c9799edbacb0cf2895b26d924fe0982d0d -> 4.10.22-x86_64-cluster-storage-operator
      sha256:14be28be191a716781e4d54d715fbe03e1232ea1dfffed36250468b9d1710139 -> 4.10.16-x86_64-powervs-machine-controllers
      sha256:154695285d8538b5c8b461f9d0bd0775e55c8721e46293b6f0560a2736562fd0 -> 4.10.21-x86_64-operator-lifecycle-manager
      sha256:1559b84402a3dd45f977de5336417f5385db8f7e12f9dad5b10156f3c80ad6cc -> 4.10.14-x86_64-jenkins-agent-nodejs
      sha256:157b2f1e5ae2e32a8a1cc4a5fe2a9479ac3209c59579f53a288624ec66b41d18 -> 4.10.11-x86_64-oauth-proxy
      sha256:16124c9abedf4b8f6f6bfc625efcafdd445610778f2186079981687d49cd03ee -> 4.10.15-x86_64-machine-os-content
      sha256:164ba3780fcf1d472014fb99137125f7d63efd2ead56e2753dd96b28f4964133 -> 4.10.11-x86_64-cluster-openshift-apiserver-operator
      sha256:166470c04f795de095ec141c0e0cc48306e32b8c7ff6ecfb969d7273c6cbce37 -> 4.10.14-x86_64-cluster-cloud-controller-manager-operator
      sha256:1696e7c02f47b9dd859c209bd4ac3f3660a7b558c88ab2df059014658fe2ef9f -> 4.10.12-x86_64-docker-builder
      sha256:16bb60d702372cc6d911f7bd8b59a1793e209c10a5ff85add134d7ea4495b34e -> 4.10.11-x86_64-vsphere-problem-detector
      sha256:172c3384fae890209db5795e988d68f9d947c0573800099a002478203fe7c71b -> 4.10.22-x86_64-csi-external-snapshotter
      sha256:17566ba7cd65dd2368d7435e00baa798f5dd17d85f4cbd864980f9c9f32cb557 -> 4.10.11-x86_64-keepalived-ipfailover
      sha256:176510d5dc4286aa3d2f5326cfbba9460aedd439479738f3038c978566f31ccb -> 4.10.20-x86_64-gcp-cloud-controller-manager
      sha256:18081858abbeeb1cf4c9ea2b18602509a1df66bdd3b85f5c60f5fdedaed4bd65 -> 4.10.20-x86_64-openstack-cinder-csi-driver-operator
      sha256:19053faa7ab18466a7cbaf43685d1e30b61b1b78ff7e8d5e39c6bfeecd583776 -> 4.10.16-x86_64-ironic
      sha256:1964290f384c76f3bed90bfeb2dbafc98ae48fa0eb30bc6a646c7dcdbcf804e2 -> 4.10.21-x86_64-machine-os-content
      sha256:19dd209a6d2643953b41c665b44ebb9c0e6b44fdf6c3de6e4d53ebe49fa789af -> 4.10.20-x86_64-alibaba-cloud-csi-driver
      sha256:1a933c0e0487e14fb5cfa5e2d5a04acc5b5516d80c0b3d462bc37224be6a4fb8 -> 4.10.17-x86_64-oauth-apiserver
      sha256:1b0bfadc90d91a74d9efb28d1175f8287e1443aa0e5cd3e50a1c67db50142c53 -> 4.10.16-x86_64-ironic-static-ip-manager
      sha256:1b2b8d82e004b3f7ea0d37424277a15d80283e18aaa365000ff00c53dc1b2b93 -> 4.10.16-x86_64-cluster-kube-scheduler-operator
      sha256:1b5f6a51eccc5c3f56389f3f423858f31c27ea7c6dc25d139196e9852d7b8d2d -> 4.10.22-x86_64-cluster-openshift-apiserver-operator
      sha256:1b65e5156d3feb7b484979af0e10afe80cf0ae872a243eb30b3956b0c0ed4084 -> 4.10.11-x86_64-ironic-hardware-inventory-recorder
      sha256:1bcf2e6787d5fdaefb41a5aea7e7745657884b2a4b3a22d166d8ccb90ba08b28 -> 4.10.16-x86_64-ovirt-machine-controllers
      sha256:1c18d023fb1b8304a191a9d5bbef0cacdb4f945b7590cf5f550f3a5b9e0bc676 -> 4.10.13-x86_64-console
      sha256:1c7fe677acdf3ba670ee62ae2524adbe84972601b56ce9c17a99e3adee774dc3 -> 4.10.20-x86_64-cluster-kube-apiserver-operator
      sha256:1c85dbe391ac00aa86aee086a83953871a5d0d97d97f14d74a4ac8ef991c8b47 -> 4.10.10-x86_64-baremetal-installer
      sha256:1c898c6a3f49b8d5f0e8578eac5ddde18e4b129aba076766da63b38e5a62186a -> 4.10.13-x86_64-cluster-config-operator
      sha256:1cae33af889e529938023f59dcc8161af7e8104e3be2d2269e3be6a440985fee -> 4.10.20-x86_64-oc-mirror
      sha256:1cc0611202e446fa049dfcfa4b7b8d61910c14f4d9330062a08c708646b54d7b -> 4.10.12-x86_64-ironic
      sha256:1dac1bef2b7911432d8b6a5ed8141fe009080a414f3b5f1740892900bb9a5744 -> 4.10.20-x86_64-csi-external-attacher
      sha256:1dfbe7cc086a9674264226ab7fe74b39bb08c9d8b8b603e95e252a2bdade93a5 -> 4.10.21-x86_64-operator-registry
      sha256:1e37c3c886236a871b18be1a0699cf911ea9ae2bb237e672c8072ea80f05294a -> 4.10.15-x86_64-cluster-image-registry-operator
      sha256:1e4e12fddde318344ee0f42b42fda9dd630730d9f56c5c03a3d4c709284a12c9 -> 4.10.20-x86_64-baremetal-runtimecfg
      sha256:1e83c76a6a425dd1417b47637ab0ea3f2d52d134a9d7177ec7801fd54e785e01 -> 4.10.13-x86_64-jenkins-agent-maven
      sha256:1ed0e2641ee54ece86d883a8d571715b36a3d51d0c42672b4a372ac562764e39 -> 4.10.14-x86_64-deployer
      sha256:1edad06622872af1c987872a7731559e19b1f5adcc32f9bf7db828b291769cfe -> 4.10.11-x86_64-cluster-autoscaler
      sha256:1f42a36a8f738de8c5d2990b0b8820bd69e7247150e2687cc5f677080c1c888f -> 4.10.11-x86_64-aws-ebs-csi-driver
      sha256:1f88eabb65686bde3c1812fccdd17250630f1106fe26e5ced9098584c118a86c -> 4.10.11-x86_64-oc-mirror
      sha256:1ff59bb9b496e8a3c16b02559bf6739c7ad1f73b518a7e236d559557c181c854 -> 4.10.13-x86_64-installer-artifacts
      sha256:202e96ad3ebefd07185f3007273accfe5211e20adc92dfe27f69417ed3d01260 -> 4.10.12-x86_64-aws-pod-identity-webhook
      sha256:2041aeb7063b7f0a84511c00a576807f9c92e97db1a99e6e7b2ff3871e030043 -> 4.10.20-x86_64-cluster-autoscaler
      sha256:20477f8be481de3e60962ddbbf9ca6be7ac672a98c5195928ea15bbb31dae170 -> 4.10.20-x86_64-hypershift
      sha256:206670233240953f9e14ba605e81db7e44db94f2ee522d119aaf0b40b549da5d -> 4.10.18-x86_64-cluster-etcd-operator
      sha256:20c27ff706eda895e92e549abbcd9e69d4619af39714e00eb345d7cf8bda560c -> 4.10.17-x86_64-console
      sha256:20e877078870b935585f4c5d9d90127e64a31cbe352771b63cb8006917583b20 -> 4.10.18-x86_64-ovn-kubernetes
      sha256:21293a419583f4985287eb723467ce7cea5f49cb787938d42a1b1c4ecca16fef -> 4.10.11-x86_64-sdn
      sha256:212f7ee148a96af2996ca548f84698f8022b9f07f808557ef31d580e394c182e -> 4.10.12-x86_64-ibm-vpc-block-csi-driver
      sha256:21780af60acd852880a5b19eb1d3597e83fd2d4e3854c48f7763f04100756a36 -> 4.10.12-x86_64-prometheus-alertmanager
      sha256:2187d2f9e8c44b659996cc80e7efabcf985cab5fe25197961afd1ba435a79554 -> 4.10.16-x86_64-cluster-csi-snapshot-controller-operator
      sha256:218cb76304cfc9791d13214387480ea0ab628ba8d2d58d1d1b7e9d1dd051245e -> 4.10.16-x86_64-multus-whereabouts-ipam-cni
      sha256:21d5d841308a0366ffd942bcf13b9616b571e4db140b9a32e6245744c0400940 -> 4.10.20-x86_64-ovirt-csi-driver-operator
      sha256:21dfdc485fd9d901e4e6a9cb235267b8bb8e8ae3b4e77ff28b554efa0be7b3cc -> 4.10.22-x86_64-openstack-machine-controllers
      sha256:21e518aaf27e97bb02757b335e25ab1b8da3933d5983810cca13a12936e64073 -> 4.10.11-x86_64-prometheus-alertmanager
      sha256:2252fc05617963c9d6c0ec85560a8bcc793f600e20aa724ac8e8b1c66769d417 -> 4.10.12-x86_64-baremetal-installer
      sha256:22db279f79f399816f1b8172f8ffa7648fa6e63b95fd5b58177f5b4025a86166 -> 4.10.20-x86_64-machine-api-operator
      sha256:23dfb2a46a325180d8189b05008e7db5fb108c33327353cd00728450bd7bf121 -> 4.10.20-x86_64-cluster-machine-approver
      sha256:23e6126f81f2cfedbe7b1fe251bcbad4c2365ed7ee12576b67fc56fcf587863a -> 4.10.22-x86_64-aws-ebs-csi-driver
      sha256:2401baf612888112dfbddb294073401d916b6dcc5fd6a61a30acacc4afc3017f -> 4.10.17-x86_64-baremetal-installer
      sha256:247cb529550b9595062e8b314f3f50a186324e2359ef17fea0e0607c798557c5 -> 4.10.12-x86_64-cluster-image-registry-operator
      sha256:2483faec531b8fccb8762ef8ebc92bb15bc11d2de53c158b56f9f34ccf8e8fe8 -> 4.10.12-x86_64-vsphere-problem-detector
      sha256:248e6554431d3c57d0588a34ab56a71564ee531abb815f7c5f670c0967ee0bce -> 4.10.12-x86_64-network-interface-bond-cni
      sha256:24cc533f1b81b8c79bd99bcfd8c7f94d07503591f46d53af8467002a4a6266a1 -> 4.10.11-x86_64-csi-external-provisioner
      sha256:253b4a7e89bee87c58d26ce52504750bddeb4b7a4bb8e5d6d9ffcad04880e288 -> 4.10.22-x86_64-openshift-apiserver
      sha256:2579c0d49c1ad674f4d2b20c255dfa17eeeabe8ed1e6f032495d1817b8d20c01 -> 4.10.12-x86_64-cluster-autoscaler-operator
      sha256:2597f77e2a8757e8ff6e15eb5a7ab65113aa44f0382b9199c0f2553daac38487 -> 4.10.22-x86_64-baremetal-operator
      sha256:25a2301d546119b4dd68a21607c3cd79e85f56a675cf3ca1b77a53469e351464 -> 4.10.22-x86_64-cluster-samples-operator
      sha256:25d54787baf1d0e87fdbc0aa4935c238ff84aa81a2a9474f99b9696f6d5e5b4c -> 4.10.16-x86_64-docker-registry
      sha256:25f3e507bdaa3cf86feb59ae017ecef9aa6e08c1325a85b02534cdbcf8fe4fe6 -> 4.10.16-x86_64-cluster-kube-apiserver-operator
      sha256:264aa917ad9287c7d11d494ad899f0beb95b349c7283bde129d4a6ef8cb2aa62 -> 4.10.11-x86_64-coredns
      sha256:2692a98a6505e477113d815186214d6d5c15c0b5b83ef6d54a5fe11d49183704 -> 4.10.22-x86_64-installer-artifacts
      sha256:26ec28123b4c5aa2111a9f359d74bf8280669c2f4f958ee3b69f1b959ac2412f -> 4.10.11-x86_64-hypershift
      sha256:2782057e05351081a8bc0f237736327eb5ae66fe8bcec14dec4a7613f366460b -> 4.10.22-x86_64-csi-driver-shared-resource-operator
      sha256:2782ad869eb429ed09f6929de0d9cafa7a84c4d2987ef09b41b80267660b04e1 -> 4.10.20-x86_64-csi-node-driver-registrar
      sha256:2787d50587694a64c146026cf9f764f8526599589504d9a43bbaa9d4bff796ae -> 4.10.22-x86_64-keepalived-ipfailover
      sha256:2789c018f05478e5de5ca6304f5f357dbbdb92c920a4c852ee5b1f2babdcb2fd -> 4.10.16-x86_64-machine-os-images
      sha256:27c74864fdd34ee238e3be817f2a9b1dbb9d02ce53d2e2d3640b916b7bfe862e -> 4.10.20-x86_64-cluster-bootstrap
      sha256:27dddaf9f7ef16c51950318747dab1e1aba3f6cd2b37ab792358f2f90856f159 -> 4.10.20-x86_64-k8s-prometheus-adapter
      sha256:282632889b2cfdceab1edb3dea6fd29899c4e31c2e72a085720855646bc5bfa6 -> 4.10.11-x86_64-hyperkube
      sha256:285af876515891957d6afc5182fca519a2e7bd6d29488dd121be3e0ae5c52c45 -> 4.10.11-x86_64-csi-external-resizer
      sha256:285dc140c8edf1cf575de1ad3dd68258d2daaea328af5609b5fcd1d2d40c4c56 -> 4.10.15-x86_64-cli-artifacts
      sha256:2896ca9235931d5c8c17c41ec71249e48d2a05bdedd6cfa04c7e22861948b082 -> 4.10.20-x86_64-sdn
      sha256:2898fe3b2155de9bf75c917a1a6f59918823a3adb78470431e149e5e58e11b99 -> 4.10.13-x86_64-ovn-kubernetes
      sha256:28a7318f5c6d2f0b26d5192a5b84be794c0a364079822550ff0543cad063d80d -> 4.10.11-x86_64-azure-disk-csi-driver
      sha256:28fefd65032305ece2c85d245bf8cd4847f17c78f3eeb7e57f4bc2bb6265ce65 -> 4.10.14-x86_64-ibm-vpc-block-csi-driver-operator
      sha256:29383a4e84dc026ecc9d313b83b8eef1280674a42e4447f5cf3b6f045a3a4f10 -> 4.10.12-x86_64-configmap-reloader
      sha256:296720785ed1895c71835840ff4e936334db403a97b8911da638698b7a87ba84 -> 4.10.18-x86_64-console
      sha256:29acf91f988935464c3e7a54d24fc75f4053ed471b8c53bbe11a1e0bc2aa9e71 -> 4.10.21-x86_64-kuryr-cni
      sha256:29bdee6180d7b4c2ce1471a49ca2469c4da30e65a15fbb10da2af4a46de521b0 -> 4.10.12-x86_64-csi-snapshot-validation-webhook
      sha256:29d53195c7fb3ae5747748646d52ec5f4b424e63336aae2a53bbbfbca564a7f5 -> 4.10.11-x86_64-cli-artifacts
      sha256:2a04576d52958bcfda335d82768846f8869dcb8da004b4b6139ff664fa1b9389 -> 4.10.22-x86_64-gcp-machine-controllers
      sha256:2a1d56936ce0fc14439bcfd1f592698aafe2c8910caf41ef2034c7b6c18c890e -> 4.10.14-x86_64-installer
      sha256:2a4cb017a138e211eba6b9c0724b12533ca504a257bb182f30a8a24b31ebcb43 -> 4.10.13-x86_64-insights-operator
      sha256:2a80445865ea24037c1ba74ca3ad96bd7a1a7867af23513011a39c9d5a9cbe85 -> 4.10.16-x86_64-gcp-pd-csi-driver
      sha256:2a8be58ac581f2a3c409c23192bd8cea4970eb9aa3b3c6ca254e6544e005b162 -> 4.10.14-x86_64-cloud-network-config-controller
      sha256:2ac4de67309de3b8f7e94e1dd864a0d0e15512fcacab8b4966c3c360de8da343 -> 4.10.14-x86_64-openshift-state-metrics
      sha256:2ad2f7b6460ddf4f7414057c06a0953bba81775fb6ceecd63d4162503a568a1e -> 4.10.16-x86_64-cluster-policy-controller
      sha256:2ad9626760792f5a49713f13ae5db14dc2d6802eafee35c9570f7ae47b3cbbc7 -> 4.10.16-x86_64-machine-os-content
      sha256:2b423e88cdd37f307aff51cbb0f53fc45deff9618f5b4f12bfb78bea7aff51a2 -> 4.10.20-x86_64-coredns
      sha256:2b470592b438c8531f46ba826a98d65397b2272964d9e263681daac12e11e717 -> 4.10.16-x86_64-cluster-cloud-controller-manager-operator
      sha256:2c28d844818f9bc139d84b86c8ec75bd2db7c8cdb80d653e7c2ee0d487ec7b99 -> 4.10.20-x86_64-multus-whereabouts-ipam-cni
      sha256:2c2b72afe73e833126c3452ddd7021057adc1a17e6f37ef31eab1f131241267b -> 4.10.12-x86_64-csi-driver-nfs
      sha256:2c360de74b0398ef5e326081ae3fdb60a5ad9babbc9dc247047be161a05b821c -> 4.10.11-x86_64-machine-api-operator
      sha256:2c9d78fdcbcfcd3c55f939bf94a5c5088e9b070c3ac33a4347a623e38e55c4a7 -> 4.10.16-x86_64-baremetal-machine-controllers
      sha256:2ca588ae8217d6e52a960e89beff794f7041591f6a040bb106769a854960ecd4 -> 4.10.12-x86_64-csi-node-driver-registrar
      sha256:2d16f5c2f356d54154d78869ac6b088852d623cf5bad9d6e1c9c50640742ee8c -> 4.10.20-x86_64-machine-config-operator
      sha256:2dc4f0e1448f6943c35cd4b2dd3429beea8ba6f3d904d9c2987febad3b6a4b5d -> 4.10.16-x86_64-cloud-network-config-controller
      sha256:2e1c654191680c8e869a16124c8d2dd81e896da4aeff5677570a556d9e23f19c -> 4.10.20-x86_64-cloud-credential-operator
      sha256:2e2957ce5bd80f1fe7ea4b74b7c38ecec6e4571a609bf03f56814091eb30cf71 -> 4.10.11-x86_64-ibm-cloud-controller-manager
      sha256:2e4fcc844f7298746be9be20e2629715ae337fb1b7c9e0586c5ecb7b6b05242a -> 4.10.20-x86_64-azure-disk-csi-driver-operator
      sha256:2eb78fa376f816585903fd72d3403c2af3e5a31563c17ca9207d1c0fbb4a7dfb -> 4.10.20-x86_64-prometheus-alertmanager
      sha256:2eddcc7702c704bbb5931a18bfa087030d206239dd9b7374c27035f86e00109b -> 4.10.12-x86_64-azure-cloud-controller-manager
      sha256:2f41850d4c23651776090b2a812b9f7794495ce5506f5c9f0f5b56cafb8def36 -> 4.10.12-x86_64-installer-artifacts
      sha256:2fb612f93bd0d79f399a42e9595340b9004fa542ea07ce397a059e7ecab9c616 -> 4.10.12-x86_64-cluster-baremetal-operator
      sha256:2fe3f143cff62cfbad922306c43b5c00fc9d4309df72f203db83bd809843363a -> 4.10.12-x86_64-baremetal-runtimecfg
      sha256:30108f9c51cd62fc87fb46ba7ca7b47b195062aeae2ae13f544f8d2fc929d850 -> 4.10.11-x86_64-alibaba-disk-csi-driver-operator
      sha256:316d0d73496b873209746b62309116588d0620fc89f5a397aa71feb83b385eb3 -> 4.10.13-x86_64-deployer
      sha256:316fbd6dd377ffd1e83a647ce9b610017d7b1ea007552ca1d0224ec576d08e58 -> 4.10.22-x86_64-operator-registry
      sha256:319e2c4215311a1b8c50b7cb4445f49bb79fc22dcfc120d07adcc3d6cfe6db9f -> 4.10.18-x86_64-must-gather
      sha256:31f027a9f9cafe0237c9c70eada0b7ab9a365e8f2eaadc52ff64bc71f1c4f045 -> 4.10.12-x86_64-cluster-kube-controller-manager-operator
      sha256:3233bf3f35cfde2edd445900ea88508a6ad899b82c117c03de20acf54163c365 -> 4.10.16-x86_64-cluster-machine-approver
      sha256:32a251b7ddd41911edc8b424d285d4000a418d9564e23a62ea5b1b48e30f62d4 -> 4.10.16-x86_64-oauth-server
      sha256:3353f58c8ad5b5a1a1e7e4a9acf815b39de690e673ad09bc2c56f1ca682b8a1b -> 4.10.22-x86_64-powervs-machine-controllers
      sha256:34484ea6afc87648dc078549986b6770c4a38a7bd50b69e5d4db6e61d9638bef -> 4.10.16-x86_64-pod
      sha256:3555186adf6d0d79b9edf02935eb49ae8f61084c5117ff3e2066556caaf7ca6f -> 4.10.22-x86_64-baremetal-machine-controllers
      sha256:3566531ea3f7077b079369abff76c4607733416ce1c0a2bd5f7cc72c630034ec -> 4.10.16-x86_64-oc-mirror
      sha256:357b2ae6eaca4596feaa1fcacba7b5083e954eea56d210f60abcb438f343d49d -> 4.10.20-x86_64-csi-external-resizer
      sha256:35f1c85a48b0c16562fdc2fd680ebef1a4785189862b5c08c7a7e2bbe285fc1b -> 4.10.20-x86_64-hyperkube
      sha256:35f3c8509bf9450c28e69bf0e134db9342107ad92bd24847acd9d01297976767 -> 4.10.20-x86_64-multus-route-override-cni
      sha256:361c8e399c80ad229ac9ea853c29c08c6c0388d44794ac5bdbc5e31ef7cb8f72 -> 4.10.20-x86_64-cluster-baremetal-operator
      sha256:362b6b049ebcb3dda7a45db1409ca4bd1332b020a2d25c0d6a1c681f890a9251 -> 4.10.16-x86_64-openstack-cinder-csi-driver
      sha256:363cfa43a511ef3350613be99429a0e50bdf87c2b850089f28cd09230efb9eff -> 4.10.12-x86_64-alibaba-cloud-controller-manager
      sha256:3681cfc62a8aefebf4fa929c6e64e71c3f91b92fbc153228ee320c8a9f3db196 -> 4.10.12-x86_64-keepalived-ipfailover
      sha256:36b41684d64f1f0ff932f994d8d37b96c91c19f1b725b97bbb31b5faa3656eb5 -> 4.10.22-x86_64-prometheus
      sha256:36c2196d324c2d8d6006c1782d9ad52eae9127e46ae1c2e49cf01a84de4f940b -> 4.10.20-x86_64-machine-os-images
      sha256:3765f9f6a72fbe27323a918aefed0443ff910e5c1a9bcc618f12b1052800b067 -> 4.10.20-x86_64-ibm-vpc-block-csi-driver
      sha256:379cf019a7a78452596263ceea1adf98714dd8a985f09531c029560526a6b15c -> 4.10.22-x86_64-csi-external-attacher
      sha256:37ab472df860086e59090eab8f3e327c3088263d669f3dea9048574bef72c201 -> 4.10.12-x86_64-machine-api-operator
      sha256:37b8d694c97ec18c285b31a8a5a62f479348b968173cc9a87a2ff33540b468a4 -> 4.10.12-x86_64-installer
      sha256:3830d8c9b55d7dffdc2224add43f42672404a22acb404651b1fd90b160c7e9e0 -> 4.10.11-x86_64-openstack-cinder-csi-driver
      sha256:387e0ec56fefa02c736e16ecfa2f0484b002fe2e5f210cf73c28b8036b9ff479 -> 4.10.11-x86_64-cluster-image-registry-operator
      sha256:3888e5e253b8caf99bc6517f98f4de6f2b39548c1bcf6b3a92b0ad0ac0ba72ed -> 4.10.11-x86_64-machine-os-content
      sha256:38935b2dc739a2b7973de0682fae7ab79a0f3d84dff47a569394329ab7b38f91 -> 4.10.15-x86_64-baremetal-installer
      sha256:38a422df6b616bc63cbb23fe4e87654c0f001f464374f0e937be6f0b5369c181 -> 4.10.16-x86_64-sdn
      sha256:38fe471535964ffd0f759b629219d371dd2158e93886b2ed14d79a8f40fb616b -> 4.10.16-x86_64-prometheus-node-exporter
      sha256:392fcbd0c0c55d47aabb1f51dffb123bed67e424c5e37629b6620a8a32dd9738 -> 4.10.10-x86_64-cloud-network-config-controller
      sha256:39594b0dd96744cb8fa31cf0485f7bee06159dc08e489edbacba3b3062efbb68 -> 4.10.12-x86_64-grafana
      sha256:396b4dd85b58492435f0e441fa3b7c5364c879b5bcab3e0f47fd7bd7159d2776 -> 4.10.12-x86_64-aws-ebs-csi-driver
      sha256:398becea0e9d87e94ee42d6a5e3c2afc2fbfbfab11394a61c03957f69ad600d1 -> 4.10.22-x86_64-cluster-openshift-controller-manager-operator
      sha256:3993b2fafdf84cdfc92b3ea2234c80ba8fe7b3501ccff59f859f797c8773da5e -> 4.10.16-x86_64-jenkins-agent-maven
      sha256:3a1f06650c28cc434e0a8aabdde93ab4020e80da4b07d4328d807f44d12985e9 -> 4.10.20-x86_64-openshift-state-metrics
      sha256:3a430c4571983d4e774a7c2a6575086ad758d6a76724cc264ba80766abf5ee6b -> 4.10.15-x86_64-tools
      sha256:3a53ca7145189af06a4a58360a4bbe84907e4ec4e598a8be0d1439ae6c5d4387 -> 4.10.17-x86_64-cluster-ingress-operator
      sha256:3b094b971f398869f48747ca90615026320daac170563de008de81af8182b8b7 -> 4.10.16-x86_64-ovirt-csi-driver
      sha256:3b2f92612cf6a35edaade46270a961653eb53152a1217fa74be685ac3cfa8b71 -> 4.10.12-x86_64-kube-state-metrics
      sha256:3b3d0401dec6c7e1710b8e4a8c29a88a9bef1ed318bf570db1f344f5d31b3cbc -> 4.10.16-x86_64-kube-rbac-proxy
      sha256:3b516fb60914d6c5e8ce62c74132b89fad796b79517242c7ccdb57803e645a02 -> 4.10.15-x86_64-cli
      sha256:3b8db74148888789d161fc3df123a5ba6717bf4e82892b8baa5eaae75eb98638 -> 4.10.11-x86_64-azure-disk-csi-driver-operator
      sha256:3bd7c6cadcff1b14338196ff712d7a19350a952af755947efe2d3f68bc9c0c6f -> 4.10.11-x86_64-ovn-kubernetes
      sha256:3be62121ce3d62781e5f1e6a3b1139f5308542d797376a990fec360c2e614f62 -> 4.10.11-x86_64-cluster-version-operator
      sha256:3bee29173117bc46265cf69b4725d1d4f679a0e56bd6e83e979604cc4fcfce18 -> 4.10.14-x86_64-machine-os-content
      sha256:3bf2dc09abede4f28e531ba0b0070dfd7844f45bf759006c31a117e03c8ce4a0 -> 4.10.20-x86_64-installer
      sha256:3c1ec9fa842deac0da405632fc93af4faab18a89179409588066a17894b4a488 -> 4.10.11-x86_64-cluster-config-operator
      sha256:3c1ed18541701e1dc2e579498593e197dc6dedebc1ecd8f5971ad92ce5ea2ded -> 4.10.22-x86_64-vsphere-csi-driver-syncer
      sha256:3c6b6e77522043e66e93a999089cf6da55de4cdc951024efc9d555089926ccc5 -> 4.10.11-x86_64-cluster-machine-approver
      sha256:3c7eed41f4a7d348f9f1ac52675aeebc77dc6e6812a527f8d4fc1941a4746bb8 -> 4.10.16-x86_64-csi-livenessprobe
      sha256:3c9efe447dc3eabfe442ca0f833ee55113d86991979a1904b6acf30085c3e304 -> 4.10.16-x86_64-openshift-state-metrics
      sha256:3cd352ab6f82a1f5f762b6283f9644e653d37dcdbab27e1fae3d5a82a76d2a9f -> 4.10.12-x86_64-telemeter
      sha256:3cf556469111fd38083f416fc5c188c92000480fd123200be64d2df9438e6a66 -> 4.10.11-x86_64-aws-machine-controllers
      sha256:3dc01598d7c0d33f78f08764bd2efc38fe78fe105cf6c47e0331bf7066e2f1a3 -> 4.10.18-x86_64-jenkins-agent-base
      sha256:3de2bf265967ad8e0213ba4ad5271b8f34b459b92a0d9da733c01c1acbbc3201 -> 4.10.18-x86_64-insights-operator
      sha256:3de41aca7b19a61e3261638121233d70eae5915c3888126ba3ea029b8a395b37 -> 4.10.11-x86_64-multus-whereabouts-ipam-cni
      sha256:3df935837634adb5df8080ac7263c7fb4c4f9d8fd45b36e32ca4fb802bdeaecc -> 4.10.20-x86_64-kube-rbac-proxy
      sha256:3e630fcf3b3a8c3b78e6766eb1e71db69a9ccdae9014e32464390806e74eaca9 -> 4.10.18-x86_64-tools
      sha256:3e88a7a7944163d4309628c7eafc4a5fc8a33c1311b3ca3bfcc1802c6138db1a -> 4.10.20-x86_64-network-interface-bond-cni
      sha256:3f276dab48846236f9f82cf5bc09564867199840b67de5aac1f11fdb8e95c4d0 -> 4.10.11-x86_64-prometheus-node-exporter
      sha256:3fa5ec3c07257003575c63f218236b76eb0a3175fd206eb9c6abb32b925b15c4 -> 4.10.22-x86_64-cluster-authentication-operator
      sha256:3fefc7c8761f0f1e05643be4c0fb8bf7c971fb8b6f1ddf5cd5138c868b2eeb7c -> 4.10.16-x86_64-ironic-hardware-inventory-recorder
      sha256:404fb0738cb39a37cc5090f68cc86f2643ab7343c5e6d601abebf2822833579f -> 4.10.14-x86_64-cluster-network-operator
      sha256:406f6fa0c9eb5b160c5a0256f36fbb2d11d8a747696fed09297a9786f56c98b0 -> 4.10.11-x86_64-jenkins-agent-nodejs
      sha256:408df31ea13f536abf7e25500e0127695659f13078633b302d7fccfdb29dbb6e -> 4.10.22-x86_64-csi-external-resizer
      sha256:4092bf2edc1fde3b4caefee62cb5fa3f5021be767ba882704c7deee8b98e080e -> 4.10.16-x86_64-azure-file-csi-driver
      sha256:40e45ad114c11d97c0355c99ee5cd19acbcf3d9ebff93bb9aa216b0c5ef88eb8 -> 4.10.20-x86_64-ovirt-machine-controllers
      sha256:40f70c39127d3daf10aed19b1678c63e2e6dd73b348da6cbff86e7a56b7d8266 -> 4.10.11-x86_64-libvirt-machine-controllers
      sha256:4123a4c500abf3a5a42fadb550dcc3d45979de11225f75c88fb58499090f692c -> 4.10.20-x86_64-jenkins-agent-nodejs
      sha256:41da4ef890e7d58faba81e3e99b59ee14f8d0974bed0d10bfe34b072881c555c -> 4.10.21-x86_64-console
      sha256:429fa6607cf6933d3019edad8e8e55d44339ece5db065c37810c24ef1101b14f -> 4.10.13-x86_64-jenkins-agent-base
      sha256:42c5ee738568eb6cc81347e420833e05c21b1544aa627149f1a099e994c4e715 -> 4.10.18-x86_64-ironic-agent
      sha256:42ea6ea5b944666bb255253f8e41a77e75fb1c43a6c7bd349c343d8ebcf65d0e -> 4.10.12-x86_64-cluster-ingress-operator
      sha256:42ec80258937241130f3215d0395cf63e8e834dde96f5105a60cf9c0eaf5d046 -> 4.10.16-x86_64-oauth-proxy
      sha256:4378b11cd6d714f4d6e0d874260db058f11580ae510e3526e6a74adf9977c64a -> 4.10.20-x86_64-csi-driver-shared-resource-operator
      sha256:439a1de7a053bb3018436f1d1154127dd5b69eb23004396e5c90024c7907824c -> 4.10.16-x86_64-multus-networkpolicy
      sha256:43e10d0f8f0678d9489dfec406dcd71dae524993dd9ad9be3916da799fb931c3 -> 4.10.16-x86_64-openstack-machine-controllers
      sha256:43e5f3775080f4e982b7a191cc7df4c359fa2ecde96fc064aaadc53eb98a1203 -> 4.10.16-x86_64-azure-file-csi-driver-operator
      sha256:44362c107867bf45198f21bf493714c38a00abbbde2393cf043613b3443eb9b1 -> 4.10.12-x86_64-csi-external-attacher
      sha256:446461dfd9491b3487234f95cc2ad498a92b4f3b4da2769fd80c09ed5930bc06 -> 4.10.11-x86_64-prometheus-operator
      sha256:44ec3e551d01975317791dd844ff96d07caf5d0a4ae365a5e78ebb114dec0efb -> 4.10.12-x86_64-csi-driver-manila
      sha256:44fca8b4f5efd9e4d5980dfaf4eacaac48e2eeda34f4cf79508199379df3a811 -> 4.10.22-x86_64-network-tools
      sha256:450150a75ffae38dae989afc90678329c420dfc1b5686d2815e6a14cb46a14f9 -> 4.10.13-x86_64-pod
      sha256:453624792501f37bdf2a564467a80c2b4c6926ca39e2143625939ad5cbf32cca -> 4.10.11-x86_64-thanos
      sha256:45ae41437fa1f4e104af7c55d44e5013bbb8d3b1e66da930e2019391d3f6bfb4 -> 4.10.12-x86_64-multus-whereabouts-ipam-cni
      sha256:4605a91b1ec7cc4223db2802961b992f0a8c607066d3eba94f8f59d000676288 -> 4.10.18-x86_64-machine-os-images
      sha256:462fa2f800ce101a515a69ad4705c813cf11785cf7306cc483285202f4d4693d -> 4.10.16-x86_64-ironic-machine-os-downloader
      sha256:463ed76431b13118f7524635d5d82bc9ce1dfc7c2b18754b611d6c0aaa8380d3 -> 4.10.20-x86_64-csi-driver-manila-operator
      sha256:46d599ef5e8952827cf4d7a1787c5eafaa6b3003f823c32484116a86dfb273ec -> 4.10.17-x86_64-multus-cni
      sha256:471f8ce9c808c9670601f3327e05181e8d1ee6f031708c1ee25bd174c8e89e7e -> 4.10.16-x86_64-console
      sha256:47220fde4a58c99560e6a3c846d7d3d53f00851cf98b5c2abb2ad634247ee6e8 -> 4.10.10-x86_64-jenkins-agent-maven
      sha256:4753002e73776c18db7f8a6320346cec611b4056d8f06d1ee3d2a14d29b72f77 -> 4.10.12-x86_64-aws-cloud-controller-manager
      sha256:487660f0e6fa0efea2c00793c52d3f0de228412732640dc07f7d3a0ab5139f48 -> 4.10.12-x86_64-azure-disk-csi-driver-operator
      sha256:487e545cc51baea2b87b5b7f21cf4beb610ff81baf367f3db90601e06b4a9314 -> 4.10.22-x86_64-kuryr-cni
      sha256:4881e23ae85f77421b66ef756e301229a2b117081c87e0b1efd60b8e749b0104 -> 4.10.12-x86_64-cluster-authentication-operator
      sha256:4961daab3005237fdc1283eb091882b6a0b589ef21faac2ad92b4927ada26296 -> 4.10.22-x86_64-cluster-network-operator
      sha256:4991d3f6df0771409c188a7d7aa2d2d3d478e049efd7f381a7cb133d1d9423ce -> 4.10.12-x86_64-gcp-cloud-controller-manager
      sha256:49b916ef259c672caeeb865852d37576dfecfc0e0be807a761697f1e71319134 -> 4.10.11-x86_64-alibaba-cloud-controller-manager
      sha256:4a2705ff802e127db0adb9b39218dab9c9fbe19b9102865b7225363636909f12 -> 4.10.16-x86_64-cluster-openshift-apiserver-operator
      sha256:4a7fe2ab4cabe7774df5d2173e86110ebbf3ecc55bf6e2e41470ad2707ebc8c8 -> 4.10.11-x86_64-openshift-state-metrics
      sha256:4aa45f1852a9bdfd93f6fb9d9fa8cb12bb8e0835b319d077a592084175364c0d -> 4.10.16-x86_64-ironic-agent
      sha256:4ad859f480b5891275b005081888b29e3d6a98c0dcb1ffb6dff8ca1a2ee23b7f -> 4.10.14-x86_64-network-tools
      sha256:4ae530f2912b7f96ae5dcb2c917cf5d0afedf30801794ef160ed8da10fd032ec -> 4.10.12-x86_64-console
      sha256:4b1878fc6ea8a9b47e81bbc516ad3d4ece5afe6d4e0a2a4e268d7bee2b46065a -> 4.10.12-x86_64-openstack-machine-api-provider
      sha256:4b547c3db1d4fc01808c65ae33e994e8acbdec5b11ecb25c8e44d16238b80db1 -> 4.10.20-x86_64-aws-machine-controllers
      sha256:4c5db15a4600d50b6cb1d28ea8b0741e9940116e45c4bb5c19754a601af47dcf -> 4.10.13-x86_64-machine-config-operator
      sha256:4c6a585c8a4f3944ab010bb89ab4fc89cc87ba9ce5428a463cfd68c9a5649f48 -> 4.10.22-x86_64-coredns
      sha256:4c71cde2f34ad71b72d3a49f98179a85c8416e79b8fcc05ee813567192695029 -> 4.10.20-x86_64-network-tools
      sha256:4ca6d4f31c0334373611ce2d4d557f73ac32cffd91f710ebc6dce28ec14b358e -> 4.10.16-x86_64-jenkins
      sha256:4ca8aa6a0f790a79c29c94e1979f3983f13ab960ec68ebd5448635ca1ad7ad41 -> 4.10.20-x86_64-csi-snapshot-validation-webhook
      sha256:4d2c27d558ca916f786fef2b1745a01d8e944ca0e3184ce933244e65cd9db16d -> 4.10.11-x86_64-aws-cloud-controller-manager
      sha256:4d518b1fb0f0969fa7fb1d135648c5d6db7e67d5eb420b0eeae3b44f21dbbe6b -> 4.10.16-x86_64-openstack-cinder-csi-driver-operator
      sha256:4d541acaa05b7b053d0cc28a2c0650261d021b7316d30297cb9669d2b039ae0d -> 4.10.20-x86_64-cli-artifacts
      sha256:4d6235bff40e083e019159a980dc4120d550b558cff2b942c67938cdbfd2f229 -> 4.10.22-x86_64-machine-os-content
      sha256:4d829de1a9542bb9a61e1a6a7b405306820e39e2f05e3baf131ff8af1d7ac329 -> 4.10.15-x86_64-deployer
      sha256:4dba958914fb7ecd4f656cdeb8522ac300d639766e6a4f50ed6155fa2ca4f93f -> 4.10.22-x86_64-jenkins-agent-base
      sha256:4dcacb7ae5c5a5c0cb2fbc8e132b14a72ee937b3dd7d201c7a66ded05e5d6790 -> 4.10.16-x86_64-cli
      sha256:4e008630f8b41b1e5b698d6ac9190a5c978ddd7e1e6d05f148c64c9fc4df3682 -> 4.10.20-x86_64-console
      sha256:4e4a94db4668e6539f8a527ffcae8e642b03066397c8b3c8715726936432b882 -> 4.10.16-x86_64-alibaba-machine-controllers
      sha256:4e84fb0af0ba9640b323f0cc217ae25139e464c2a657776d4c1379c4cd400d66 -> 4.10.20-x86_64-jenkins-agent-maven
      sha256:4edeb76c776b55e4ff6e6374903dff9023bb3c7360805518e8ddf6dc2f62ddb4 -> 4.10.16-x86_64-machine-api-operator
      sha256:4f88eed2a0e38cdc7b3194794506bea31219c4857e060ce903dc42c55ea569bf -> 4.10.17-x86_64-ironic-agent
      sha256:4fc3da6d3e1f5dbdbc7722c6f1a821241b8fb6957a8efe62922f9a325e802f04 -> 4.10.11-x86_64-cluster-openshift-controller-manager-operator
      sha256:4ff334703e46554ddf5dd45c3993fb50a20407b06e64a64f4fede8f8ff74654b -> 4.10.20-x86_64-operator-marketplace
      sha256:5031724cefb5eca107d36f8830f251e7a6fd15ecf9725a75fd31836732446a06 -> 4.10.18-x86_64-cluster-version-operator
      sha256:5053ac23ba627eacdba51d725c5c115e66a270264b6b7dbc3c1d0aaf6eed97e7 -> 4.10.16-x86_64-keepalived-ipfailover
      sha256:50685b19f1be05b3b63c255cda051eb1d8e77a12c09e68100fdde57753b8f4db -> 4.10.12-x86_64-aws-ebs-csi-driver-operator
      sha256:5085b404248def7058806ad61102dad09a02208777bf15fd4e275c435bd6031a -> 4.10.22-x86_64-oauth-server
      sha256:50fe25468e854383e340698fe032626ebdd8ed369b0c9a8c2ae66ef496d49190 -> 4.10.16-x86_64-alibaba-cloud-controller-manager
      sha256:51579b9515872933949b84a57b54688f87d94fb0ef6cbe6765eba9bc51edd8e0 -> 4.10.16-x86_64-aws-ebs-csi-driver
      sha256:532f44f6f87626b533cc7420a97eb923e93c2032c8bd5685b94f4a4af0359aec -> 4.10.16-x86_64-hypershift
      sha256:53501ab17ffbc07722ee37dea9a4b88d9e52c0a8d3117510ceb6073ea38c535f -> 4.10.22-x86_64-csi-snapshot-validation-webhook
      sha256:53757499f08e902462edb7e55b0a6c52d1ed54e495defe309f87493c1b35981d -> 4.10.22-x86_64-cluster-version-operator
      sha256:53845e90465cef628b9eb1ca6069c50eea34ab4ccc219e3c3cbfe97fb9c7e12c -> 4.10.12-x86_64-cli-artifacts
      sha256:53a6035edb3e7ed537d93314f801c665a32b932739202c6a692cab5d31bd21a8 -> 4.10.11-x86_64-multus-admission-controller
      sha256:5464c60a20bf697933a9b88e6ed3f01fa6f2d56f895b70c57a5bebe0179e40db -> 4.10.17-x86_64-ovn-kubernetes
      sha256:54abe19357100e52db5d47bbea0f33bd8f594e379a28975898e546e9819ffe16 -> 4.10.12-x86_64-openshift-controller-manager
      sha256:54b88753839d2681496fe6ab703edf2d3b73820786e962b9457c2225590b1afb -> 4.10.12-x86_64-sdn
      sha256:54bd5d99cb2e4332c63f194a2f0d10f0535adf53e4562a3fdb408c67b1599d27 -> 4.10.11-x86_64-driver-toolkit
      sha256:54f6a7392dae29b4e3fefe663645599e7b262ede04f9327209eab429295b97f5 -> 4.10.13-x86_64-ironic
      sha256:55042eae49a12cd11576d3963d5740f22661155cb580e84398e4e2e5490b430a -> 4.10.11-x86_64-machine-os-images
      sha256:555aec3569a4159de48ed3e3f9f2a077299148097bca8cb91d40ab26542e950a -> 4.10.12-x86_64-cluster-monitoring-operator
      sha256:556755f650cb1a24608012830aa0326e61ac63f27c3363051a7c3beaf99e13ed -> 4.10.12-x86_64-cluster-etcd-operator
      sha256:55caac306e682979d343343b4bfec8c38d545a81eb859d789e0bd90cf44c3d9b -> 4.10.12-x86_64-cloud-credential-operator
      sha256:56a1dcfe82cd3aa9ec44bdcc77df93163faf48bb5d1a38d4f023d48a34c8cab6 -> 4.10.22-x86_64-cluster-node-tuning-operator
      sha256:56b2f624dd37b7e0ef9be62dd86a79b5820731f66d2387ff7c6045ed88bc75f0 -> 4.10.17-x86_64-ironic
      sha256:56e5c71eb11efe0e995ca34c1c6ccacdd02eba476287b39133473bfe3c1efdac -> 4.10.21-x86_64-hypershift
      sha256:56ebfc7de16ac215963533060d7ce15f4216868dc6f8a3bc775a288fa87baa56 -> 4.10.20-x86_64-insights-operator
      sha256:5704951785bb7b6ed278ab43c5f7ed8b757dc81ac491358fe6ce72cb77657823 -> 4.10.14-x86_64-jenkins
      sha256:57433dbdadcd05b83c67b11f2f841a0cb6ee946a76c8d31c0c20366913ceb13c -> 4.10.16-x86_64-csi-external-snapshotter
      sha256:57c07728c6c522e476fb56b6a75fea063f8d5b71b7ed90e16f95799c6e70fdc7 -> 4.10.22-x86_64-cluster-etcd-operator
      sha256:57d069fbf5c4ea61bd260e6ea8f94e5f6e7e732a0129ba63bf1642ac910da73a -> 4.10.16-x86_64-driver-toolkit
      sha256:57db8816c0d7c1876fade2c6963098154f7d6e256db9fba63857bd4fb345a07f -> 4.10.16-x86_64-telemeter
      sha256:5833fb78ad3697c753004797f0412198d46bbed5fc7cac3746554d60e0d33110 -> 4.10.12-x86_64-kuryr-controller
      sha256:58639bcaa95e2b243215ad4d111c5cfa69d3db2bb476857066afab07de84d07d -> 4.10.11-x86_64-gcp-cloud-controller-manager
      sha256:5876036a0b15e58566fd0c9cde4c67b4c0d1dcdc56ee743da690dd0b89579d90 -> 4.10.22-x86_64-openstack-machine-api-provider
      sha256:5897bbcc6cac7006c45f4eae650e96a9ef22138b16cd742b1ec9ec044bfb113a -> 4.10.11-x86_64-ironic-machine-os-downloader
      sha256:5945ffeafc13da9ed19240c24db57609f7636627bac29c88b4d57799f39d71c4 -> 4.10.16-x86_64-oauth-apiserver
      sha256:595becb5c254b89c5f16d2a751a3cd0880b161b3461df0b507f3aa7eb5f16711 -> 4.10.14-x86_64-cli
      sha256:59db09bc04d2e7439ffbf51f7cd9ba9b9cd9f27961ad4f455e2fd017747da170 -> 4.10.22-x86_64-jenkins-agent-maven
      sha256:5a21f5d6a768676571bbd5f4729ff3573c120ee1a2bafc448be52b45db7c220d -> 4.10.20-x86_64-cluster-monitoring-operator
      sha256:5a25a26071fa9bc47fc225a7094c5766d2b5ba5672f50fef74b6ca42386d08c9 -> 4.10.16-x86_64-csi-external-attacher
      sha256:5a93821c05629a4247d7a496e606b39838b6d23c6614848dd7eab0d907fbca2d -> 4.10.20-x86_64-alibaba-cloud-controller-manager
      sha256:5ae10a5aa60a664893a010afadb0d02833826980d0148f22497fc947c1fb142e -> 4.10.13-x86_64-cluster-monitoring-operator
      sha256:5b0aeb800705510a2e7e41f79922e374f180d3c60a67768abff8b849c82b4574 -> 4.10.17-x86_64-installer
      sha256:5c3e8963d0e5dbce2e30dc0341c7453c30a42395622905b718a8fe9974da41ab -> 4.10.12-x86_64-prom-label-proxy
      sha256:5c67f5bac2b8f49de79f20e7cf4059b9c43c84fbcade4890c9cf42dc9fb85b6a -> 4.10.20-x86_64-vsphere-cloud-controller-manager
      sha256:5c7695a09013027e98241e246d7bb1db49b570c5544ea66ec435c38a156b75f8 -> 4.10.11-x86_64-azure-cloud-controller-manager
      sha256:5ca849270c4ca182b2670e965bca0cea71004581ff128a76c232aac03d79649c -> 4.10.20-x86_64-cluster-kube-scheduler-operator
      sha256:5cb7c8ade56925510e83683e198b95e8a4ddec34de13ad9fb39b691d0c821ece -> 4.10.14-x86_64-baremetal-installer
      sha256:5cb8eed4d9713a7a3da331026e21773c76d52feef9a591921807ccf217f66757 -> 4.10.13-x86_64-cli
      sha256:5d096169f16025175f3515cfe5ddae9a4f51ccad941cebeaa2f8055f4b7c3b55 -> 4.10.18-x86_64-jenkins-agent-maven
      sha256:5d323d5758294f02c481a50fd22bd74be391846f8a9b2bfdba4507499ebda487 -> 4.10.12-x86_64-oc-mirror
      sha256:5d6e0a54c1c6d636218fa32837604c38452295a810ed7213854c0b9e3d013d33 -> 4.10.14-x86_64-cluster-version-operator
      sha256:5da7e070826cda9425f84cca10cdf94df1724f7050e2492f0b42c7e13aaa5f80 -> 4.10.12-x86_64-multus-admission-controller
      sha256:5eb4f9d4fec49f612c477a1ef96a3268869a0c09fd1d32afa80173c7cdb07232 -> 4.10.11-x86_64-kuryr-cni
      sha256:5ed785112aa91b0e1ef2a68949b672f0240649a81829a47cd64ea8183cf8a3e7 -> 4.10.16-x86_64-cluster-openshift-controller-manager-operator
      sha256:5f341bd1af1b152f2690e4bf2e90b86719d9f5aa98aa208462af56ab6de40762 -> 4.10.12-x86_64-multus-networkpolicy
      sha256:5f3dc4af08738c4ddf7ace3fcbea0d5f8d10321414fe2681f3a056ee55c8e969 -> 4.10.22-x86_64-cluster-monitoring-operator
      sha256:5f55a0c0979ef0961fef7f267d2ce56c3f05f1dae8d8dac8811f68cf2811abbd -> 4.10.22-x86_64-ovirt-machine-controllers
      sha256:5f7ecf6ffed4afb2cf08e2f886c0db2e446073c891fd70a38ffa8b99d3e76794 -> 4.10.20-x86_64-gcp-pd-csi-driver-operator
      sha256:5f9ee3afa744e790dbb61d08f44e30370c9a5ff041054bf99dc1afe58792cd7b -> 4.10.20-x86_64-haproxy-router
      sha256:5fb4522e88f5cc4f758d61dbacc32bbdd21da8a4bbca254bccad149d3db584f6 -> 4.10.20-x86_64-jenkins
      sha256:5fea93487ae95e3d3fc4be59de900876ba802b10056208130914f1e23f512e1e -> 4.10.20-x86_64-jenkins-agent-base
      sha256:600b55374202e02d22a78754c8eae1d2851974fc7b853946e3a2ad95b0710907 -> 4.10.11-x86_64-csi-driver-shared-resource
      sha256:6011aad069b5e8113a2ba0e82a8efbcc57522c3302e5d47cbfc2d388816fc6ca -> 4.10.12-x86_64-multus-route-override-cni
      sha256:6066981e2b360c670ce2b872db3ae6cacb13bf9cb3b80c5235955ea5de29be14 -> 4.10.15-x86_64-must-gather
      sha256:608a8c9f893b3fae358028b44238f3184af2f3bd5cde844b8b2688583b8468ea -> 4.10.16-x86_64-haproxy-router
      sha256:609e7ee8bf69cd59ea830cc47686743c9a87cb915713b1ba11dd7b87388277fc -> 4.10.16-x86_64-alibaba-cloud-csi-driver
      sha256:60b190ab6c4cf44f62f71cc8301b21a780eaeba6dc4ef28b414caa5d6b59d38e -> 4.10.16-x86_64-installer-artifacts
      sha256:60dce8520e81772948599f58b5d07bd7ac6e74e5e866f5ce59557f677f5c2862 -> 4.10.16-x86_64-csi-driver-shared-resource
      sha256:6119a71fbf48dc417da82b53e2f7fcc39b72689b0cd79377433239ba6d755f79 -> 4.10.22-x86_64-cli
      sha256:628bb1f9d1f1ab3e7bb7bb70212f39d3bcbdff74875b7640a6dd9b6ea09d216f -> 4.10.12-x86_64-cluster-machine-approver
      sha256:629b4771d02bedec74330d1e7ab8f1a166a15811a92bc4f5739515df5471139e -> 4.10.22-x86_64-network-metrics-daemon
      sha256:62c301edbac383720ec6cfb7faeabd01ccb5bf1d3e63d7587fcad99eb4b7b3c7 -> 4.10.20-x86_64-csi-snapshot-controller
      sha256:62dcbec3f6ddb0be4b4553ec902f93dc7cfb8060ffcc0617b7cdeb7089ed53a8 -> 4.10.16-x86_64-jenkins-agent-nodejs
      sha256:6332f67ddb8aaba08639689ea91e5a0075e07dbfaea704a8a2b4cb7645281136 -> 4.10.12-x86_64-prometheus-operator
      sha256:63364747637b0e0b595e709e8192f3010240f08936be0370be0072bb4b447e43 -> 4.10.12-x86_64-machine-os-content
      sha256:6391d4447930a45c0fb6affbadd27180be2176b701479708cf356c714e6ee40e -> 4.10.16-x86_64-csi-snapshot-validation-webhook
      sha256:63d75d173e0c0f4ba880b90487cc1c2216d1e4a7d4b60f1051b853d428bc2a90 -> 4.10.16-x86_64-csi-driver-nfs
      sha256:640c00071581a3140a84ea8b376f741a534278676a992c653773ef54df406bdf -> 4.10.11-x86_64-baremetal-runtimecfg
      sha256:645d0885041eec1a0660def5a541f785f545f2056b642b38bc352519fc3a0a1a -> 4.10.22-x86_64-prometheus-config-reloader
      sha256:64d2f8809236309d106cf707ac95603e78a087456dac1d6889388422de99266e -> 4.10.16-x86_64-openstack-cloud-controller-manager
      sha256:656c64152333ab7f2b70017ad77c363e6373bada47f10147fb2bdaaf150768f4 -> 4.10.16-x86_64-cluster-ingress-operator
      sha256:65e6a5433b03f5be3e11798c4b6874ecc30bc81535712c240d9aa282e00228e5 -> 4.10.22-x86_64-service-ca-operator
      sha256:664d6e80b84d8ffedc3ae3216f0715dee166446e4c2bd941b47649ff0729f547 -> 4.10.16-x86_64-cli-artifacts
      sha256:670f6703b4e76b2600d0abf3a0bb9d52175dcc8fce78002e9fbdb3330208d41a -> 4.10.11-x86_64-cluster-node-tuning-operator
      sha256:67accb79ef3346eb3710429400f31151b4b7ef4226ef2c9b3172960c9fa88d91 -> 4.10.11-x86_64-k8s-prometheus-adapter
      sha256:67bb4ef2eaf4ff42b90f83fe1628aec8c5a26a7c2c457c95dbbdd2bf63aec594 -> 4.10.12-x86_64-haproxy-router
      sha256:67e4cc45bfa2fc96bb997f965727bbd6b59dee5e7e300bf1774167901667a8b1 -> 4.10.20-x86_64-network-metrics-daemon
      sha256:682142ba83c2c218529237cd179f14eac085116cc8d2549467aefa1825940757 -> 4.10.10-x86_64-cluster-image-registry-operator
      sha256:687a995cdf5285a57df33a54d6fd6413cab8f3c18dcc5ce6f99b22c2b1373fbf -> 4.10.11-x86_64-openstack-machine-controllers
      sha256:6885e79c6ddd2c593b39a13630c75f1af6351199940973b24f93e9b5e795cede -> 4.10.22-x86_64-gcp-pd-csi-driver
      sha256:69322de62138a64e0614d12803b76bd0d9114a66a38165309d3f20dc60084f6f -> 4.10.13-x86_64-etcd
      sha256:69801a072eec5e5af42f8effd5f0abbcfec30511f34856e6a1ddbdcfcaaba890 -> 4.10.21-x86_64-ironic-hardware-inventory-recorder
      sha256:6981b16d17d3b3b623948d3c8f2fe98f3e660f67d2ba3526f186a236b76e6daa -> 4.10.14-x86_64-cluster-etcd-operator
      sha256:69bcfc204958769d7ca0c7d239e3f9b4ae13c55f6de969006bbdd62c11554de5 -> 4.10.11-x86_64-csi-external-snapshotter
      sha256:69dd39666e2ffcb2a3b271c41e2ec5b885f926bd12b0184bc7bdf4d5492cac78 -> 4.10.12-x86_64-cluster-network-operator
      sha256:69e63802c64e683eb13a208444d5e1127db66d7e8a0123bfefcad5720d3d82a7 -> 4.10.13-x86_64-jenkins
      sha256:6a3215dbfe1549b070927d5523ba045a5216eab922f027962a11d4e4a19938e4 -> 4.10.11-x86_64-gcp-pd-csi-driver-operator
      sha256:6aa91bba9cc9409f44c49a5cf99c9467cb7459dd2002a80c1579813d239a5167 -> 4.10.20-x86_64-ironic-machine-os-downloader
      sha256:6aec23ee4ec58d357cad5da7cc1c19eaab57ab1c4c4c67783c86be974cb37905 -> 4.10.16-x86_64-gcp-cloud-controller-manager
      sha256:6b34ada37b4728e08aad35dbd13f1c5e03ee02c056dc606d717804bf5f99af81 -> 4.10.10-x86_64-hypershift
      sha256:6b5b786a9a57818cdfbbef58c35d2af27adbbe328ad13bf5f8dd00c95b491407 -> 4.10.16-x86_64-gcp-pd-csi-driver-operator
      sha256:6b907f82a8f8c15fa993635f9f657424d4e3d960d2f62f5efb62f2db4b462046 -> 4.10.11-x86_64-ovirt-csi-driver-operator
      sha256:6bd619680948d177f8dd7a26ef844b8d6c27111d3014345d8021541a25b65596 -> 4.10.20-x86_64-docker-builder
      sha256:6c0f162fbd0daf43d8a4f580c764221f35b9e60fd05cc5fcdcf4ac1708484f94 -> 4.10.12-x86_64-operator-marketplace
      sha256:6c7f41cd2202ff4a687baff9133a1ccba8e77311b96cef2a781831353db90be4 -> 4.10.22-x86_64-kube-rbac-proxy
      sha256:6cfb59e6e69e8bca57ab13417cb6b1c653687139898844ddbe002724658a1e8b -> 4.10.16-x86_64-aws-machine-controllers
      sha256:6d1294466a3930e22322fc5fc2d462f15dad8f572f6ae9fbc862cee96ed365d2 -> 4.10.11-x86_64-vsphere-cloud-controller-manager
      sha256:6e1757b07a9753a10ab7f91a06eda5a1b803c52f250327c3c2489474e0efa572 -> 4.10.10-x86_64-console-operator
      sha256:6e325c74353c8184f2637011b0f3c545bbfb3dd5911992eebe2eba78defa84ec -> 4.10.11-x86_64-vsphere-csi-driver
      sha256:6e963275041b08eafa47fa119287e1d4428b5af642c3befc6f0c90900d3516a4 -> 4.10.16-x86_64-coredns
      sha256:6eb2d82ed0bd65de1ecca019b2483b01ade5954b79d53c342449f8fcbe77044a -> 4.10.22-x86_64-cluster-policy-controller
      sha256:6f06c811c9144d839783d88d820f0ab4bc050b70f36ab149a9cf4e816216963d -> 4.10.10-x86_64-installer
      sha256:6f7e688be03637659483d9d8c0b827ac6f2e7a8b3367f22aeaf1f2fef58b1b26 -> 4.10.20-x86_64-cluster-autoscaler-operator
      sha256:6f899d942c2ffdb7e63c49cce5b1edddebf46865b8a0155db46433424f9f0f3f -> 4.10.16-x86_64-operator-lifecycle-manager
      sha256:6fa56e78795c51739de881cce4d2bf42c6fcdbcfa274f740e0ae3d3b331007c1 -> 4.10.11-x86_64-cloud-credential-operator
      sha256:6ffdafc3bb2a509068b59d35739d311ab537d8239a29bdee3ccc69a326e2a4a0 -> 4.10.16-x86_64-cluster-authentication-operator
      sha256:703dcc99ea8b944b99849eb90762ca5ed7469240dd9a7fcfb6b61086da81d670 -> 4.10.22-x86_64-deployer
      sha256:7126d7321ba855bf24aa4e7e03ac92c5ecacc19ad567ab6fbd3b3e2b2b991e4f -> 4.10.22-x86_64-telemeter
      sha256:716345aa2f23ed9d22f552986df2d8171459e7a7f106533c87bd9b6a1b584f86 -> 4.10.20-x86_64-ibm-cloud-controller-manager
      sha256:717c4a3cdc726abfbb1037a2a62fa3566e8575a05a328001e39e14d9677f5d8a -> 4.10.22-x86_64-docker-registry
      sha256:71884b31586890c11eb4db557e16af12db9b80ac532ccd4cb66e6fb8028da626 -> 4.10.16-x86_64-ibm-vpc-block-csi-driver-operator
      sha256:719a14eba98e5e59888f7d77f5409456cfdc8af4864a111caa9de5390ff27dd7 -> 4.10.11-x86_64-csi-node-driver-registrar
      sha256:71f81992c8fec5321280422acc6ee83c5aad31437726a3294ec40bd0de1ce7a9 -> 4.10.12-x86_64-gcp-machine-controllers
      sha256:7207c8cf55d084d87fa01b33854cff0aefc446d2e2de663b54a8bb297db9cfb0 -> 4.10.12-x86_64-kube-proxy
      sha256:7253ad6b80ba1edf737f172ce78d5b30af9af0c409eb5429b069cce2619c9cc2 -> 4.10.15-x86_64-machine-config-operator
      sha256:727fe0f3dcdad60f8beee58590b33815345e12d86f9526af81b45de17fb28f59 -> 4.10.12-x86_64-prometheus
      sha256:72c985011200f103ad95899acd09b01af755bb976803bc8a16b7be78f19f7a40 -> 4.10.12-x86_64-aws-machine-controllers
      sha256:734668b956aad96b92be60b70ec7cf283d3887cd1c1172fec9f47ed9efe0279e -> 4.10.18-x86_64-kuryr-controller
      sha256:734692fdccaca8c3317f078dfee7cb2df25550f47247b67ffd242cac6ad0bb55 -> 4.10.12-x86_64-hyperkube
      sha256:73610667c64c1fe9980c6d42ef5c770f16521dcf52fb0d188c6c7c6e79c499d4 -> 4.10.12-x86_64-container-networking-plugins
      sha256:7405dd87cca280b924d82ccc170ca86c472b717fee1b45fb8a257f5f2ed343b8 -> 4.10.11-x86_64-grafana
      sha256:7454636d75de0b64ac1c73226335a0b8e57effc5834cac49acf42fb6bf1d5e8a -> 4.10.16-x86_64-jenkins-agent-base
      sha256:748fbfabc432acef10b37e564688ba5e0fe934361ecb1913e40fa4d34cdd6bd3 -> 4.10.22-x86_64-egress-router-cni
      sha256:7493b9ed031744f304cae107b7fe50bb98e2df01d66ea9c67b0749010b524631 -> 4.10.14-x86_64-console
      sha256:7498045d6cb60989631e733ee0053c1803b55cc059b817e8dcff43555f1ae345 -> 4.10.16-x86_64-cluster-samples-operator
      sha256:74af1b8f610579bf5112a9cdd9338814a07362a19fb9cd24a753348e6473ae42 -> 4.10.12-x86_64-cluster-kube-apiserver-operator
      sha256:750e142e941a9b9e1822c52bc671d01ebcb5bf479b42fab978b76c44b17685b5 -> 4.10.12-x86_64-k8s-prometheus-adapter
      sha256:7599b4734c2d7f0e9d779d5bbfcd1af2184d0e8ace662fb4b927e55fefe36a0d -> 4.10.20-x86_64-operator-registry
      sha256:75b59fe9fe36b4d70f7870e30cffba31bf1bf9a9db4a667e794647a3fef58397 -> 4.10.12-x86_64-ovirt-csi-driver
      sha256:7680ef83f71e2e5d646d8fb067dc6409b83efe178c3c9a44f9ec40a8477e937a -> 4.10.22-x86_64-multus-whereabouts-ipam-cni
      sha256:772edcafbacf208e6094b25f0ce924f5e3c9651a611dd28e8493f1ce274d9874 -> 4.10.22-x86_64-ibmcloud-machine-controllers
      sha256:777111fc99844b71b012b69c6f67cf73a60c76be351943240a9169fcf00c45fa -> 4.10.22-x86_64-configmap-reloader
      sha256:779868230ca30c4dda8b6dded85d3a208ec9357ceeee3cdf5bba5fcd08fa4bfb -> 4.10.16-x86_64-operator-registry
      sha256:77ccc01dc7a7a6d5518a689ebfa50fa70c208ee804cf58ab0e0c19dcda7126e5 -> 4.10.13-x86_64-kuryr-cni
      sha256:78049cd460ca2d42cb3de21c6041a6de305708b3d9d976a6e7dcfde5ad991d18 -> 4.10.18-x86_64-kuryr-cni
      sha256:780d613972d764910b16e8e9b3fae1711a3779137bf6ac39bbd3ec463fb26767 -> 4.10.16-x86_64-kube-proxy
      sha256:789bee8a99c93997d319b86674efa8c5ebbd5f7d91bc03be457edaff26cdc8c1 -> 4.10.12-x86_64-kuryr-cni
      sha256:78b457959f1c8ed6ef4d0886be2272c10864749f18c0eacf22309d30e211ebec -> 4.10.15-x86_64-jenkins
      sha256:794adb97803879fc56331519893da0d8d17f31e058c0bca4972bc9937a253ef4 -> 4.10.12-x86_64-service-ca-operator
      sha256:79cfdf0364d05a02840622bf801f80c5832ddad8afd820b323f95b3f96b57354 -> 4.10.22-x86_64-cluster-image-registry-operator
      sha256:79f6d5231a3aefe8d62a7438d10fdbabf762e216391f8210279569d2cfdd172d -> 4.10.12-x86_64-csi-driver-shared-resource-operator
      sha256:7a0ad0b0acbcdef440367c985a2e095a10de23e2f22b0527c5b6b78022755f40 -> 4.10.22-x86_64-installer
      sha256:7a3376e6677d457d54e90d9e4b0b34f26ed4e406065c378005a9408674506527 -> 4.10.22-x86_64-cloud-credential-operator
      sha256:7a748393b965dc34298c3d199becff47f59b4e22d6af195d5577687ba90b9bca -> 4.10.11-x86_64-telemeter
      sha256:7b0f935a0d253c1b08adce9d7852fc9c5fb0946cc42e81f976c1ddbaec38d5bd -> 4.10.11-x86_64-aws-pod-identity-webhook
      sha256:7b2c006c283e3e427f9e613bdc5449e0c8b1fd4c09d20845601719edb7ab8b9e -> 4.10.22-x86_64-azure-disk-csi-driver
      sha256:7b7ad5898f2d0d0a854fd8571ba79900c24d1cf285f71db32669578526405716 -> 4.10.11-x86_64-cluster-cloud-controller-manager-operator
      sha256:7b825eefec544f43af2413e58d1ec12648e8ed939e5015b80ea8a65b7170bf4b -> 4.10.12-x86_64-cluster-update-keys
      sha256:7bc7260f7921fbd5a9aebee70ba0fbb822f0e0247b04ac64fe13c7af8d941c90 -> 4.10.22-x86_64-cluster-machine-approver
      sha256:7c05e7f0aed54a7ffa2c4e95d458e395a6781647682026dd614c1cbaeb713eb9 -> 4.10.18-x86_64-vsphere-csi-driver-operator
      sha256:7c330b88fb2d0bf06b02892745c7fc3096b6325a49c854f3a7d80a87048f6401 -> 4.10.22-x86_64-alibaba-cloud-csi-driver
      sha256:7c7f10fb6da55f4d51531591ec78781efaccca440dc5cc011cf4791b7f2e54c9 -> 4.10.11-x86_64-network-metrics-daemon
      sha256:7c9ce037d8f73dc66e5d10c540863e70cbc7726147ebf270aaed8f4dc37b681b -> 4.10.13-x86_64-cli-artifacts
      sha256:7ca4e7ff48674a9b16b0846d3a2fc1ac56a342fc258a9bf5014521628db86f9e -> 4.10.20-x86_64-installer-artifacts
      sha256:7cb1aa390a4ce1b9c733f2aa508b463fe96de104aac245c1d068ec3f113d8b0b -> 4.10.22-x86_64-prometheus-node-exporter
      sha256:7cbb46dd203262d6770eb1e9462992f40c23f9aa72a687ad3427c6931b4046f6 -> 4.10.14-x86_64-machine-config-operator
      sha256:7cff8d9451a474e61d09656773e7657fb160dfe58ddefede65072cc0489299de -> 4.10.20-x86_64-tools
      sha256:7d22ba343e3a9ded6578f4633d986e10da058ed66065934f99e1da7540e0b085 -> 4.10.22-x86_64-vsphere-csi-driver-operator
      sha256:7d49895c6f16adc787898646b6882f4e4f91805afd25a63526634cd99989b389 -> 4.10.16-x86_64-csi-driver-manila
      sha256:7da733e0487a8d62c5cf231c13e3b676634c2a2c4426344233ed6ee779c4b933 -> 4.10.16-x86_64-cluster-dns-operator
      sha256:7dd4fca1f03f65c7837e3b3420d1df10ff612ac36f5f1e8ecbec0d6fc0c49659 -> 4.10.22-x86_64-grafana
      sha256:7e3b6d3b6f035940be06837fa0b87165d08217603dd203598fe4995296157f07 -> 4.10.20-x86_64-cloud-network-config-controller
      sha256:7f27ae3d7a6157515600ccba05bf0716684c344b657dcb8ffb9a6c66f290859f -> 4.10.12-x86_64-azure-file-csi-driver
      sha256:7f7f0e1be5316d4b1d74808f3a242e59d5d38befffd37a328d84d46ec1760e42 -> 4.10.11-x86_64-ibm-vpc-node-label-updater
      sha256:7fa76f0e5476b71f1ebd762950e98b4e92ca0bc4c2450c5edf2b4774d6e32cca -> 4.10.10-x86_64-operator-lifecycle-manager
      sha256:7fb3216f52191e7bd03df1c895c2d7093e023c59cc69244f244e784ecaec332b -> 4.10.20-x86_64-azure-cloud-node-manager
      sha256:7fb7ecd29c8ddc0ec460344ccef7a2b3a3500dacea1238228e0464c5f27f0d65 -> 4.10.20-x86_64-multus-admission-controller
      sha256:8007206cc3df989f6dc9198a255fe27efcb7e49bed8067693a958566e53cd624 -> 4.10.12-x86_64-jenkins-agent-maven
      sha256:80334f856ee9a5eae61651a5b18411247dbb32a6fb005cffeacc6ffcdfa7b622 -> 4.10.17-x86_64-installer-artifacts
      sha256:807018485197689feb08fc4117f242499e1714f56607c86ba74a36a5b84e8448 -> 4.10.16-x86_64-cluster-storage-operator
      sha256:809ae2b469a4ab20b0b2e93302372821061f2eb1fbd7ccc9df6bdbe950710779 -> 4.10.20-x86_64-gcp-pd-csi-driver
      sha256:80c70784939e8d42c3436e296baab2c3f3322b483e82f63c8a9fe34d3f9988aa -> 4.10.12-x86_64-azure-file-csi-driver-operator
      sha256:80e193b9e36e7262afedbaf1d6608d542eb1614187a12d3720c506531ac57ca2 -> 4.10.22-x86_64-openstack-cloud-controller-manager
      sha256:8101a2aa4bc0785c7b0ea992061ffccbddedd48fbe0763c903486058da2ebbfc -> 4.10.11-x86_64-jenkins
      sha256:812af35a109e30a2bd2a084d220894fc14b59bab2f58d28b616d3796fba809b4 -> 4.10.22-x86_64-console-operator
      sha256:81528ecc79d63850fdba7f9833f810aa8d34721d18cc9580d0b9a1a8c8285ee8 -> 4.10.15-x86_64-ironic-agent
      sha256:8230959f216c9dfdc5f26a806e30f508320a54edc5dafb0b2b99cf47ae495c92 -> 4.10.11-x86_64-cluster-etcd-operator
      sha256:8282348bd5997bbcf1e2ae8b18b6efe0b06c9a0f8ddffe296062dd21ea90129c -> 4.10.11-x86_64-installer
      sha256:82db40fc70d17ceaefc83ecab75a134b5bfb44deffb343d2868a2f17c5b82302 -> 4.10.22-x86_64-azure-disk-csi-driver-operator
      sha256:82f18ea4307e001a0a69458118f3cf2ea873e021ac0f3cca3899a942c5f612c2 -> 4.10.11-x86_64-insights-operator
      sha256:836f15921afe0dd891457efd1d0b9ad01bb354cc7765d508fe08ffc02ac4b223 -> 4.10.21-x86_64-cluster-image-registry-operator
      sha256:83b6757c470c598148fc005bfcaf18aac8ab97d53f9b26c6e0450f05f753be22 -> 4.10.12-x86_64-openstack-cinder-csi-driver
      sha256:83fc8253611fc117513c9b5381d1849b3fe57d24300618cfc7def83fdc7fdb88 -> 4.10.22-x86_64-openshift-controller-manager
      sha256:841a035526ab33d076bc785dc1c430bde77d773765f2ef2e4a33452e01d26ab3 -> 4.10.22-x86_64-cluster-config-operator
      sha256:842d25aa2db5207150eabc1a2b5981667abbcdf53b28f9be85ce43dbbdac386f -> 4.10.20-x86_64-csi-driver-shared-resource
      sha256:8461e1b386b14ab00e52943010c5705a5e26e02ccf1f869d08c3059d4ad52d9f -> 4.10.12-x86_64-tools
      sha256:849f33260d8cdd4720630ed86c46beb9ca76cbe83982fe04073090f69adf0616 -> 4.10.22-x86_64-ibm-vpc-block-csi-driver
      sha256:84be7ff78010f7a6127ee6dcc13082fff11afb502e18ebe18e258fb3f5ece84a -> 4.10.22-x86_64-console
      sha256:84c0418d83452f9815d6c2a9dbde03f59c087b203ad507fbeccc832936aa3eab -> 4.10.22-x86_64-oc-mirror
      sha256:84e5dec6cc27b7c738d2465e7e999d1af2012dd3f825be118b47dcb5dca37dc6 -> 4.10.20-x86_64-csi-external-snapshotter
      sha256:84edddb4322cfe50d8545c41014a8f5595a4260a20d9a269e144909c5949582b -> 4.10.22-x86_64-driver-toolkit
      sha256:8524c9458dd9517ae6d72b447c8c822ee3960b0bf660c6388d046b0ed8f3704f -> 4.10.22-x86_64-csi-livenessprobe
      sha256:8592585e1d462b9492a889d7dac33f15acfbde18b850a4f87b9d0382c4611bed -> 4.10.16-x86_64-vsphere-problem-detector
      sha256:85bea6925e4af08ceb4d9e831d917f21893d40b45dadfc2d0f2c17bc250d625b -> 4.10.12-x86_64-jenkins
      sha256:85fe6f78cd27adec4c597d06e6f681f807d5582b1c7cd121de26351f3ccf1e7f -> 4.10.22-x86_64-azure-file-csi-driver-operator
      sha256:86098ed0fd51af5ad2d5b7d0cc98ad025c4c29187d49dc0ce0a3d5420dfe3aa6 -> 4.10.15-x86_64-aws-machine-controllers
      sha256:8669914f068501bdb755e484418b49dff9bc6eeb9bb07e07bf7e2e261a7496b1 -> 4.10.22-x86_64-csi-driver-manila
      sha256:86a84e949d6d3890844e7a66a23d6cf4e2290c1b26c380fe57d4ee11e6f8f60c -> 4.10.22-x86_64-ironic-agent
      sha256:86c8418ddb94a43186146f082fd94d9b2068a07639dc0c27b05f43aacf3eb6ab -> 4.10.11-x86_64-cluster-samples-operator
      sha256:86dd7b08961016140fa77d264174f3bed58bea59db09697ba00d9c8feb2a84a2 -> 4.10.14-x86_64-kube-proxy
      sha256:86ff76fbc90d77b16c42c0022c72d5b71102ad52e186a654af7a3cd1c8b4dda1 -> 4.10.22-x86_64-ovirt-csi-driver-operator
      sha256:87a166dd8f21d8ba612ee0f49dc8839cb222f77156dcb67ed82e1c3ed13127cb -> 4.10.22-x86_64-container-networking-plugins
      sha256:8811af65a4e41cdb4a697eed0f043c24b2152df54f961b9f36e05ce6f4e1f853 -> 4.10.13-x86_64-network-tools
      sha256:882c554587a704c7f6ff8390cb7398a50f022da9155d8cf2afc45ebefb03a831 -> 4.10.20-x86_64-ibm-vpc-node-label-updater
      sha256:8886eb8d0f279a077eb667c59488cae3154a8e4f92be25e92df8590e7d9e62df -> 4.10.22-x86_64-csi-node-driver-registrar
      sha256:88a9264246ba1c3954426dafdf277320c038ea6fd5ec9d399bd805a75ea65a33 -> 4.10.16-x86_64-multus-admission-controller
      sha256:8901f71be47872f0346e8461f3f78f7e84fa6d8affabf70ef1e428442cce9c35 -> 4.10.12-x86_64-docker-registry
      sha256:8998f41054ed54588ca7719493e4c3785c603c85bdb345080443ba58781255c9 -> 4.10.12-x86_64-image-customization-controller
      sha256:89ac84fc71b4eab3168fae7f5509996e4414938f63a2097f9ff96d700ef4e366 -> 4.10.22-x86_64-csi-driver-manila-operator
      sha256:8a3207dae1eb921166f5143c0b8bdfa05248a97cbe3a664930763ca5990931e0 -> 4.10.12-x86_64-cluster-autoscaler
      sha256:8a5a316dec56c12072bd11525534dc01cad163fd44766be4e1fb3406796e662d -> 4.10.12-x86_64-thanos
      sha256:8afbd420cf7d7662f61adee70305e8dc4f13db6a45a15a896a3ef889dfd54957 -> 4.10.12-x86_64-csi-external-provisioner
      sha256:8b0e194c1cb6babeb2d8326091deb2bba63c430938abe056eb89398c78733eb6 -> 4.10.20-x86_64-service-ca-operator
      sha256:8b2d4b65d1eed177da19d92889a7258e9c01bb68f2ec678e5402163961f3a61d -> 4.10.22-x86_64-kube-state-metrics
      sha256:8b4e7b65c95e72a414a1907d75016f0abf5b5bd095aa1276abd39057b8ec8671 -> 4.10.11-x86_64-ovirt-csi-driver
      sha256:8b6857a6b10bff8e941a8c51ddc2f22a821f42fef25e4ed9a7024b1de97a2000 -> 4.10.22-x86_64-multus-cni
      sha256:8b75cbb25d16e48afd464eeaa6676ec6a181a1b5db455c57f5f466bf4c7bd319 -> 4.10.22-x86_64-pod
      sha256:8b9a92276f84629472c0c9ab379d1e7da6a4a1638d4d2291acacbdb1dfaeda3e -> 4.10.22-x86_64-gcp-cloud-controller-manager
      sha256:8ba3828cab268a9331a9ba6380bffea85564ca6a10f270a48c55384ea07d79f2 -> 4.10.11-x86_64-ibm-vpc-block-csi-driver
      sha256:8bb06c4ac4a0a126f831ebf95e0ac0980e01d352ae268140ae547c88a79209e4 -> 4.10.12-x86_64-ironic-agent
      sha256:8bf20ebd25c8faf36f26600c6cac8d198329c30221dce0736b67c0e465d98e80 -> 4.10.11-x86_64-jenkins-agent-base
      sha256:8c00e6df3b34edfdf8c3bbf4d23b39fa91de238d322f657dc4d8341f3db77e70 -> 4.10.22-x86_64-aws-cloud-controller-manager
      sha256:8c8a00dfc70a375817b55366579d216dd9b9fba56f75703e41384ed1f2923c85 -> 4.10.11-x86_64-cluster-kube-scheduler-operator
      sha256:8caebd4b7973d32f1d7590ed5f3d55825d7e27169c80b21dfc7f216394843e67 -> 4.10.20-x86_64-ironic-hardware-inventory-recorder
      sha256:8cf14583bba13b0da47c5823c2dc03d9b4a1df6af288c2e988926dcc786c6e23 -> 4.10.16-x86_64-cloud-credential-operator
      sha256:8d47f15a2d09e076f905603ea774c4e71c11e2a2f4b96cd67926e0f1f18ebb8c -> 4.10.20-x86_64-vsphere-csi-driver
      sha256:8d5a0710ae02a702b3b539c6ee002c102a4cda63220b7aa7ba75bd6f5083dde1 -> 4.10.14-x86_64-cli-artifacts
      sha256:8d6ecbb4e09572ffe28f5b39167ba899d2d9ed757b74b92b15bff5ecae745bd2 -> 4.10.13-x86_64-installer
      sha256:8dedcb3c0b3dc9178d16afe41bc1f483106260e19a5ac0daffcefa0167bde60c -> 4.10.17-x86_64-ironic-hardware-inventory-recorder
      sha256:8df02a097fca2887c85b5ed5c1891e7972f4cd811eeac078f3089d71c32133b9 -> 4.10.20-x86_64-cluster-dns-operator
      sha256:8e817e996a72b9c4467019f696eb2cd358f9f37eca9324dab9c6737056b19bf6 -> 4.10.11-x86_64-cluster-kube-controller-manager-operator
      sha256:8e8ccc0bc92245b24afc762c98b24ecbf24a9b28a7d4fe497de790e761633411 -> 4.10.22-x86_64-k8s-prometheus-adapter
      sha256:8e98944cb0a7fe849f310964aadac4c06afb577dd87ce62d0b609102a32ff05b -> 4.10.11-x86_64-machine-config-operator
      sha256:8efdd17953dd735e046f1fc38c2fbaa66bb8120e39ccb6ca8153e68826ded4f9 -> 4.10.10-x86_64-jenkins-agent-base
      sha256:8f30fda78e5915109aaa8bb98e59b36f92f6e2af43a95c872e00ab8ff7f33bba -> 4.10.17-x86_64-machine-os-images
      sha256:8fabf3ffcb4997eecd390bbd0dad704e808bafd9f8f7abee2b0618f5180694fc -> 4.10.14-x86_64-jenkins-agent-base
      sha256:9053faaf26e77ee41b159cc91af3bfdc9e76c5d707e5b4675b462d1be45af267 -> 4.10.22-x86_64-prom-label-proxy
      sha256:90dbda956061d1996db361f11d3b6fe7f4823ffa25ec14029031d36fd836e930 -> 4.10.15-x86_64-cluster-monitoring-operator
      sha256:911d53006058fdc8669b824da59c8ffbdf7adfda427448d161e36e1e196081ed -> 4.10.20-x86_64-cluster-config-operator
      sha256:917638b079377275196070b45ae28ed7e8549b036fb0cc9fb26492459bfe74a6 -> 4.10.16-x86_64-cluster-etcd-operator
      sha256:917832f1fce7b620637edbadd6c3d5fe7455ba04232129ea4b570d5e65b63100 -> 4.10.20-x86_64-docker-registry
      sha256:91cb28f93685b7e881892c23e98be9d94ef2a1b91ffb03a254e57570cd8f382e -> 4.10.12-x86_64-alibaba-cloud-csi-driver
      sha256:920de3b037df8e9649a4a0e50f60507a9a202fc9de12f3a7b95748ca89829684 -> 4.10.22-x86_64-vsphere-cloud-controller-manager
      sha256:928ef7f43a9c793ebe2a6134acedfd487e5d94d79e3507394525091dc92679dd -> 4.10.20-x86_64-egress-router-cni
      sha256:92c0fbe4aab6876902a0ebd930885ae080a4318d278f0bf0a7a9128ee104c6d8 -> 4.10.20-x86_64-openstack-cloud-controller-manager
      sha256:931d4d1fcecc302b891df71b202f5bda336b52258054499a7a5f32427714da6b -> 4.10.12-x86_64-deployer
      sha256:938310aeedba130b66204b062b7d86d562bdff2d6767fd643176cea3754e0d37 -> 4.10.22-x86_64-vsphere-problem-detector
      sha256:939403bd3d7ae50a07497a80062267910855e0fa4ba6d67c2fcb552a00035472 -> 4.10.16-x86_64-installer
      sha256:9394739967aa563dd6a710be8e48075b5d73ca59292938a13d7188ab10b2f593 -> 4.10.15-x86_64-installer
      sha256:939a5284c40fd69ab00782c23c932b2ba46fbd6514aaaedf97ffb7b011b91984 -> 4.10.12-x86_64-must-gather
      sha256:93a968b65a29e7e13dc7f9b3b7957746f5abc945219d3ccb2912f35413291b89 -> 4.10.13-x86_64-cluster-network-operator
      sha256:940c81537e6691be5843d15c0de0a0af8f979b7ab8c894565b712a7bd969a835 -> 4.10.20-x86_64-azure-file-csi-driver-operator
      sha256:941b6eecb4ecda5796fe070e39e9328159e482fed3dcf09bf8d6c189f62a3c1a -> 4.10.20-x86_64-must-gather
      sha256:945ad44b06380a0dd71d6cd51f8d96e44874526bb8ebb1de3a78fefaacbfbe01 -> 4.10.11-x86_64-openshift-controller-manager
      sha256:94627a07ed45571cd5cc14c6509a162242efa5eb8accf1bc7930d36fe3f7662c -> 4.10.16-x86_64-openshift-controller-manager
      sha256:94a4cd55cb067f6d3747843b5532114806b6c81e8421e370bfe92ccafcc6bca7 -> 4.10.16-x86_64-service-ca-operator
      sha256:954a538d6387e380c0bae49dc3e055036d6379f10c6d0fb115f75a821cfb29bf -> 4.10.12-x86_64-cloud-network-config-controller
      sha256:9579fa1cc98e68d0fd414b4c8511e14062186d85ae1c8aeb1f2292e2edc46aa2 -> 4.10.20-x86_64-cluster-ingress-operator
      sha256:957a2dc20cc01a3f2fbaa836927aa6833699322657915846e9a84c191fa25bb7 -> 4.10.12-x86_64-prometheus-node-exporter
      sha256:962c253a360d377bb0f0c32f12990a5569380a0caa131daaa459a6c2aec8a348 -> 4.10.16-x86_64-network-interface-bond-cni
      sha256:963a226d6859b9e31cfc5326e30e59fe9c26d624544fd3ea95c9515cf602b844 -> 4.10.16-x86_64-kube-state-metrics
      sha256:966597f3bca51e0bc7f33b5892bf929c37081b8fe3812e55d82e353d6603aa6f -> 4.10.22-x86_64-kube-proxy
      sha256:968c55e2aceeea83edff635fadafc21af88c9c769bf1fa1aa10502701d000dd5 -> 4.10.18-x86_64-machine-config-operator
      sha256:96a0a30df8f3282fef2a206b6af6b35df455128f654345ee6321bd8462defc28 -> 4.10.22-x86_64-openshift-state-metrics
      sha256:97253518289360f50c3de3be8bf508fadf50a18935e7990d2bdde658080fdb9d -> 4.10.22-x86_64-aws-pod-identity-webhook
      sha256:97612c1027a874f2f5561f13f98aa9fd484764bd822538a4aa4ceb44a18a3f99 -> 4.10.21-x86_64-kuryr-controller
      sha256:978956809bd9364261b44492ad80e9bf5e8edca14d2e151df084ac9517acc9fa -> 4.10.22-x86_64-aws-ebs-csi-driver-operator
      sha256:978d83935ff5928d2eb979e52ff2dc4dd8372fedddea5c4eaa1f2a977793f6c8 -> 4.10.11-x86_64-cluster-kube-storage-version-migrator-operator
      sha256:981dd44a6b2bcb15e16e1218b7c10239550e52df96f50b4cfc5ef218cb060b66 -> 4.10.14-x86_64-sdn
      sha256:98255666d80f741f8bf5569e39c50422ea4d74b2a52cc2781baf7bb844e0419d -> 4.10.15-x86_64-jenkins-agent-nodejs
      sha256:9869058a7f141b290519650542135b4da045595d0956431490695c1e34848c65 -> 4.10.20-x86_64-vsphere-csi-driver-operator
      sha256:98d37ed61e064e98c5743f2c45b9706b89ded21c49ba939beb3f0ae32e24826c -> 4.10.12-x86_64-oauth-apiserver
      sha256:9971a93be34246ba519cb8e1f8a4f4e547f348e5b7e5f86abc05d7bccc7c3f50 -> 4.10.12-x86_64-oauth-server
      sha256:9a52a1eef4a91aa1e5274d358023eab8b7f791ff0f6d44156b1c2d1fcd9aad4e -> 4.10.11-x86_64-kube-state-metrics
      sha256:9a750db9c42aca6c047f2a10f29d5a82ef7b7a84c8e61cefb96ed2fc8d9ca934 -> 4.10.16-x86_64-etcd
      sha256:9aa1cabdc538aa8bec9aaf8ae0f170c7083a121e1476e05d547fce4d386ec379 -> 4.10.13-x86_64-openstack-machine-controllers
      sha256:9acf2701c0428d632b2f63d1cca9b5d54d51a25716859aa23d13c6f19e2e6207 -> 4.10.20-x86_64-libvirt-machine-controllers
      sha256:9ae4b3c25801ace768dafc7af54e010fe72d696e8f8e77fcd0f1eb9134565709 -> 4.10.11-x86_64-service-ca-operator
      sha256:9b2f05497ca63ea047f8dfc907f4b3ca0ec3580aa33b05b54d13d4965496c7e5 -> 4.10.16-x86_64-multus-cni
      sha256:9b72ab3d8e909a5dc88e59d63f2a8ea13b4fc5800e28265a22e03211c45d65c9 -> 4.10.18-x86_64-installer-artifacts
      sha256:9b81affa829bb311f29fcefe2f35c1d6e2fbfc416e54c642e05e1154ac911a5e -> 4.10.11-x86_64-haproxy-router
      sha256:9c628431df0323c8ba75fba4a19e992e2b32f313af5674c9f37fbdf230793f55 -> 4.10.20-x86_64-telemeter
      sha256:9cd654b717d7cdc4e35cd758746e75d5aed6f5741f25ad34699cab7c04bfc411 -> 4.10.21-x86_64-cluster-network-operator
      sha256:9d1955b1b883a58fd05ef3a4fa046f9845e5074adf05189126e78603441aa4be -> 4.10.16-x86_64-kuryr-cni
      sha256:9d5feef858db2169c4903731ad115695532fdfc900eedf38af7d257d83c44e46 -> 4.10.13-x86_64-kuryr-controller
      sha256:9d6149c1bc4f5629e1d18ecc0a15b7e8677519b0fbb8a13b2eead89d918460f1 -> 4.10.13-x86_64-machine-os-content
      sha256:9d7dbe4acbedcdcb28f39a5e3a12df6ba60fbd4b4e8113b3f3536f266412c64c -> 4.10.18-x86_64-ironic-hardware-inventory-recorder
      sha256:9dbcee7091d8dfbcc733f96da159ba0f50666477c14bbc21a60af3acc7a8115e -> 4.10.20-x86_64-container-networking-plugins
      sha256:9dcc935085be87bb81912ac01de8b2edd8db567a8937f155fe65c73483ee1e47 -> 4.10.11-x86_64-kuryr-controller
      sha256:9dcda2acb658db3936c50738f22f41ed7a6916b2755be10a64704a86dd541695 -> 4.10.11-x86_64-csi-livenessprobe
      sha256:9df3a5ff7c988a0962fb82843a208e04bc2fb019ec317b5439eec6a5b32d8331 -> 4.10.20-x86_64-aws-ebs-csi-driver-operator
      sha256:9e073bb660a1cbef117c11b36fb0fce935a24d51fcfcab9f8cb897971167b768 -> 4.10.20-x86_64-openstack-machine-controllers
      sha256:9e0b4bad12bcc4519a3156e13dab30a359116faf950a2da2f3188cbe5cfada2f -> 4.10.17-x86_64-kuryr-cni
      sha256:9f0db534e1a3648e3041d2e2df985ddac14f652d10161d61a51b2a6e49d765ac -> 4.10.12-x86_64-operator-lifecycle-manager
      sha256:9f205fa01025ea4f3567f00ebc8013a16e6c43e371eed32913749f021591a696 -> 4.10.11-x86_64-prom-label-proxy
      sha256:9f5a92a74168309a75edcc2d0972e91ba5730520fd2590fdbd2a00ce9c43e0f2 -> 4.10.11-x86_64-vsphere-csi-driver-syncer
      sha256:9f7d8427df91ca9ef78c9292261f8539f235b2cd423652c804b2d5f833048927 -> 4.10.18-x86_64-tests
      sha256:9ffa7294599af6a7a81abd0973d650954cde6b49e5fba010ebf81b7a4f6dc4d4 -> 4.10.12-x86_64-vsphere-csi-driver
      sha256:a0106455e0db7d93dd2f02e9dd18876098123e5ccd904d8e4f630b9f793c02a0 -> 4.10.12-x86_64-baremetal-operator
      sha256:a061f390054dfb0869ef862f9cd6160af4e6c2cae2214629fd4145ad68692d82 -> 4.10.22-x86_64-prometheus-alertmanager
      sha256:a08582edfb2b3c6ba84636bfefd74020d20023bfed66cf0329c24dbbdb6f42d3 -> 4.10.16-x86_64-prometheus-config-reloader
      sha256:a0cc7d0c4463dd387d9dfae42e954f5b5f22a642bbae2814eb4c4fceed53bd1b -> 4.10.21-x86_64-image-customization-controller
      sha256:a0fb9e3fb93589d705b19eb89fc91684da73ac452044444d8aeb93cb6ef84268 -> 4.10.22-x86_64-ironic-hardware-inventory-recorder
      sha256:a18d11de075fc9b11409f4bc4864bc73023d1510b0bb62b78f156d9a8819386e -> 4.10.13-x86_64-haproxy-router
      sha256:a1f63453ab87e2833e3866339bde826c8233d2a73a4ce8f0c055a47507df86c7 -> 4.10.18-x86_64-azure-machine-controllers
      sha256:a2733d8dad9ff5433dcb741e524077e05b31313134a0bcb40468f88a8588d5c9 -> 4.10.18-x86_64-jenkins-agent-nodejs
      sha256:a2deab149a9d0ab4c279a9bba8f396559d33494ee91f1bf9d926309f36cfd09f -> 4.10.11-x86_64-azure-machine-controllers
      sha256:a31c8139c3d275bfd6f3e8bbad53fe1b37c0b72abbc2119072651649772d0595 -> 4.10.17-x86_64-alibaba-cloud-csi-driver
      sha256:a3f4e4cf64df0e0fde74a50e875ae95de2d913a85d42cb10ece8df693941c328 -> 4.10.18-x86_64-cluster-network-operator
      sha256:a410449c6a7658223be195c3f8b77f1efbca939d415f786ead996655f07149fd -> 4.10.11-x86_64-baremetal-machine-controllers
      sha256:a4934b12e3698a384826bae660ebfb84a30f68e0519fee949ebeb4f4074f26c6 -> 4.10.22-x86_64-alibaba-disk-csi-driver-operator
      sha256:a51d5ba37a13803ad2f98d0b61a8571db4943feaa5c7b316b3932860b6ad627a -> 4.10.20-x86_64-image-customization-controller
      sha256:a553ba018b3e7bfffbd62351508858735fd15f4b0084ab541d87c4d52c7b8882 -> 4.10.12-x86_64-operator-registry
      sha256:a5a393cb0584f1c147a3f12eb879d23daf30db05cc51bedd3e7b2870b7428891 -> 4.10.11-x86_64-csi-driver-manila-operator
      sha256:a5a3a2b75ae62a921a4ab43b55f99401e93d55da42c5ba4b5cbd98c1bc785fff -> 4.10.18-x86_64-network-interface-bond-cni
      sha256:a6e7ae09b81bd3380ac2d1a93c88f4bb1272a109436804fc294ca6837de0bbbe -> 4.10.13-x86_64-jenkins-agent-nodejs
      sha256:a6fb2b5743584a424952bbee8f897da41a6835fd09712f95dcef73b598b5c777 -> 4.10.14-x86_64-installer-artifacts
      sha256:a753cc51cec26978d9c26c3b60e9d3522c9c20fad2a583f06f19cafe5420418b -> 4.10.11-x86_64-ovirt-machine-controllers
      sha256:a7ec6b7d56c6d3061adf0c292803da1ae8717cbaaaa74957f94553246cdc9288 -> 4.10.16-x86_64-aws-cloud-controller-manager
      sha256:a7fe4a3f98674ae13a15e571fecdb1d1f45942ae6ffd243e4ab249bd7695564d -> 4.10.11-x86_64-baremetal-installer
      sha256:a809ec90f76df0de59d142ca72aada1f9615038da6401bcfea070263ba82c0d2 -> 4.10.11-x86_64-ironic-static-ip-manager
      sha256:a85a7ef6abfb53323d24490a95a09c15cd4b75c57cdab8b8dc90ab5125be128f -> 4.10.20-x86_64-prometheus-operator
      sha256:a85d093e161ccaf88e4f28085be64bfe2ed88b4beee7492dae02b776fabb53ac -> 4.10.22-x86_64-cluster-update-keys
      sha256:a8d2a3aff8eb4d3148b9b1555417a82fdd34231b2cb5e4855c138888f832dc99 -> 4.10.18-x86_64-operator-registry
      sha256:a8ec64a22d4ca9cf3a956e223a9d1b5ad89c30a48e85a9c6fa995f2db32e4389 -> 4.10.11-x86_64-csi-snapshot-controller
      sha256:a8fbd0f03dcbc0c5de61f2209f1ff30857dfdfe3662a9ccfb9331e4ce45369fe -> 4.10.12-x86_64-azure-disk-csi-driver
      sha256:aa1556eaf6dae00fb8bb34c495faecaeebb9e374176f8e01ca46de025e63f2f8 -> 4.10.22-x86_64-azure-file-csi-driver
      sha256:ab592552bb6f276206acfe2d930a375a6469773613a0c7337a4f318c167cdf45 -> 4.10.22-x86_64-azure-cloud-controller-manager
      sha256:ac020b6552bf0d8398875fa533ceececdb7306b2932425ad7663a72507910cf9 -> 4.10.11-x86_64-etcd
      sha256:ac3943dfdb6797ef63b6e8b03d173d8d7d0ce31acee7a275ad93b67d5aa05d38 -> 4.10.12-x86_64-cluster-openshift-controller-manager-operator
      sha256:ac9c793e65afe8cc6f2829ae8c463c8e7333c4a0117096944a9a80564fa2bb6d -> 4.10.22-x86_64-openstack-cinder-csi-driver-operator
      sha256:acc51e36e777911e621e0e5db41783d61b303383c80faeeb4689d94cd055b50f -> 4.10.11-x86_64-oauth-server
      sha256:accd274c04166fd97abc765531819e846852f6879ce36f4aa34fc1099ddbc9e0 -> 4.10.11-x86_64-csi-driver-manila
      sha256:ace4bc2c5421ffc6362853723567bce6858b1c757b8b71511320990adee1b02b -> 4.10.22-x86_64-azure-machine-controllers
      sha256:ad1935a0a2e9c2b71a3e7226df57174fa72a034fdb00626dc29795316f7d6485 -> 4.10.11-x86_64-network-interface-bond-cni
      sha256:ad28f353697fbaa0369410c2cd8d530829acc43ded3b2a3cf1159bb07fb35f29 -> 4.10.16-x86_64-cluster-monitoring-operator
      sha256:ad556954d4370fb7d8daa209e841a569f93f15514f6ad2d47f9b5b25f23ef5d2 -> 4.10.12-x86_64-ironic-machine-os-downloader
      sha256:ad94f68b119e6a926598a325ef68f651d92ed0b84b6bd92f2c9739c4c9b8a429 -> 4.10.12-x86_64-vsphere-csi-driver-syncer
      sha256:adbe8c7c9904deebe13c0ae7d37293f17746258f8fddaace74d946fc5f4f8711 -> 4.10.12-x86_64-jenkins-agent-base
      sha256:ae83e28ebb7809e6663254f08ade153c542ccd02be02f30b524ef9be23295e70 -> 4.10.12-x86_64-driver-toolkit
      sha256:aef7466948bdb5b9c9017195c15d6ec3b0068c7d8284afcc1946b37be2084aa7 -> 4.10.14-x86_64-tools
      sha256:b00039c2f7ccd7a04e448e4d68846eed16e05ca22bc571f48ef467a5b58aac4f -> 4.10.16-x86_64-baremetal-installer
      sha256:b07b1df4cacbb5df087e36376edca8c3be9c3b4790edb293b960ad39e41763ab -> 4.10.16-x86_64-configmap-reloader
      sha256:b0f5a089001654d82050bc8d01203b2a51a7ed5b2aa7800603c125604a11da9e -> 4.10.20-x86_64-operator-lifecycle-manager
      sha256:b11dd589fc5982c71b7c5cc86e67e042e31d09f40c21c4f3299c3f4b1d1b6c31 -> 4.10.15-x86_64-jenkins-agent-base
      sha256:b1dd92c848a3c88cd70b085664218a28bf14650d26e486f5c346570872906cd8 -> 4.10.20-x86_64-prom-label-proxy
      sha256:b27d61fe749b808c28d3a5a362c4b667b01c5ad786e5e95eca9b7574707e152f -> 4.10.20-x86_64-alibaba-machine-controllers
      sha256:b2cbd5eef708bd4a9b74eaee35c16f784cb801fed9ae162d8f559e6363911b49 -> 4.10.12-x86_64-cluster-storage-operator
      sha256:b2cd3b5b71c97fde881f63c30c80c73d22263c9db7d293ec5a44c83ac82474ab -> 4.10.11-x86_64-cluster-network-operator
      sha256:b32a8c2ea25ee1479bc4c07cc4ab4b700d6cdacb3bccf15a263289a1b99e1fa1 -> 4.10.20-x86_64-cluster-samples-operator
      sha256:b33d7446e83d62d9fddfd654cdf1e4aae10ca492a62d0b3f024738b9a2cf8a22 -> 4.10.13-x86_64-aws-ebs-csi-driver-operator
      sha256:b3f241d18f0bacf3129b12775b4269fc22a6c4173d0862fcf96bf9f408effa57 -> 4.10.22-x86_64-ibm-vpc-node-label-updater
      sha256:b423ef0c303743a98073af05379a582206d1f35d83aa3967f1fb2f5a33a9390a -> 4.10.20-x86_64-multus-cni
      sha256:b4987bb4b90446a77b1924ba93eca07183fe3089f7c7c9845f2cba8b39a1c45f -> 4.10.11-x86_64-gcp-machine-controllers
      sha256:b49f17af371c80cc324f44f694dc811ebe6c34c686fdad6356ebf8579feca665 -> 4.10.20-x86_64-kuryr-controller
      sha256:b4c94364186f1b11d20876d1770947783902e54214cdd4dd114576541562f7c9 -> 4.10.20-x86_64-vsphere-csi-driver-syncer
      sha256:b4ec7d102354434c5e92c2a64a708b5f300e7c6c14a3351f9030aa2d28352b16 -> 4.10.11-x86_64-cluster-baremetal-operator
      sha256:b508279b34ea86f485ec0ade265c62240f7c4a69365182b9fe86d4b7bccc8814 -> 4.10.14-x86_64-tests
      sha256:b52ed323365b45ceef82fc63e0e6723de825c6b1345f4234cba07ecd28aeeb24 -> 4.10.22-x86_64-image-customization-controller
      sha256:b5633b54bc94b2bb8b2135dd1504e173b49008ca01a122b657177d5e5807d733 -> 4.10.20-x86_64-kube-storage-version-migrator
      sha256:b5bcc1882b9afe3fd26f3e68da2cf1b72cf7f31850dc634f0d38c1515d8c4092 -> 4.10.22-x86_64-multus-admission-controller
      sha256:b5d406a68f3fb45008977ea6f4d1cda50c8a878cf0055c11308b1785d5a06023 -> 4.10.12-x86_64-prometheus-config-reloader
      sha256:b5efb1a7b0176f89d8ac6bbc27fab231b0316c02fa6a1e5f6459f18849782007 -> 4.10.12-x86_64-ibm-vpc-block-csi-driver-operator
      sha256:b68fb02d088259b67b2c198d64d1b217a20cf073a9d336e371e81c83f46ce877 -> 4.10.20-x86_64-openshift-controller-manager
      sha256:b6dd3ebf4ee8157c6a64535fab4a46d3909ce11776743ce0ea16c61a77bd987a -> 4.10.16-x86_64-prometheus-alertmanager
      sha256:b6f953327c1950cafb720a312b2e34ff2abd2bdf49252b21c307a367075c46ca -> 4.10.11-x86_64-cli
      sha256:b75e523f8575960a878bd8bd0480b541b91bdab44e111e4425e62dbfd2370830 -> 4.10.16-x86_64-machine-config-operator
      sha256:b93993f797e0fc3a71692f6ed51b0a7d4bae1fcb29e7045fae5caa897490f38b -> 4.10.13-x86_64-hyperkube
      sha256:b9469fd28f6b6d071ca98ced7cd94534c70041db09fbde57817ebd1db4230ff8 -> 4.10.12-x86_64-cli
      sha256:ba00a1b6d0460a7aaffe2c22d65442e397489e2a918cdf4bb2c660d722784f2e -> 4.10.12-x86_64-openstack-cloud-controller-manager
      sha256:ba44dead03ea74107f90d58525106fb52d27a120b73c6cc8e2be31d37043ca1c -> 4.10.18-x86_64-cli
      sha256:ba88a2ea6c14472b499d9e0092456cbd07ee79a5378ec3cb24c4749600eace41 -> 4.10.22-x86_64-oauth-apiserver
      sha256:ba93e06839b0c0cdcd9a29e515ed588d41eb71fd422dd42dfe92906bfab88fe8 -> 4.10.22-x86_64-operator-marketplace
      sha256:baadaa53f6e3d7ccefa57f243c0d07f4d78a8e51a442a37a091b7a6f0eac511d -> 4.10.12-x86_64-powervs-machine-controllers
      sha256:bae6c474e40958bfc17bbc351630bd4af77a541834482bd1225068a97e68cfbb -> 4.10.21-x86_64-ironic-agent
      sha256:baf60329774f80bc1f415d90be72b9db5878a048a27cb2c7edee13fd712ddd44 -> 4.10.18-x86_64-image-customization-controller
      sha256:baf9b99c6c61cc07a1c03e6c2459b0d8f63778173145dc7e6dca68fe84d85985 -> 4.10.16-x86_64-cluster-kube-storage-version-migrator-operator
      sha256:bb336a7d3c3da934aab9ec31035f0ec0e27446b1c3e8a83ca3566914e5a9f2c0 -> 4.10.22-x86_64-hyperkube
      sha256:bb988c41bcdb6a3162ba36fc3c40609df1f7f0fe81d4697e1e6ebb129839d326 -> 4.10.12-x86_64-openshift-apiserver
      sha256:bbb8f6fe437c7217a5ec747d7835ae338605a0f6828595219be21199e2933832 -> 4.10.12-x86_64-alibaba-disk-csi-driver-operator
      sha256:bbcec9d1944546409d709d41db80d75cda0caae78358f09c1383cfa088b95965 -> 4.10.16-x86_64-openstack-machine-api-provider
      sha256:bc77e682a77f28e6215c3e7143e90f31bb7c9ec4cb32fad97a7629c4644da0e7 -> 4.10.11-x86_64-kube-storage-version-migrator
      sha256:bc7b463cd1cfb4296ee5d7aa933eaa86cee75f0f83893712eed1488d6215f757 -> 4.10.11-x86_64-network-tools
      sha256:bc8934d08d5be878a54f8738e964efe8aae8899342f656dccc1127f7d0381c59 -> 4.10.11-x86_64-jenkins-agent-maven
      sha256:bccd7ef5585e8e06fc4aa21cf83232f5795498399ded8dad1faa23e6b27f0e5e -> 4.10.13-x86_64-ironic-agent
      sha256:bcff6eee85c121c4fb0aa622ff84d9a6858bd81b6e3b915c78b44609e4292eb7 -> 4.10.16-x86_64-multus-route-override-cni
      sha256:bd3059b069305ca0dc66311d65e83c4fa453c3c21737540d13ce0d630d41dbc0 -> 4.10.16-x86_64-kuryr-controller
      sha256:bd6071061332a4038d2b4cdfc548480866f7a52e9e5d23d5ba4cd6222d97ee8d -> 4.10.16-x86_64-container-networking-plugins
      sha256:bd96fa879d99301edb93936ce2d270fb78cc2b7d4a750c6ccb1d91cd4cf47742 -> 4.10.13-x86_64-baremetal-installer
      sha256:bda0266de7f68d6e368d15895f16e2201ed7a777fcf5479ead4a2ca0038754b4 -> 4.10.22-x86_64-cluster-kube-scheduler-operator
      sha256:bdfd31c7748f42db51f7d156b2ccf2f3dc14a2b290e7ec8a27451b343373eaa8 -> 4.10.20-x86_64-cluster-csi-snapshot-controller-operator
      sha256:be0d46f5a50a9d4770947e8c8eea773893a7e39b9a0629badbe86f1d3f00a594 -> 4.10.22-x86_64-cli-artifacts
      sha256:be0e5f7ea8495a53ad0f9991d7d23bbd8d9fcadc07f2ca08bb1e4b2048c2c777 -> 4.10.12-x86_64-cluster-openshift-apiserver-operator
      sha256:be586e0ed59099d4b45a6ae29d1bd9bc6667cf69ec8a9caa5ed335574905b95f -> 4.10.13-x86_64-cluster-update-keys
      sha256:be753a60fd2f2540b6141e0d04cd1c9b9b6021793690f5da04ebea9b0e3a30f1 -> 4.10.12-x86_64-ibm-cloud-controller-manager
      sha256:beb439f2abc4ff78cc9bce881c932d342d0a505ea3b3ba0bf7dd7b50f61613fc -> 4.10.11-x86_64-egress-router-cni
      sha256:bf18efa3598c6a1fc012ed4f734ff8b4b32a36d08ef2bc72977d81b36bc5cc0a -> 4.10.11-x86_64-cluster-monitoring-operator
      sha256:bf24d3ea512f1372f435a1209de3b7e2641da3ea5f9816a793f34497cce3c037 -> 4.10.20-x86_64-cluster-image-registry-operator
      sha256:bf66551d71829070b90631ca82e9b59544a5620f5dd1290ff68e749f0e0f40ad -> 4.10.11-x86_64-openstack-cinder-csi-driver-operator
      sha256:bf72af3b6c4d3a4eccbabf81ebfcc39168f258ae61f264b489a52ee4381e7cb6 -> 4.10.12-x86_64-egress-router-cni
      sha256:bf7dc8b573d8776f8e3ca0bb4605b6c2d35f2c52dc5badbf261c6f717e949893 -> 4.10.22-x86_64-ironic-machine-os-downloader
      sha256:c084fbdeaefaab2d0d7addc2e4101b838876ea23137158704107e551753798a7 -> 4.10.16-x86_64-azure-machine-controllers
      sha256:c09189b0b03bd631ad04db7f0519d717fec4a11321d2d665ee9dc10c11466f7c -> 4.10.20-x86_64-etcd
      sha256:c0aa0d9d7fbbe39405f7ea540ea38164de47a69c03cfd24da01a955a577984f0 -> 4.10.20-x86_64-azure-machine-controllers
      sha256:c0c406b9da73af80ae1324ce21d1bd14b27c6ff160506cac03cc60224438397b -> 4.10.15-x86_64-network-tools
      sha256:c112ea4839c6540d2e95cae47a757d4effe43f03cbf26850dccf34496bd4ac66 -> 4.10.22-x86_64-ironic
      sha256:c1abc56336185ab4c9fcadcd482ed9f56df5625fffedca34c021b7d614864f8f -> 4.10.16-x86_64-grafana
      sha256:c27569c1ccc8916b4d38eee07ababa5d694d71eda1dacd272088163d6b3bae94 -> 4.10.14-x86_64-machine-os-images
      sha256:c29cb321d7ac72d86a86ba4a74a0774ed2ebf9910d65c1805245a17d7b005b88 -> 4.10.12-x86_64-cluster-bootstrap
      sha256:c2b8940cd4f5df5e1ca7664fb4e6cbdd181065d1c60c2d1517b01f039b22ca17 -> 4.10.20-x86_64-cluster-storage-operator
      sha256:c2c5eabe9caa18f3609c14126a1374997276d15da9984f915a6896aca47d33eb -> 4.10.20-x86_64-cluster-openshift-apiserver-operator
      sha256:c3041ca45204aff7cdfce497cd359399d92b7c2dcc6881190f0a1d8fbee05cf5 -> 4.10.12-x86_64-hypershift
      sha256:c334b140ac6dffe468e15eb9d4b92557c94e932f5cc4ce42330245394f537892 -> 4.10.16-x86_64-egress-router-cni
      sha256:c386419adedc81526bb8a9dd83df9d220738ebd33332f40bf9626d71dab8c362 -> 4.10.12-x86_64-ovirt-csi-driver-operator
      sha256:c39695fa02dab94949d8906faea829abbc9c6e8844e277b51b6aa50c67b2f4dd -> 4.10.18-x86_64-hypershift
      sha256:c3af37a94205b11c4d43c61a501284d6f87ccecb2ac8747ea852c7e72f763ca3 -> 4.10.11-x86_64-cluster-storage-operator
      sha256:c3bc4f639bf1f5dd2b0cd2f801658b398363bc0d275e1bd345ae84316ee095dd -> 4.10.10-x86_64-jenkins
      sha256:c409f2eb7da20321a00d265b9bc7bb4b48da80015098c3a74fede70cc3e86f2d -> 4.10.12-x86_64-openstack-cinder-csi-driver-operator
      sha256:c4240dae68a407256b481e84c9931c88fcdef9ad79003a35bfb26120328e61f4 -> 4.10.22-x86_64-ovirt-csi-driver
      sha256:c43036d5bb262560b97134548b85cc8e1a6ad9b2477f6985ee146e4e215d1c6c -> 4.10.22-x86_64-multus-route-override-cni
      sha256:c4490dd68743c5a506c22d9754646128d64b1f025d8ccfd980f34df023bfd0c2 -> 4.10.12-x86_64-cluster-config-operator
      sha256:c4612fc29718345a9471d3c8162120499483b27e269f8c933dd67e8fb7b3b7a7 -> 4.10.16-x86_64-cluster-image-registry-operator
      sha256:c46776a9e2d8e87c2ba148042d77dad77c3db7e4971e444c74b95f0ffe5a73a3 -> 4.10.12-x86_64-ibmcloud-machine-controllers
      sha256:c4a9891276fbe5d0a69d2182863ee76e8a50dac5e2ef7d464cb39236b72ed1ef -> 4.10.11-x86_64-docker-builder
      sha256:c534ca3ea1a5b6ff4bf60364110d98612814b2d94f904ab46c0c35f8a1624033 -> 4.10.12-x86_64-network-tools
      sha256:c549852d65976ce4377837eeb3d3524682309b96d9df688d20b8e3151e6f5168 -> 4.10.11-x86_64-cluster-authentication-operator
      sha256:c55fd21fc8b7db6527541f65631823331b81577d24e736299ba4915bd31e645d -> 4.10.11-x86_64-prometheus-config-reloader
      sha256:c563d6872a1fd8707183b691bf5f13c00e37bcbb173eeb1817a6cd869812fac4 -> 4.10.20-x86_64-thanos
      sha256:c56de76d3498e5576fed5018880f3ecd3a7b6a9c85f9e037cdc4288e124f81d3 -> 4.10.20-x86_64-cli
      sha256:c5745c8744d1ca1f00e4dfe7f75dc9bd64474e9daa1d40d8452494f821ec8465 -> 4.10.13-x86_64-tests
      sha256:c6a1d08e0802dc5c95d27756138cbf85a271e3be149808de7a82d7f6a4306b4c -> 4.10.15-x86_64-installer-artifacts
      sha256:c7394eecbf6923305dfce7295469d10b9ce7557af983c8be648d0bb14fbfeda9 -> 4.10.16-x86_64-cluster-bootstrap
      sha256:c82007ac1866afba39319a555f5dd7268d17976d975cf2bda362d826bc76e4a3 -> 4.10.16-x86_64-alibaba-disk-csi-driver-operator
      sha256:c884f8b049793df05b81b67db60626c05d04c3a36e1a18d7fe0dfd305e874082 -> 4.10.18-x86_64-cluster-monitoring-operator
      sha256:c88cda9ed3220dedab12566d7de664f71d712ef1d8f795c51cb92e8737da5375 -> 4.10.16-x86_64-tests
      sha256:c937d69ff6b5030715af7d55bab317b8f04f65b41c354069f75863551eb246ba -> 4.10.21-x86_64-cluster-etcd-operator
      sha256:c94bb43bdb877deb5a59f1767e6fe86709696b56beb9036add59483aa934b16b -> 4.10.11-x86_64-docker-registry
      sha256:c96906a0465726251977fb2e3325e6beb2742ce1f6ec26eb7a7a9883264b5b90 -> 4.10.16-x86_64-image-customization-controller
      sha256:c97d41bbf92ffc8032a704cebcf5de51e09101ff9cf7927fb3917a2a0628794e -> 4.10.14-x86_64-cluster-node-tuning-operator
      sha256:c9a873c826b5dc5ed8257b0c2e9e5c5a6e6630a519abe27a59cdba88b99ec0a7 -> 4.10.22-x86_64-tests
      sha256:ca4f156e26c892fd998bda5145aab16cdccc31d64e6d30fb983425562d344fb6 -> 4.10.20-x86_64-csi-external-provisioner
      sha256:ca576a564b69aa0f6a9f5f5bbd38ce0beb8ab7ff6edec9e9e8f8ea586bec52ed -> 4.10.16-x86_64-libvirt-machine-controllers
      sha256:cab8d34966cfe2c372bfa7e3ccff294b9c6a1b5423a14e2d1784d277d5ef5f4c -> 4.10.11-x86_64-csi-driver-shared-resource-operator
      sha256:cb2d6c5da1f813d8f7e6ca2648038a001e57cd8be7ca88d29ce2e97e031fb546 -> 4.10.12-x86_64-azure-machine-controllers
      sha256:cb2dd344296dcf6140d637d849f443faa8e80436b9ac515835f8f19367505f8c -> 4.10.22-x86_64-gcp-pd-csi-driver-operator
      sha256:cb70d956fa55b463d4107c7a31375b0955f22763c50c608b755079978bc6ec0b -> 4.10.18-x86_64-network-metrics-daemon
      sha256:cb786b2e448e5cfd290e801c9ddcb0c389ac7e7f16b79b77ab8f8ce8a1e3249d -> 4.10.22-x86_64-cluster-bootstrap
      sha256:cb8f3566d3fb7e380dfff6a9c291ed60d7c700477377e77b154c266e7c4a66ad -> 4.10.22-x86_64-vsphere-csi-driver
      sha256:cc200547f588bba56d2a0c54f62dcb95ce08db1be4289f43bc66d56cbb8aa9c4 -> 4.10.11-x86_64-kube-rbac-proxy
      sha256:cc24ea8dcc994d6ce2bd6bb8b3b25e18f099c0448fbbdbb5565048608f086152 -> 4.10.22-x86_64-ibm-vpc-block-csi-driver-operator
      sha256:cc369ec84138627a89354595f9d3f0ed8b627a0af3bc9426626c90ed22f962db -> 4.10.12-x86_64-etcd
      sha256:cc39d9c05b0c15cbb5c1ed92cc943a433a4c823e9dd70ab0768e0d209f32a0c8 -> 4.10.12-x86_64-gcp-pd-csi-driver-operator
      sha256:cc7878ca3d2cdad7d19cb752bfe123fc63d048f9a544b47b697bac5b1bd51670 -> 4.10.12-x86_64-azure-cloud-node-manager
      sha256:cc83500a51c225da8f897a506be19c8271bdc7839b3d1e47d9f870d00025edb6 -> 4.10.22-x86_64-cluster-kube-controller-manager-operator
      sha256:cc90ac7bd085f843f9f16143da914212b89ecdd18dd222b5bc7d76027cf084bd -> 4.10.17-x86_64-kuryr-controller
      sha256:cd01bf5381567a8d3eb55fecfc65a5fdd6dba0fd58abea465083f69e7ecd52ca -> 4.10.12-x86_64-machine-os-images
      sha256:cd7d8f3ad2026c4fde7a39ae4bc4dfa9fb0c7a66929294b7699ccfb10d26f936 -> 4.10.11-x86_64-cluster-update-keys
      sha256:cdaddccef20f321fa2e61476dd07407d98229ea2fdf5dd47300e0257d94586ce -> 4.10.16-x86_64-ibm-cloud-controller-manager
      sha256:ce0c29a0a607a6b5ac3ea392e8b29da156b662e11ca601a40689ba7da4af691b -> 4.10.22-x86_64-cluster-cloud-controller-manager-operator
      sha256:ce567b192830ba929e48f071bc86922f85dae262ed6ec9c4c098e02e6eccf64c -> 4.10.11-x86_64-cluster-ingress-operator
      sha256:ceb0d1d2015b87e9daf3e57b93f5464f15a1386a6bcab5442b7dba594b058b24 -> 4.10.12-x86_64-coredns
      sha256:cf62f8a6084502ce24af4dc707c8613138301b3f773b5baf332fd511bf92c5d4 -> 4.10.20-x86_64-powervs-machine-controllers
      sha256:cf9a7ab08a77f435d2dda452c266c49fc61834ef40ccc079268307b7da5bcda7 -> 4.10.18-x86_64-machine-os-content
      sha256:cfd067090a2d8004c32bdc1ec4b0d086635385368509517f6ef5ac8f48a76325 -> 4.10.12-x86_64-libvirt-machine-controllers
      sha256:cfecfae9863d07b24afe606caf62db29e27174d9c2697af14f86b89053193a2c -> 4.10.12-x86_64-csi-snapshot-controller
      sha256:d0e506b786984b64baccb7f778b9d66428157e66ca516c14e9363399049c2b47 -> 4.10.22-x86_64-csi-snapshot-controller
      sha256:d0e8c45bd2eec22e1c02c382e2a1916a9bb88e1b0938c42748ec20f7b1a0f523 -> 4.10.20-x86_64-vsphere-problem-detector
      sha256:d180f1bc034552140b10f4580a7018e1bd1fbd6e249bc9066058f8c4095ba443 -> 4.10.16-x86_64-network-metrics-daemon
      sha256:d18e48b45f2fda7f58e263cce7a4fab8393fc395834d4483dbc349d2586f0895 -> 4.10.20-x86_64-aws-ebs-csi-driver
      sha256:d19869b0107d9c6a3c88a274441de92d148de33576ee5f015547e57e2ec88ba4 -> 4.10.12-x86_64-tests
      sha256:d1a0180d8665dc6903da38be280e6101ce83836c93be4409cd6005bc87b5e31c -> 4.10.11-x86_64-gcp-pd-csi-driver
      sha256:d1a74cedb3547fa62c385a8f27c155209a6fabe77350a19e78b513a3b2ee68c6 -> 4.10.11-x86_64-cluster-bootstrap
      sha256:d1d6013ad583fec1f7f55feeead8b16477c3eb7d12e029491a8a8bdd3a32214a -> 4.10.11-x86_64-cluster-kube-apiserver-operator
      sha256:d22d574612e756b3c8e687a17aeddc8f7535990abcefb828e043fe7d3500ae27 -> 4.10.11-x86_64-console-operator
      sha256:d2988ce4a7a8cfeeefab8456435531faafe3804d3ad0b78822fb1eb0e95fcbad -> 4.10.22-x86_64-csi-external-provisioner
      sha256:d2a634f7198daba84dd4268893e8e5cfd10386095d5332c56f1032f1aa547b3b -> 4.10.22-x86_64-cluster-baremetal-operator
      sha256:d2d75f8f8d2e7af1321221e825eeb219a85fbb6b0f33346148835708123ec550 -> 4.10.11-x86_64-oauth-apiserver
      sha256:d2f400620b99bee4f7aa646df29ccefbba99111aae48a0b8b55579e12c7e9641 -> 4.10.22-x86_64-cluster-autoscaler-operator
      sha256:d306afdccd309312d6c006f1415c63fee492ff80ef62974dbc09c64d7ad36850 -> 4.10.12-x86_64-ironic-hardware-inventory-recorder
      sha256:d38099c9a6f51c0a6aba8a55fde679e7211f79c61e1d6c437b8a3339bdc14876 -> 4.10.20-x86_64-oauth-apiserver
      sha256:d38d99215bf992169e549187ebc6d94c5a9d00dc37bf0ff00eca7e2b57a0cb10 -> 4.10.12-x86_64-csi-external-snapshotter
      sha256:d414d33adc4681ddb1272d5248cddf7ed0ae7c7e51f2f199179591e3c2fc468d -> 4.10.16-x86_64-cluster-autoscaler-operator
      sha256:d4305fe75a7676708f245043b63b53510e584b48d61a9a56733db54145d8431a -> 4.10.22-x86_64-sdn
      sha256:d486bed1127bd9cb55d0ca6ce3ecaad92fb25d5aee58103744e6bf90f83243a9 -> 4.10.21-x86_64-kube-rbac-proxy
      sha256:d54a6308eba6353c183254585625d8cbcd08491973795c318f00b23c23e70bc7 -> 4.10.20-x86_64-csi-livenessprobe
      sha256:d5a2c6850d691bb12172be344f6f1e39be6b20e3f8af83b7c91a47ec54bd4204 -> 4.10.12-x86_64-cluster-policy-controller
      sha256:d5eab2e10baf86fd0f302ec99ed64fa4c856bf30c0e790dbb424232b7d3ab242 -> 4.10.20-x86_64-tests
      sha256:d611349bb5d5d5613997e724263e60361b883cc1ab50f3470610a85afde2e54e -> 4.10.11-x86_64-csi-snapshot-validation-webhook
      sha256:d61574eb883e5c6dd355c58affc9eee0ac5caf3361f5da5893290253c9b3c5b5 -> 4.10.12-x86_64-cluster-csi-snapshot-controller-operator
      sha256:d787f47ee2a410f924ea00b2428f0cf2275eb059adac96ca1b69c71ad20ccb1d -> 4.10.12-x86_64-oauth-proxy
      sha256:d792f8b256aaa445e5b5659988146d0080a1a40d6770694f4644054a69ba7b70 -> 4.10.10-x86_64-jenkins-agent-nodejs
      sha256:d8c6305243a85a81567d15091c6dde8b27575cb877c03c4a385eeafd80ba7485 -> 4.10.20-x86_64-alibaba-disk-csi-driver-operator
      sha256:d8d6991b9ff9e24bc54ae90eb066ba6874908eab67e9f64064133c394c372eb2 -> 4.10.12-x86_64-cluster-cloud-controller-manager-operator
      sha256:d99d90a291de213c4094d5a983eed8da081fa967515fa2908249eec5af4de954 -> 4.10.18-x86_64-baremetal-installer
      sha256:d9b48020b45fd3429d508c83d78a57f90c587f870c6e492b403dbe332afed191 -> 4.10.18-x86_64-deployer
      sha256:d9ccf587dbd1d1a96297827e6f53476774843366d6787a0001f0e3b12691c5dc -> 4.10.22-x86_64-jenkins
      sha256:da3e472cd13968193748dd8b4328c2ed1cb12f15e544702d737283a28748ddd8 -> 4.10.11-x86_64-cluster-autoscaler-operator
      sha256:da6b35614c5cedc1e612a55cef40a35f121cf2ebec1f49c42d6f73987a6fcc8d -> 4.10.12-x86_64-baremetal-machine-controllers
      sha256:daa77dcf59222aa5ed278b090a02db9fc5986f5589f115977266853b4ff5267f -> 4.10.14-x86_64-ovn-kubernetes
      sha256:db89a4cec5a97ca59c6b173277c8808a2d61b83e594b6bd6572cfc497d1260ae -> 4.10.11-x86_64-multus-cni
      sha256:dbae1d0084d0e757e6bca192945891271f178a003ec4cf6f9d2b43be812fcba8 -> 4.10.20-x86_64-cluster-openshift-controller-manager-operator
      sha256:dbc0fe68425884902ab5bcf23ece53dbb41cb7b1e6473dbec330749b20cc8b4a -> 4.10.16-x86_64-ibm-vpc-block-csi-driver
      sha256:dcb61fe5e82c0d01b90155f20b0b6ce37446867ca0d005b727fc073f86ed1354 -> 4.10.22-x86_64-oauth-proxy
      sha256:dcd32eb14949c0b7072500052915c4814e28f0a44545cc073fbfd2fca685263b -> 4.10.11-x86_64-kube-proxy
      sha256:dd14a9187f8be780d982707b09cb824644ceaa8a22b1065590a8e74b172a09c9 -> 4.10.10-x86_64-installer-artifacts
      sha256:dd28fd03b50c7a0f2f502c20e920167fcef79803619809d475f8dd4a0d46c63f -> 4.10.14-x86_64-jenkins-agent-maven
      sha256:dda511a05e243ac922b29b29de99f347cab19088a1c95222f196dd5c9c1502bc -> 4.10.12-x86_64-machine-config-operator
      sha256:ddb52dbbf6982031d2cb7ebd3571d2ad8b99d2df3dd17a8b1cab37bb55be6dc3 -> 4.10.16-x86_64-csi-driver-manila-operator
      sha256:ddd29a22d824dd1ed3623656f53f608909a23c53d99a1fec10df39f1f30f8e3a -> 4.10.22-x86_64-etcd
      sha256:ddde42d368e1a277529a9e47ea8f506caf1c872b67af8b76ad7970aaf50b3328 -> 4.10.20-x86_64-cluster-authentication-operator
      sha256:de27fde7dd8a92ae38e27bb3cdddb3747d178d75831545d4299826c7395d4f83 -> 4.10.15-x86_64-kube-state-metrics
      sha256:de386fe70e517a67a1fff9077c54f7ddbf15c305a70c9b1b0c972761b380a47f -> 4.10.16-x86_64-k8s-prometheus-adapter
      sha256:de9a5cdae382f064a3fec4aaaba6bb6dd5509654df4226a4961904c789053ebf -> 4.10.18-x86_64-network-tools
      sha256:df112f6832c28ce3987d1bf4904c9e19dd56f773f66a597583eb72ad141fad69 -> 4.10.16-x86_64-docker-builder
      sha256:df2661b04189f185fe3f0cd2e0323f201984160ac638803e337fa437e263e3ca -> 4.10.12-x86_64-csi-driver-manila-operator
      sha256:df2ca7f54a8d34f568a92c077c56ed8983fffc51146b55964971c1f970288aaa -> 4.10.22-x86_64-alibaba-cloud-controller-manager
      sha256:df6c3e84c4e51fec6df6c026fbe85da712ccca4f3bd8f2af1a5bbd71aa0e51c5 -> 4.10.16-x86_64-ovn-kubernetes
      sha256:df9d0722e24161ecc5ceb80f93661ea64187bd6bd8012c9ef7267b28724ed35a -> 4.10.16-x86_64-kube-storage-version-migrator
      sha256:dfb70380c79430444afc9e3535cdcf43f2d337bb5fc2b35fead1c0233e56412e -> 4.10.11-x86_64-alibaba-cloud-csi-driver
      sha256:e00bb52818c63cf36d81dda240bd70759c3ba3e0c31fd9e76f976605b630d5de -> 4.10.16-x86_64-azure-disk-csi-driver
      sha256:e00dea4c352633bd0bb9c464a58c8ba4407293639e92f3828792e0dfd80af0d7 -> 4.10.11-x86_64-ibm-vpc-block-csi-driver-operator
      sha256:e0c134d4266596cde4ff501098aab2238bb7da8452a00bb4116eaf00f7bb479c -> 4.10.20-x86_64-prometheus-node-exporter
      sha256:e0eeb2ac5f04bc44e3fd822c8e14bae4bdef5a09ed48033252999b4274d5ce10 -> 4.10.11-x86_64-azure-file-csi-driver-operator
      sha256:e1142ebafd619bfeb47984fdbf79675c7b1d489d3d1ec872c83b96d776f2188a -> 4.10.16-x86_64-cluster-version-operator
      sha256:e183cf6b13de9d66c9ac97a304a99c5463698659d30a1a6a59d64b220436ad61 -> 4.10.16-x86_64-ovirt-csi-driver-operator
      sha256:e2264c4f44681c22da1360c0be3ed4c53e5d2800e7ec574c0677004f33925bb0 -> 4.10.15-x86_64-tests
      sha256:e286feb010ecf070713a2598e00071f3fbddc154bf6b26851c7157f81a13756d -> 4.10.16-x86_64-aws-pod-identity-webhook
      sha256:e28fa56dbc0111d291b33e1f2192db0010e974f76bcbb163b14762aa3e881b8d -> 4.10.12-x86_64-csi-livenessprobe
      sha256:e2908f3a320cc927ed6f28ac112d7ebba67e2402d26ff211dcce8dbdff8c243f -> 4.10.16-x86_64-openshift-apiserver
      sha256:e2df25e109989f297577ad32df596130d01949bcec66c7866e71ab79e1568c93 -> 4.10.16-x86_64-baremetal-operator
      sha256:e2ee2873395352454cf6a2f6205db218864a2f85402534c0131365dcdddfe2ac -> 4.10.11-x86_64-console
      sha256:e322662d8fa936c134d449b5f56b60038b0e21c04971011739cbf72b18c490bf -> 4.10.22-x86_64-csi-driver-nfs
      sha256:e33bf3759eb15a5feba08eec0150f575a568936ad4b47cc1a56ff94465407264 -> 4.10.20-x86_64-cluster-node-tuning-operator
      sha256:e34392181be8bf87735b4c0075fc85610e34b321ebf6d15c8854c6ba13c27e6c -> 4.10.16-x86_64-cluster-baremetal-operator
      sha256:e3c14b46fdaf585a04e2c6314629dbf074044c64736be1a7b1484d55e47597df -> 4.10.16-x86_64-prom-label-proxy
      sha256:e3f30714d035e2a5c986fb85844b5f802583effc12dcf11d2263820f03d7b399 -> 4.10.16-x86_64-cluster-autoscaler
      sha256:e46b2ee640fc190ae20994d65ce534b3597b7c8f198e38b0744e9c7b2b96775f -> 4.10.13-x86_64-tools
      sha256:e4847406687d739a2cdf53e2891ef65e33efdeaeb0a0a2cb23c1fbfed34bcec0 -> 4.10.11-x86_64-must-gather
      sha256:e502002dbdd43c714a889c515048f25b0576c74ae421fed0b1969d477820e1fb -> 4.10.20-x86_64-ovirt-csi-driver
      sha256:e52032d4e2993366d3b3e6fc81837b2cd79f1a8be5638b9cf409a9aa7547495a -> 4.10.12-x86_64-openshift-state-metrics
      sha256:e5b50f23be5b64f771e3e1cc965dccb06d097394cbf8d75cb59e37c91e332eb6 -> 4.10.11-x86_64-multus-networkpolicy
      sha256:e5de346e1ec1ac3be22c3f756155238764be3bd0bc0c80a81a6be54a0d10f88d -> 4.10.20-x86_64-kuryr-cni
      sha256:e618a2b8a5155ce0affee2bcf1ba39a211e4dcc7d541768c187e8864bec742ce -> 4.10.11-x86_64-ibmcloud-machine-controllers
      sha256:e6555b411d817e70d1ccc41ce5991f586fbf97b912290942c0dcc799f0ffcc5f -> 4.10.11-x86_64-pod
      sha256:e678ae5422e6a76a99feb885bce2245d141b8e7e21262594ee2c4b5bac7654ed -> 4.10.16-x86_64-hyperkube
      sha256:e69601cd392f50a7b79ac9183d8f6c71546e778fc5ab9b524dcb02860a7b726a -> 4.10.16-x86_64-cluster-update-keys
      sha256:e6b32fd3106979d072d30353a9293c8c3ca3773c3f47f779d03edd4c33af722c -> 4.10.20-x86_64-baremetal-installer
      sha256:e6b4bb4796434d5c90c5856572e146e6e09435175609f9515a412a1f016dd1d6 -> 4.10.16-x86_64-gcp-machine-controllers
      sha256:e6e98f8f447fed3e27ceb7e83886295e037f6ebea7e604808cf9917f46c617e0 -> 4.10.22-x86_64-multus-networkpolicy
      sha256:e722c109afeff423580d21a582cbe033a7a62ad83c3258079f4c4d7ab42593c8 -> 4.10.20-x86_64-ironic-agent
      sha256:e73e648434bcc46d6c8538afb6f958ed2f93bd766768d1d3498e4b9afea369ae -> 4.10.20-x86_64-machine-os-content
      sha256:e7b7f8f79ff7f81b8e923fa83a2a7fe6b31a08256923ab4b16b8731e6e762eca -> 4.10.22-x86_64-cluster-kube-storage-version-migrator-operator
      sha256:e8460e2334fc7baa3a499de6f7b9f847ed85355c44ded0ce9c7f415b6366216b -> 4.10.11-x86_64-ironic-agent
      sha256:e85d9d1dcdbad90cc7fd8e0fdd61703c59769cd646c5ca2e490f50f7a3ab5ea0 -> 4.10.16-x86_64-vsphere-csi-driver-syncer
      sha256:e8abf21e45c8b036244d89cb6e9144be0ef4a124f6d671f9d2b6c95c6dfb6061 -> 4.10.11-x86_64-cluster-policy-controller
      sha256:e8bbefd4ae99d6f668723488774f357234af37fdb817ece7e36fd779ab46073a -> 4.10.22-x86_64-network-interface-bond-cni
      sha256:e8dc92312868e94d808e36b972a8202d43f3ab3d3ed4088ed3e96f0445f02a37 -> 4.10.22-x86_64-prometheus-operator
      sha256:e8eb244c9ac2a1cc02e7bf8ad2c0c7cebdb958b576a43943e3d5872a43057941 -> 4.10.12-x86_64-vsphere-csi-driver-operator
      sha256:e8f1ca523a6b51fddee1024e36cc554866ffa7554d34465577d0b2fb6f37c8c0 -> 4.10.16-x86_64-cluster-node-tuning-operator
      sha256:e909a22deaba850732deda5a51d76757e05839de48b425c7104e99cb20b63988 -> 4.10.16-x86_64-network-tools
      sha256:e98105dd0ee81c9c5da6772504ffe610850af371075421f16fd2389b37c69a70 -> 4.10.22-x86_64-machine-config-operator
      sha256:e9e1170ea40b20a729e4630df96dbb2860909071723af29226d0ad05b486b5b5 -> 4.10.20-x86_64-kube-proxy
      sha256:ea10b9d060cdc37b2200fdbe0c1f87706308f3218fda8624693d8400b638b232 -> 4.10.20-x86_64-cluster-kube-storage-version-migrator-operator
      sha256:ea1f013e3a6fca4d1d7777c455f0e34be49bebd2cb1ccac16b140d49196916e3 -> 4.10.22-x86_64-alibaba-machine-controllers
      sha256:ea8deedfcb56d218578e9593b0063cfd2dfa5d58d6383cac426d79797bd0144b -> 4.10.20-x86_64-aws-cloud-controller-manager
      sha256:eb2c6d88f011c84dbe1223404d154b543242bb05bbefb5e852188aa07ba6dc0a -> 4.10.12-x86_64-cluster-node-tuning-operator
      sha256:eb84295ecfe46cc4891a86772879f773e259691791254e707d72e3220f465e60 -> 4.10.16-x86_64-csi-driver-shared-resource-operator
      sha256:ebd5f7d86ea128b4e8b0cc95e1f1b17568a14b7a46e76c90c91f547adac6db21 -> 4.10.12-x86_64-gcp-pd-csi-driver
      sha256:ec0470c0c23e59b36b744491e19d84cda0f7c56b0708f810a2673cf5f116da63 -> 4.10.12-x86_64-ibm-vpc-node-label-updater
      sha256:ec4d43b04970f8c74326efd1de466c43ba3f1935db9a02271b282ef8956b44cb -> 4.10.11-x86_64-configmap-reloader
      sha256:ec963bd701d29857d454551b3a2b20a01d842f7ef6a8aa5efc4ee43cc7150690 -> 4.10.18-x86_64-cli-artifacts
      sha256:ec9d6b92d5edd85132736190e959e92b3506c5ad842f518b977fe0f3ecb6ea4b -> 4.10.20-x86_64-cluster-etcd-operator
      sha256:ece0fdfc6a4ee72f6fe21e5100fac0276986aab5b380cc7c657177a1389509b5 -> 4.10.22-x86_64-cluster-autoscaler
      sha256:ed156a682c3facdcaf020f11a98785d07ce2852c023648f04315e8b89f9d763c -> 4.10.20-x86_64-multus-networkpolicy
      sha256:ed2a3ffb5bcc749255ec4a194763c8f2739dc58fb00489ec1ebaee22ba85c527 -> 4.10.11-x86_64-csi-external-attacher
      sha256:ed52f6768fec23920a69e831f516bd016a358a193527f852b9813ddb7a49cae7 -> 4.10.22-x86_64-operator-lifecycle-manager
      sha256:ed8832b7cfa7e63d00fe68e5c15c6cd90075b554090adeb71776274f3619340f -> 4.10.16-x86_64-ibmcloud-machine-controllers
      sha256:ed8e981affb53d27eb0d80a126cccc898a358208a48967cc50c144e99aaa1fa4 -> 4.10.12-x86_64-alibaba-machine-controllers
      sha256:edaebae22106ccccb7efa2484d4db557e92c93fbc948fe3ee191bef7583aafb7 -> 4.10.11-x86_64-cluster-dns-operator
      sha256:ee352bdfb32857610269eba37dd08beb68927c15cd6e95bfdfbdedf8aad7b7cc -> 4.10.22-x86_64-baremetal-installer
      sha256:ee39df24f3ef2f6cc352087be977a5b906c67da5168205d3735d517d248ac034 -> 4.10.11-x86_64-openstack-cloud-controller-manager
      sha256:ee94a62918653cce8d46bbbc6848d9a3e225d8219e82f17da509899d03f75d1d -> 4.10.20-x86_64-kube-state-metrics
      sha256:eed0f48ed00838fb9c5879bc6b6e5481ea14bf14ea1116985ce4bbecc2be53aa -> 4.10.22-x86_64-libvirt-machine-controllers
      sha256:efbd14dc0e517b9ffae67448ce0ef095998c3ea0f61e359fbd99232485ebbe83 -> 4.10.14-x86_64-oc-mirror
      sha256:eff047d1c90424286196dc50565248c71c422292a64eb09d938f0236bdcbb71a -> 4.10.14-x86_64-must-gather
      sha256:f00cad2323c7696cb204bd906a33adb6ae6ea21d90f3cbc41164d05a33213db2 -> 4.10.11-x86_64-cluster-csi-snapshot-controller-operator
      sha256:f018c9c933ca2fe30b72a97b9a3367b0951f618d3f9887960f55881b96ea1674 -> 4.10.21-x86_64-cluster-storage-operator
      sha256:f029861619ef5e8bb40085da96aedb15f8aa7f4ebbc9ba38a97792b722ca14e9 -> 4.10.21-x86_64-sdn
      sha256:f02a929136f498466dc3fa354adde5d7c8f39093c108eb6420eabca488defec1 -> 4.10.15-x86_64-console
      sha256:f04d5feabe0fbdf54b4e889faa2705840b3cd60eb9c49a65881c95bceb10d10d -> 4.10.13-x86_64-must-gather
      sha256:f04e5f0a78963b7ecb5c990a148c1efa995772d56ba78ef443cc5074126f56a5 -> 4.10.11-x86_64-tools
      sha256:f0c18571a1482be874ae3b2ea7495d846e1d994326e22b280d64669bea983c37 -> 4.10.12-x86_64-ovirt-machine-controllers
      sha256:f161673930ffcc897e097257b86f2452eccb61aeca0fc95c2989371b796e275d -> 4.10.13-x86_64-cluster-etcd-operator
      sha256:f1b506f37a4b078614825e5fbfee7cc10ab9530fb8c833ecca9e4fdcd38f0889 -> 4.10.16-x86_64-vsphere-csi-driver-operator
      sha256:f1bc45570e533c68b086f69979526a32ef7319c02b1d629814bbe6e2247b34e5 -> 4.10.20-x86_64-azure-cloud-controller-manager
      sha256:f1c6d2ef75c5205f9c71bf338a482ae6ae5a454366a6ab52be2ebebab9e22ac3 -> 4.10.11-x86_64-azure-cloud-node-manager
      sha256:f1f81b3042c532c3f4dbfabe6d5d77fe7a8919ca5e21e3f0d582b6cd61052996 -> 4.10.16-x86_64-cluster-kube-controller-manager-operator
      sha256:f248dbcac98210179760adda94b502b6e4f224f424a0993cac79c00daf26a154 -> 4.10.20-x86_64-csi-driver-manila
      sha256:f2946c1aac575654ac4a77dab7b82a1b94668099fa0f7772d033ec4449d83d31 -> 4.10.14-x86_64-hypershift
      sha256:f2c6a0051307e665bf19e2b47a7789fb66df7dadd855567a2c73ec919f598b44 -> 4.10.11-x86_64-operator-registry
      sha256:f2e494ab3fa1c2bb16c1c947e1f44891c8d87f4ca45aebbab229014f75daf4a5 -> 4.10.11-x86_64-aws-ebs-csi-driver-operator
      sha256:f37529115e8b399ea0fa53664775cd40a7c584823a66b28a4efaafa82e28b8f3 -> 4.10.11-x86_64-image-customization-controller
      sha256:f402e22053856f04b402b812fd449399846ff906a694e91179562c0a03cfed44 -> 4.10.10-x86_64-machine-os-images
      sha256:f43548046f8d8901a36edd8cac6879ea56b4623d0c1e1a108d46706515777f9a -> 4.10.16-x86_64-prometheus
      sha256:f52a840ce398e4c852b000a9dd14044785d27a6461aa6d871bbfc8570e3659a1 -> 4.10.22-x86_64-openstack-cinder-csi-driver
      sha256:f544ba90a7bd85c8d203a10d2817ae2444c669e682cdd1641cc9bb0337fd374b -> 4.10.22-x86_64-jenkins-agent-nodejs
      sha256:f5b0f920ca63c5b5978629deed3495e48e87bdba427957ea6947e7da784873b8 -> 4.10.22-x86_64-insights-operator
      sha256:f5c20ea0c5df3636194a02aff2226c0a8285438277fd28c7ab6f98cc215e5be3 -> 4.10.22-x86_64-ibm-cloud-controller-manager
      sha256:f5d254b52f95f0874f21a84fb0f997772039bcf25895518e419160ac513f053c -> 4.10.18-x86_64-operator-lifecycle-manager
      sha256:f5f2cab0e27ed0bb465ba870fa02f0b65ba8a4c68102970df9e6608666290f01 -> 4.10.16-x86_64-cluster-network-operator
      sha256:f6081de5f3484be277edcb8e1c1d89ed9075db733cda09c3858f8bca20f5b268 -> 4.10.22-x86_64-tools
      sha256:f61d5d69cc47455985b452999e2672c81ca8c3be318b01f8d1a4517e32dfdcd3 -> 4.10.13-x86_64-hypershift
      sha256:f652f1290c91f152916125d152b20963f74b06e0372a5d7bb75c87c625b9bd03 -> 4.10.16-x86_64-tools
      sha256:f67a6ff8759a1e64f04ae6811951d85ef6488a1340d9d1c351d5debfd32b917e -> 4.10.12-x86_64-kube-storage-version-migrator
      sha256:f69b86f903f69f8d2473da3ce28237074ea250b9d474eb33649a3baaebd96d32 -> 4.10.17-x86_64-machine-os-content
      sha256:f6db8982f4785945a032e82de504f4801e66e22ad30d0e79f546dafdf38987c8 -> 4.10.11-x86_64-cloud-network-config-controller
      sha256:f73ad6033579bb8f0101cab04cdda8cca221e6f0bc241f429750b9e5de2c332e -> 4.10.10-x86_64-console
      sha256:f73e796fbea16937ea897c1b1cfdfb908d83e323568c55da8cc90d1f7152c9f3 -> 4.10.20-x86_64-console-operator
      sha256:f74814abda5f14a1bc450a9ae0e1c1c44bc9a95d6104e31649fcee9920cccf1d -> 4.10.16-x86_64-cluster-config-operator
      sha256:f7cca4650f446b8e62c4ea70eefa64af48fa26b24da1f6898d526ce9765b39bb -> 4.10.16-x86_64-csi-snapshot-controller
      sha256:f813585a8dcef3bf60b57d04458232ef51e10ab3a6e4e7606dda68b976c0a868 -> 4.10.16-x86_64-aws-ebs-csi-driver-operator
      sha256:f8929a0ee5da4d53383d95d0716f8a2419628d4be758568629a7341bdc9956b1 -> 4.10.12-x86_64-vsphere-cloud-controller-manager
      sha256:f8e8253ab40975fc3b8b7c94e51aa0128eff7ca3aabafd0668d87e2e9f7ac66d -> 4.10.16-x86_64-csi-external-resizer
      sha256:f8f817d9775c73beac0f2387962a43e187a9c1bf1dfc87dda159970fd2226ee1 -> 4.10.20-x86_64-ironic-static-ip-manager
      sha256:f92d5c825c00e1930930917b7cf882e69627172f42a1674844c1a1a34734be1c -> 4.10.20-x86_64-baremetal-operator
      sha256:f95284c3ee005b9a93ce02a703a03269b41d48688b6f8b1503bcdb40e13b296c -> 4.10.22-x86_64-cluster-csi-snapshot-controller-operator
      sha256:f96b1ab88cebe8430c9ff6c5d84f6064f5409b34e69b60b3b54b50c8745df28e -> 4.10.12-x86_64-cluster-dns-operator
      sha256:fa0e8fac013161255551ff469f3ad7147e53b4851ac3c6b3487f0f4aa46c7341 -> 4.10.20-x86_64-baremetal-machine-controllers
      sha256:fa14b77345ce258fdb531a52c98abc6a3f5ad6d7aaa82f6aca30ab46d52c75ec -> 4.10.12-x86_64-network-metrics-daemon
      sha256:fa83f24b892664b7060e777ac5cd4f4cdac234678f12981edf04df3e5da4c7b2 -> 4.10.22-x86_64-ironic-static-ip-manager
      sha256:fa9d2e9f4392d7e6cc1237292e8aa250f37a812e87e2f0f2edd6566f7d7e7885 -> 4.10.18-x86_64-installer
      sha256:faa5f6a5b996faca76af7edf1475a157f791bcbe5ef469a5e1d48f57f8e8793b -> 4.10.20-x86_64-ibm-vpc-block-csi-driver-operator
      sha256:fb4a570ec773e9afd99dfdf32d08da7c0183d595dedf32d58d43f489c5ee32e4 -> 4.10.20-x86_64-azure-file-csi-driver
      sha256:fb4ce4f9568adb4462b8733a308f1d5e515124adf28ecc759fb7126e4fab7caf -> 4.10.17-x86_64-cluster-etcd-operator
      sha256:fc35f95ef80f0c54d13344a55e879dfa1deae622d3c51cc59a8a56d05cdea80f -> 4.10.12-x86_64-cluster-samples-operator
      sha256:fc63206c1a18edf13028978b75be6bb0091b9d64888a3a7c2e8ff15441b80764 -> 4.10.11-x86_64-operator-lifecycle-manager
      sha256:fcb3cb6b22fe4946336955472cfbfd0d47034b837f3113cbcf219be77ad64cae -> 4.10.20-x86_64-pod
      sha256:fcb99aa10e36fa1f706685899780238c0f4801ace8b14340ec1a0a7e0758624f -> 4.10.10-x86_64-tests
      sha256:fcf334e561470bd151ba0fe965e90392621210d37567d795e113560643b95982 -> 4.10.11-x86_64-alibaba-machine-controllers
      sha256:fd59b3faf37bddb363bc72d328273a7a35e1fd358bae5a799ed2595ed2e0e5b3 -> 4.10.22-x86_64-cloud-network-config-controller
      sha256:fdb091b4496bfcc2df65907b020c8192723a061d04fa7f16cf3c16a64d8e3ba2 -> 4.10.15-x86_64-ovn-kubernetes
      sha256:fdb8e07238367f8e086049f0255e5f7d0edfcfe2f28bc72bfbb3cfa02d176f2c -> 4.10.11-x86_64-operator-marketplace
      sha256:fe1f8f96e82f95609b1ad107b1bdc1eb8bde7416bee6f778cfef4cb5c055e79a -> 4.10.16-x86_64-azure-disk-csi-driver-operator
      sha256:fe8636a10559d181f6c3dd1dfd18e4bfd8247d014616083597014a63460d5d5b -> 4.10.16-x86_64-insights-operator
      sha256:fec2141a2e6e3661f8851f661028a90f73099e8f9000d91d22c5cf5e91638098 -> 4.10.20-x86_64-cluster-network-operator
      sha256:fee261289761341aaa662801cff6689eb7e47ad1b492ca6288af18a964ab2406 -> 4.10.11-x86_64-vsphere-csi-driver-operator
      sha256:feeefecb66d3028ba1d38277b4e449107d37faf9ad5c99aad29e9e1bf85128de -> 4.10.20-x86_64-cluster-version-operator
      sha256:ff31026f624c40e37d5b647cb68699f61d51e9f22580ee777d10ed4a1f867f94 -> 4.10.12-x86_64-cluster-kube-scheduler-operator
      sha256:ff4caeeea320fd651f0f65c656d468c275fedd05bc9954d168ebd6acab00df60 -> 4.10.12-x86_64-jenkins-agent-nodejs
      sha256:ff507b711bc99f95cdb847a4ec936ea44aa4abf738d2e8dc1b0f4a443731cdf3 -> 4.10.11-x86_64-powervs-machine-controllers
  openshift/release-images
    blobs:
      quay.io/openshift-release-dev/ocp-release sha256:39382676eb30fabb7a0616b064e142f6ef58d45216a9124e9358d14b12dedd65 1.428KiB
      quay.io/openshift-release-dev/ocp-release sha256:131166daf0e7cb73dc04340306c3c662854b73c92568f3d12dd9b34921206dfa 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:1ff0e4fadf5a43907cee4d88100e5fffd4e8cf15cba87c179736e332b54bf3fd 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:3373f848a7cb7ca66de1d9190b99e63acfe0644ed837da756ca1168365013312 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:349f892f94f10805b162d21d52b546e59e2841c3d123626f69e56b60905d87be 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:410958a7381bdd2cdf2b5889f615ecd78c17efc520ad3c2384b46ca7d6a7b43f 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:42adf6d30e200016db7b1090d02d8894f914e5ca0f857a056daa62ca2286fafa 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:98f1d706f38e267926dddf47c4aa39a048999baaf3a455e35c5b0dfdaaf677fe 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:ac17c8416ac7b2f70f8069221e8778885ed09164b74be515c4d12f6559833bcf 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:c43c79bdb8cf4f07566518d6b573bc5128ebc11681eaaf8966bab448104f0cbf 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:dff699d79bf994ea742b6daa9ae947800748eabb2a45d6f8426de94dd2ccbe78 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:f66b3c5faf19d24c2b51f100fd4981b2267a2e212b4a4ffe13ef554fe0418b5b 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:f8e3af430c54fbf89de9d441a684d7e2d854a30b6dbfc7a67adcdc8fd913ea91 1.73KiB
      quay.io/openshift-release-dev/ocp-release sha256:c02a84f5c5e67324c2eb43cf3b35fa46e667026c5dc53414a8a08ec5a86000ae 598.3KiB
      quay.io/openshift-release-dev/ocp-release sha256:118cfde96331f6db88cf88ccf982c093cc3b4ab89f8be292337bbaf05761fc43 598.3KiB
      quay.io/openshift-release-dev/ocp-release sha256:4dee79d6f4cb4d991a8c44cbc5f7ce90bc70672c8ab1f901af6ace6ea4c39244 598.3KiB
      quay.io/openshift-release-dev/ocp-release sha256:4560b997a304911fd2488f14dc97efd2108f0fdf369bfdb156c97b137bb7089d 598.4KiB
      quay.io/openshift-release-dev/ocp-release sha256:eaad0d963f161a797a4ee245ee3fd6b8456a126bf5d09dd46a088111ab09cbae 598.6KiB
      quay.io/openshift-release-dev/ocp-release sha256:59ffcf9df278e40681e261e3f777f540831d85f3cdd9ea0291702dc318c7f54a 598.6KiB
      quay.io/openshift-release-dev/ocp-release sha256:aae716ea749fb029ccdc7d01e2d526497dbc27cdeb4cfe6c506402293c81eb38 598.7KiB
      quay.io/openshift-release-dev/ocp-release sha256:1f21d30313cb071549a4e202d84aa8a29bab1d35e454829c87141f90a506fe5a 598.8KiB
      quay.io/openshift-release-dev/ocp-release sha256:148ab000b569762ab6763622e53bde6b6eccf3ee9768baae728b38b9cb2ffab0 598.8KiB
      quay.io/openshift-release-dev/ocp-release sha256:e299d2963d42425b5f67f5c068ca7cf8f23478312442e99167a787b3ee2c752e 598.8KiB
      quay.io/openshift-release-dev/ocp-release sha256:75132ce122535583cbc1cd8a101684db84aba6f90865a066ca06c6cd9b7174c1 598.9KiB
      quay.io/openshift-release-dev/ocp-release sha256:a3ab98453804dd8a53e7bbb2558251789098d0f86102c27a9fb8049c7390d862 599.1KiB
      quay.io/openshift-release-dev/ocp-release sha256:da5839e0efa13ff257746674543c151df660351b0451173fcc5fb464e6d93767 10.62MiB
      quay.io/openshift-release-dev/ocp-release sha256:66ab094148e45140732dcaabf86556d54f557433f1c04f2023c07361c8d1c374 10.64MiB
      quay.io/openshift-release-dev/ocp-release sha256:804449b22e26cc93ec79fad318d5af873434fe344ccaf377c2487df2ad851776 10.64MiB
      quay.io/openshift-release-dev/ocp-release sha256:f70cb1865d54b2ff4ae0de4eccc872d16704750c6b1d03b9a61e68f9a7bcdab2 10.68MiB
      quay.io/openshift-release-dev/ocp-release sha256:7c20bc76d0477296afb6317a3b9d2bb1b2ddcaafdb0fcf62676bb7e3c8928ce3 10.71MiB
      quay.io/openshift-release-dev/ocp-release sha256:67c4675a80ba55c8715ff1339cb2a9b7a3a9cf721a5e09b4beac37ed7f400e51 16.42MiB
      quay.io/openshift-release-dev/ocp-release sha256:9ef334919d82346308da0afe70becce00627b3fbc1a00522536744cbfb9c6c26 16.49MiB
      quay.io/openshift-release-dev/ocp-release sha256:a7635174fc67fd997681528eb831bf1c492bbb6b30ffd430aca98a9a89975bab 16.59MiB
      quay.io/openshift-release-dev/ocp-release sha256:df37870c9b590d1df90ae85c5186370452cb17de6e25f22b683b5f3a2f4dc1d5 19.25MiB
      quay.io/openshift-release-dev/ocp-release sha256:2a83e9b81ea230196be6c07ac8246eb4f1bb4afd084ae925d2d5590c503591bd 19.35MiB
      quay.io/openshift-release-dev/ocp-release sha256:bfa96aae4624087b0d0c7e9d982dba7fae5bfc13f5c64129dbf57f690c76f404 22.75MiB
      quay.io/openshift-release-dev/ocp-release sha256:b9b6baad1b09df56fddf44eb59e9cd6a47d0d7d62e196226ae7ac192a81a9ae4 22.75MiB
      quay.io/openshift-release-dev/ocp-release sha256:926c0c2c679305de55b865fd18f2c77d2e0d4bbe0cd1f371c21cf1936e8276e4 22.76MiB
      quay.io/openshift-release-dev/ocp-release sha256:4a5879bbd954da3d9d14db4c9cca70f0dd0ff2dcb5e5c1dfbd2edeefc866dc7f 22.76MiB
      quay.io/openshift-release-dev/ocp-release sha256:c692ab5b8175b380a4fffc7e41f537219c90207fdfedf5ab337525b1f69024e1 22.76MiB
      quay.io/openshift-release-dev/ocp-release sha256:03b848f840db99f9a98492d8192d414b8a87e581dbb7d0edff5e9ac4e5d997e8 22.76MiB
      quay.io/openshift-release-dev/ocp-release sha256:5ee8b551f095ac4a2098148853845251c136cf3262320ea6d18a1feda9c7740c 22.76MiB
      quay.io/openshift-release-dev/ocp-release sha256:1059cb97519dff7e3905cb15b93da68a218041d3b3d4cc28f2f6a5dd17c3f82a 22.76MiB
      quay.io/openshift-release-dev/ocp-release sha256:237bfbffb5f297018ef21e92b8fede75d3ca63e2154236331ef2b2a9dd818a02 79.51MiB
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

### Pruning Images

Another great feature added was pruning images that ensures the older content in the mirrored registry gets purged especially if it will not be needed anymore.  In this example we will demonstrate it with with a mirror of the Universal Base Image (ubi).   First lets create our imageset configuration file for the ubi7 image:

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

As we can see on the second run we mirrored the ubi8 image which superseded the ubi7 image. The mirror operation noticed this and then proceeded to prune the ubi7 image from the registry.   If we had wanted to keep both images on our registry we would have needed to specify both in our imageset configuration file.

### OpenShift Update Service Graph Creation

The OpenShift Update Service (OSUS) provides over-the-air updates to OpenShift Container Platform, including Red Hat Enterprise Linux CoreOS (RHCOS). It provides a graph, or diagram, that contains the vertices of component Operators and the edges that connect them. The edges in the graph show which versions you can safely update to. The vertices are update payloads that specify the intended state of the managed cluster components.

With oc mirror we can also mirror the graph data to our disconnected registry which enables our disconnected clusters to still show the visual of what versions we can update to.  In this example we will take the original 4.9.12 imageset file we created and just add the graph: true option.

~~~bash
$ cat << EOF > ~/imageset-configuration.yaml
apiVersion: mirror.openshift.io/v1alpha2
kind: ImageSetConfiguration
storageConfig:
  local:
    path: metadata
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
      sha256:0305702b3b7208c339c5df376aaacf6992465e34490bc16c2d66e423039b2b06 -> 4.9.12-x86_64-insights-operator
      sha256:0310a7b9ec788788a2a6324475add611b5c5304511fe27e7444500fdefbc6ef0 -> 4.9.12-x86_64-ironic-static-ip-manager
      sha256:038452cc237d09fbbea348a454007760fb2d04e1e2439e931c505665c2c98071 -> 4.9.12-x86_64-haproxy-router
      sha256:03cbd8048e00835d29ced43ddb4548e979e51dda727239cfdf027c9ef47339cf -> 4.9.12-x86_64-egress-router-cni
      sha256:0503429fe988d46d901cd8c625c5ca3e27d0eddd151cd6ee2af346c6397fbffe -> 4.9.12-x86_64-baremetal-machine-controllers
      sha256:051c76b923d9826bd577aeb1e176a4096a9af47e9a7c1819158282a5a417170b -> 4.9.12-x86_64-machine-config-operator
      sha256:06ca4e45c6ec3aba104a066905322e35b44c8a2c293d9f2821c83ed2cb743633 -> 4.9.12-x86_64-prometheus-config-reloader
      sha256:07c709f51f7383720c2acca830b3441c188ab0f077616d075a5a6bd9cbd1a2e4 -> 4.9.12-x86_64-cluster-storage-operator
      sha256:0b8a09ab3c370f7ef89319f3ff66cc346bbfc1cc48b58c2d40ef7d61b33a349c -> 4.9.12-x86_64-multus-route-override-cni
      sha256:0d741e590eee37fd9708609dde99c68f0b8fba9d0d5cee172227037b7446e3c7 -> 4.9.12-x86_64-ironic-ipa-downloader
      sha256:0e0a6328ea7449e2b3ecaa057f59f98cb49e2c71b60ea0d9a4f3d358ef472f36 -> 4.9.12-x86_64-ironic-inspector
      sha256:0fdd27a12ee71d1268ef2e7c4cfe8dbdd3a86e3010f77db3f2e530b928fa2a42 -> 4.9.12-x86_64-csi-node-driver-registrar
      sha256:1141f74833abe8de6384022fb7a800171640a40464a82669421517f5f7d2a128 -> 4.9.12-x86_64-csi-external-snapshotter
      sha256:119ce8d95beaef7fcc420fb1acc6d311a1a6309a371a1ccc427b64a7f2d1e371 -> 4.9.12-x86_64-csi-external-provisioner
      sha256:1267dd9b35b81041888212be03415b2fab37d1ac9e0fb4d9ddcf60c72f7a99ad -> 4.9.12-x86_64-container-networking-plugins
      sha256:13a775771eb0fbb24216f5cbaeab394426671bf5d7a5aa23268607e51b59ecbd -> 4.9.12-x86_64-jenkins-agent-base
      sha256:13bb02aabde612c09fe136b0ca02487cb5f48b9ddf871afbe0d4f2b4b1b66b3a -> 4.9.12-x86_64-csi-external-resizer
      sha256:183b7a1d2b4d3600a7251eda82f90ff45d4b5324b3bfdba6a091aa735fcff4be -> 4.9.12-x86_64-cluster-version-operator
      sha256:18740b88ca23a49a1cbbf3be74bf96ff536e961bb11235d4539cffccda7101ed -> 4.9.12-x86_64-kuryr-cni
      sha256:1938512df87a22d0a3c4f9d7aafa9557d7268f3511b52f9494946bb7478bc2da -> 4.9.12-x86_64-ovirt-machine-controllers
      sha256:19ac6ad2cac43fd4134dfed13168ac0761a014ec55d277eb4d1259caac757b04 -> 4.9.12-x86_64-cloud-credential-operator
      sha256:1bbe2e9e053350f5af04d80e9a088fb6cc5ca7898b8c1dfdeb5269a4befffa60 -> 4.9.12-x86_64-cluster-node-tuning-operator
      sha256:214b7e2b9bece6260311a2dc28bfc12c379e37186c09582221ec1fe724a995d1 -> 4.9.12-x86_64-kuryr-controller
      sha256:22e31d53a7c6a92176d5a183fd213fff4e2e68c343ccf6cca9c7fc1363e34836 -> 4.9.12-x86_64-multus-cni
      sha256:23244e61eaee6a986e1ac26def05a6eeefa3150b9e740787d8ec0be765b6c8ec -> 4.9.12-x86_64-tools
      sha256:235f6b6a1c9c41dbf3b8867bdb25da59c5e3a15af93b61fb13b4b5f41814d954 -> 4.9.12-x86_64-cluster-policy-controller
      sha256:23921ee4c51c3fc5b32598d89233353855b335cb1d3592bd4e14cda9b23aa0e3 -> 4.9.12-x86_64-cluster-bootstrap
      sha256:251f6fcf7b079421f5ed480db37715ce1fc35e4bbbf859b3ffc449b84d8fd246 -> 4.9.12-x86_64-baremetal-operator
      sha256:279a3393fbb0c344b410de5aed4702ae41c49382a5c271c5a6d915defa72c47a -> 4.9.12-x86_64-jenkins-agent-nodejs
      sha256:292d0fbd28892d4952e0cd914cb4b1eca7b5b31dad3ff26abbbfe3ee57ea24e6 -> 4.9.12-x86_64-cli-artifacts
      sha256:2ddae06a7ca8031611b8ec5a63050195a5b747c66289b8ad810bc8da73aa58d4 -> 4.9.12-x86_64-ironic-hardware-inventory-recorder
      sha256:2fc2c9d9fae3070c00239ea3d0d1d9fa7477c99c296b17b2fc352794c535912e -> 4.9.12-x86_64-network-metrics-daemon
      sha256:2ff95508332a75a0b0b50be84416297781946d0feb687df36c8082baff564857 -> 4.9.12-x86_64-prometheus-alertmanager
      sha256:33001ffab5cba9aaff19e475e0118ea158c99797bf6f3e210832f129ac34d71c -> 4.9.12-x86_64-cluster-kube-storage-version-migrator-operator
      sha256:3730bafc9c8a397887d4213b220afffe11cb6835ea5754e60a0cfe967efbdaf5 -> 4.9.12-x86_64-prometheus
      sha256:37e092de518988e67fb60bc5eaaa365058b6be6a1877613af5d12e2c4a2ec129 -> 4.9.12-x86_64-oauth-server
      sha256:3e4dd326434d115808c7bff14d8a38806c6757d928f2359737496fe6a32b7b4e -> 4.9.12-x86_64-cluster-kube-controller-manager-operator
      sha256:40302e29af4c6e3e9a6e73544a2fd4239da691b0afde937e76d078a642d30817 -> 4.9.12-x86_64-service-ca-operator
      sha256:406384d7815dc29c85c903318b1a98a2f3bd65b440a9344b71d6ddfa3e98c287 -> 4.9.12-x86_64-ovirt-csi-driver-operator
      sha256:426f99588fbce292824dba75372675b83daea64a1cf5d321fb5e4182fc43867e -> 4.9.12-x86_64-cli
      sha256:42eb156593fbc94fa12eaa5938c8335d500e222be370475ee19a57ad7f7f42a8 -> 4.9.12-x86_64-mdns-publisher
      sha256:43150eb3f35005a1c04fa4ed6fef3c8cd3199991658a88530d28cd1a32a5597a -> 4.9.12-x86_64-aws-cloud-controller-manager
      sha256:4446e75f22adf0e091bb337da46174ba7e82b394da5b04a2dc9c60b749c7cd02 -> 4.9.12-x86_64-vsphere-problem-detector
      sha256:46d7b63794f6ceec9fbcc63bbf170aff5ee3152ccb24ba51b8167ac59a30ca6a -> 4.9.12-x86_64-cluster-etcd-operator
      sha256:4b14ffcea52f9eb5952546634cb26bfb1d523a4dd81382021c71673fed91efa2 -> 4.9.12-x86_64-operator-lifecycle-manager
      sha256:4b56346c5c3c09eee6d8d37b6796f1e4f15e5a8de53b2afc6a4d3e6fb780ee24 -> 4.9.12-x86_64-csi-driver-manila
      sha256:4b979717b90adb7f1758e00403495f09a24e53bc537dbd2eb69398b83e3d8819 -> 4.9.12-x86_64-kube-state-metrics
      sha256:4f4021f6a725ee1bc3c393535742b720ee2fc5ffc978849a2b67fc437debc283 -> 4.9.12-x86_64-prometheus-node-exporter
      sha256:50e0b3eced37efe7c94f746e0260d4750144962ef16ab334b93c08b3635f946c -> 4.9.12-x86_64-csi-snapshot-controller
      sha256:550304f5ac4929e68b97108da732b8271997dee09db91a2d4fb55d3b143ad7eb -> 4.9.12-x86_64-aws-machine-controllers
      sha256:5982ecdb04297b4c546da927abbbb9bdaf772737e794218c20b43a5d4ede9d22 -> 4.9.12-x86_64-k8s-prometheus-adapter
      sha256:5a947cf105f932656f89e0a31973fec637a756ea0a66bea80fb623c37a91e7e0 -> 4.9.12-x86_64-installer
      sha256:5c4039e2846f73e3703f63a2aa52a27a85d8c3c9c3a076e05e4aac33301a5715 -> 4.9.12-x86_64-cluster-autoscaler
      sha256:5e33f9d095952866b9743cc8268fb740cce6d93439f00ce333a2de1e5974837e -> 4.9.12-x86_64-kube-rbac-proxy
      sha256:63149966fd4008c90409477bd30490445d8f11dd484009fdcbd665237d1e85a9 -> 4.9.12-x86_64-multus-admission-controller
      sha256:644b470e3cc0e417c8766bdecaa4944ded16267a542fc93d477b364a6905fbe7 -> 4.9.12-x86_64-csi-driver-manila-operator
      sha256:65f83e849f9cb76bad217bceee4e34a30d062e17a2a1a80237462d26d98e8870 -> 4.9.12-x86_64-gcp-machine-controllers
      sha256:66996ef32742e67e768985c759f248b5af8b503403c9844e535f3a1ee4c24c07 -> 4.9.12-x86_64-openstack-cinder-csi-driver-operator
      sha256:66bf4dc3d0a3f0559c48d7dc7b9b45af7c90e5b6be704421362645d225c09cf8 -> 4.9.12-x86_64-azure-cloud-controller-manager
      sha256:67cb844c3e059ef41d746d6132ff1a3cf4240bf959191fbf31a680461b95397f -> 4.9.12-x86_64-vsphere-csi-driver-syncer
      sha256:6a78c524aab5bc95c671811b2c76d59a6c2d394c8f9ba3f2a92bc05a780c783a -> 4.9.12-x86_64-cluster-image-registry-operator
      sha256:6e2a44337dbe5bf89d6f0f16124df38bb86f71c866a9ae38badb450e58e7f42c -> 4.9.12-x86_64-operator-registry
      sha256:6e68ba361e31be5dd1b3ecd0fe5318b337b1915d5db2d8b635cb6f8bb8ee7f97 -> 4.9.12-x86_64-machine-api-operator
      sha256:71a76b1a54d9b9ccba384c353db5777f4a3b37fff2c7027225bb534d09565615 -> 4.9.12-x86_64-openshift-apiserver
      sha256:72a75a1eecdfe8e42703fcde0bb63c1f5f256dd21dba92bc36edf54e9f2a845a -> 4.9.12-x86_64-cluster-kube-scheduler-operator
      sha256:72d072f33452fc8c5fb0751ac3743688cc4d44959654bb0747dad000218f6f05 -> 4.9.12-x86_64-azure-machine-controllers
      sha256:79c8e38ac956a6be5ba8dfbefc10e30ad0b5d3229ea8951fd9745b57fa5c6a90 -> 4.9.12-x86_64-cluster-autoscaler-operator
      sha256:7b90fbeebf473c2e85acc23bd116c3d09750377f32a800ae09caeae101d34957 -> 4.9.12-x86_64-vsphere-csi-driver-operator
      sha256:7bc4f0c55d6e8d92cb429337759824f8535e56a172e75b8c82959a4f4f270ab4 -> 4.9.12-x86_64-azure-disk-csi-driver
      sha256:7d78a787b5655292d4440942ae018ad5d1c985881c4e9d95d887d4f8450c7899 -> 4.9.12-x86_64-csi-livenessprobe
      sha256:7f734c4f3a69377a667c97ef00642accf60f2f0020ab89531eea3d650f1a89ca -> 4.9.12-x86_64-ovirt-csi-driver
      sha256:810097f053d1859d516ab784d975d41ae435ee91f5eaa7c90a02e643620c18fb -> 4.9.12-x86_64-cluster-network-operator
      sha256:82158554ee3c838c24ac3d894d5c92a30f9e6705e6e17520115a5edd62a2bf75 -> 4.9.12-x86_64-ovn-kubernetes
      sha256:82bfc9ea845d7948c362061afa266d7e3dc1465cec041371e99cb1bcb5056036 -> 4.9.12-x86_64-deployer
      sha256:83bc064c1957b18d231d0befd2bf14bcbadb3ebe20ff3b21dc4b23f048aec547 -> 4.9.12-x86_64-gcp-pd-csi-driver-operator
      sha256:8bbd804da57f3f961ce8cf8057ac7a946166df966bad4bca3295eeb16eadde5b -> 4.9.12-x86_64-libvirt-machine-controllers
      sha256:8ce4be01db73a940acf798ea8102dc06f340f3afec470a3af72abfe130114de9 -> 4.9.12-x86_64-prometheus-operator
      sha256:94145128ec7805e478fada43a8a8ade8054fb50b56c97f159fe9da28a56851c7 -> 4.9.12-x86_64-telemeter
      sha256:948def25e5c80edcc357064d740d78b97bb8fa4f940f13d0b501714c7edf57a1 -> 4.9.12-x86_64-console
      sha256:94926d57aaefa23376615302a14a96d4bca45dd6c3184a8595042b0ae847eb1b -> 4.9.12-x86_64-kube-storage-version-migrator
      sha256:961b30e0ab62cf04d000c1eb8330e62ff2daf4545ee094d3c9acd4c443e3c5d2 -> 4.9.12-x86_64-docker-builder
      sha256:96487fbdd3efaf1ef9c8e34bd17470095daa3dd01bf9c5c42e5c67ee99b21bbc -> 4.9.12-x86_64-aws-ebs-csi-driver-operator
      sha256:9716b0f99225f7aa9680a8f07fc02c3a8233b010acd332d7c4651d5e0177de3f -> 4.9.12-x86_64-cluster-monitoring-operator
      sha256:980d27d0eb312864f4dacf8f125c7a2ec6d507817045517ebfb80ea25601e1f6 -> 4.9.12-x86_64-cluster-config-operator
      sha256:9df074e84d628156a71c314708d4cc003e020d3e4948926e30654b5eb0c2a958 -> 4.9.12-x86_64-tests
      sha256:9e2d384be6ebe5fc9c089ab3ed1e1f082c180aed520ee4ff306e46fa56c12728 -> 4.9.12-x86_64-baremetal-installer
      sha256:9f56d95ed665c1442fb4ce0b6415dbecf17729372527c46570aee5c372282a1e -> 4.9.12-x86_64-openstack-cinder-csi-driver
      sha256:a4b8af157bd962a43aa8d44707743a6d743b70faeb72548c5a91869a50ae6b5e -> 4.9.12-x86_64-thanos
      sha256:a4e51611597318e86a82389b481f49a0978d5deb403f705b14346161c0b62104 -> 4.9.12-x86_64-console-operator
      sha256:a6322f222f98adc1585ff8777c73140a56ac3cdcd8a6949309884b79496bcbb6 -> 4.9.12-x86_64-sdn
      sha256:a928705d99ccf81cfb7c0ef07a515f67a488d7052a2d7ba34ddabaa140295d78 -> 4.9.12-x86_64-grafana
      sha256:aaaec3417207330eaeb203f459bdd819c27bac1956f1d9f0684f42dc5a3807e6 -> 4.9.12-x86_64-cluster-update-keys
      sha256:aaf0854244f356ccb3f1cd70356362ac5ef7a9f227e08128740306430fd75497 -> 4.9.12-x86_64-coredns
      sha256:ad7e0cca53e098a448f9d5df9ad5410515ee1ed3c83dbbe8ab72fc8ae73b986c -> 4.9.12-x86_64-kube-proxy
      sha256:b98f4e019aa3cae1ac333a8241bafdbe52caccfcdcd7f640d1a7410dd33dd788 -> 4.9.12-x86_64-multus-whereabouts-ipam-cni
      sha256:bb0a5b9d4d3d5c774d58e1f2f11abd260ea1b47e79c64047f753d04927b5c202 -> 4.9.12-x86_64-ironic
      sha256:bb5f32d6a5294d211e7016a3aeefc1a02dd8ce3773747bcead61ab40f3a13366 -> 4.9.12-x86_64-operator-marketplace
      sha256:bc6efcc92aa2dde7d917eb2922e3eb19eeebcd4e8f19a499fc536fc2e45183ec -> 4.9.12-x86_64-cluster-dns-operator
      sha256:bc7227d80dc8f416eabc2bf1d326fa41ec9ab849b2a06291ef98d25a83f5ccb6 -> 4.9.12-x86_64-gcp-pd-csi-driver
      sha256:bc820dec82b685d589a6d71fea17baf28d7404d0351d7d91876476c4c2932c0c -> 4.9.12-x86_64-cluster-samples-operator
      sha256:bd81d21f0593cab25f321b000e8e18f5ff50fb741289261874fa332407b9da05 -> 4.9.12-x86_64-vsphere-csi-driver
      sha256:c017f8b24e9d9a913456373c69dec63e3315ddae052ac6ad9cee25f856abe502 -> 4.9.12-x86_64-docker-registry
      sha256:c10376143bb2fc51f72619cb7d45c8905d7c5a10b0150e1a1f65c54a90fff1bc -> 4.9.12-x86_64-csi-external-attacher
      sha256:c56c86d030185bda241514593970e80f75ae75afd9bc6288388944bc2a1dfb1f -> 4.9.12-x86_64-pod
      sha256:c5c4823cce587db60358f78f2a324a6def77930b0fa392a2939e04041b5a4a6b -> 4.9.12-x86_64-openstack-machine-controllers
      sha256:c6124ae70fd1f19ada3d5930ffc912dd5a3fb09dcffabc6425e9e327a61528fa -> 4.9.12-x86_64-prom-label-proxy
      sha256:ca8dc8a6e1a672565fb1db1bdf0bc49d1878bc9621b15b293334937d42a34972 -> 4.9.12-x86_64-openstack-cloud-controller-manager
      sha256:cc04ac8cc0ebd5ac0b7bd69e27a5d3958d311b61e3f78ed896acc2445d5e1ba7 -> 4.9.12-x86_64-azure-cloud-node-manager
      sha256:ccd64e8b56711ced7558d8e593ae32867cb2178ea73078f47d20bc7e2e56119b -> 4.9.12-x86_64-aws-pod-identity-webhook
      sha256:cd13cdf851def78178b6c1072910fcbceabdb121b888cf82377469fea75a0bf3 -> 4.9.12-x86_64-azure-disk-csi-driver-operator
      sha256:ce897bc72101dacc82aa593974fa0d8a421a43227b540fbcf1e303ffb1d3f1ea -> 4.9.12-x86_64-driver-toolkit
      sha256:cf33109e11798f970fa181f7ab08be92301b245dc2b7d17226f38b4146d585cd -> 4.9.12-x86_64-openshift-state-metrics
      sha256:cff70e2271d5bcc26426cf3f22f3439219677e6175fcaa00c2e5a5da9379d5d6 -> 4.9.12-x86_64-jenkins-agent-maven
      sha256:d622ce62d93f99c3f5e4ffdcafc19ee3c31f82630185b882c7bd4d332fc435cd -> 4.9.12-x86_64-multus-networkpolicy
      sha256:d74f9409782832026f98e261089bad47fa50be5282c2775dbe521f5fda0e0ee4 -> 4.9.12-x86_64-jenkins
      sha256:d8c857820a05e64b271622508038330f411f08d87cedd16a3ac13095ed920b4b -> 4.9.12-x86_64-installer-artifacts
      sha256:db28495dcf72f26368357ed71b745c3c7b5e2a40c787bc8a76440f16872a0dbc -> 4.9.12-x86_64-cluster-csi-snapshot-controller-operator
      sha256:df22255abc474a2b61b323925f76126fa2f8c99affb1c48c2a0eb16c4b4a1056 -> 4.9.12-x86_64-cluster-ingress-operator
      sha256:e03cefa188318d283eb39b799950a3540d0cfe14becf8995d391dfff5dd0369a -> 4.9.12-x86_64-network-tools
      sha256:e146605c1b75917d26c07268b361134aeda68983b2e2b060c202420b8267aa45 -> 4.9.12-x86_64-oauth-proxy
      sha256:e2998b64e69d5a903a76e5f47f1f9aba9b4f5db13f0dbe265c85121317c1cd33 -> 4.9.12-x86_64-configmap-reloader
      sha256:e5c16190ac98e446c7cf90cba8e14e996f81021ea4a38abd17b87c13b30bd6f8 -> 4.9.12-x86_64-oauth-apiserver
      sha256:e79fb8754d7c01a60c1763fe39104343783a6498bf10254b4206eae532c1679e -> 4.9.12-x86_64-cluster-openshift-controller-manager-operator
      sha256:e80f8718cc38cb025680205d890d4f622f975297bc437cf52ae1fbce0e89bf1c -> 4.9.12-x86_64-cluster-authentication-operator
      sha256:eae57963e5746607da7ce8070275abd301ab420ce767706d5b36972f64a0e450 -> 4.9.12-x86_64-must-gather
      sha256:edc8e8eafe3f73633f3ffe4a393e21f4495d518e9f044f127a3616315cdd78b4 -> 4.9.12-x86_64-openshift-controller-manager
      sha256:ee96cf6a6fc395bb5432ba2322c3fa740832ebe490211e53b401f35cbe615636 -> 4.9.12-x86_64-csi-snapshot-validation-webhook
      sha256:efbc54ce84c0b4dd7fe7b3e6fa2e6cd488b57d9c6fe7d055ef6395ade51b5906 -> 4.9.12-x86_64-cluster-openshift-apiserver-operator
      sha256:f0d490ad627c935e07384b942859a938a8445e66486450919537d5bac8f92f62 -> 4.9.12-x86_64-cluster-baremetal-operator
      sha256:f22b23274a3f220805ef2b83502d9af379b6841dfbd593d526f072acd6c780dd -> 4.9.12-x86_64-cluster-machine-approver
      sha256:f2a6bffb17480fb2caa5a52512efa348a09b960d23782d02ec8733df948dbff6 -> 4.9.12-x86_64-csi-driver-nfs
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
  ubi8/ubi
    blobs:
      registry.access.redhat.com/ubi8/ubi sha256:7e3624512448126fd29504b9af9bc034538918c54f0988fb08c03ff7a3a9a4cb 1.747KiB
      registry.access.redhat.com/ubi8/ubi sha256:1c716916a225fab7df9407f9190793c5757af63a875810145426432ec461250c 1.748KiB
      registry.access.redhat.com/ubi8/ubi sha256:01ab2c1195006b264cb8a77d0756617e33944292964dcbd65b4eb5bab57eb22f 1.752KiB
      registry.access.redhat.com/ubi8/ubi sha256:847f634e7f1e9901731ab73fc8bae8971d44de50c0bbe89acd830b08a475db13 1.753KiB
      registry.access.redhat.com/ubi8/ubi sha256:066fa0d6d5ac443e9f672920f40ede88ea4c69934f4d792e21cb89a61a76cb58 4.26KiB
      registry.access.redhat.com/ubi8/ubi sha256:4891eb50ccc7df2e2a4ed7b8887e782b34bd253a473d28648fd0632d312eb83c 4.262KiB
      registry.access.redhat.com/ubi8/ubi sha256:f754849582f5232784db3791c3a1092482fbec296274a00a0bdee25e3e20c1c4 4.264KiB
      registry.access.redhat.com/ubi8/ubi sha256:6acdcdbcf1a1be6435c4756ca7c4637b651dd95da205d7319f0d2495230fa2d5 4.275KiB
      registry.access.redhat.com/ubi8/ubi sha256:5e70be1586c73537515c1c2e1441ca2f90c489b814b2fee24c3ac929a2f01853 72.03MiB
      registry.access.redhat.com/ubi8/ubi sha256:e46e89129c9d273e7b57a9b719d06e3f9e0469a35cdfcc7ea4bda6b7a69cf965 72.95MiB
      registry.access.redhat.com/ubi8/ubi sha256:db0f4cd412505c5cc2f31cf3c65db80f84d8656c4bfa9ef627a6f532c0459fc4 74.73MiB
      registry.access.redhat.com/ubi8/ubi sha256:d6050ae37df36bd42b8d5ae50c9ec8016ec8191cd44d03ff5b67a2e513b16f8f 81MiB
    manifests:
      sha256:08e221b041a95e6840b208c618ae56c27e3429c3dad637ece01c9b471cc8fac6
      sha256:48ba6276b423eafd63c7c54949cbc98b47165e5660329b7bae9632f6c592b258
      sha256:6edca3916b34d10481e4d24d14ebe6ebc6db517bec1b2db6ae2d7d47c2ecfaee
      sha256:bc2a3d7583a955f1c2f9753b5c01ebca12287c5f68a8ce7e85b2c7fd64c145d3
      sha256:c2931a594cab69b4a2e912fd5d9bbc7e3542e47c341974fad6e407c8d91e76c3
      sha256:6edca3916b34d10481e4d24d14ebe6ebc6db517bec1b2db6ae2d7d47c2ecfaee -> latest
  stats: shared=5 unique=297 size=9.833GiB ratio=0.99

phase 0:
  provisioning.schmaustech.com:5000 ubi8/ubi          blobs=12  mounts=0 manifests=6   shared=0
  provisioning.schmaustech.com:5000 openshift/release blobs=288 mounts=0 manifests=140 shared=5
phase 1:
  provisioning.schmaustech.com:5000 openshift/release-images blobs=7 mounts=5 manifests=1 shared=5

info: Planning completed in 27.29s
sha256:08e221b041a95e6840b208c618ae56c27e3429c3dad637ece01c9b471cc8fac6 provisioning.schmaustech.com:5000/ubi8/ubi
sha256:48ba6276b423eafd63c7c54949cbc98b47165e5660329b7bae9632f6c592b258 provisioning.schmaustech.com:5000/ubi8/ubi
sha256:bc2a3d7583a955f1c2f9753b5c01ebca12287c5f68a8ce7e85b2c7fd64c145d3 provisioning.schmaustech.com:5000/ubi8/ubi
sha256:c2931a594cab69b4a2e912fd5d9bbc7e3542e47c341974fad6e407c8d91e76c3 provisioning.schmaustech.com:5000/ubi8/ubi
sha256:6edca3916b34d10481e4d24d14ebe6ebc6db517bec1b2db6ae2d7d47c2ecfaee provisioning.schmaustech.com:5000/ubi8/ubi:latest
sha256:0305702b3b7208c339c5df376aaacf6992465e34490bc16c2d66e423039b2b06 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-insights-operator
sha256:2ff95508332a75a0b0b50be84416297781946d0feb687df36c8082baff564857 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prometheus-alertmanager
sha256:cc04ac8cc0ebd5ac0b7bd69e27a5d3958d311b61e3f78ed896acc2445d5e1ba7 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-azure-cloud-node-manager
sha256:df22255abc474a2b61b323925f76126fa2f8c99affb1c48c2a0eb16c4b4a1056 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-ingress-operator
sha256:e146605c1b75917d26c07268b361134aeda68983b2e2b060c202420b8267aa45 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-oauth-proxy
sha256:c5c4823cce587db60358f78f2a324a6def77930b0fa392a2939e04041b5a4a6b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openstack-machine-controllers
sha256:0503429fe988d46d901cd8c625c5ca3e27d0eddd151cd6ee2af346c6397fbffe provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-baremetal-machine-controllers
sha256:ca8dc8a6e1a672565fb1db1bdf0bc49d1878bc9621b15b293334937d42a34972 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openstack-cloud-controller-manager
sha256:0e0a6328ea7449e2b3ecaa057f59f98cb49e2c71b60ea0d9a4f3d358ef472f36 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic-inspector
sha256:cf33109e11798f970fa181f7ab08be92301b245dc2b7d17226f38b4146d585cd provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openshift-state-metrics
sha256:bc820dec82b685d589a6d71fea17baf28d7404d0351d7d91876476c4c2932c0c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-samples-operator
sha256:e03cefa188318d283eb39b799950a3540d0cfe14becf8995d391dfff5dd0369a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-network-tools
sha256:948def25e5c80edcc357064d740d78b97bb8fa4f940f13d0b501714c7edf57a1 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-console
sha256:5c4039e2846f73e3703f63a2aa52a27a85d8c3c9c3a076e05e4aac33301a5715 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-autoscaler
sha256:4b14ffcea52f9eb5952546634cb26bfb1d523a4dd81382021c71673fed91efa2 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-operator-lifecycle-manager
sha256:f7ce7247375fa57b2be95733fb26a85751a5f41b28652e96612bea2ee6a25442 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-cloud-controller-manager-operator
sha256:d74f9409782832026f98e261089bad47fa50be5282c2775dbe521f5fda0e0ee4 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-jenkins
sha256:bc6efcc92aa2dde7d917eb2922e3eb19eeebcd4e8f19a499fc536fc2e45183ec provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-dns-operator
sha256:0fdd27a12ee71d1268ef2e7c4cfe8dbdd3a86e3010f77db3f2e530b928fa2a42 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-node-driver-registrar
sha256:d622ce62d93f99c3f5e4ffdcafc19ee3c31f82630185b882c7bd4d332fc435cd provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-multus-networkpolicy
sha256:6a78c524aab5bc95c671811b2c76d59a6c2d394c8f9ba3f2a92bc05a780c783a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-image-registry-operator
sha256:fd7ce3da297b589c7b3c34f6dc820f4d71a51ec367424749e76fbfad06298456 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-baremetal-runtimecfg
sha256:07c709f51f7383720c2acca830b3441c188ab0f077616d075a5a6bd9cbd1a2e4 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-storage-operator
sha256:c6124ae70fd1f19ada3d5930ffc912dd5a3fb09dcffabc6425e9e327a61528fa provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prom-label-proxy
sha256:94926d57aaefa23376615302a14a96d4bca45dd6c3184a8595042b0ae847eb1b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kube-storage-version-migrator
sha256:0310a7b9ec788788a2a6324475add611b5c5304511fe27e7444500fdefbc6ef0 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic-static-ip-manager
sha256:5e33f9d095952866b9743cc8268fb740cce6d93439f00ce333a2de1e5974837e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kube-rbac-proxy
sha256:aaf0854244f356ccb3f1cd70356362ac5ef7a9f227e08128740306430fd75497 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-coredns
sha256:a4e51611597318e86a82389b481f49a0978d5deb403f705b14346161c0b62104 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-console-operator
sha256:183b7a1d2b4d3600a7251eda82f90ff45d4b5324b3bfdba6a091aa735fcff4be provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-version-operator
sha256:644b470e3cc0e417c8766bdecaa4944ded16267a542fc93d477b364a6905fbe7 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-driver-manila-operator
sha256:fe552892df8d59716997e8149a384ef65cade7610de3264652eb77e2d93575ed provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-kube-apiserver-operator
sha256:ee96cf6a6fc395bb5432ba2322c3fa740832ebe490211e53b401f35cbe615636 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-snapshot-validation-webhook
sha256:37e092de518988e67fb60bc5eaaa365058b6be6a1877613af5d12e2c4a2ec129 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-oauth-server
sha256:c56c86d030185bda241514593970e80f75ae75afd9bc6288388944bc2a1dfb1f provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-pod
sha256:82bfc9ea845d7948c362061afa266d7e3dc1465cec041371e99cb1bcb5056036 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-deployer
sha256:cd13cdf851def78178b6c1072910fcbceabdb121b888cf82377469fea75a0bf3 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-azure-disk-csi-driver-operator
sha256:4b56346c5c3c09eee6d8d37b6796f1e4f15e5a8de53b2afc6a4d3e6fb780ee24 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-driver-manila
sha256:40302e29af4c6e3e9a6e73544a2fd4239da691b0afde937e76d078a642d30817 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-service-ca-operator
sha256:0b8a09ab3c370f7ef89319f3ff66cc346bbfc1cc48b58c2d40ef7d61b33a349c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-multus-route-override-cni
sha256:038452cc237d09fbbea348a454007760fb2d04e1e2439e931c505665c2c98071 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-haproxy-router
sha256:22e31d53a7c6a92176d5a183fd213fff4e2e68c343ccf6cca9c7fc1363e34836 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-multus-cni
sha256:e80f8718cc38cb025680205d890d4f622f975297bc437cf52ae1fbce0e89bf1c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-authentication-operator
sha256:292d0fbd28892d4952e0cd914cb4b1eca7b5b31dad3ff26abbbfe3ee57ea24e6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cli-artifacts
sha256:4f4021f6a725ee1bc3c393535742b720ee2fc5ffc978849a2b67fc437debc283 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prometheus-node-exporter
sha256:235f6b6a1c9c41dbf3b8867bdb25da59c5e3a15af93b61fb13b4b5f41814d954 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-policy-controller
sha256:fd41b7237cac235fead9bda6dc9bf5c6cbde163ebf9d9249f33065d5ceadded0 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-etcd
sha256:cff70e2271d5bcc26426cf3f22f3439219677e6175fcaa00c2e5a5da9379d5d6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-jenkins-agent-maven
sha256:550304f5ac4929e68b97108da732b8271997dee09db91a2d4fb55d3b143ad7eb provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-aws-machine-controllers
sha256:66bf4dc3d0a3f0559c48d7dc7b9b45af7c90e5b6be704421362645d225c09cf8 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-azure-cloud-controller-manager
sha256:5982ecdb04297b4c546da927abbbb9bdaf772737e794218c20b43a5d4ede9d22 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-k8s-prometheus-adapter
sha256:3730bafc9c8a397887d4213b220afffe11cb6835ea5754e60a0cfe967efbdaf5 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prometheus
sha256:214b7e2b9bece6260311a2dc28bfc12c379e37186c09582221ec1fe724a995d1 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kuryr-controller
sha256:f60434187e593c079d98e94b6ba69bce34e5daa5ac161e834827d594b070592b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-hyperkube
sha256:e5c16190ac98e446c7cf90cba8e14e996f81021ea4a38abd17b87c13b30bd6f8 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-oauth-apiserver
sha256:2ddae06a7ca8031611b8ec5a63050195a5b747c66289b8ad810bc8da73aa58d4 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic-hardware-inventory-recorder
sha256:8bbd804da57f3f961ce8cf8057ac7a946166df966bad4bca3295eeb16eadde5b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-libvirt-machine-controllers
sha256:7d78a787b5655292d4440942ae018ad5d1c985881c4e9d95d887d4f8450c7899 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-livenessprobe
sha256:f22b23274a3f220805ef2b83502d9af379b6841dfbd593d526f072acd6c780dd provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-machine-approver
sha256:9df074e84d628156a71c314708d4cc003e020d3e4948926e30654b5eb0c2a958 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-tests
sha256:980d27d0eb312864f4dacf8f125c7a2ec6d507817045517ebfb80ea25601e1f6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-config-operator
sha256:83bc064c1957b18d231d0befd2bf14bcbadb3ebe20ff3b21dc4b23f048aec547 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-gcp-pd-csi-driver-operator
sha256:4b979717b90adb7f1758e00403495f09a24e53bc537dbd2eb69398b83e3d8819 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kube-state-metrics
sha256:f83965a48127fcd265ef40ce6037df806980a088f8c83798be5b91e5cc09a706 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic-machine-os-downloader
sha256:0d741e590eee37fd9708609dde99c68f0b8fba9d0d5cee172227037b7446e3c7 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic-ipa-downloader
sha256:e2998b64e69d5a903a76e5f47f1f9aba9b4f5db13f0dbe265c85121317c1cd33 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-configmap-reloader
sha256:db28495dcf72f26368357ed71b745c3c7b5e2a40c787bc8a76440f16872a0dbc provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-csi-snapshot-controller-operator
sha256:42eb156593fbc94fa12eaa5938c8335d500e222be370475ee19a57ad7f7f42a8 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-mdns-publisher
sha256:23921ee4c51c3fc5b32598d89233353855b335cb1d3592bd4e14cda9b23aa0e3 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-bootstrap
sha256:f2a6bffb17480fb2caa5a52512efa348a09b960d23782d02ec8733df948dbff6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-driver-nfs
sha256:eae57963e5746607da7ce8070275abd301ab420ce767706d5b36972f64a0e450 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-must-gather
sha256:ccd64e8b56711ced7558d8e593ae32867cb2178ea73078f47d20bc7e2e56119b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-aws-pod-identity-webhook
sha256:d8c857820a05e64b271622508038330f411f08d87cedd16a3ac13095ed920b4b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-installer-artifacts
sha256:a928705d99ccf81cfb7c0ef07a515f67a488d7052a2d7ba34ddabaa140295d78 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-grafana
sha256:94145128ec7805e478fada43a8a8ade8054fb50b56c97f159fe9da28a56851c7 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-telemeter
sha256:9f56d95ed665c1442fb4ce0b6415dbecf17729372527c46570aee5c372282a1e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openstack-cinder-csi-driver
sha256:3e4dd326434d115808c7bff14d8a38806c6757d928f2359737496fe6a32b7b4e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-kube-controller-manager-operator
sha256:c017f8b24e9d9a913456373c69dec63e3315ddae052ac6ad9cee25f856abe502 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-docker-registry
sha256:33001ffab5cba9aaff19e475e0118ea158c99797bf6f3e210832f129ac34d71c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-kube-storage-version-migrator-operator
sha256:b98f4e019aa3cae1ac333a8241bafdbe52caccfcdcd7f640d1a7410dd33dd788 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-multus-whereabouts-ipam-cni
sha256:a6322f222f98adc1585ff8777c73140a56ac3cdcd8a6949309884b79496bcbb6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-sdn
sha256:06ca4e45c6ec3aba104a066905322e35b44c8a2c293d9f2821c83ed2cb743633 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prometheus-config-reloader
sha256:67cb844c3e059ef41d746d6132ff1a3cf4240bf959191fbf31a680461b95397f provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-vsphere-csi-driver-syncer
sha256:13a775771eb0fbb24216f5cbaeab394426671bf5d7a5aa23268607e51b59ecbd provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-jenkins-agent-base
sha256:1267dd9b35b81041888212be03415b2fab37d1ac9e0fb4d9ddcf60c72f7a99ad provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-container-networking-plugins
sha256:961b30e0ab62cf04d000c1eb8330e62ff2daf4545ee094d3c9acd4c443e3c5d2 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-docker-builder
sha256:13bb02aabde612c09fe136b0ca02487cb5f48b9ddf871afbe0d4f2b4b1b66b3a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-external-resizer
sha256:bb5f32d6a5294d211e7016a3aeefc1a02dd8ce3773747bcead61ab40f3a13366 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-operator-marketplace
sha256:23244e61eaee6a986e1ac26def05a6eeefa3150b9e740787d8ec0be765b6c8ec provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-tools
sha256:c10376143bb2fc51f72619cb7d45c8905d7c5a10b0150e1a1f65c54a90fff1bc provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-external-attacher
sha256:bb0a5b9d4d3d5c774d58e1f2f11abd260ea1b47e79c64047f753d04927b5c202 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ironic
sha256:7bc4f0c55d6e8d92cb429337759824f8535e56a172e75b8c82959a4f4f270ab4 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-azure-disk-csi-driver
sha256:66996ef32742e67e768985c759f248b5af8b503403c9844e535f3a1ee4c24c07 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openstack-cinder-csi-driver-operator
sha256:bd81d21f0593cab25f321b000e8e18f5ff50fb741289261874fa332407b9da05 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-vsphere-csi-driver
sha256:18740b88ca23a49a1cbbf3be74bf96ff536e961bb11235d4539cffccda7101ed provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kuryr-cni
sha256:9e2d384be6ebe5fc9c089ab3ed1e1f082c180aed520ee4ff306e46fa56c12728 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-baremetal-installer
sha256:ce897bc72101dacc82aa593974fa0d8a421a43227b540fbcf1e303ffb1d3f1ea provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-driver-toolkit
sha256:46d7b63794f6ceec9fbcc63bbf170aff5ee3152ccb24ba51b8167ac59a30ca6a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-etcd-operator
sha256:65f83e849f9cb76bad217bceee4e34a30d062e17a2a1a80237462d26d98e8870 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-gcp-machine-controllers
sha256:e79fb8754d7c01a60c1763fe39104343783a6498bf10254b4206eae532c1679e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-openshift-controller-manager-operator
sha256:1bbe2e9e053350f5af04d80e9a088fb6cc5ca7898b8c1dfdeb5269a4befffa60 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-node-tuning-operator
sha256:19ac6ad2cac43fd4134dfed13168ac0761a014ec55d277eb4d1259caac757b04 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cloud-credential-operator
sha256:bc7227d80dc8f416eabc2bf1d326fa41ec9ab849b2a06291ef98d25a83f5ccb6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-gcp-pd-csi-driver
sha256:9716b0f99225f7aa9680a8f07fc02c3a8233b010acd332d7c4651d5e0177de3f provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-monitoring-operator
sha256:50e0b3eced37efe7c94f746e0260d4750144962ef16ab334b93c08b3635f946c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-snapshot-controller
sha256:0213887c325d3967d122fe875b45c9259e2b8388db9dc4e0a25c0561414b8737 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-aws-ebs-csi-driver
sha256:810097f053d1859d516ab784d975d41ae435ee91f5eaa7c90a02e643620c18fb provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-network-operator
sha256:71a76b1a54d9b9ccba384c353db5777f4a3b37fff2c7027225bb534d09565615 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openshift-apiserver
sha256:5a947cf105f932656f89e0a31973fec637a756ea0a66bea80fb623c37a91e7e0 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-installer
sha256:00bfbe7800a9c803d13e666122a7b1f52bea6e53b0fb2352d8566d1e75759708 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-keepalived-ipfailover
sha256:efbc54ce84c0b4dd7fe7b3e6fa2e6cd488b57d9c6fe7d055ef6395ade51b5906 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-openshift-apiserver-operator
sha256:2fc2c9d9fae3070c00239ea3d0d1d9fa7477c99c296b17b2fc352794c535912e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-network-metrics-daemon
sha256:edc8e8eafe3f73633f3ffe4a393e21f4495d518e9f044f127a3616315cdd78b4 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-openshift-controller-manager
sha256:79c8e38ac956a6be5ba8dfbefc10e30ad0b5d3229ea8951fd9745b57fa5c6a90 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-autoscaler-operator
sha256:1141f74833abe8de6384022fb7a800171640a40464a82669421517f5f7d2a128 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-external-snapshotter
sha256:aaaec3417207330eaeb203f459bdd819c27bac1956f1d9f0684f42dc5a3807e6 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-update-keys
sha256:43150eb3f35005a1c04fa4ed6fef3c8cd3199991658a88530d28cd1a32a5597a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-aws-cloud-controller-manager
sha256:f0d490ad627c935e07384b942859a938a8445e66486450919537d5bac8f92f62 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-baremetal-operator
sha256:82158554ee3c838c24ac3d894d5c92a30f9e6705e6e17520115a5edd62a2bf75 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ovn-kubernetes
sha256:ad7e0cca53e098a448f9d5df9ad5410515ee1ed3c83dbbe8ab72fc8ae73b986c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-kube-proxy
sha256:406384d7815dc29c85c903318b1a98a2f3bd65b440a9344b71d6ddfa3e98c287 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ovirt-csi-driver-operator
sha256:279a3393fbb0c344b410de5aed4702ae41c49382a5c271c5a6d915defa72c47a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-jenkins-agent-nodejs
sha256:6e68ba361e31be5dd1b3ecd0fe5318b337b1915d5db2d8b635cb6f8bb8ee7f97 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-machine-api-operator
sha256:119ce8d95beaef7fcc420fb1acc6d311a1a6309a371a1ccc427b64a7f2d1e371 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-csi-external-provisioner
sha256:72d072f33452fc8c5fb0751ac3743688cc4d44959654bb0747dad000218f6f05 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-azure-machine-controllers
sha256:251f6fcf7b079421f5ed480db37715ce1fc35e4bbbf859b3ffc449b84d8fd246 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-baremetal-operator
sha256:8ce4be01db73a940acf798ea8102dc06f340f3afec470a3af72abfe130114de9 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-prometheus-operator
sha256:72a75a1eecdfe8e42703fcde0bb63c1f5f256dd21dba92bc36edf54e9f2a845a provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cluster-kube-scheduler-operator
sha256:96487fbdd3efaf1ef9c8e34bd17470095daa3dd01bf9c5c42e5c67ee99b21bbc provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-aws-ebs-csi-driver-operator
sha256:f5628b30aa047fe32cba9308c70c581f7d9812f40a3e651a84f0532af184bfd2 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-machine-os-content
sha256:03cbd8048e00835d29ced43ddb4548e979e51dda727239cfdf027c9ef47339cf provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-egress-router-cni
sha256:a4b8af157bd962a43aa8d44707743a6d743b70faeb72548c5a91869a50ae6b5e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-thanos
sha256:1938512df87a22d0a3c4f9d7aafa9557d7268f3511b52f9494946bb7478bc2da provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ovirt-machine-controllers
sha256:7f734c4f3a69377a667c97ef00642accf60f2f0020ab89531eea3d650f1a89ca provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-ovirt-csi-driver
sha256:7b90fbeebf473c2e85acc23bd116c3d09750377f32a800ae09caeae101d34957 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-vsphere-csi-driver-operator
sha256:63149966fd4008c90409477bd30490445d8f11dd484009fdcbd665237d1e85a9 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-multus-admission-controller
sha256:4446e75f22adf0e091bb337da46174ba7e82b394da5b04a2dc9c60b749c7cd02 provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-vsphere-problem-detector
sha256:426f99588fbce292824dba75372675b83daea64a1cf5d321fb5e4182fc43867e provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-cli
sha256:6e2a44337dbe5bf89d6f0f16124df38bb86f71c866a9ae38badb450e58e7f42c provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-operator-registry
sha256:051c76b923d9826bd577aeb1e176a4096a9af47e9a7c1819158282a5a417170b provisioning.schmaustech.com:5000/openshift/release:4.9.12-x86_64-machine-config-operator
sha256:dd71b3cd08ce1e859e0e740a585827c9caa1341819d1121d92879873a127f5e2 provisioning.schmaustech.com:5000/openshift/release-images:4.9.12-x86_64
info: Mirroring completed in 58.01s (0B/s)
Pruning 13 manifest(s) from repository openshift-logging/logging-curator5-rhel8
Pruning 8 manifest(s) from repository openshift4/ose-oauth-proxy
Pruning 3 manifest(s) from repository openshift-logging/elasticsearch-operator-bundle
Pruning 13 manifest(s) from repository openshift-logging/elasticsearch-proxy-rhel8
Pruning 13 manifest(s) from repository openshift-logging/elasticsearch6-rhel8
Pruning 13 manifest(s) from repository openshift-logging/kibana6-rhel8
Pruning 13 manifest(s) from repository openshift4/ose-kube-rbac-proxy
Pruning 13 manifest(s) from repository openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:0bc94a614ac031c45530e18b702fa102a349f40c24a59b16d536f8199d3b04ff from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:20b07dc6e878d6b446f53382d46d619b642f5770b5557185430ce3f1027e9e58 from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:7af1bcccd4219e6f0678bd0570e65e00e6924d0c7013a081a8036168badba724 from repo openshift-logging/elasticsearch-operator-bundle
Deleting manifest sha256:08b3eb667df212d8983ba777920536f2d3b9bd363c2cb718780907fd13aafe13 from repo openshift4/ose-oauth-proxy
Deleting manifest sha256:4229507d8b89b1a24f16477661b41a4a7a6925d90fed348dd609978297fe88c4 from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:3a5c08cba4458bfa6eadb89b1021b4c1d079d4dffcf8696e6805aac998134927 from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:4fad94662f25baf49926835945125fd98146ab22c1bb6137e4ed3941f1ca9216 from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:12757e265f9b0a04939e9f7e2708867c2cc20b0b474b3a11e02edfa8b002d616 from repo openshift4/ose-oauth-proxy
Deleting manifest sha256:46752e10e726c84be876c1dfd565fba4815054afd71db84af16a854346930a8f from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:a909c412c3f41c780d193e55611561b89f42e140f02dd2057f9877104beea263 from repo openshift-logging/elasticsearch-operator-bundle
Deleting manifest sha256:5830aaf028a6a94ade5bb40840180911dd80ef0dc4eb93080cf8bde3476cd585 from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:6654f0fb5a4cc17a7b202c8348ee1a73ec95b2176d07562e4219554ebe9f174f from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:20081ac90a5a3b548669d7dcea7e5bd45da7c44dd7279a2ca5baf765d1214e70 from repo openshift4/ose-oauth-proxy
Deleting manifest sha256:119265db56646a10f10947f14ed12e5b409ff8c5cc3bdc3ef9d3f3d3364b82f6 from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:c45340a9bdd7b544b9410d1c52fa6e9b444571d506aa05d6849281df2014ef1c from repo openshift-logging/elasticsearch-operator-bundle
Deleting manifest sha256:4c4777c63d906f332d658183f76cc0f7c1eefbfa58adfa6b7b584ab710323ac7 from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:59c4a4b20ad42a32f09c60fada67a9f20c237e8dce8ece678dced5c37a87b65b from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:1cbe10ebc4c421a53bf9a652596efe5510a8591bf9cf1336729fa8bef9a8963c from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:79ed4a8e586776a7d6f0c2d1a18e5d3323504d0dc7a5878cdf032957babe5a5a from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:2e80a063a29ef4917ec42d4465185ab0ab5b821fc2335c12d8064ab2dedcec1e from repo openshift4/ose-oauth-proxy
Deleting manifest sha256:036c78fea970d6bbe45aa1097a8ed701c72ce1117404450dd7e717a1bedd2b2a from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:64adeb26441159a1d2c3624fdb2a8b76f642f48d1171ce46bbfee7767839c77f from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:363822fd64a18478e51fd30c6851172c4554cff467059e61d643e8d048fe4cec from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:5ed92d7ac40e29491d237353b27237b38824eff3fe983628f199ff4ab6431ca1 from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:0a50f810f74d571d7371a80074027f2eb428f61424400ac070743442f47e4272 from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:849379f0269df32524a3faeb5c3d57bf75494159d34395b50eb9452611310054 from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:6c01bead972377b810fe92313354687f2032ea36666f74a31063566b28239455 from repo openshift4/ose-oauth-proxy
Deleting manifest sha256:64cb1964e1ece3ca7ff609dfe59c974208095200ae79a95517646aef7330f279 from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:435d314d38947a8cc87880957f09ce8dfd6269cbcc4d4474ae1a128ddf93f04d from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:81436241c1042cff2523bfcc73d050700efa107b3fb41c0475ae18b4a3b7b43c from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:22a058039a594a12029865d21061c1cd1e22c7ef23538f52fc2c9671995c689c from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:8abca8585eff035f45616a265500f2ce06ef4e442f13875b3c21211652bf7f7f from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:753aa77878a0081118266cf12fc3715723b623633d5eaafb658d8ff0bcec6bf2 from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:85ce36395a59bc42746f7002fae89e677e6ecaaef4db5c351f4eac57f5d381b3 from repo openshift4/ose-oauth-proxy
Deleting manifest sha256:7edf2fbe72e88de3b48ee40b9c960b5b567c27d1902745f7f6d8a5836d89432b from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:45ed97c7f8252fa3566b3c5f68cc30bd0b3b9182a483f11ed3d32e55658ce27a from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:8f2b1b2b15cab8a10b70a16e0af15dd73f0cf6932b16aac07c872beab229392d from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:9ccdc664571e8253482b1742b3752f8ad97f3a3fe6be3cd8a9daa85d11c10598 from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:83ba24d948341f4b768fbff7212f6109c75b784229d184a2556078e036873025 from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:96810c770f75057b8bba6b78a5ba414c8289315d8af1a387f681ae5e4fb7d6dd from repo openshift4/ose-oauth-proxy
Deleting manifest sha256:90cffb969ef49252a28fc5542eca6ad4dae64d4145a59643342b3409d34250c2 from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:b73fb8e9127df35d8a7ae199320b11d27e0cce6e962f91cfc17293d50e1aca55 from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:7ef542a41916c19fae8c3500afc4b575809d0091001709b8df8ef107df046aa7 from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:4c2a45ac44848e9defb8ac4224ae517df83a3fa3e94349fb4593cb38dfafc585 from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:eadbaa6bb2f6fe639c8ffe5608dd1cd7769fb7d814f2cda92e4d664b0eabd1c3 from repo openshift4/ose-oauth-proxy
Deleting manifest sha256:8968f9ce2e74fdc5d8256c5a5c3f34ae6db8f6f86de0149fd66168168df54015 from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:543dbcf59475f67ec1fb2c29e8c88ad067e35cda75835924719a2619e0348619 from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:c0584e6b83f02d37cd70d1a67dd7dd954c5fb4f387fa9908b01c065801ab5cda from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:bd257297e0fc1269905c4211b4650e5863d13b8700b2a981b2a6c58f216e47a2 from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:a2f2573009880db9e2e21e68c90bbf0d32192774e0bee60d85a9c1117cc096a5 from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:9375a864efbb3011eb4b9e1754b338d8875a0cc70fc46996dec07946d4c9a2ae from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:552036784912510925e9315ea5061bfb4a6f31335fcc162b80177b7da26363ab from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:04c57db2d67b32fa53f021ea003704226517e493b6cc655aba02aab0d23323a1 from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:c2073dba02ece384f46c096cc7d381eada238a44761ff6db9c539a5ecebfbecc from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:99bddf1517cd5c323b34fd4690c59ed3592f812f959a43981df1cc78bed27cc9 from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:c137a9b5a58a7e74526ed6285fe30eafbaaf34cf8f88c76ec607df955cbc9519 from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:a7863257f2bd010979de552d78252195f077b17ff28af140f066102fa0893afb from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:61a164ad711478ac603242338f7b3c55986319479878b092be128581a823598e from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:c6c240f1572a3bad0e5ed98da599ec8cf807bba46a5ad321648e4edd133a16fe from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:b95246b7c3d0f619c92595918f9959d4d4540f492184f40353ecac5788370d3b from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:cd473c718cf65172a4cfa37c8fe9cbe7ab020bfa0cad3793dd9a51b8e6cb2c9e from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:0ca3b10eeed3148fdde0cc79e379e6cf5b66fb8df18ea810cf58838caaf92f70 from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:cfa7ab769658065d18573d619a814e084dc78e5e037d82c4bbcabf91b4da620b from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:b7562f5d22d8860bca1fd0d4dd7610e84153f111aeced03b7c6646495f3ee5a5 from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:66159ce8eadcd6697e653bddcac81a38bb9c060f3fb27fcc8f07a352870a4428 from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:0d0bd7b54472b47a65480f5b5832b773c5c79161eedbc15cbf80a8615f7d7e47 from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:d1b1fc1c4e215ab53266800aff65e6b83278ab9add1a416e0a991f111b8af47b from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:bbe43427c7cbd4007ff673afbae66070c4ed0302d82068ab987a996076d68d79 from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:dcd19739325a15140f8bbfe20f384b404552a382f02d353233470f59af9d5ee9 from repo openshift-logging/elasticsearch6-rhel8
Deleting manifest sha256:ece1828d3b83d7d080c14061017a0954454d6eaa1b64cbbe9eeed51d3fbaaa59 from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:cc9680f70f4d1e1c6be9d98966e01caee9a3ab3523656ce52b4fbc423c39d7fe from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:f7893725ed892239b1896ecfe3e0bb8152a70881e277c0def2df1b5e18f96c56 from repo openshift-logging/logging-curator5-rhel8
Deleting manifest sha256:0fd6cac32b8d10b0eaeecdd38c50df577067bd9ef27f20a8d03dba053e3aab04 from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:f64d3b646c1c0ad13cac02c0e6eab377eccf040c33e896fa5d0c4be90d1f85d4 from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:d4c60531f59953f399c6a2b0f8239128cdc2640f72f2d1e9ac4d6add618f9f01 from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:dda0674e21d5372ca54a504727e065ff2700e2b9d4a2d7c32aa0021ffd317d8c from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:150f56a69ff535982d1af00896a1b0f951cbb75666f9d139811a79b29e86b04b from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:fd73a0644b87fd24ede361dd4f3cc82a1a5cd48235a63c2413779de5a9644cb2 from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:f0bee519bb4f20aa1b7f4a1ca01db0d6a068bbcc0fa07416c242c0acffc83422 from repo openshift-logging/elasticsearch-proxy-rhel8
Deleting manifest sha256:fcc486c24f642b390d7c77765a33c6fc656719e0ab1345f18f186d6c15f7c12c from repo openshift-logging/kibana6-rhel8
Deleting manifest sha256:ff4441d4553634913a48ab282b1baa70cbcaca4bc81d3dd805408304b230f098 from repo openshift4/ose-kube-rbac-proxy
Deleting manifest sha256:19580a0c859f4970c09131b11f1717a417bf0fce987b0d0d0ec14aa0ae41123a from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:2305040b28a6443a794956b3d854abe7e05756228055967f24689f191ef7596f from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:32072064d66d79c1464a4f36bdba832e7ece25ed0c011b6e4a17d132408767aa from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:42f31b5843a05f5813fbd277f6304fe9e3f170fe516b35a0b1f93a0be8d936e1 from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:594b6b4c6224276ee07c5b1953ba71bc8ba1eb0536d244d6cd64c0c0e971fa31 from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:5bf6c8da6f400c6ca0ed3a0294f614f45bdc2477759708723d77d15cfab52e34 from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:a834960d45d27966bb4f866b87014c02b80c463cf0298fecb2d943b51200d2dd from repo openshift-logging/elasticsearch-rhel8-operator
Deleting manifest sha256:c0fb2fe7c385dcac5e5e5cd587ab5ef0b27c6cb821a62a0927a80bc2e9b7c96b from repo openshift-logging/elasticsearch-rhel8-operator
Writing image mapping to oc-mirror-workspace/results-1660664346/mapping.txt
Writing UpdateService manifests to oc-mirror-workspace/results-1660664346
Writing ICSP manifests to oc-mirror-workspace/results-1660664346
~~~

With our imageset run complete we may have noticed one additional item was shown in the output.  Did we see the following:

~~~bash
Adding graph data
~~~

This tells us that along with the images being mirrored the graph data for OSUS was also mirrored into our local registry.  But oc mirror did more for us as well.   If we look in the ICSP manifest directory we will see something else:

~~~bash
$ ls -l oc-mirror-workspace/results-1660664346
total 40
drwxrwxr-x. 2 bschmaus bschmaus     6 Aug 16 10:37 charts
-rwxrwxr-x. 1 bschmaus bschmaus   642 Aug 16 10:39 imageContentSourcePolicy.yaml
-rw-rw-r--. 1 bschmaus bschmaus 29415 Aug 16 10:39 mapping.txt
drwxrwxr-x. 2 bschmaus bschmaus    52 Aug 16 10:37 release-signatures
-rwxrwxr-x. 1 bschmaus bschmaus   351 Aug 16 10:39 updateService.yaml
~~~

There appears to be an updateService.yaml file along with our imageContentSourcePolicy.yaml.   Lets take a look at whats inside:

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

It appears to be a UpdateService custom resource showing where to find the graph data in our local registry.  Its just another example of oc-mirror and its capabilities in action.

Hopefully these example gave a good idea on how the oc mirror plugin works and how it simplifies the mirroring process for disconnected environments.  I personally found it a lot more user friendly then the old style of mirroring content specifically when it comes to dealing with operators.  
