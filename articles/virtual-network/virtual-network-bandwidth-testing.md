---
title: Testování propustnost sítě virtuálního počítače Azure | Microsoft Docs
description: Zjistěte, jak otestovat propustnost sítě virtuálního počítače Azure.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: d65b86cc63a4fd39824a6421afd5ce9abb7fd270
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28200975"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Šířky pásma nebo propustnost testování (NTTTCP)

Při testování propustnost sítě v Azure, je nejvhodnější použít nástroj, který cílí sítě pro testování a minimalizuje použití další prostředky, které by mohlo mít vliv na výkon. Doporučuje se NTTTCP.

Zkopírujte nástroj na dva virtuální počítače Azure stejnou velikost. Jeden virtuální počítač funguje jako odesílatele a druhý jako příjemce.

#### <a name="deploying-vms-for-testing"></a>Nasazení virtuálních počítačů pro testování
Pro účely tohoto testu musí být dva virtuální počítače ve stejné cloudové služby nebo do stejné skupiny dostupnosti, aby jsme používat svoje interní IP adresy a vyloučit z testu nástroje pro vyrovnávání zatížení. Je možné otestovat s virtuální IP adresa, ale tento druh testování je mimo rámec tohoto dokumentu.
 
Poznamenejte si IP adresa příjemce. Umožňuje volání této IP "a.b.c.r"

Poznamenejte si počet jader na virtuálním počítači. To umožňuje volání "\#num\_jader"
 
Spusťte NTTTCP test pro 300 sekund (nebo 5 minut) na virtuální počítač odesílatele a příjemce virtuálních počítačů.

Tip: Při nastavování tento test poprvé, můžete se pokusit kratší dobu testovací k získání zpětné vazby dříve. Jakmile nástroj funguje podle očekávání, rozšiřte na 300 sekund, aby nejpřesnější výsledky zkušebního období.

> [!NOTE]
> Odesílatel **a** musíte zadat příjemce **stejné** testovací parametr doba trvání (-t).

Při testování jednoho datového proudu TCP 10 sekund:

Příjemce parametry: ntttcp - r -t 10 - P 1

Odesílatel parametry: ntttcp-s10.27.33.7 -t 10 - n 1 -P 1

> [!NOTE]
> V předchozím příkladu lze používat pouze k potvrzení vaší konfigurace. Testování platný příklady jsou popsané dál v tomto dokumentu.

## <a name="testing-vms-running-windows"></a>Testování virtuální počítače se systémem WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Získáte NTTTCP do virtuálních počítačů.

Stažení nejnovější verze: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Nebo je-li přesunout hledání: <https://www.bing.com/search?q=ntttcp+download> \< – musí být nejprve dosáhl

Zvažte umístění NTTTCP samostatné složky, jako je c:\\nástroje

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Povolit NTTTCP přes bránu Windows firewall
Na příjemce vytvořte pravidlo povolení v bráně Windows Firewall umožňuje provoz NTTTCP příchod. Je to nejjednodušší provádět umožnit programu celý NTTTCP podle názvu místo povolit příchozí určité porty TCP.

Povolit ntttcp přes bránu Windows Firewall takto:

netsh advfirewall firewall přidejte pravidlo program =\<cesta\>\\ntttcp.exe název = "ntttcp" protokol = všechny dir = v akci = povolit povolit = yes profilu = ANY

Například, pokud jste zkopírovali ntttcp.exe k "c:\\nástroje" složky, bude příkaz: 

netsh advfirewall firewall přidejte pravidlo program = c:\\nástroje\\ntttcp.exe název = "ntttcp" protokol = všechny dir = v akci = povolit povolit = yes profilu = ANY

#### <a name="running-ntttcp-tests"></a>Spouštění testů NTTTCP

Spusťte NTTTCP na příjemce (**spustit z CMD**, nikoli z prostředí PowerShell):

ntttcp -r –m [2\*\#num\_cores],\*,a.b.c.r -t 300

Pokud virtuální počítač má čtyři jader a IP adresu 10.0.0.4, by vypadat například takto:

ntttcp - r – m 8,\*, 10.0.0.4 -t 300


Spusťte NTTTCP na odesílatele (**spustit z CMD**, nikoli z prostředí PowerShell):

ntttcp -s – m 8,\*, 10.0.0.4 -t 300 

Počkejte, než pro dané výsledky.


## <a name="testing-vms-running-linux"></a>Testování virtuální počítače se systémem LINUX:

Použijte nttcp pro linux. Je k dispozici z <https://github.com/Microsoft/ntttcp-for-linux>

Na Linux virtuálních počítačů (odesílatele i příjemce) spusťte tyto příkazy a příprava ntttcp pro linux na virtuální počítače:

CentOS – instalace Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu – instalace Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Ujistěte se a nainstalujte na obou:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Jako v příkladu Windows předpokládáme, že příjemce Linux IP je 10.0.0.4

Spusťte na příjemce NTTTCP pro Linux:

``` bash
ntttcp -r -t 300
```

A na odesílatele, spusťte:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Test výchozí hodnota je délka 60 sekund, pokud žádný čas parametr je zadána.

## <a name="testing-between-vms-running-windows-and-linux"></a>Testování mezi virtuální počítače se systémem Windows a LINUX:

Na této scénáře jsme by měl povolit režim no-sync, můžete spustit test. To se provádí pomocí **-N příznak** pro systémy Linux a **příznak -ns** pro systém Windows.

#### <a name="from-linux-to-windows"></a>Z Linux do Windows:

Příjemce <Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Odesílatel <Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Ze systému Windows do systému Linux:

Příjemce <Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Odesílatel <Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Cloudové služby instance testování:
Je nutné přidat následující části do vaší ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Další postup
* V závislosti na výsledky, může být místo pro [optimalizovat počítače propustnost sítě](virtual-network-optimize-network-bandwidth.md) pro váš scénář.
* Přečtěte si informace o tom [šířky pásma je přidělen k virtuálním počítačům] (virtuální machine sítě throughput.md)
* Další informace s [Azure Virtual Network nejčastější dotazy (FAQ)](virtual-networks-faq.md)
