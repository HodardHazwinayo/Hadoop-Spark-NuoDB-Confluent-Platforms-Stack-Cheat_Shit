# NuoDB Insights Monitoring Installation
********************************************
## Install NuoDB Collector - all NuoDB hosts
*******************************************
## Software
********
curl -L https://github.com/nuodb/nuodb-collector/archive/1.1.0.tar.gz -o /data/nuodb/nuodb-collector-v1.1.0-rel.tar.gz
mkdir /data/nuodb/nuodb-collector
tar -xzf /data/nuodb/nuodb-collector-v1.1.0-rel.tar.gz -C /data/nuodb/nuodb-collector

## Installation
*************
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o /data/nuodb/get-pip.py
python /data/nuodb/get-pip.py
pip install pynuodb pynuoadmin six
curl https://dl.influxdata.com/telegraf/releases/telegraf-1.15.2-1.x86_64.rpm -o /data/nuodb/telegraf-1.15.2-1.x86_64.rpm
yum localinstall -y /data/nuodb/telegraf-1.15.2-1.x86_64.rpm

## Configuration:
**************
cp -r /data/nuodb/nuodb-collector/nuodb-collector-1.1.0/nuocd /opt/
cp /data/nuodb/nuodb-collector/nuodb-collector-1.1.0/conf/nuodb.conf /etc/telegraf/telegraf.d
cp /data/nuodb/nuodb-collector/nuodb-collector-1.1.0/conf/outputs.conf /etc/telegraf/telegraf.d/outputs-pdc.conf
sed -i -e 's/INFLUXURL/INFLUXURL_PDC/g' /etc/telegraf/telegraf.d/outputs-pdc.conf
chown -R telegraf.telegraf /etc/telegraf

cat <<EOF >/etc/default/telegraf
PYTHONPATH=/opt/nuodb/drivers
INFLUXURL_PDC=http://hostip:8086
NUOCMD_VERIFY_SERVER=/etc/nuodb/keys/nuoadmin.cert
NUOCMD_CLIENT_KEY=/etc/nuodb/keys/nuocmd.pem
EOF

## Lastly disable the default Telegraf output plugin to avoid unnecessary log errors:
************************************************************************************
sed -i -e 's/\[\[outputs\.influxdb\]\]/#\[\[outputs\.influxdb\]\]/g' /etc/telegraf/telegraf.conf

## To enable and start the telegraf collector:
********************************************
systemctl enable telegraf
systemctl start telegraf