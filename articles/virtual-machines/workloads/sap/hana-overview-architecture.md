---
title: Přehled SAP HANA v Azure (velké instance) | Microsoft Docs
description: Přehled nasazení SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/04/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c1cd4e7d65897634b5a8a8fa8be46275bbd4b88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676864"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Co je SAP HANA ve velkých instancích Azure?

SAP HANA v Azure (velké instance) je jedinečné řešení pro Azure. Kromě poskytování virtuálních počítačů pro nasazení a spouštění SAP HANA vám Azure nabízí možnost spouštět a nasazovat SAP HANA na holé servery, které jsou pro vás vyhrazené. Řešení SAP HANA v Azure (velké instance) sestavuje na nesdíleném hardwaru hosta/serveru, který vám byl přiřazen. Serverový hardware je vložený do větších razítek, která obsahují výpočetní výkon, server, síť a infrastrukturu úložiště. SAP HANA v Azure (velké instance) nabízí různé serverové SKU a velikosti. Jednotky můžou mít 36 procesory Intel a 768 GB paměti a můžou jít až o jednotky, které mají až 480 procesorů Intel a až 24 TB paměti.

Izolace zákazníka v rámci razítka infrastruktury se provádí v klientech, což vypadá takto:

- **Sítě**: izolace zákazníků v rámci infrastruktury služby Virtual Networks na zákazníka přiřazeného zákazníka. Tenant je přiřazen jednomu zákazníkovi. Zákazník může mít více tenantů. Izolace sítě klientů zakazuje síťovou komunikaci mezi klienty v úrovni razítka infrastruktury, a to i v případě, že klienti patří stejnému zákazníkovi.
- **Komponenty úložiště**: izolace prostřednictvím virtuálních počítačů úložiště, ke kterým jsou přiřazené svazky úložiště. Svazky úložiště se dají přiřadit jenom k jednomu virtuálnímu počítači úložiště. Virtuální počítač úložiště se přiřazuje výhradně jednomu klientovi v zásobníku infrastruktury. V důsledku toho jsou svazky úložiště přiřazené k virtuálnímu počítači úložiště dostupné jenom v jednom konkrétním a souvisejícím tenantovi. Nejsou viditelné mezi různými nasazenými klienty.
- **Server nebo hostitel**: jednotka serveru nebo hostitele není sdílená mezi zákazníky nebo klienty. Server nebo hostitel, který je nasazený pro zákazníka, je atomická výpočetní jednotka, která je přiřazená jednomu jednomu tenantovi. Nepoužívají se *žádné* hardwarové oddíly ani softwarové dělení, což by mohlo mít za následek sdílení hostitele nebo serveru s jiným zákazníkem. Svazky úložiště, které jsou přiřazené k virtuálnímu počítači úložiště určitého tenanta, jsou připojené k tomuto serveru. Tenant může mít jednu z několika serverových jednotek různých SKU, které jsou výhradně přiřazeny.
- V rámci SAP HANA na infrastruktuře infrastruktury Azure (velké instance) se nasazuje a izoluje mnoho různých tenantů, a to prostřednictvím konceptů tenanta na úrovni sítě, úložiště a výpočetní úrovně. 


Tyto jednotky holých serverů se podporují jenom pro spouštění SAP HANA. Na virtuálních počítačích se spouští vrstva aplikace SAP nebo vrstva pro střední brambory. Časová razítka infrastruktury, která spouštějí SAP HANA v Azure (velké instance), jsou připojená k nevykostěným síťovým službám Azure. Tímto způsobem je k dispozici připojení s nízkou latencí mezi SAP HANA jednotek Azure (velké instance) a virtuálními počítači.

Od ledna 2021 rozlišujeme mezi dvěma různými revizemi razítek velkých instancí HANA a umístěním nasazení:

- "Revize 3" (rev 3): jsou razítka, která byla k dispozici pro zákazníka k nasazení do července 2019
- "Revize 4" (Rev 4): nový návrh razítka, který je nasazený v těsné blízkosti hostitelů virtuálních počítačů Azure a který je doposud vydaný v oblastech Azure v:
    -  USA – západ 2 
    -  East US
    -  Východní USA 2 (v rámci dvou Zóny dostupnosti)
    -  Střed USA – jih (mezi dvěma Zóny dostupnostiy)
    -  West Europe
    -  Severní Evropa


Tento dokument je jedním z několika dokumentů, které pokrývají SAP HANA v Azure (velké instance). V tomto dokumentu se zavádí základní architektura, zodpovědnosti a služby poskytované řešením. Jsou zde také popsány funkce vysoké úrovně řešení. Pro většinu ostatních oblastí, jako jsou například sítě a připojení, se na Další informace pokrývají další dokumenty a podrobné informace. Dokumentace SAP HANA v Azure (velké instance) nepokrývá aspekty instalace SAP NetWeaver ani nasazení SAP NetWeaver na virtuálních počítačích. SAP NetWeaver v Azure je popsaná v samostatných dokumentech, které se nacházejí ve stejném kontejneru dokumentace Azure. 


Různé dokumenty s pokyny pro velké instance HANA se týkají těchto oblastí:

- [Přehled a architektura v Azure SAP HANA (velké instance)](hana-overview-architecture.md)
- [SAP HANA (velké instance) infrastruktury a připojení v Azure](hana-overview-infrastructure-connectivity.md)
- [Instalace a konfigurace SAP HANA (velké instance) v Azure](hana-installation.md)
- [SAP HANA (velké instance) s vysokou dostupností a zotavením po havárii v Azure](hana-overview-high-availability-disaster-recovery.md)
- [Řešení potíží a monitorování v Azure SAP HANA (velké instance)](troubleshooting-monitoring.md)
- [Nastavení vysoké dostupnosti v SUSE pomocí STONITH](./ha-setup-with-stonith.md)
- [Zálohování a obnovení operačního systému pro SKU typu II u razítek revize 3](./os-backup-type-ii-skus.md)
- [Úspory pro Velké instance SAP HANA s využitím rezervace Azure](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md)

**Další kroky**
- Přečtěte si informace [o těchto pojmech](hana-know-terms.md)
