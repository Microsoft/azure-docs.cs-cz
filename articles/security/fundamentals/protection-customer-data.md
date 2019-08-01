---
title: Ochrana zákaznických dat v Azure
description: Tento článek popisuje, jak Azure chrání zákaznická data.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 06ae18fa2217d7e7cfef7924473520d8d21a8a3e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612597"
---
# <a name="azure-customer-data-protection"></a>Ochrana zákaznických dat Azure   
Přístup k zákaznickým datům podle operací Microsoftu a pracovníků podpory je ve výchozím nastavení zakázaný. Když se udělí přístup k zákaznickým datům, vyžaduje se schválení vedoucího a pak se pečlivě spravuje a protokoluje přístup. Požadavky na řízení přístupu jsou navázány následujícími zásadami zabezpečení Azure:

- Ve výchozím nastavení není přístup k zákaznickým datům.
- Žádné účty uživatele nebo správce na virtuálních počítačích zákazníka (VM).
- Udělte nejnižší oprávnění, které je nutné k dokončení úkolu. požadavky na audit a přístup do protokolu.

Pracovníkům podpory Azure jsou společnosti Microsoft přiřazeny jedinečné podnikové účty služby Active Directory. Azure se spoléhá na podnikovou službu Active Directory, kterou spravuje Microsoft Information Technology (MSIT), za účelem řízení přístupu ke klíčovým informacím v systémech. Vícefaktorové ověřování je povinné a přístup je udělen pouze ze zabezpečených konzol.

Všechny pokusy o přístup se monitorují a dají se zobrazit prostřednictvím základní sady sestav.

## <a name="data-protection"></a>Ochrana dat
Azure poskytuje zákazníkům se silným zabezpečením dat, a to jak ve výchozím nastavení, tak i jako možnosti zákazníka.

**Oddělení dat**: Azure je víceklientská služba, což znamená, že více zákaznických nasazení a virtuálních počítačů je uloženo na stejném fyzickém hardwaru. Azure používá logickou izolaci k oddělení dat jednotlivých zákazníků od dat ostatních. Oddělení poskytuje škálu a ekonomické výhody pro víceklientské služby a současně zajišťuje přísnou prevenci zákazníkům přístup k datům jiného typu.

**Ochrana dat na REST**: Zákazníci zodpovídají za to, že data uložená v Azure jsou zašifrovaná podle jejich standardů. Azure nabízí rozsáhlou škálu možností šifrování, která zákazníkům poskytuje flexibilitu při výběru řešení, které nejlépe vyhovuje jejich potřebám. Azure Key Vault pomáhá zákazníkům snadno udržovat kontrolu nad klíči, které používají cloudové aplikace a služby k šifrování dat. Azure Disk Encryption umožňuje zákazníkům šifrovat virtuální počítače. Šifrování služby Azure Storage umožňuje šifrovat všechna data, která jsou umístěna do účtu úložiště zákazníka.

**Ochrana dat v přenosu**: Zákazníci můžou povolit šifrování pro přenosy mezi vlastními virtuálními počítači a koncovými uživateli. Azure chrání data při přenosu mezi externími součástmi a přenášenými daty interně, například mezi dvěma virtuálními sítěmi. Azure používá standardní protokol TLS (Transport Layer Security) 1,2 nebo novější 2 048 s CESG šifrovacími klíči RSA/SHA256, jak to doporučila/NCSC, k šifrování komunikace mezi těmito službami:

- Zákazník a Cloud.
- Interně mezi systémy a datacentry Azure.

**Šifrování:** Šifrování dat v úložišti a přenosu je možné nasadit zákazníky jako osvědčený postup pro zajištění důvěrnosti a integrity dat. Zákazníci můžou nakonfigurovat své cloudové služby Azure tak, aby používaly protokol SSL k ochraně komunikace z Internetu a dokonce i mezi virtuálními počítači hostovanými v Azure.

**Redundance dat**: Microsoft pomáhá zajistit, aby data byla chráněna v případě, že dojde k cyberattack nebo fyzickému poškození datacentra. Zákazníci se můžou rozhodnout pro:

- Úložiště v zemi/v oblasti pro požadavky na dodržování předpisů nebo latenci.
- Úložiště mimo zemi nebo mimo oblast pro účely zabezpečení nebo zotavení po havárii.

Data je možné replikovat v rámci vybrané geografické oblasti pro redundanci, ale nelze je přenést mimo ni. Zákazníci mají více možností pro replikaci dat, včetně počtu kopií a počtu a umístění datových center replikace.

Při vytváření účtu úložiště vyberte jednu z následujících možností replikace:

- **Místně redundantní úložiště (LRS)** : Místně redundantní úložiště udržuje tři kopie dat. LRS se replikuje třikrát v rámci jednoho zařízení v jedné oblasti. LRS chrání vaše data před normálním selháním hardwaru, ale ne proti selhání jednoho zařízení.
- **Redundantní úložiště zóny (ZRS)** : Zónově redundantní úložiště udržuje tři kopie dat. ZRS se replikuje třikrát po dvou až třech zařízeních, aby se zajistila vyšší odolnost než LRS. Replikace probíhá v jedné oblasti nebo ve dvou oblastech. ZRS pomáhá zajistit, aby vaše data byla odolná v rámci jedné oblasti.
- **Geograficky redundantní úložiště (GRS)** : Při vytváření účtu úložiště se automaticky vybere geograficky redundantní úložiště. GRS udržuje šest kopií dat. V GRS se vaše data replikují třikrát v rámci primární oblasti. Vaše data se taky replikují třikrát v sekundární oblasti od primární oblasti po stovkách kilometrů a poskytují nejvyšší úroveň trvanlivosti. V případě selhání v primární oblasti Azure Storage převzetí služeb při selhání do sekundární oblasti. GRS pomáhá zajistit, aby vaše data byla odolná ve dvou různých oblastech.

**Zničení dat**: Když zákazníci odstraňují data nebo odejdou z Azure, Microsoft dodržuje přísné standardy pro přepsání prostředků úložiště před jejich opětovným použitím, jakož i fyzické zničení vyřazeného hardwaru. Microsoft provádí kompletní odstraňování dat na žádost zákazníka a ukončení smlouvy.

## <a name="customer-data-ownership"></a>Vlastnictví zákaznických dat
Microsoft nekontroluje, schválí ani nesleduje aplikace, které zákazníci nasazují do Azure. Společnost Microsoft navíc neví, jaký druh dat si zákazníci můžou uložit v Azure. Microsoft nenárokuje vlastnictví dat na informace o zákaznících, které jsou zadané v Azure.

## <a name="records-management"></a>Správa záznamů
Azure navázal interní požadavky na uchovávání záznamů pro data back-endu. Zákazníci zodpovídají za identifikaci vlastních požadavků na uchovávání záznamů. U záznamů uložených v Azure zodpovídá zákazníkům za extrakci svých dat a uchování jejich obsahu mimo Azure pro dobu uchování určenou zákazníkem.

Azure umožňuje zákazníkům exportovat data a auditovat sestavy z produktu. Exporty se ukládají místně, aby se zachovaly informace pro dobu uchovávání definované zákazníkem.

## <a name="electronic-discovery-e-discovery"></a>Elektronické zjišťování (e-Discovery)
Zákazníci Azure zodpovídají za dodržování požadavků e-Discovery při používání služeb Azure. Pokud si zákazníci Azure musí zachovat zákaznická data, můžou data exportovat a uložit místně. Zákazníci si navíc můžou vyžádat export svých dat z oddělení zákaznické podpory Azure. Kromě umožnění zákazníkům, aby mohli exportovat svá data, Azure provede interní protokolování a monitorování.

## <a name="next-steps"></a>Další postup
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
