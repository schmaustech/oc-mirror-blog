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

Now that we have the file create let us go ahead and run the oc mirror command to mirror the contents to our local registry of provisioning.schmaustech.com:5000:

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
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:ce192b0df65d883e34cd96c5d2acfb9a6268a28964fb0142ed1d0b55f8e7b560 3.161KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:217bbef25e6e364298e9b300e1e87d72f418331fe083cd4449802a5798aac322 4.65KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:0630a72f52fd03c3c50ad90ebd7e71d24a3e38c3760e9ec2f84105eaff61405b 5.525KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4e80d22d9377aa6c13076868d997de1dd71dad1117e92169b11961bec39553ee 5.648KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:33ef73131becd5dbc3d8f913659a9d82fc6584f22aba85d3840226f891d8a16a 5.659KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:08bc210159fafe42e9b1bfe3d494f3dd42ba73b03890a050445dc75f28186302 5.668KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:7c39c0af3092ba28874fb4a302397b5413b448c042200f387ec8da5e811171b7 5.671KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:bfb55ea4477ba7630497a5b2eb179ad214571883267634c0c4697de51bcef69a 5.687KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:a3f7fa4c01c84bd5c55cd69bf0a749813e6eb4e17b0df5e344d4727d87465ab8 5.712KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:7c5522133ff006728ca80b42003cf76019cec4311801b73fe218b661bb58e437 5.715KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:51f1f8de7be3bdf89050b4e69e8f42876311556ec1bd83857d5609cd40735c60 5.72KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:fd654516d00a320221a2accf51d4af7d97628ea23a0c5c0a3a70ac62d4ba901e 5.72KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:2cb68ba3a6a2704c8c8b171b643dda06525437744f72cbf9430bb3bb3d06b6cd 5.723KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d1a9e73e12ad162d62471317fb715eaa01cad24145a5cf48345ff7e41cb37d4d 5.724KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:64caea48a4c8e51033f49b44403ef573d1e5263d1c69194c1e4e3ae099109cd8 5.729KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:64c99470a65ec4557db5d067f8edc846488929321faac286cd577fb76f5b67e5 5.73KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:adf0174f08ec538c487ab0a04cac715b330174eb86afa613d967702f7b719356 5.745KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b35aa4a418e6c0ee2d330b0f60a694b7256502a5ebc4f67087823aec00b71ccc 5.745KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b1b58b48b9cb453cb4a2d793d3894ad4d695778a924ccb05488d2e752cf599c1 5.754KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:762b58d25362b9b53b71a0330ebb197d079fd7d5c7556bb20941b96598b7e20e 5.765KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:9fe6cec96704ffdf512ad2755c42ddfd36f2ab2aec3a27bae4cce42a8c480e14 5.779KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:11504734ada557d7019a0821311fcf14f9166c498ddcd1dfc79797f442ddad75 5.781KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c2f2e81292564d421ece8b7fcda30056744b2759dd264621404323f0b40cdc25 5.783KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:7ed20fbe001fde4887abfb01dd9df2ac75b044fd62c3a997462545e57d3d23e6 5.784KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5343024eee853eda0b1e7e45ae92c835c9541f1f78bc4d68a96bd86cf1bbb1ce 5.785KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:de158608fc190de01e24606922760a496eb0630c31bd811e5042c8b8f5585cd9 5.786KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:11a53a7cb13121531bf1e284f37ac41a42b916d27867fa1172598fd1353e2648 5.787KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:afeab4cab1f691f705feb8df6139f7339761a973dbea31edb365c475f38ce1ae 5.787KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:af50c7ad28f68110d7bc1ea156888b51b5dca94fb54ea01ac2e835561b6d936b 5.8KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:0db638342031bb53b1fb60f7b4093a5038807e583e23e6d4bf6875fbf91cfec2 5.803KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:796ea52ce463b5a38ed70c053254d2debdf130fdfe659c3560cd4941176b1228 5.807KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:eb1125ad283ba5a8d933ae842d9900c9aa8ac7646ed6f103ca139ffc36cad31b 5.812KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b8bbeb4473c7d6f88f5e06761d3b4929165b4aedb96bb3da4f7dac25c58022b8 5.814KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:bad0eb15cba4904037f6bd6a0369ebd66263041cc1600f046791f04dc70ca3a1 5.817KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:aa63d7794535e4a91928a52a7ddf21d5aade65d711d680f4bb0d76d5761a3e8a 5.818KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:544dce06261cda03b8de86d98ef83b4213a57a5c9b16a19cb745061c202e9763 5.819KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:94841004a6d146dde6ee237d30e0c6e8c624c6d6cc87344c7b3e6bb18586a7c8 5.82KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:bd0e0cf6d95aaecad060eaf68326421cd6ca8c7c00f7d8e1a46012ba7066cdde 5.82KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f53fe3cab359afcd8df5a64c633f92a07b182a3262806152053b6616c1cb07da 5.82KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:19aad1abc17b0b95f689e1d6973beb7fbda8b42f00f73c570bd81db52f509cfc 5.825KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:06bcdf9e5bffca01d0395f349a5c6fe8522560425b81adca4f6d54b2e6b8e854 5.829KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b5f66630c6b9da38d8d57d7e98659218d88e2d2e64e6d07d044d52eee66e9bff 5.829KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:a222782effea5f5f0c3927c2b18c0b3ff39482c1b1899426b52e7091c714a29e 5.831KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:965345d687008890857918dfbe660f0c9ab9d6ab38441ac5f7f8dcfe03128e4f 5.832KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:78bc8512fb0712d0fe76e5fd8151b57c8c17d00e969eb368989c2717955fcb75 5.837KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:77ddc078f8656bc4f910a5808bb1a94a67f128388e2f0c9c5136d587d6f7f3e5 5.843KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b9a19de2e421ca58db58267ef961c35f4bf7ba6a81ea448a57b9aececa1c474e 5.844KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:84735959c7eb0f9f8228d184a0b94771dc3f55c911b3f09002564effa7ce8596 5.846KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d09961f5a2ae379943c02d6640abd23cdcddddc2ed402ec5dae510a081a05cbf 5.847KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:08b2e6062cc4fb4602e59171b72ba54eda998c6437e7a94864422ffa5b57129c 5.85KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4f8139b71b6d76b1fe0785e33b1b3e15387ed5d4a82c5555a619f99475c915f0 5.85KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:eac92f9ff6eedb20e8542a8e09e5b43d4efdaea0410f6490217f8ebdd0940d85 5.85KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:eb2ef93111f9b27ca04cf5a279f1f24d06ab882287278d7c7f7292a996c1a79d 5.851KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:273bf70a567b509a1cddd33e62fff7c10ca721267b59ff6736bd6f2fdcc0f845 5.857KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:33ba61bd50ced10c3212a864abac33beb786c52feaa0b81005373bcd28eef5e3 5.857KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:db3eb80a1853f40043c27344170f472ee772fde4ca7a7283180849f8e2817a50 5.86KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:15ee3d087c87def26cb8f09ebb7bec8334983cb8236ce2bf0cee11cfe80420d7 5.861KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:6acfe956b6f24b870c683243ac1259733a89a0a3f57635e203e553692a11605b 5.861KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d7e9207f859c7bf217f3bf56363755a317f44593fe529f57877a5ce24c0d0f80 5.861KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:66c3e8e94022ed1a02ec9197196195fdc4272f8e8498947bc3360f5a83a74b4b 5.868KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:ddeb3abc262a550e7f15faa0e2607b83076e07daed1fcfb14392768b6df273ab 5.87KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b20896eb00174f276abc014ae0e4a91a00650ee98ac461c52ff6dac3ecbc8b36 5.873KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d5d10803f3ebd3ef51495518565dedc1c8c3b722a016026c613fc22dba95ea90 5.875KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:1805a5fe38acd3ed4bf2b4dd9227ca0775d757f038ffe9417ea9c080914725c6 5.877KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:18700f0cc87ec0f95ccb957f233d524b32464851d47ef30c8dd9c645dc946a9f 5.877KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c427d41c081539bc3898d3c813e4630f91e400092e58d70a6dee5c703d3fce28 5.877KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:0e5826a234bba0d070b46b5388473312b8200d1d72be7950ee37ac77a7da3272 5.881KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:28ea52b98c63aa5dd899d67bf267a3b7dd623f5a694b97a56793bb12597e2de9 5.883KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:cd8dbba196f0bad509792b9399ec0fe7f58173af3d1df0fa68667bc7d04829a6 5.884KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:6bc90b6758f8e16645e8fc549bb070f1f25f6cf7950508d982364591c9aef67c 5.885KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:7164db7bacdcf032f3bb22c85b9bce773d1cb11e1ecdc515e4002e0d66473f1a 5.885KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:80a9b8de48eab5c1d72ba7f5af4b1068ee87a0eb7cf73c812ddcd47a8f457464 5.885KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:eb5a2ef63600f505b819c3004de322a9933fc1c498c866f38ff08f167ca710c2 5.888KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:86b6940844c3a5fa1c9f474aa83c4c15129bd07c25f7c2121e23c223925bdfc5 5.889KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:38047d0839d5fcb63b5b7fecbc6aa10a716b8e453acfadbfa21960b7c9b0fc7a 5.891KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:eab3844f198112ee9fddd8a979984552e29cbd89f13f06711f87f987030f8cb8 5.891KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f9004cd5d39f008fa2fa7858d04833ec62a34e6709ea1c8218320848583c1e1f 5.893KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5e77e74e95e2dbff030da2f1d1f6d8913893735a609211561fa72896d11d0069 5.896KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:016f1a293e81aa4d019351d2ab6f9f1df45a8d1e48ac57918fca754339c1cecf 5.899KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:da7205cdf9b52f324f49c1b518d0bdfa6738e3229be73f6cf5b9efb3bef23fab 5.899KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:e9ded038ecadd8e14f2c27875dc1561b08f9d0c4450019063cab24ca6a018f6b 5.899KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:39ad8dd030b00d41438db7af977a9b985ae896ad4005c3d9f5bd0bf5285bd013 5.902KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:9c1ec5544983673248f036f074f16e065f3ac437553466502be1aadee328c450 5.905KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:85eb1eba8745c22b36bd85cf97febb02567f13a5c98e5decc38ed726a6167c87 5.909KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:06f2e3dc603da8f016a90a95772c555d5674e41f3ead8b1a1425e106f43d62aa 5.912KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4c750495733e2e996cb2acdcfa96f5aabf7a49b17ff7e73ce92b27719887f3e4 5.914KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c914f67e6a1b4a84ffbf91743d3972b61609b8133fa1f915aea5f9ea9d7ad1e1 5.915KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:45b19353d12703145530a9a7229f2c1ea4168ae42be729f898817bd7b9f10281 5.916KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f8517523838468766fe503f52b6909274a3e96d9779c1b8a6caf01f56c308dc5 5.926KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:3815748a01c39a3beb0dce1b2755ab1038d6c5e5ea8c6c830a71c2ede769a630 5.927KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:8baa835dc1c7e1816a8d4b809b8140810928532aa93d059b0650d1be31f6c620 5.93KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f74a3835778df0df7489a77b7532f4ebbbd449b9930b0795485d21988de84137 5.937KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:12e74538ccea688b6f2b9bab20d680a6409317e23643a91cf640f168f201614c 5.941KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5b47ca152f642cddc142e3004c042701a5fe61d40b3bbf3b4ab95509681fc85c 5.942KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5dfe025f14b7a83836de295e0d46cd83429df113807e93427fc8e0c6300fe10d 5.947KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:8b9cca9cf6bc58b8f0a0539a84baeb4e2fb088d8182acae539f4f4fe6e9d2656 5.947KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:40c3c600333c5cd6d1e9b6a932f21127ccd33167c5dc868a160f5d1d18a862e1 5.952KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:a4db761609719aa02bfd6178c8f9e1fa469ea11d7f82fdb06ca77c5b1ec2a992 5.954KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:24d857cf14355de875d6fcda04f2a212687cbb72f4e9fb01fe9ec8c434a78237 5.955KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:9c354781f60392bb6dbe50080b1367089819600bca4c331297580a22cac049bc 5.96KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c9fcc638a558f5e7977c7f2dd7665eb94e1187d9e0d0dd58ab0861c700226402 5.961KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:95cdbb319c5dc46cd9e3a65dcbb336bffe8a4688185265ef4b2e4e92f3a534a4 5.965KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:1d3b81473a678baf01f66f2d7ad2e31406bfbeb6f2d0c29a2889eb0282290fa5 5.967KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:57fb66a420fe8d8a099f95770a29b3eb1fe1cc28ec602351ba525cf3674d107e 5.967KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b8a78f00c0b6feb0eda55b9eda465edf1027683e36029d3e46ec35f29b4391ba 5.974KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:ed8020909d3f158fe4dd9e2a87e43e0f33e72e69e3c7d4a67e3362b87196ec6b 5.975KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d303a4056e5e1f1c17c39544b1ded733ed5a7fe32cb352c7992914944d91d37e 5.98KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:ddf469856f8ebfd0b94aff00507d7a60ce04d2221a6045f81cf3dae6b47e31b1 5.984KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:fc5371cc82c67d60940c39b81514a3f2a00c538d54e2bc530088f2613fa53938 5.985KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:49feba1a85af57acbe9b85ddb9ea6ab66fbd1b5b97fb9642f8b98c23f8a5a2d6 5.988KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:145b8a079626c744f86d79e73f5f497ca1daa428b236de33c1d94e99d16cd0c8 5.989KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4f6f4640e5833081d7f64e40ca48ef8602c841c4327e3256351b2937f20df933 5.99KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5e95f4dd62d64ff8f53ce6b4da1f2b2cb03b9d7495883486ad34da8b6fce6d21 5.994KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4c66dac47a4507b5b1daef4d1eb3f48f40728ff80c354f314a73ccf4898ed2bf 5.995KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:70465a631086136850a8267207990baa86e8a71eba56f99faca29cd4e49ffbfd 6.002KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:0d23744ab5f1413152b34c148d89e925ae2ab2d2abf387a6044deece7b6b0eb5 6.013KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:71fd7a9f7c91b4b9ab3f71c0b5311c24393534fbe4c49a4f78074e60f6b42a3a 6.044KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d3d1c1fcbb3a5e7da67ec60a3a5102e5d60e8a0904baca7d9364189b542bf304 6.051KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:55425c0237e89acd2523f9a24f3fe21c9aa7df00ce5f490bc722794b6e2e10ee 6.097KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:9efb1f8bb8ab8197515e03b151f90d9828726c9c53564497b25a28bdd7a9753d 6.103KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:abd5ea3a48e346ec0480185c10c1c747300b38cad4b98e52205324375ff838a1 6.105KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:428a0444765588a83dfbfe9438827ecfd5fa66a554182dcc29533f97d9d69311 6.106KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5f5543adb768eff6c8f032d9389e83646fb262d9d33edab4d6314c3c2c665032 6.123KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:9812569baa8e732172abd04145e7112ab5e7a184a73b275955a76c6019280ec4 6.125KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:1c76cc43112ac5b3f5760efdc9ae742e4efd9d4f99d9b50535c614642ca46a00 6.128KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f8c56db533e8f0757fc88d2a8b93b4a20a78fbbb0b47ccc251e5249ce6d92f41 6.146KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d6e3e8c6a67adb84297e2b898b7cf748f1e6f523f5a7d8c1860a5ba816821d8c 6.156KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:bc8fbc6cfc5c904a48c69b1c8939312ff8edb2c57f3a79dfa08b5b0ee7b2b2c0 6.192KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d9498e3e713636b7cc58a9ce7edc13c3ea523f45b32fdae887b60fdfa02786f6 6.213KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:64f6876665da38daa306ace9806cc9f9312550a4fd16a184832871c8cea8e5e2 6.374KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:3392b80b544814d322236693b9cae0e93d4e913aba4f2e54f802b9303e131c85 6.416KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:6d8a79878867d06780c37cbf35b5405fedb09f36fa7c985bce08f8fa62fb0615 6.51KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:102647be7c2718461d24ec3aae1219e226a2fc5a02b54cd5377d076d5b3775f4 6.546KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:0af72d08730faf9431afcafc14e5ffcbdc17a9e69e6b8fb979955696e940f8ad 6.563KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5da96ca6fbceea6c134b4e6c7b1e0a12e071c9941e13e2c3763c37a75c628d71 6.567KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:fd7f8e60467eb13eb190c51dd3d86966a3f901b94d96bcf8e92e2a0e9082d915 6.582KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:1ff163ad55e52a5e4d3680d18c93aad14d89d02fdf606a5460902f0ab8111d2d 6.603KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:cff77b5e4c7658fd7e4b03091207e5c5902e88aaacb543ff42a5b14787c03d37 6.643KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:44b2b73833ff67e93c98546f0a7ce0b7ad465d352ec4e4ef4eea8ea63c75c877 6.696KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d03ecd40f907251d2ccc510ad29b06b4b2fe875e5278ffdb1eee9a4647a907b8 7.021KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f9afb4eabe29109b8b9ced665ff300631969f7c20c89923c57dcb3fd7175d6d2 7.102KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:04bd9b0f6785330cffc850596a8293e6255394fcefbc4b7d36b734bba7b366a5 12.27KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:3a6e00b8ea27fcd20d489ee3a3141e9c804510b6dc37851462bace5aef21b586 374.5KiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:8040e99619ff2ad3b90dd7097213255f6460acd582a9d65f32a5388f23ba5189 2.716MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:e63a491468ee6dfb49dac24cd7d4ee23f79c42c46a1f227ab3ca12008b879f05 3.44MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:961d94212659ea340ae5a8de1a33c2b9b9739b01ae398818fe0d61bb9a149433 4.802MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:28d47ea7720b0dd29867de093fb7fad50812c8f242743dffef7856e5a58e550a 5.034MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:da471f9ac607dd48718ba466684786676a36a848109b6e590248516ca4a2961b 5.915MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:a048f6705d3b7c5b982e961681217ca551688ecad9115486e6d97f8629269b3f 6.042MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:af733ea2d56b8e8789ecca0727a73b570e40a8c4ad20dd583fafa93375053a75 6.993MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:405fd37d00114f374ca6704f372a72fd55eaa56ee564d9db36ad637d3d8407ea 7.585MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:468832300b755cc7ca9fa58d8f60af9b189c8dffb4f0b4e6609dbb7a94fe0ebf 8.893MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d3c48a7c60a44c138beb6caf24c081f4c24e5698b10d8024a18e9a912cbad7b1 9.185MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:21d09198a26e9fbdce25ee36b2163783f6a131faf8841053fae55541ac569afa 9.22MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:8bf8bc78d689926ba04c3686d18c5fc1677a8fe135e0a2ce77640d67faba6857 9.386MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:0efdf0a242aef64b44ac17b5dab9ac19f6dd66eed0e27622bcda6666836ed32c 9.476MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:99febc066ce59eeee9a0af22e978211306d1d845e5e341fdb681fc2b9276ad15 9.534MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f43eacb86576cfc775abc5d058b804eb2040e3e6dd57d232dd9b0af2c9c64d31 10.91MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:76830d3e13e55784d4e5cfb1a4a11b78047d55816984e927fea0be4952c9cb53 11.45MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:1510e1321086e5f3b5b1cbc590cfaf8fbf280c3419327baf3a13e37efeceb1f4 11.85MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:487bb488b5f1a0b6a1f10ae974c1f0a41cfa9e2e5c48e77ebc690e19aecdb5ec 13.21MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:6810d0b32afa026e527f754ab376a5a6f8b6cf6ee34233e2dbdb4ddd71a62f6c 13.44MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:97231ef0121ebd6dee8221a8a6bf6a35214931b90b84d060d468da406f8c8c0b 13.57MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:64b33fcb67935dc72e3655d43ed47521271c1769db6543d60393961a44db6ec4 14.15MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:70f8470ac6953d5a8fab1c1269c8bcf159e37cca2d3969ec71473adfe4b568d4 14.61MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:24e8d2e218b03efbd1c2548e6c8c7fc5a216edb5f2fe7e6e83a49fca53d1018a 14.83MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:fd233661273c64fee89e4fd343231748352d96ba03197320fb173e0416265dec 14.86MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:96a8fc5ce7b43f667984457b889a915a0c35b9096735d7774541cf949a913fee 16.12MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:804abaa69f500750187b402000f4334a90dfe7be4b71c56c1150516d75a9fb16 17.16MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:10e9ae65b69ca820edc5a7f7b0f45c692ff247cc78c7a7890c5033abc048f629 17.29MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:3b4ace74afa170ecebb0dc6116beae7b9476c72751a1d07d3dde17cd53f7bc4c 17.44MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:0d5a05ee7e100781f2cac5fbfae2169912b43426f21cc4fde6655540df3f91fb 17.87MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:54b906177a4d2b0618aedc9a81602136205bb4c9d7d9095b4837aefea18b795c 18.02MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:ccfc9d78a4fdf29533d57e550fb6a4453c74f26e6aa2ba0e7d046f2b01cdd2f2 18.1MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:e82e7d95c2f4ed17c1646f52f41fabebfc29a9eaefda14e1c9a1e3550c393ed0 18.18MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:0e39912dd5610986643adc8ca48fd4e1a397d6b9150ba311df28f5be0ed520fb 18.63MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:a2720d25185cc14165349ec1e30fab1023a419ef28367131eee8f78e77f13711 18.92MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:af9e7f82057be11519f70d1c772eba2b53f065e6e3074693b7ae7636870276b3 19.7MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5c344f65db64fcbb2aed622e596acb8866a03890b41aceabc2bd1b2795a5a2cf 19.89MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:76834f57a119f4d370a120bc54ad376d2e846cbb1458b4fafd7981585717f895 19.99MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:88b11fc9fe314375a4fe59d4cd9a9193d08e3b7bb61782dfa4180680ebef1c46 20.09MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d5f9207186fb5e4cc1f05052086b3627afc4bdc9de13dd10990456fb5a85ff2f 21.1MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:6df6462667e06155de3c18d25b7ece40cba6e033d7669182f1d768141d24489a 21.11MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c67baba1e9cbd1d8eeb0384784480534fe1ba2b96eca6d3d28ebbe18e50a0e56 21.32MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:e0689321defa2640dae332b26eacfab33fbc58e049c55cf019fd292ddad7f667 21.33MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:71bcd49f86cd22699a4b211757cb6f80bbc0e818857724dd706368fe0b59ace6 21.6MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:3c1274a35216f8237b6e8e7e2b4d9d67cb2e74d36e65a2751d5bfe4ffad1afe7 22.67MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d723cbc52baae92894f0cf7e151303129c074b04a37210f4e85edd8c29538afe 24.65MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:2506b5459443372cb9e84376dc89aca56281bbf94f788e26ae717e5d8838dd3d 24.7MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:2247ef326cd6c317992818df6edeb9bcd4dc938e8b587885dcb35e40e222fcb2 24.78MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:70c818a0cf5c5bc95d429d6410492ad9b5d7aab577ae4758be9ba6937b83117c 24.93MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c2032b437f3c7ddfd6772a1dd6b1c61c9e5112f036397c440857a67b6410f7c2 25.05MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:bd856d4e2df814456905a7addcdb073c8e6b1168fed3771d51ab8aa7008c0b38 25.29MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:dea9e8cef0a45950376056981f29edc990600bb6ee823d7575f93880abdc6802 25.35MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d55b5908424f75afade301b39b04da9380352d16942f02bcdb48a226430a0fe4 25.39MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f5591c88a126680a5459344f6f2e67713dc034cb96a58e6e0b11dd319f69c15e 25.62MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:a6effd552debbb9b8f8d3ca0c9327c5c96f6e5f89b17c58ac5487e4f88ffae06 25.81MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:47fcbd58764cbacb47957b17b6eda0407b23aba7fa416fae8b2598c4e98be2ba 25.98MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:1131776b7cc0daf252bbcf3f51e79ae2972e3542f8b29ec3ef88bd967d9ae12f 26.04MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:2ca1cf936896af116a210e77e6f498c38ca1c87a37fa7356edf217c823b5f996 27.21MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:0988f9bb863a676036d4daf95023d0874f5a596be3597fd609fce3c41e0c50f4 27.26MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:ae7747797362fa33af97c562b481038bf06796fcad589f216d1e72dd24d4e3b0 28.25MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:1034c6f35c4eeb97642b5077d2710b80764b2d97810e1b709e6455bb8e164219 28.26MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f38f48ea5fe454322983465a54c5f20afe077092a11098e3fac3afbe88b2d54a 28.44MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:7c7945ce90493a58f102c766b284a8d127ce1c92d5acd0834deda4830a2c69f6 28.83MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:eeafc8c7f83959bb1f80b0bd51f4854602d5e5ec39cf0b4cc4bab77b78b93184 29.15MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d0dcc01a5b3643463e486c160e23fe07c87c400dbf0956cf1970bc2a78d66b9f 29.3MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:8f17910097341fabcd49222bb30a5dccc971a6f8213ee59f77e3f706fae500ea 29.56MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:7effbb56ea6ac2de02e51cc725b5db0aee8ac7c9e731c590b77d471f5d6b05ca 29.56MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d5cf8b84525c9e9f9882d20fee8279ac7bcb095b4a20bfcf7645568bacd4bdbd 29.59MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5598bb0e774186f39433724abfbf054789ec61b1c87cab5cecbc84b2580be0d4 29.61MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:dff4b01fa9d2f8bcdf80e406c77b4fee60068046c4ab75702cf2112973d4728e 29.61MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:494ae1847e4a4b92194f4d671452abf443971e1e7042cf36d835f896a60476cd 29.63MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c2b12b53ad82ad320f26ad52dcfa836d5de9e7aaef2e0d4ceeb46005d54298e4 29.64MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:84bda9df689df65c1ec8cd37c8ce13c8639ad80da66116e17170d2d0f5ca8e8c 29.68MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:5788b7adfcea841704d4535e19a3993a6e69163cc15d74843b195965a90394ab 29.71MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:15f3fa57e65d16ed976bea80fc7c8aed0e20f8af3069af85a36fee5cd0dbe8ae 29.78MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4879a344e5603fe8e4431825a448491bb7b7e4a7f39d09fdb15ecbc813e7183a 29.84MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:3091d852b55ed1d9ad20fcd602e71a12061270e29e3ee02e9c5d0f1fa435d738 29.97MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:7f7255527d260b403875e8180cff4ca9f09f893c42eaf2ed65cf09a7ef22cd2c 30.19MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:28a02fd1a277f9b5339f958c69fc2c6fd87d38201d8dc01e678bd27b6dfa0b3c 30.21MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c1211fac6ad2fc86066bfc3294ef23c957ca8e37953dc4c3c07fb112a153d6a8 30.25MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:122833c0cdcdfa996f53043709a8388d5c59a436ce6036eb266fe6de76bbdaea 30.87MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:44b174cbaec38dbeb1b584e1e859dd9fe3121200bd2e4e986d8445e45390d7d5 31.12MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:e45b640651ec29810b1b6c80bc5fbe41ca627257a9b875bdddfe196ae21226d5 31.35MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:9dca186a78952d3ba3f9a164d41ca3fbb13006c266744eaae5315351aded142a 31.82MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:e3b97e7499e547573709b41ebe392718bd8a5d9720e5bef46ced707e23054b8a 31.87MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c054ab6c0de2e5ae8a76236b3129ab8ca2b2c52164c46b6b78dc785c5586d9bc 32.52MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:75cf9943196266b8ca3decb117fc15d028188f8b06394bed9fff8a29b602c4eb 32.77MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:2c2701d400bba41b506ca073ed4c31f05ede23ff6c0b593be732cab74995617d 33.48MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:7e9557b6b185c2f7f7742ca4bac4c683f3964cdbebb80ad9bc09e912c02523ce 34.6MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:8c4766afd687f820e1022f4fcbda3bb80aafe25dd2dcd602950744cf658174fd 34.89MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:dd921d73823c8ffe10faac26ac98a806000032fc0ca2b792dec1d00c9d9390d8 37.69MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:e279974983f64e83d636f72df3fb32ef3baeeba96e09357581426c332ce2446f 38.36MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d00febf470ea1ec9ea912dc293a7eb8a9db93e5144e901d39002eec946f3799e 38.55MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:949342418efc491e9e1d3836dd5a8c4ea8bfe3804de9d13bf5a919a8b0d9b5da 38.72MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:52b6d912784a850bbeef9b296cf2ecd3e973cd930ae7576c2ab01af91e44085d 39.11MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:86cb77f62affe2b91d2ddff91410d783bebc062ebb0700b2f58a35b13209d069 39.35MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:9e3d58e52d931408991ea9dc68a24d3bbf5588ac2aef3b394877f2c3f30f83fa 40.88MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d18ddc13857eb2c8629afa84910a91e31925070b2873a886b621acd0305a3a5c 41.15MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:9347df20b6ae99c198e8a8bcb3bb2927237c75f8a21d4ab6279011152b8607d2 41.51MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:3fb5a8b38a1d2e950a57608106bedb9e0f43c84b03c5ae2bf979fbe6546934a0 43.34MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:9b405cb9eaf61b7c6f0edd309502994c027362a937b4cdfc026bb7221cdbebc6 44.97MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f6cb5e01b9160f3baa5821a57b2a95e542e46a04a3e0fa426764bc20734686d6 45MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c95fd2c6c33924ad9356c54d5e76d751d42e513f7e5a55c060beb881485d9367 45.86MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:55006bb1219d58b16f56b61022987eabdc0c7ed3c58668dc777b02f75594cff8 47.29MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b3f9c609e51e53e8c8ff67e48c5dea2e91a07fd95ff2a28ec0f074c5c24eadf3 49.21MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:74ce203b8fc1cd42ba1ffa3d96596701be3e274745562bdcc5552f10abeb3889 50.28MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:697b43e78cbbb6d6c21909ee564810d59ea9120ee704bf3dfe98c54ac44cf9ac 55.48MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:f2c0dd9442146750ad68a5c6dadc58f57a5c33ed3092e47ffdac96ba4c720fbb 60.97MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b332c9645cde8561399e4187dddb72c4c5afc9ff8e4a18bba42c5ea429d241a0 61.78MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:e45eeb718043804cd10f0049223e076844484edbf045db7ffb37c9ac523a9943 68.26MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:eac1b95df832dc9f172fd1f07e7cb50c1929b118a4249ddd02c6318a677b506a 79.44MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:ac56bdc7f9934acede05653e9e01e73e961c31818b522c0732ad35350bb3a82b 81.67MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:fa4e8e96f9912234c4251c398716789a2b4c507cc35bbe2c05faf2478548af51 86.39MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:3bc2b1a1d6ad14c37395b0ecff7597e086cd82aab0da308bf81166f00e96e066 86.48MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c4d797b450eea478878adc15410a4499670377c6c6b940dfbf7168e9404b0bcb 90.54MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:21cc75ba870c93c3f211ca8d7d4a4feae1299a85e851370a048da0769b00ba93 91.16MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:1659aa99fadee664637ab731d9799a558c578b9662c57bf865f8773d3f84c295 91.57MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:0eee8ab8157f509c4d7234cf7f27481e1a089c81ed860f678cf0ba6464ee1f1d 92.95MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:cac26e82c2fc50c1a4ef9b7d94bd1c9a5f87a5aefcb8416dda0f7dd8acd3a956 93.69MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:d6c329dfb7dfd7b4b4b92a4e06403b8026893c472691b505b031859a801f0760 98.93MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:c3de55fc203c511e333741faaee84b6517805d5c27c13932fecef228e51bbe66 103.8MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:ff3c954207c8bbb1ccb4960c24debfc4d86b57cf88dfe1bf04f6e57fd01b9e05 105.2MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b69a79a83f8d53971681286e098a77439424457a3ef4a37c68d03df086e4e38e 108.3MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:802b0aadb7eda0706c515d197469be532f5f01df44b468c7892a7f1e5e9ea0a3 112.4MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:fe5dc203b1d8cbc03e537a6927ba2bdd90ccabb2605396e93eb4bd63cf693ece 118.1MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:e4c859ae5a10f95d281d436cef542d499f725808a06a36581af67cd53aadca8c 120.7MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:bc859789b7d44f07fe0beb389effbf1bac3f9a6bc6f00bb5af3dabcaa5773089 124.4MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4f57d0b5804e7850b608b24b21d0a8ec6fd6a78e7db02b5eb3a3974f7f4760b9 129.6MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:16eea94743037d8c322a890d0cd529d083b28ff4f266e3d237962dcbdda700f1 130MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:eb5e55c4ee159cd075c2a983e0d8f2eaec5a26937cb96198afab484d08514ce2 132.6MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:1455608b65152f6cb87418862117776ee5bc6433a5bf3f529391997fdabf976b 138.1MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4c4b1b44eece5abd517ef2484e063965fe69a307cff5d71e206fbe2b267bcdf7 151MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:181ff758e9d7e038c3778b2f6c287055af01a6dc9b5cd5f1895dfdf4dda0151e 152.6MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:6a6e6defe8c5ac973230a8b0675825b08b1f4a0433ee0ab63fbc011ed96d9085 186.4MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:fb9a7c404864ab3cd0d646b95c45595801023671074fa089b5ffa4e97da76047 254.4MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:a2d54da2eff27f0ca47a9f1974616e07c6439965bb1463c1ed1349aa80b0b537 275.1MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:89219d8ed23382a9a7e509334773168d5239ba94ba65ce6ce0b11263ca3cddf9 399.5MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:b230f550b80daed3062f18bfc5d3096d950e92cbbbbd4400857f911fb67a8694 422.2MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:4c1b6ddc54a209e07632e6a2dedbb4f25d4eb3b6f3597f410d7ea97c71256f5c 431.1MiB
      quay.io/openshift-release-dev/ocp-v4.0-art-dev sha256:98b97d2851af2ea0a6cce7907eaf336ca293d3f4b6fde828af15a3ac76e5d9f1 435.8MiB
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

With the operator imageset file created we can now mirror the operator:

~~~bash

~~~

### Prefer Direct Update Paths and Skip Intermediate Releases

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
