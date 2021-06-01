sudo su

docker pull cityofzion/neo-privatenet
docker run --rm -d --name neo-privatenet -p 20333-20336:20333-20336/tcp -p 30333-30336:30333-30336/tcp cityofzion/neo-privatenet
docker exec -it neo-privatenet /bin/bash

apt update
apt install nano
apt upgrade

git clone https://github.com/Fando-TE/weather-dapp.git

### Merubah Kodingan Smartcontract

cd weather-dapp/smartcontract/
nano weather-dapp.py
cd /neo-python/

### Jalankan Neo-CLI
np-prompt -v -p

### Mengaktifkan Log
config sc-events on
config sc-debug-notify on
config vm-log on

### Buat wallet baru
wallet create test

### Open salah satu wallet yang tersedia
wallet open neo-privnet.sample.wallet
atau
wallet open neo-privnet.wallet

### Kirim gas dan neo ke wallet test (opsional)
==wallet send neo {alamat wallet fando} 10000000 --fee=0.1==
wallet send neo AJ8UToqm1j1mubxgJxeivFrhui91eUF2eM 10000000 --fee=0.1

==wallet send gas {alamat wallet fando} 1000 --fee=0.1==
wallet send gas AJ8UToqm1j1mubxgJxeivFrhui91eUF2eM 1000 --fee=0.1

### Lakukan Klaim untuk mendapatkan token gas
wallet claim all

### Build Smartcontract weather daap menjadi format .avm
sc build weather-dapp/smartcontract/weather-dapp.py

### Deploy smartcontract weather daap
sc deploy weather-dapp/smartcontract/weather-dapp.avm True False False 0710 05 --fee=0.1

## Tipe Data
Signature = 00
Boolean = 01
Integer = 02
Hash160 = 03
Hash256 = 04
ByteArray = 05
PublicKey = 06
String = 07
Array = 10
InteropInterface = f0
Void = ff

## !NOTE! Current versions of neo-python/neo-boa does not calculate fees correctly,
## deploy with an extra network fee as contract is > 1024 bytes (--fee=0.1, see "Importing")

### Function Smartcontract
deploy [dapp_name, oracle, time_margin, min_time, max_time]
updateName [new_name]
updateOracle [new_oracle]
updateTimeLimits [time_variable, value]
agreement [agreement_key, customer, insurer, location, timestamp, utc_offset, amount, premium, dapp_name, fee]
resultNotice [agreement_key, weather_param, wind_speed , wave_height, wave_period, cloudCover, oracle_cost]
claim [agreement_key]
transfer [sender, receiver, amount]
refundAll [agreement_key]
deleteAgreement [agreement_key]
getDataByNumber [agreement_key]


### Testing Smartcontract
neo> sc build_run sunny-dapp/smartcontract/sunny_dapp.py True False False 0710 05 deploy ['weather',b'#\xba\'\x03\xc52\xe8\xd6\xe5"\xdc2 39\xdc\xd8\xee\xe9',1,5,864000,0.1]
neo> sc build_run sunny-dapp/smartcontract/sunny_dapp.py True False False 0710 05 agreement ['ID1',b'\x01\x1c\xaau\xb1\xba\xdc\xa9\xd9\xbf&\xb3\xc4\xbc\x99A\x8f\xc6w\x89',b'#\xba\'\x03\xc52c\xe8\xd6\xe5"\xdc2 39\xdc\xd8\xee\xe9','kupang',1622525500,0,1000,10000,'weather',1]
neo> sc build_run sunny-dapp/smartcontract/sunny_dapp.py True False False 0710 05 resultNotice ['ID1',49,51,4,4,79,1]
neo> sc build_run sunny-dapp/smartcontract/sunny_dapp.py True False False 0710 05 claim ['ID1']
neo> sc build_run sunny-dapp/smartcontract/sunny_dapp.py True False False 0710 05 transfer [b'#\xba\'\x03\xc52c\xe8\xd6\xe5"\xdc2 39\xdc\xd8\xee\xe9',b'#\xba\'\x03\xc52\xe8\xd6\xe5"\xdc2 39\xdc\xd8\xee\xe9',100]
neo> sc build_run sunny-dapp/smartcontract/sunny_dapp.py True False False 0710 05 refundAll ['ID1'] 
neo> sc build_run sunny-dapp/smartcontract/sunny_dapp.py True False False 0710 05 deleteAgreement ['ID1']

### Invoke Smartcontract:
neo> sc invoke 0x787177654e549a1b8bf3f6dcacbfec3b006a5286 deploy ['weather',b'#\xba\'\x03\xc52\xe8\xd6\xe5"\xdc2 39\xdc\xd8\xee\xe9',1,5,864000,0.1] --fee=0.1
neo> sc invoke 0x787177654e549a1b8bf3f6dcacbfec3b006a5286 agreement ['ID1',b'\x01\x1c\xaau\xb1\xba\xdc\xa9\xd9\xbf&\xb3\xc4\xbc\x99A\x8f\xc6w\x89',b'#\xba\'\x03\xc52c\xe8\xd6\xe5"\xdc2 39\xdc\xd8\xee\xe9','kupang',1622525500,0,1000,10000,'weather',1] --fee=0.1
neo> sc invoke 0x787177654e549a1b8bf3f6dcacbfec3b006a5286 resultNotice ['ID1',49,51,4,4,79,1] --fee=0.1
neo> sc invoke 0x787177654e549a1b8bf3f6dcacbfec3b006a5286 claim ['ID1']
neo> sc invoke 0x787177654e549a1b8bf3f6dcacbfec3b006a5286 transfer [b'#\xba\'\x03\xc52c\xe8\xd6\xe5"\xdc2 39\xdc\xd8\xee\xe9',b'#\xba\'\x03\xc52\xe8\xd6\xe5"\xdc2 39\xdc\xd8\xee\xe9',100] --fee=0.1
neo> sc invoke 0x787177654e549a1b8bf3f6dcacbfec3b006a5286 refundAll ['ID1'] 
neo> sc invoke 0x787177654e549a1b8bf3f6dcacbfec3b006a5286 deleteAgreement ['ID1']
