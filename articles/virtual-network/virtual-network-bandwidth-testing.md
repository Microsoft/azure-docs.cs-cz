---
title: Testování propustnost sítě virtuálních vzatovirtuálních sítí Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak otestovat propustnost sítě virtuálních strojů Azure.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: 80e8a5e5de1da2098d895e09b36fb209050743a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60743069"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Testování šířky pásma/propustnost (NTTTCP)

Při testování výkonu propustnosti sítě v Azure je nejlepší použít nástroj, který cílí na síť pro testování a minimalizuje využití jiných prostředků, které by mohly ovlivnit výkon. Doporučuje se NTTTCP.

Zkopírujte nástroj do dvou virtuálních počítačích Azure stejné velikosti. Jeden virtuální virtuální soud funguje jako SENDER a druhý jako příjemce.

#### <a name="deploying-vms-for-testing"></a>Nasazení virtuálních mích pro testování
Pro účely tohoto testu by měly být dva virtuální aplikace ve stejné cloudové službě nebo ve stejné sadě dostupnosti, abychom mohli používat jejich interní IP adresy a vyloučit vykladače zatížení z testu. Je možné testovat s VIP, ale tento druh testování je mimo rozsah tohoto dokumentu.

Poznamenejte si IP adresu příjemce. Nazvěme to IP "a.b.c.r"

Poznamenejte si počet jader na virtuálním počítači. Nazvěme\#to\_"num cores"

Spusťte test NTTTCP po dobu 300 sekund (nebo 5 minut) na virtuálním počítači odesílatele a příjemce.

Tip: Při prvním nastavení tohoto testu můžete zkusit kratší zkušební období, abyste získali zpětnou vazbu dříve. Jakmile nástroj pracuje podle očekávání, prodlužte zkušební dobu na 300 sekund pro nejpřesnější výsledky.

> [!NOTE]
> Odesílatel **a** příjemce musí zadat **stejný** parametr doby trvání testu (-t).

Testování jednoho datového proudu TCP po dobu 10 sekund:

Parametry přijímače: ntttcp -r -t 10 -P 1

Parametry odesílatele: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> Předchozí ukázka by měla být použita pouze k potvrzení konfigurace. Platné příklady testování jsou popsány dále v tomto dokumentu.

## <a name="testing-vms-running-windows"></a>Testování virtuálních aplikací se systémem WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Získejte NTTTCP na virtuální chod.

Stáhněte si nejnovější verzi:<https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Nebo hledat, pokud <https://www.bing.com/search?q=ntttcp+download> \< se stěhoval: -- by měla být první hit

Zvažte umístění protokolu NTTTCP do\\samostatné složky, například nástroje c:

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Povolit ntttcp prostřednictvím brány firewall systému Windows
V přijímači vytvořte pravidlo povolit v bráně Windows Firewall, které umožní doručení přenosů NTTTCP. Nejjednodušší je povolit celý program NTTTCP podle názvu, nikoli povolit příchozí příchozí porty TCP.

Povolit ntttcp prostřednictvím brány Windows Firewall, jako je tento:

netsh advfirewall firewall add\<\>\\rule program= PATH ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

Pokud jste například zkopírovali soubor ntttcp.exe do složky nástroje c:\\, bude to příkaz: 

netsh advfirewall firewall add rule\\\\program=c: tools ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>Spuštění testů NTTTCP

Spusťte NTTTCP na přijímači **(spuštění z CMD**, nikoli z prostředí PowerShell):

ntttcp -r –m\*\#\_[2\*num jádra], ,a.b.c.r -t 300

Pokud má virtuální virtuální počítačů čtyři jádra a IP adresu 10.0.0.4, bude to vypadat takto:

ntttcp -r –m\*8, ,10.0.0.4 -t 300


Spusťte NTTTCP na SENDER **(spustit z CMD**, ne z prostředí PowerShell):

ntttcp -s -m\*8, ,10.0.0.4 -t 300 

Počkejte na výsledky.


## <a name="testing-vms-running-linux"></a>Testování virtuálních počítačů se systémem LINUX:

Použijte nttcp-for-linux. Je k dispozici od<https://github.com/Microsoft/ntttcp-for-linux>

Na virtuálních počítačích s Linuxem (SENDER i RECEIVER) spusťte tyto příkazy a připravte ntttcp-for-linux na virtuálních počítačích:

Centos - Instalace Gitu:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Instalace Gitu:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Vytvořit a nainstalovat na obou:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Stejně jako v příkladu systému Windows předpokládáme, že IP přijímače Linuxu je 10.0.0.4

Spusťte NTTTCP-for-Linux na přijímači:

``` bash
ntttcp -r -t 300
```

A na SENDER, běh:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Výchozí délka testu je 60 sekund, pokud není uveden žádný časový parametr.

## <a name="testing-between-vms-running-windows-and-linux"></a>Testování mezi virtuálními počítači se systémem Windows a LINUX:

V těchto scénářích bychom měli povolit režim bez synchronizace, takže test lze spustit. To se provádí pomocí **příznaku -N** pro Linux a **-ns příznak** pro Windows.

#### <a name="from-linux-to-windows"></a>Od Linuxu po Windows:

> \<systému Windows přijímače:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Odesílatel \<Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Od Windows po Linux:

Přijímač \<Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Odesílatel \<Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testování instancí cloudových služeb:
Do souboru ServiceDefinition.csdef je třeba přidat následující oddíl.
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Další kroky
* V závislosti na výsledcích může být prostor [pro optimalizaci propustnost sítě počítače](virtual-network-optimize-network-bandwidth.md) pro váš scénář.
* Přečtěte si, jak [se virtuálním počítačům přiděluje šířka pásma](virtual-machine-network-throughput.md)
* Další informace o [nejčastějších dotazech na virtuální síť Azure (nejčastější dotazy)](virtual-networks-faq.md)
