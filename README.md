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
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:d851aca55b090483dcda2af05dbf048848399647d96dd3d4089f92d35f827db7 1.753KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:659cc5a28850a9cec8081d759126964381089684a3533338234de0be8a3ab46c 4.409KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:136c0e9eb53faf9ae0ed499dd6b6069a965f91131db2818b7ce9a93fb74e197f 4.411KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:38fe0f9483180dadc659633269a95f2117b9332826339d6cece457e00d8cb2f0 4.411KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:ceff175efb86e5464d7160cba19cca6dc59a801dd847b1b069c99f2aad7947dd 4.411KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:22832722bba973f119ea040fb521ce37fd3ea16fc444fb56bbe255550986d95f 4.413KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:28a2293e605e635cb6ef0d09e2347b75050509ebd7c75b87f6847e5c43a8e476 4.413KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:e77e79c4841ecca394a38a26286439448054493c8cf595eadd563814c4ae087a 4.413KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:08dc47fac7e6d5f40450a5ecf75f78a8833e604c83567ba9860a8bc153b86962 4.424KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:b5da01f29c6a181cdafc7a28e33ed57f0e3b13d1913450d25d72976f2d1c2907 4.424KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:e0b5dccf461df99fcb9bc11cae4dcd95b1c60c91cb1e4b17bac8562e4ec819ce 4.424KiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:01b3f5c9eb1b336aa106b3d114a9f8b2a5a3be18a79b4379e6398863d926d45e 15.59MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:57cb0cfdadd782c86e4d5cf07335fb64154b959c0d8900496be3735ad2e85138 18.15MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:6c425223dd97cc703b8fb2fa0f8904fca09eec06f048ab7dc34ed1e818cf8184 18.15MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:885b6d3571ee46af651aea440f9d599e088df7400c0151f4ecf73076d40d4b19 18.18MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:71c87f12ca0e35fd76a6f713512e8c85f660683abab8c1fbf3806ee34920c46c 18.42MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:09cf259d81dce3fa62b7ea83a617358370be170970d0632c2f408f63119d6d8f 18.43MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:85709ddc9cfc3cec7043cd31ebfa53f97f705c50e841e051c6b65df7a8fa680e 19.52MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:9a004564f0149b5a45c0f5e118f5a27174a93f245695956b87bfdd33a22a0b7d 19.52MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:bf91b897e3a40da210ed4c96dc8cc3c4f073c68b99f3921a32e7b2966430e80b 19.72MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:71c93b2a36fbe0bdfc96b6cec8a9da250fea7d28a66fd41fc6db5851e2b59c49 19.72MiB
      registry.redhat.io/openshift-logging/elasticsearch-proxy-rhel8 sha256:744db8e918c5c882f601488dcdbe3c4e9fa01913fe57119975eb5b16a18f6250 71.97MiB
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

~~~bash

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

~~~bash

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

Hopefully these example gave a good idea on how the oc mirror plugin works and how it simplifies the mirroring process for disconnected environments.  I personally found it a lot more user friendly then the old style of mirroring content specifically when it comes to dealing with operators.  
