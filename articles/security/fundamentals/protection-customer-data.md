---
title: Ochrana zákaznických dat v Azure
description: Přečtěte si, jak Azure chrání zákaznická data prostřednictvím oddělení dat, redundance dat a zničení dat.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2020
ms.author: terrylan
ms.openlocfilehash: 14589e4efe22d89468b069bf6ff7e3d9babcc714
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543788"
---
# <a name="azure-customer-data-protection"></a>Ochrana zákaznických dat Azure   
Přístup k zákaznickým datům podle operací Microsoftu a pracovníků podpory je ve výchozím nastavení zakázaný. Pokud je udělen přístup k datům souvisejícím s případem podpory, je mu uděleno pouze použití modelu JIT (just-in-time), který používá zásady, které jsou auditovány a prověřené proti zásadám dodržování předpisů a ochrany osobních údajů.  Požadavky na řízení přístupu jsou navázány následujícími zásadami zabezpečení Azure:

- Ve výchozím nastavení není přístup k zákaznickým datům.
- Žádné účty uživatele nebo správce na virtuálních počítačích zákazníka (VM).
- Udělte nejnižší oprávnění, které je nutné k dokončení úkolu. požadavky na audit a přístup do protokolu.

Pracovníkům podpory Azure jsou společnosti Microsoft přiřazeny jedinečné podnikové účty služby Active Directory. Azure se spoléhá na podnikovou službu Active Directory, kterou spravuje Microsoft Information Technology (MSIT), za účelem řízení přístupu ke klíčovým informacím v systémech. Vícefaktorové ověřování je povinné a přístup je udělen pouze ze zabezpečených konzol.

Všechny pokusy o přístup se monitorují a dají se zobrazit prostřednictvím základní sady sestav.

## <a name="data-protection"></a>Ochrana dat
Azure poskytuje zákazníkům se silným zabezpečením dat, a to jak ve výchozím nastavení, tak i jako možnosti zákazníka.

**Oddělení dat**: Azure je víceklientská služba, což znamená, že více zákaznických nasazení a virtuálních počítačů je uloženo na stejném fyzickém hardwaru. Azure používá logickou izolaci k oddělení dat jednotlivých zákazníků od dat ostatních. Oddělení poskytuje škálu a ekonomické výhody pro víceklientské služby a současně zajišťuje přísnou prevenci zákazníkům přístup k datům jiného typu.

**Ochrana dat v klidovém** stavu: zákazníci zodpovídají za zajištění, že data uložená v Azure jsou zašifrovaná v souladu s jejich standardy. Azure nabízí rozsáhlou škálu možností šifrování, která zákazníkům poskytuje flexibilitu při výběru řešení, které nejlépe vyhovuje jejich potřebám. Azure Key Vault pomáhá zákazníkům snadno udržovat kontrolu nad klíči, které používají cloudové aplikace a služby k šifrování dat. Azure Disk Encryption umožňuje zákazníkům šifrovat virtuální počítače. Šifrování služby Azure Storage umožňuje šifrovat všechna data umístěná do účtu úložiště zákazníka.

**Ochrana před přenosem dat**: Společnost Microsoft nabízí celou řadu možností, které můžou zákazníci využívat k internímu přenosu dat v rámci sítě Azure a externě na celém internetu pro koncového uživatele.  Mezi ně patří komunikace prostřednictvím virtuálních privátních sítí (s využitím šifrování IPsec/IKE), TLS (Transport Layer Security) 1,2 nebo novější (prostřednictvím komponent Azure, jako jsou například Application Gateway nebo front-in Azure), protokolů přímo na virtuálních počítačích Azure (například Windows IPsec nebo SMB) a dalších. 

Kromě toho je u všech přenosů dat v provozu mezi datacentry Azure povolená možnost šifrování ve výchozím nastavení pomocí MACsec (Standard IEEE na vrstvě Data-Link), aby se zajistila důvěrnost a integrita zákaznických dat. 

**Redundance dat**: Microsoft pomáhá zajistit, aby data byla chráněna v případě, že dojde k cyberattack nebo fyzickému poškození datacentra. Zákazníci se můžou rozhodnout pro:

- Úložiště v zemi/v oblasti pro požadavky na dodržování předpisů nebo latenci.
- Úložiště mimo zemi nebo mimo oblast pro účely zabezpečení nebo zotavení po havárii.

Data je možné replikovat v rámci vybrané geografické oblasti pro redundanci, ale nelze je přenést mimo ni. Zákazníci mají více možností pro replikaci dat, včetně počtu kopií a počtu a umístění datových center replikace.

Při vytváření účtu úložiště vyberte jednu z následujících možností replikace:

- **Místně redundantní úložiště (LRS)**: místně redundantní úložiště uchovává tři kopie vašich dat. LRS se replikuje třikrát v rámci jednoho zařízení v jedné oblasti. LRS chrání vaše data před normálním selháním hardwaru, ale ne proti selhání jednoho zařízení.
- **Úložiště redundantní v zóně (ZRS)**: úložiště redundantní v zóně uchovává tři kopie dat. ZRS se replikuje třikrát po dvou až třech zařízeních, aby se zajistila vyšší odolnost než LRS. Replikace probíhá v jedné oblasti nebo ve dvou oblastech. ZRS pomáhá zajistit, aby vaše data byla odolná v rámci jedné oblasti.
- **Geograficky redundantní úložiště (GRS)**: geograficky redundantní úložiště je při vytváření účtu úložiště ve výchozím nastavení povolené. GRS udržuje šest kopií dat. V GRS se vaše data replikují třikrát v rámci primární oblasti. Vaše data se taky replikují třikrát v sekundární oblasti od primární oblasti po stovkách kilometrů a poskytují nejvyšší úroveň trvanlivosti. V případě selhání v primární oblasti Azure Storage převzetí služeb při selhání do sekundární oblasti. GRS pomáhá zajistit, aby vaše data byla odolná ve dvou různých oblastech.

**Zničení dat**: Když zákazníci odstraňují data nebo odejdou z Azure, společnost Microsoft je přísné standardy pro přepsání prostředků úložiště před jejich opětovným použitím, jakož i fyzické zničení vyřazeného hardwaru. Microsoft provádí kompletní odstraňování dat na žádost zákazníka a ukončení smlouvy.

## <a name="customer-data-ownership"></a>Vlastnictví zákaznických dat
Microsoft nekontroluje, schválí ani nesleduje aplikace, které zákazníci nasazují do Azure. Společnost Microsoft navíc neví, jaký druh dat si zákazníci můžou uložit v Azure. Microsoft nenárokuje vlastnictví dat na informace o zákaznících, které jsou zadané v Azure.

## <a name="records-management"></a>Správa záznamů
Azure navázal interní požadavky na uchovávání záznamů pro data back-endu. Zákazníci zodpovídají za identifikaci vlastních požadavků na uchovávání záznamů. U záznamů uložených v Azure zodpovídá zákazníkům za extrakci svých dat a uchování jejich obsahu mimo Azure pro dobu uchování určenou zákazníkem.

Azure umožňuje zákazníkům exportovat data a auditovat sestavy z produktu. Exporty se ukládají místně, aby se zachovaly informace pro dobu uchovávání definované zákazníkem.

## <a name="electronic-discovery-e-discovery"></a>Elektronické zjišťování (e-Discovery)
Zákazníci Azure zodpovídají za dodržování požadavků e-Discovery při používání služeb Azure. Pokud si zákazníci Azure musí zachovat zákaznická data, můžou data exportovat a uložit místně. Zákazníci si navíc můžou vyžádat export svých dat z oddělení zákaznické podpory Azure. Kromě umožnění zákazníkům, aby mohli exportovat svá data, Azure provede interní protokolování a monitorování.

## <a name="next-steps"></a>Další kroky
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
