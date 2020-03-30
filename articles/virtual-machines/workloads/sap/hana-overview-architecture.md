---
title: Přehled SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Přehled nasazení SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 39fcf5d0fe2273c4debd3ae5ebe5fd1190ddc959
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616951"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Co je SAP HANA ve velkých instancích Azure?

SAP HANA v Azure (velké instance) je jedinečné řešení pro Azure. Kromě poskytování virtuálních počítačů pro nasazení a spouštění SAP HANA vám Azure nabízí možnost spouštět a nasazovat SAP HANA na serverech s holým kovem, které jsou pro vás vyhrazené. Řešení SAP HANA v Azure (velké instance) je postaveno na nesdíleném hardwaru host/server, který je vám přiřazen. Hardware serveru je vložen do větších razítek, které obsahují výpočetní/serverovou, síťovou a úložnou infrastrukturu. Jako kombinace je certifikována na míru hana integrace datových center (TDI). SAP HANA v Azure (velké instance) nabízí různé serverové skum nebo velikosti. Jednotky mohou mít 36 procesorových jader Intel a 768 GB paměti a přejít na jednotky, které mají až 480 procesorových jader Intel a až 24 TB paměti.

Izolace zákazníka v rámci razítka infrastruktury se provádí v tenantech, což vypadá takto:

- **Sítě:** Izolace zákazníků v rámci zásobníku infrastruktury prostřednictvím virtuálních sítí na klienta přiřazeného zákazníka. Tenant je přiřazen k jednomu zákazníkovi. Zákazník může mít více klientů. Síťová izolace klientů zakazuje síťovou komunikaci mezi klienty na úrovni razítka infrastruktury, i když klienti patří stejnému zákazníkovi.
- **Součásti úložiště:** Izolace prostřednictvím virtuálních počítačů úložiště, ke kterým jsou přiřazeny svazky úložiště. Svazky úložiště lze přiřadit pouze k jednomu virtuálnímu počítači úložiště. Virtuální počítač úložiště se přiřazuje výhradně jednomu klientovi v zásobníku infrastruktury s certifikací SAP HANA TDI. V důsledku toho jsou svazky úložiště přiřazené k virtuálnímu počítači úložiště přístupné pouze v jednom konkrétním a souvisejícím tenantovi. Nejsou viditelné mezi různými nasazenými tenanty.
- **Server nebo hostitel**: Server nebo hostitelská jednotka není sdílena mezi zákazníky nebo klienty. Server nebo hostitel nasazený k zákazníkovi je atomová holé výpočetní jednotky, která je přiřazena k jednomu klientovi. *Nepoužívá* se žádné hardwarové dělení nebo měkké dělení, které by mohlo vést ke sdílení hostitele nebo serveru s jiným zákazníkem. Svazky úložiště, které jsou přiřazeny k virtuálnímu počítači úložiště konkrétního klienta, jsou připojeny k takovému serveru. Klient může mít jednu až mnoho jednotek serveru různých jednotek sku výhradně přiřazeny.
- V rámci razítka infrastruktury SAP HANA v Azure (velké instance) se mnoho různých klientů nasadí a izoluje proti sobě prostřednictvím konceptů klienta na úrovni sítí, úložiště a výpočetních prostředků. 


Tyto jednotky serveru holé kovy jsou podporovány ke spuštění pouze SAP HANA. Aplikační vrstva SAP nebo vrstva středního zboží se spouští ve virtuálních počítačích. Razítka infrastruktury, které spouštějí jednotky SAP HANA v Azure (velké instance), jsou připojeny k páteřním sítím Azure. Tímto způsobem je k dispozici připojení s nízkou latencí mezi jednotkami SAP HANA v Azure (velké instance) a virtuálními počítači.

Od července 2019 rozlišujeme mezi dvěma různými revizemi razítek hana velké instance a umístěním nasazení:

- "Revize 3" (Zj 3): Jsou razítka, která byla zákazníkům k dispozici před červencem 2019
- "Revize 4" (Zj 4): Nový návrh razítka, který se nasadí v těsné blízkosti hostitelů virtuálních zařízení Azure a který se zatím vydává v oblastech Azure:
    -  USA – západ 2 
    -  USA – východ 
    -  Západní Evropa
    -  Severní Evropa


Tento dokument je jedním z několika dokumentů, které pokrývají SAP HANA v Azure (velké instance). Tento dokument představuje základní architekturu, odpovědnosti a služby poskytované řešením. Jsou také diskutovány možnosti řešení na vysoké úrovni. Pro většinu ostatních oblastí, jako je například síť a připojení, čtyři další dokumenty pokrývají podrobnosti a informace o podrobnostech. Dokumentace SAP HANA v Azure (velké instance) nezahrnuje aspekty instalace SAP NetWeaver nebo nasazení SAP NetWeaver ve virtuálních počítačích. SAP NetWeaver v Azure je pokrytsamostatnými dokumenty nalezenými ve stejném kontejneru dokumentace Azure. 


Různé dokumenty hana velké instance pokyny pokrývají následující oblasti:

- [Přehled SAP HANA (velké instance) a architektura v Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruktura SAP HANA (velké instance) a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalace a konfigurace SAP HANA (velké instance) v Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Řešení potíží a monitorování SAP HANA (velké instance) v Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Vysoká dostupnost nastavená v SUSE pomocí stonithu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Zálohování a obnovení operačního systému pro skladové sloky typu II razítek revize 3](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Další kroky**
- Odkazovat [Znát podmínky](hana-know-terms.md)