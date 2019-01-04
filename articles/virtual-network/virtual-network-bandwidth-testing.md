---
title: Testování propustnosti sítě virtuálních počítačů Azure
titlesuffix: Azure Virtual Network
description: Zjistěte, jak otestovat propustnost sítě virtuálních počítačů Azure.
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
ms.openlocfilehash: b02f5011b9482481ca3fbe439061c160b3c73a1e
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016609"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Šířka pásma nebo propustnosti testování (NTTTCP)

Při testování výkonu propustnost sítě v Azure, je nejvhodnější použít nástroj, který cílí na síť pro testování a minimalizuje využití dalších prostředků, které by mohly mít vliv na výkon. Doporučuje se NTTTCP.

Zkopírujte nástroj na dva virtuální počítače Azure stejné velikosti. Jeden virtuální počítač funguje jako odesílatele a další příjemce.

#### <a name="deploying-vms-for-testing"></a>Nasazení virtuálních počítačů pro účely testování
Pro účely tohoto testu dva virtuální počítače by měl být ve stejné cloudové službě nebo stejné sadě dostupnosti tak, že můžeme pomocí svých interních IP adres a vyloučit z testovacího nástroje pro vyrovnávání zatížení. Je možné otestovat pomocí virtuální IP adresa, ale tento druh testování je mimo rámec tohoto dokumentu.

Poznamenejte si IP adresu příjemce. Pojmenujme tuto IP adresu "a.b.c.r"

Poznamenejte si počet jader ve virtuálním počítači. To budeme nazývat "\#num\_jader"

Spusťte NTTTCP test na 300 sekund (nebo 5 minut) na virtuálním počítači odesílatele a příjemce virtuálního počítače.

Tip: Při nastavování tento test poprvé, můžete se pokusit kratší zkušebního období získat zpětnou vazbu dříve. Jakmile nástroj funguje podle očekávání, prodloužení doby testu do 300 sekund, velmi přesné výsledky.

> [!NOTE]
> Odesílatel **a** musíte zadat příjemce **stejné** parametr doba trvání testu (-t).

K otestování jeden datový proud TCP po dobu 10 sekund:

Příjemce parametry: - r -t ntttcp 10 - P 1

Odesílatel parametry: ntttcp-s10.27.33.7 -t 10 - n 1 -P 1

> [!NOTE]
> V předchozím příkladu by měla sloužit pouze k potvrzení vaší konfigurace. Příklady platných testování jsou popsané dále v tomto dokumentu.

## <a name="testing-vms-running-windows"></a>Testovací virtuální počítače s WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Získejte NTTTCP do virtuálních počítačů.

Stažení nejnovější verze: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Nebo je-li přesunout hledání: <https://www.bing.com/search?q=ntttcp+download> \< – by měl být nejprve přístupů.

Zvažte uložení NTTTCP samostatné složky, například c:\\nástroje

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Povolit NTTTCP přes bránu Windows firewall
Na straně příjmu vytvořte pravidlo povolit v bráně Windows Firewall NTTTCP provoz dorazí. Je nejjednodušší povolit celého programu NTTTCP podle názvu, místo aby určité porty TCP příchozí.

Povolit ntttcp přes bránu Windows Firewall takto:

netsh advfirewall firewall přidat pravidlo program =\<cesta\>\\ntttcp.exe název = "ntttcp" protocol = žádné dir = v akci = umožňují povolit = yes profile = ANY

Například, pokud jste si zkopírovali ntttcp.exe k "c:\\nástroje" složky, to může být příkaz: 

netsh advfirewall firewall přidat pravidlo program = c:\\nástroje\\ntttcp.exe název = "ntttcp" protokol jakékoli dir = = v akci = povolit enable = yes profile = ANY

#### <a name="running-ntttcp-tests"></a>Spouštění testů NTTTCP

Spustit NTTTCP na straně příjmu (**spusťte z příkazového řádku**, nikoli z prostředí PowerShell):

ntttcp - r – m [2\*\#num\_jader],\*, a.b.c.r -t 300

Pokud virtuální počítač má čtyři jádra a IP adresou 10.0.0.4, by vypadalo takto:

ntttcp - r – m 8,\*, 10.0.0.4 -t 300


Spusťte NTTTCP na odesílatele (**spusťte z příkazového řádku**, nikoli z prostředí PowerShell):

ntttcp -s-m 8,\*, 10.0.0.4 -t 300 

Vyčkat, než výsledky.


## <a name="testing-vms-running-linux"></a>Testovací virtuální počítače s LINUXEM:

Použijte nttcp pro linux. Je k dispozici <https://github.com/Microsoft/ntttcp-for-linux>

Na virtuálních počítačů s Linuxem (odesílatele a příjemce) spusťte následující příkazy, které připraví ntttcp pro linux na virtuálních počítačích:

CentOS – nainstalujte Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - nainstalujte Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Zkontrolujte a nainstalujte na obou:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Stejně jako v příkladu Windows předpokládáme, že příjemce Linux IP adresa je 10.0.0.4

Na straně příjmu spusťte NTTTCP pro Linux:

``` bash
ntttcp -r -t 300
```

A na odesílatele, spusťte:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Zadaný testovací délka výchozí hodnota 60 sekund, pokud žádný parametr času

## <a name="testing-between-vms-running-windows-and-linux"></a>Testování mezi virtuálními počítači s Windows a LINUX:

V tomto scénáři jsme měli povolit režimu no-sync, můžete spustit test. To se provádí pomocí **-N příznak** pro systémy Linux a **příznak -ns** pro Windows.

#### <a name="from-linux-to-windows"></a>Od Linuxu po Windows:

Příjemce <Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Odesílatel <Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Od Windows po Linux:

Příjemce <Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Odesílatel <Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testování instance cloudové služby:
Budete muset přidat následující části do váš soubor ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Další postup
* V závislosti na výsledky, může být místo pro [optimalizovat počítače propustnost sítě](virtual-network-optimize-network-bandwidth.md) pro váš scénář.
* Přečtěte si, jak [šířky pásma je přidělen k virtuálním počítačům](virtual-machine-network-throughput.md)
* Další informace najdete [Azure Virtual Network – nejčastější dotazy (FAQ)](virtual-networks-faq.md)
