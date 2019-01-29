---
title: Služba metadat Azure Instance | Dokumentace Microsoftu
description: Rozhraní rESTful a získat informace o virtuálních počítačích s Linuxem compute, network a nadcházející údržbě události.
services: virtual-machines-linux
documentationcenter: ''
author: harijayms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: 772070b73f1d2dcf83d15283f6be70ab4fef148b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155661"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata service


Služba Azure Instance Metadata poskytuje informace o spuštěných instancí virtuálních počítačů, které lze použít ke správě nebo konfiguraci virtuálních počítačů.
To zahrnuje informace, například SKU, konfigurace sítě a nadcházející údržbě události. Další informace o jaký druh informací je k dispozici, najdete v části [metadat kategorie](#instance-metadata-data-categories).

Služba Azure Instance Metadata je přístupné pro virtuální počítače IaaS vytvořených prostřednictvím koncového bodu REST [Azure Resource Manageru](https://docs.microsoft.com/rest/api/resources/). Koncový bod je k dispozici na dobře známé nesměrovatelných adres IP (`169.254.169.254`), který je přístupný pouze z v rámci virtuálního počítače.

> [!IMPORTANT]
> Tato služba je **obecně k dispozici** v oblastech Azure.  Pravidelně obdrží aktualizace ke zveřejnění nové informace o instancích virtuálních počítačů. Tato stránka zobrazuje aktuální [kategorie dat](#instance-metadata-data-categories) k dispozici.

## <a name="service-availability"></a>Dostupnost služeb
Služba je k dispozici v obecně dostupné oblasti Azure. Ne všechny verze rozhraní API může být k dispozici ve všech oblastech Azure.

Oblasti                                        | Dostupnost?                                 | Podporované verze
-----------------------------------------------|-----------------------------------------------|-----------------
[Všechny obecně dostupné globálními oblastmi Azure](https://azure.microsoft.com/regions/)     | Obecná dostupnost   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Obecná dostupnost | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01
[Azure China](https://www.azure.cn/)                                                           | Obecná dostupnost | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | Obecná dostupnost | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01

V této tabulce aktualizuje, když jsou k dispozici aktualizace služby, a nebo nové podporované verze jsou k dispozici

Vyzkoušet služba Instance Metadata, vytvoření virtuálního počítače z [Azure Resource Manageru](https://docs.microsoft.com/rest/api/resources/) nebo [webu Azure portal](http://portal.azure.com) v oblastech nahoře a postupujte podle níže uvedených příkladech.

## <a name="usage"></a>Využití

### <a name="versioning"></a>Správa verzí
Služba Instance Metadata se systémovou správou verzí. Verze jsou povinné a je aktuální verze na Global Azure `2018-04-02`. Aktuální podporované verze jsou (2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02)

> [!NOTE] 
> Předchozí verze preview naplánované události, které jsou podporované jako verze api-version {nejnovější}. Tento formát se už nepodporuje a v budoucnu budou zastaralé.

Jak při přidávání novějších verzí, starší verze i nadále přístupný z důvodu kompatibility Pokud skripty mají závislosti na konkrétních datových formátů. Předchozí verze preview (2017-03-01) ale nemusí být k dispozici, jakmile je služba obecně dostupná.

### <a name="using-headers"></a>Používání hlaviček
Když dotazujete služba Instance Metadata, je nutné zadat hlavičku `Metadata: true` zajistit požadavek nebyl přesměrován náhodně.

### <a name="retrieving-metadata"></a>Načítání metadat

Instance metadata jsou k dispozici pro spouštění virtuálních počítačů vytvořit a spravovat pomocí [Azure Resource Manageru](https://docs.microsoft.com/rest/api/resources/). Všechny kategorie dat pro instanci virtuálního počítače pomocí následující žádosti o přístup:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> Všechny instance metadatové dotazy jsou malá a velká písmena.

### <a name="data-output"></a>Výstup dat
Ve výchozím nastavení, vrátí služba Instance Metadata data ve formátu JSON (`Content-Type: application/json`). Ale různých rozhraní API vrátí data v různých formátech, pokud o to požádá.
V následující tabulce je odkaz jiné formáty dat, které můžou podporovat rozhraní API.

Rozhraní API | Výchozí formát dat | Další formáty
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | žádný

Pro přístup k odpovědi jiné než výchozí formát, zadejte jako parametr řetězce dotazu v žádosti o požadovanému formátu. Příklad:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>Zabezpečení
Služba Instance Metadata koncový bod je přístupný jenom zevnitř spuštěné instance virtuálního počítače na nesměrovatelných adres IP. Kromě toho každá žádost s `X-Forwarded-For` záhlaví zamítá službou.
Musí také obsahovat žádosti `Metadata: true` hlavičky k zajištění, že skutečnou žádost byla přímo určené a není součástí neúmyslnému přesměrování. 

### <a name="error"></a>Chyba
Pokud je datový prvek nebyl nalezen nebo chybně vytvořený požadavek, vrátí služba Instance Metadata standardní chyby protokolu HTTP. Příklad:

Kód stavu HTTP | Důvod
----------------|-------
200 OK |
400 – Chybný požadavek | Chybí `Metadata: true` záhlaví
404 – Nenalezeno | Požadovaný element neexistuje 
405 Metoda není povolena | Pouze `GET` a `POST` jsou podporovány požadavky
429 příliš mnoho požadavků | Rozhraní API v současné době podporuje maximálně 5 dotazů za sekundu
Chyba 500 služby     | Zkuste to znovu za nějakou dobu

### <a name="examples"></a>Příklady

> [!NOTE] 
> Všechny odpovědi rozhraní API jsou řetězce JSON. Všechny následující ukázkové odpovědi jsou tisk pretty pro lepší čitelnost.

#### <a name="retrieving-network-information"></a>Načítání informací o síti

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Odpověď**

> [!NOTE] 
> Odpověď je řetězec formátu JSON. Následující příklad odpovědi je tisk pretty pro lepší čitelnost.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Načítají se veřejná IP adresa

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Načítání všech metadat pro instance

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-12-01"
```

**Odpověď**

> [!NOTE] 
> Odpověď je řetězec formátu JSON. Následující příklad odpovědi je tisk pretty pro lepší čitelnost.

```json
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Načítání metadat v systému Windows virtuální počítač

**Požadavek**

Instance metadata dá načíst ve Windows pomocí nástroje PowerShell `curl`: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

Nebo prostřednictvím `Invoke-RestMethod` rutiny:
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**Odpověď**

> [!NOTE] 
> Odpověď je řetězec formátu JSON. Následující příklad odpovědi je tisk pretty pro lepší čitelnost.

```json
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Kategorie dat instance metadata
Tyto kategorie dat jsou k dispozici prostřednictvím služba Instance Metadata:

Data | Popis | Verze zavedena 
-----|-------------|-----------------------
location | Oblasti Azure virtuální počítač běží v | 2017-04-02 
jméno | Název virtuálního počítače | 2017-04-02
nabídka | Nabízí informace pro image virtuálního počítače. Tato hodnota platí jenom pro Image nasazují z Galerie imagí Azure. | 2017-04-02
vydavatele | Vydavatel image virtuálního počítače | 2017-04-02
SKU | Konkrétní SKU pro image virtuálního počítače | 2017-04-02
version | Verzi image virtuálního počítače | 2017-04-02
osType | Linux nebo Windows | 2017-04-02
platformUpdateDomain |  [Aktualizační doména](manage-availability.md) virtuální počítač je spuštěný | 2017-04-02
platformFaultDomain | [Doména selhání](manage-availability.md) virtuální počítač je spuštěný | 2017-04-02
vmId | [Jedinečný identifikátor](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) pro virtuální počítač | 2017-04-02
vmSize | [Velikost virtuálního počítače](sizes.md) | 2017-04-02
subscriptionId | Předplatné Azure pro virtuální počítač | 2017-08-01
tags | [Značky](../../azure-resource-manager/resource-group-using-tags.md) pro váš virtuální počítač  | 2017-08-01
resourceGroupName | [Skupina prostředků](../../azure-resource-manager/resource-group-overview.md) pro váš virtuální počítač | 2017-08-01
placementGroupId | [Skupiny umístění](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) sady škálování vašeho virtuálního počítače | 2017-08-01
plán | [Plánování](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) pro virtuální počítač v něm je Imagí Azure Marketplace, obsahuje název, produktu a vydavatel | 2017-04-02
publicKeys | Kolekce veřejného klíče [https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey] přiřazená k virtuálnímu počítači a cesty | 2017-04-02
vmScaleSetName | [Název škálovací sady virtuálního počítače](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) sady škálování vašeho virtuálního počítače | 2017-12-01
zóna | [Zóna dostupnosti](../../availability-zones/az-overview.md) vašeho virtuálního počítače | 2017-12-01 
ipv4/privateIpAddress | Místní adresa IPv4 z virtuálního počítače | 2017-04-02
ipv4/publicIpAddress | Veřejnou IPv4 adresu virtuálního počítače | 2017-04-02
subnet/address | Adresa podsítě virtuálního počítače | 2017-04-02 
podsítě/předpona | Předpona podsítě, například 24 | 2017-04-02 
ipv6/ipAddress | Místní adresa IPv6 z virtuálního počítače | 2017-04-02 
macAddress | Adresa mac virtuálního počítače | 2017-04-02 
scheduledevents | Zobrazit [naplánované události](scheduled-events.md) | 2017-08-01
identity | (Preview) Spravované identity pro prostředky Azure. Zobrazit [získání přístupového tokenu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01 

## <a name="example-scenarios-for-usage"></a>Ukázkové scénáře pro využití  

### <a name="tracking-vm-running-on-azure"></a>Sledování virtuálních počítačů spuštěných v Azure

Jako poskytovatel služeb může vyžadovat ke sledování počtu virtuálních počítačů spuštění softwaru nebo agenty, které je potřeba sledovat jedinečnost virtuálního počítače. Aby bylo možné získat jedinečné ID pro virtuální počítač, použijte `vmId` pole z služba Instance Metadata.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Odpověď**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Umístění kontejnery, oddíly dat na základě selhání a aktualizační doména 

Pro určité scénáře, umístění různých datových replik je prvořadý význam. Například [umístění repliky HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) nebo kontejneru umístění prostřednictvím [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) může vyžadovat znát `platformFaultDomain` a `platformUpdateDomain` virtuální počítač běží na.
Můžete taky využít [zóny dostupnosti](../../availability-zones/az-overview.md) instance tato rozhodnutí. Tato data přímo přes služba Instance Metadata se můžete dotazovat.

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**Odpověď**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Získání dalších informací o virtuálním počítači během případ podpory 

Jako poskytovatel služeb může se zobrazit volání podpory Pokud chcete získat více informací o virtuálním počítači. S výzvou zákazníka sdílet metadata výpočetní poskytuje základní informace pro podporu profesionální vědět o typ, který virtuální počítač v Azure. 

**Požadavek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Odpověď**

> [!NOTE] 
> Odpověď je řetězec formátu JSON. Následující příklad odpovědi je tisk pretty pro lepší čitelnost.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Získávání prostředí Azure, ve kterém je virtuální počítač spuštěný. 

Azure má různé suverénních cloudech, jako je [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Někdy potřebujete prostředí Azure tak, aby některé rozhodování modulu runtime. Podle následující ukázky vám ukáže, jak toho dosáhnout.

**Požadavek**

> [!NOTE] 
> Vyžaduje jq k instalaci. 

```bash
  metadata=$(curl "http://169.254.169.254/metadata/instance/compute?api-version=2018-02-01" -H "Metadata:true")
  endpoints=$(curl "https://management.azure.com/metadata/endpoints?api-version=2017-12-01")
 
  location=$(echo $metadata | jq .location -r)
 
  is_ww=$(echo $endpoints | jq '.cloudEndpoint.public.locations[]' -r | grep -w $location)
  is_us=$(echo $endpoints | jq '.cloudEndpoint.usGovCloud.locations[]' -r | grep -w $location)
  is_cn=$(echo $endpoints | jq '.cloudEndpoint.chinaCloud.locations[]' -r | grep -w $location)
  is_de=$(echo $endpoints | jq '.cloudEndpoint.germanCloud.locations[]' -r | grep -w $location)
 
  environment="Unknown"
  if [ ! -z $is_ww ]; then environment="AzureCloud"; fi
  if [ ! -z $is_us ]; then environment="AzureUSGovernment"; fi
  if [ ! -z $is_cn ]; then environment="AzureChinaCloud"; fi
  if [ ! -z $is_de ]; then environment="AzureGermanCloud"; fi
 
  echo $environment
```

### <a name="failover-clustering-in-windows-server"></a>Převzetí služeb clusteringu ve Windows serveru

Pro určité scénáře, při dotazování služba Instance Metadata pomocí clusteringu převzetí služeb při selhání, je potřeba přidat trasy do směrovací tabulky.

1. Otevřete příkazový řádek s oprávněními správce.

2. Spusťte následující příkaz a poznamenejte si adresu rozhraní pro cíl v síti (`0.0.0.0`) do směrovací tabulky IPv4.

```bat
route print
```

> [!NOTE] 
> Následující příklad výstupu z virtuálního počítače s Windows serverem s clusteru převzetí služeb při selhání povolené obsahuje pouze směrovací tabulky IPv4 pro zjednodušení.

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

3. Spusťte následující příkaz a použijte adresu rozhraní pro cíl v síti (`0.0.0.0`) tedy (`10.0.1.10`) v tomto příkladu.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Příklady volání metadat služby používající různé jazyky ve virtuálním počítači 

Jazyk | Příklad: 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Přejít  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata
    

## <a name="faq"></a>Nejčastější dotazy
1. Vyskytla se chyba `400 Bad Request, Required metadata header not specified`. Co to znamená?
   * Služba metadat Instance vyžaduje hlavičku `Metadata: true` mají být předány v požadavku. Předejte toto záhlaví volání REST umožňuje přístup ke služba Instance Metadata. 
2. Proč mi zobrazuje informace o výpočetní prostředky pro virtuální počítač?
   * Služba Instance Metadata aktuálně podporuje pouze instance vytvořené pomocí Azure Resource Manageru. V budoucnu podpora pro virtuální počítače pro cloudové služby, mohou být přidány.
3. Jsem vytvořil Můj virtuální počítač prostřednictvím Azure Resource Manageru delší zpět. Proč mi viz výpočetních informací o metadatech?
   * Pro všechny virtuální počítače vytvořené po. září 2016, přidejte [značka](../../azure-resource-manager/resource-group-using-tags.md) testováním schopností compute metadat. Pro starší virtuální počítače (vytvořené před. září 2016) přidávat nebo odebírat disky rozšíření nebo data do virtuálního počítače k aktualizaci metadat.
4. Mi nezobrazují všechna data pro novou verzi
   * Pro všechny virtuální počítače vytvořené po. září 2016, přidejte [značka](../../azure-resource-manager/resource-group-using-tags.md) testováním schopností compute metadat. Pro starší virtuální počítače (vytvořené před. září 2016) přidávat nebo odebírat disky rozšíření nebo data do virtuálního počítače k aktualizaci metadat.
5. Proč se zobrazuje chyba `500 Internal Server Error`?
   * Zkuste odeslat žádost podle exponenciální regrese systému. Pokud se problém nevyřeší, obraťte se na podporu Azure.
6. Kde se dá sdílet další dotazy nebo připomínky?
   * Odeslat své poznámky http://feedback.azure.com.
7. By to fungovalo pro instanci virtuálního počítače Škálovací nastavit?
   * Ano je dostupná pro nastavení instance Škálovací metadat služby. 
8. Jak získat podporu pro službu?
   * Jak získat podporu pro službu, vytvořte problém podpory na webu Azure portal pro virtuální počítač, pokud nejste schopni získat odpověď metadat za dlouhá opakování 
9. Získat žádosti vypršel časový limit pro moje volání služby?
   * Volání metadata se musí provádět z primární IP adresy přiřazené síťové karty virtuálního počítače, kromě toho v případě, že jste změnili trasy existuje musí být trasu pro adresu 169.254.0.0/16 mimo síťovou kartou.
10. Můžu aktualizovat Mé značky v sadě škálování virtuálního počítače, ale nejsou zobrazeny v instancích na rozdíl od virtuálních počítačů?
   * Aktuálně pro ScaleSets značky zobrazit pouze k virtuálnímu počítači na restartování nebo obnovení z Image/nebo změnit disk na instanci. 

   ![Podpora metadat instance](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Další postup

- Další informace o [naplánovaných událostí](scheduled-events.md)

