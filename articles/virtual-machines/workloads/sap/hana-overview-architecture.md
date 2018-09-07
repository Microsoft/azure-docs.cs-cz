---
title: Přehled systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Přehled o tom, jak nasazovat SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c177dbad1145dee6eda3202d8076997cc7673dfc
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44026917"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Co je SAP HANA v Azure (velké instance)?

SAP HANA v Azure (velké instance) je jedinečné řešení na Azure. Kromě toho, že virtuální počítače pro nasazování a spouštění SAP HANA, Azure vám nabízí možnost spouštět a nasazovat SAP HANA na holých serverů, které jsou vyhrazené. SAP HANA v Azure (velké instance) řešení je založena na nesdílený hostitele nebo server holého hardwaru, který je vám přiřazena. Hardware serveru je součástí větší razítek, které obsahují výpočty/server, sítí a infrastruktury úložiště. Jako kombinace je HANA přizpůsobené integrace System center data (TDI) certified. SAP HANA v Azure (velké instance) nabízí jiný server SKU nebo velikosti. Jednotky může mít 36 Intel Procesorových jader a o velikosti 768 GB paměti a přejděte do jednotky, které mají až 480 Intel Procesorových jader a až 24 TB paměti.

Izolace zákazníka v rámci razítka infrastruktury se provádí v tenantech, který bude vypadat takto:

- **Sítě**: izolace zákazníků v rámci zásobníku infrastruktury prostřednictvím virtuální sítě na tenantovi zákazníka, které jsou přiřazeny. Tenant se přiřadí pro jediného zákazníka. Zákazník může mít více tenantů. Izolace sítě tenantů zakazuje síťové komunikace mezi klienty na úrovni razítka infrastruktury i v případě, že klienti patřit do stejné zákazníka.
- **Úložiště komponent**: izolace prostřednictvím úložiště virtuálních počítačů, které mají svazky úložiště, které jsou jim přiřazeny. Svazky úložiště je přiřadit pouze jeden úložiště virtuálního počítače. Úložiště virtuálního počítače je exkluzivně přiřazená jedna jednoho tenanta v zásobníku certifikovaných infrastrukturu SAP HANA TDI. V důsledku toho svazky úložiště přiřazené k virtuálnímu počítači úložiště přístupné v jedné konkrétní a související tenanta pouze. Nejsou viditelné mezi různé nasazené tenanty.
- **Server nebo hostitele**: jednotka serveru nebo hostitele není sdílena mezi zákazníky nebo tenantů. Server nebo hostitele nasazený pro zákazníka, je jednotka atomic výpočetní úplné obnovení systému, který je přiřazen do jednoho jednoho tenanta. *Ne* dělení hardwarové nebo softwarové dělení se používá, který může způsobit sdílíte s odběratele jiného hostitele nebo server. Úložné svazky, které jsou přiřazeny k z konkrétního tenanta úložiště virtuálního počítače jsou připojené taková na server. Klient může mít jeden na mnoho server jednotek z různých skladových položkách exkluzivně přiřazená.
- V rámci SAP HANA na razítku infrastruktury Azure (velké instance) mnoho různých tenantů se nasazují a izolované proti sobě základními pojmy tenanta na sítě, úložiště a výpočetní úrovně. 


Tyto jednotky úplné obnovení systému server jsou podporovány pouze spuštění SAP HANA. Aplikační vrstvě SAP nebo úloh VMware střední vrstvy běží na virtuálních počítačích. Razítka infrastruktury, na kterých běží SAP HANA v Azure (velké instance) jednotky jsou připojené k páteřních služeb sítě Azure. Tímto způsobem je k dispozici připojení s nízkou latencí mezi virtuálními počítači a SAP HANA v Azure (velké instance) jednotky.

Tento dokument je jednou z několika dokumenty, které se týkají SAP HANA v Azure (velké instance). Toto téma představuje základní architekturu, zodpovědnostech a služby poskytované řešení. Základní možnosti řešení jsou popsány také. Většinu ostatních oblastech, jako je například sítě a připojení čtyři další dokumenty, které pokrývají podrobnosti a informace o procházení. Dokumentace k systému SAP HANA v Azure (velké instance) nezahrnuje aspekty instalace SAP NetWeaver nebo nasazení SAP NetWeaver na virtuálních počítačích. SAP NetWeaver v Azure je zahrnutých v samostatných dokumentech nalezen ve stejném kontejneru dokumentace k Azure. 


Jiné dokumenty velká Instance HANA pokyny zahrnují následující oblasti:

- [SAP HANA (velké instance) přehled a architektura v Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalace a konfigurace SAP HANA (velké instance) v Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Řešení potíží s SAP HANA (velké instance) a monitorování v Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Vysoká dostupnost v SUSE lze nastavit pomocí využitím techniky STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Operační systém zálohování a obnovení pro SKU typu II](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Další kroky**
- Přečtěte si [vědět podmínky](hana-know-terms.md)