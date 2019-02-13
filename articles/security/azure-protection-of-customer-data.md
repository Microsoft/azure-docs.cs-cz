---
title: Ochrany zákaznických dat v Azure
description: Tento článek popisuje, jak Azure chrání data zákazníků.
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
ms.openlocfilehash: 49615dcb2f077d2e1d8b93a4bb900b435e4c87bf
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104484"
---
# <a name="azure-customer-data-protection"></a>Ochrana dat zákazníků Azure   
Ve výchozím nastavení je odepřen přístup k zákaznickým datům tak, že Microsoft operations a pracovníky podpory. Když jsou udělena přístup k zákaznickým datům, vyžaduje se schválení vedení a poté přístup pečlivě spravované a protokoluje. Požadavky na řízení přístupu jsou určeny následující zásady zabezpečení Azure:

- Žádný přístup k zákaznickým datům ve výchozím nastavení.
- Žádné účty uživatele nebo správce na virtuální počítače (VM).
- Udělit nejnižší oprávnění, které jsou nutné k dokončení úkolu. audit a protokolování žádosti o přístup.

Podpora Azure jsou určení pracovníci jedinečných podnikových účtů služby Active Directory společností Microsoft. Azure využívá Microsoft podnikové služby Active Directory, spravované pomocí Microsoft informace o technologii (MSIT), k řízení přístupu k systémům informace o klíči. Je požadováno ověřování službou Multi-Factor Authentication, a udělit přístup pouze z zabezpečené konzol.

Všechny pokusy o přístup se monitorují a mohou být zobrazeny prostřednictvím základní sadu sestav.

## <a name="data-protection"></a>Ochrana dat
Azure poskytuje zákazníkům silné dat zabezpečení, ve výchozím nastavení a jako možnosti zákazníka.

**Oddělení dat**: Azure je víceklientská služba, což znamená, že více zákazníka nasazeních a virtuálních počítačů jsou uložené na stejném fyzickém hardwaru. Azure používá k oddělení dat jednotlivých zákazníků od dat ostatních logickou izolaci. Oddělení nabízí škálování a ekonomický přínos služeb s více tenanty a přitom zákazníkům přísně brání přístupu k datům nepřípustným.

**V klidovém stavu ochrany dat**: Zákazníci odpovídají za zajištění, že je v souladu s jejich standardy šifrovaná data uložená v Azure. Azure nabízí širokou škálu možností šifrování, zákazníkům to dává flexibilitu zvolit řešení, které nejlíp vyhovují jejich potřebám. Služba Azure Key Vault pomáhá zákazníkům snadno kontrolu nad klíči, používané cloudovými aplikacemi a službami k šifrování dat. Azure Disk Encryption umožňuje zákazníkům šifrovat virtuální počítače. Azure Storage Service Encryption umožňuje šifrování všech dat, které je umístěn do účtu úložiště zákazníka.

**Ochrana dat během přenosu**: Zákazníky můžete povolit šifrování pro přenosy mezi virtuálními počítači a koncoví uživatelé. Azure chrání data přenášená do nebo z externí komponenty a přenosu dat v interním, například jde o vztah mezi dvěma virtuálními sítěmi. Azure používá standardní zabezpečení TLS (Transport Layer) 1.2 nebo vyšší protokol s 2 048 bitů RSA/SHA256 šifrovací klíče, dle doporučení CESG/NCSC, k šifrování komunikace mezi:

- Zákazník a cloudu.
- Interně mezi systémy pro Azure a datových center.

**Šifrování**: Šifrování dat v úložišti a přenosu je možné nasadit pomocí zákazníkům doporučujeme pro zajištění důvěrnost a integrita dat. To je jednoduché pro zákazníci moci nakonfigurovat jejich cloudové služby Azure pro použití protokolu SSL k ochraně komunikace s Internetem a dokonce i mezi své virtuální počítače hostované v Azure.

**Redundanci dat**: Microsoft zajišťuje, že data jsou chráněna, pokud je cyberattack nebo fyzickému poškození do datového centra. Zákazníci mohou zvolit:

- Úložiště ve své zemi požadavky na dodržování předpisů nebo latence.
- Mimo zemi úložiště pro účely zabezpečení nebo po havárii pro obnovení.

Data je možné replikovat v rámci vybrané zeměpisné oblasti za účelem zajištění redundance, ale nemůže být přenesen mimo něj. Zákazníci, mají několik možností pro replikaci dat, včetně počtu kopií a počet a umístění datacentra replikace.

Při vytváření účtu úložiště, vyberte jednu z těchto možností replikace:

- **Místně redundantní úložiště (LRS)**: Místně redundantní úložiště udržuje tři kopie dat. LRS se replikuje třikrát v rámci jednoho zařízení v jedné oblasti. LRS chrání vaše data před běžnými výpadky hardwaru, ale ne před výpadkem celého selhání.
- **Zónově redundantní úložiště (ZRS)**: Zónově redundantní úložiště udržuje tři kopie dat. ZRS se replikuje třikrát v zařízení dvě až tři poskytují větší odolnost než LRS. Replikace v rámci jedné oblasti nebo napříč dvěma oblastmi. ZRS pomáhá zajistit, že vaše data byla odolná v jedné oblasti.
- **Geograficky redundantní úložiště (GRS)**: Při vytváření účtu úložiště se automaticky vybere geograficky redundantní úložiště. GRS udržuje šest kopií dat. S GRS vaše data se replikují třikrát v rámci primární oblasti. Vaše data se také replikuje třikrát v sekundární oblasti vzdálené stovky mil od primární oblasti, takže poskytuje nejvyšší úroveň odolnosti. V případě selhání primární oblasti Azure Storage převezme služby při selhání do sekundární oblasti. GRS pomáhá zajistit, že vaše data byla odolná ve dvou oblastech.

**Odstranění dat**: Když zákazníci odstranit data, nebo nechte Azure, Microsoft se řídí přísné normy pro přepsání úložiště prostředky před jejich opětovné použití, stejně jako fyzické zničení Vyřazená z provozu hardwaru. Microsoft provádí kompletní odstranění dat na žádost zákazníka a ukončení smlouvy.

## <a name="customer-data-ownership"></a>Vlastnictví dat zákazníka
Microsoft nepodporuje zkontrolovat, schválit ani monitorování aplikací, které zákazníci nasadit do Azure. Microsoft navíc neví, co druh dat zákazníci zvolit uložení v Azure. Microsoft si nečiní nároky na vlastnictví dat nad informacemi o zákaznících, který je zadán do Azure.

## <a name="records-management"></a>Správa záznamů
Azure má navázat interní uchovávání záznamů požadavky pro back endovým datům. Zákazníci odpovídají za identifikaci požadavků uchování záznamu. Pro záznamy, které jsou uložené v Azure zákazníci odpovídají za extrahování jejich dat a zachování jejich obsah i mimo Azure na dobu uchování specifikovaného zákazníkem.

Azure umožňuje zákazníkům exportovat data a sestavy z produktu auditu. Exporty se uloží místně do uchovávat informace pro uživatelem definovaný uchování dat časové období.

## <a name="electronic-discovery-e-discovery"></a>Elektronické zjišťování (e-discovery)
Zákazníci Azure jsou zodpovědné za vyhovující požadavkům elektronického zjišťování v jejich používání služeb Azure. Pokud zákazníci Azure musí zachovat svoje zákaznická data, mohou exportovat a uložit data místně. Kromě toho zákazníci můžou požádat o export dat z oddělení zákaznickou podporou Azure. Azure navíc umožňuje zákazníkům, chcete-li exportovat data, provádí podrobné protokolování a monitorování interně.

## <a name="next-steps"></a>Další postup
Další informace o Microsoft nemá pro zabezpečení infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
