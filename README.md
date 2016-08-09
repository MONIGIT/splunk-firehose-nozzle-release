# splunk-firehose-nozzle-release

## Dev

Local development

* Install & boot [bosh-lite](https://github.com/cloudfoundry/bosh-lite) 
* Add a [recent stemcell](http://bosh.io/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent) to bosh-lite
```
bosh upload stemcell ~/Desktop/bosh/bosh-stemcell-XXXX.X-warden-boshlite-ubuntu-trusty-go_agent.tgz
```
* Get the Splunk bits
```
wget http://download.splunk.com/products/splunk/releases/6.4.2/linux/splunk-6.4.2-00f5bb3fa822-Linux-x86_64.tgz \
    -O ~/Downloads/splunk-6.4.2-00f5bb3fa822-linux-x86_64.tgz
bosh add blob ~/Downloads/splunk-6.4.2-00f5bb3fa822-linux-x86_64.tgz splunk_6.4.2
```
* Get the [Golang bits](https://golang.org/dl/)
```
wget https://storage.googleapis.com/golang/go1.6.3.linux-amd64.tar.gz \
    -O ~/Downloads/go1.6.3.linux-amd64.tar.gz
bosh add blob ~/Downloads/go1.6.3.linux-amd64.tar.gz golang
```
* Generate a deployment manifest
    * Copy `./manifest-generator/examples/properties` to `./my-properties.yml`
    * Customize `./my-properties.yml` to your environment
    * Generate the manifest ``./scripts/generate-bosh-lite-manifest.sh `bosh status --uuid` properties.yml``
* Iterating
```
bosh create release --force
bosh upload release dev_releases/cf-splunk/`ls -rt dev_releases/cf-splunk/ | grep "cf" | tail -n1`
bosh deploy --recreate
```

## Creating a Tile
Checkout the `bosh-release` branch of
[tile-generator](https://github.com/cf-platform-eng/tile-generator)
and make sure `tile-generator/bin` is in `$PATH`

```
bosh create release --force --with-tarball
mv dev_releases/cf-splunk/*.tgz tile/resources/
cd tile
tile build
./tile_custom.sh
```

## jobs

* `splunk-forwarder`: bosh managed HTTP event collector co-located with a universal forwarder
* `splunk-full`: bosh managed search head and indexer. Intended for internal testing only (not 
HA and doesn't persist past rebuilds)
