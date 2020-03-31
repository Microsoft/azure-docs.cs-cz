---
title: Ochrana zákaznických dat v Azure
description: Tento článek se zabývá tím, jak Azure chrání zákaznická data.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 741cbc82f2ed3ffffb553b146d981b4e35a273f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726680"
---
# <a name="azure-customer-data-protection"></a>Ochrana zákaznických dat Azure   
Přístup k zákaznickým datům ze strany pracovníků společnosti Microsoft a pracovníků odborné pomoci je ve výchozím nastavení odepřen. Při udělení přístupu k zákaznickým datům je vyžadováno schválení vedení a poté je přístup pečlivě spravován a protokolován. Požadavky na řízení přístupu jsou stanoveny následujícími zásadami zabezpečení Azure:

- Ve výchozím nastavení není přístup k zákaznickým datům.
- Žádné účty uživatelů nebo správců na virtuálních počítačích zákazníků (VM).
- Udělte nejnižší oprávnění, která je vyžadována k dokončení úkolu. auditace a protokolování žádostí o přístup.

Pracovníkům podpory Azure přiřazuje Microsoft jedinečné podnikové účty služby Active Directory. Azure se spoléhá na microsoft corporate active directory, spravovaný Microsoft Information Technology (MSIT), pro řízení přístupu ke klíčovým informačním systémům. Je vyžadováno vícefaktorové ověřování a přístup je udělojen pouze ze zabezpečených konzol.

Všechny pokusy o přístup jsou monitorovány a lze je zobrazit prostřednictvím základní sady sestav.

## <a name="data-protection"></a>Ochrana dat
Azure poskytuje zákazníkům silné zabezpečení dat, a to jak ve výchozím nastavení, tak jako možnosti zákazníků.

**Oddělení dat**: Azure je služba s více klienty, což znamená, že více zákaznických nasazení a virtuálních počítačích se ukládá na stejném fyzickém hardwaru. Azure používá logickou izolaci k oddělení dat každého zákazníka od dat ostatních. Segregace poskytuje rozsah a ekonomické výhody služeb pro více klientů a zároveň důsledně brání zákazníkům v přístupu k datům druhé strany.

**Ochrana dat v klidovém stavu**: Zákazníci jsou zodpovědní za to, že data uložená v Azure budou šifrovaná v souladu s jejich standardy. Azure nabízí širokou škálu možností šifrování, které zákazníkům poskytují flexibilitu při výběru řešení, které nejlépe vyhovuje jejich potřebám. Azure Key Vault pomáhá zákazníkům snadno udržovat kontrolu nad klíči, které používají cloudové aplikace a služby k šifrování dat. Azure Disk Encryption umožňuje zákazníkům šifrovat virtuální počítače. Azure Storage Service Encryption umožňuje šifrovat všechna data, která jsou umístěna do účtu úložiště zákazníka.

**Ochrana dat na cestě**: Zákazníci mohou povolit šifrování pro provoz mezi vlastními virtuálními počítačůmi a koncovými uživateli. Azure chrání data při přenosu do nebo z vnějších součástí a data v tranzitu interně, například mezi dvěma virtuálními sítěmi. Azure používá standardní protokol TLS (Transport Layer Security) 1.2 nebo novější s 2 048bitovými šifrovacími klíči RSA/SHA256, jak doporučuje CESG/NCSC, k šifrování komunikace mezi:

- Zákazník a cloud.
- Interně mezi systémy Azure a datovými centry.

**Šifrování**: Šifrování dat v úložišti a při přenosu mohou zákazníci nasadit jako osvědčený postup pro zajištění důvěrnosti a integrity dat. Pro zákazníky je jednoduché nakonfigurovat cloudové služby Azure tak, aby používaly protokol SSL k ochraně komunikace před internetem a dokonce i mezi virtuálními počítači hostovanými v Azure.

**Redundance dat**: Společnost Microsoft pomáhá zajistit, aby byla data chráněna v případě kybernetického útoku nebo fyzického poškození datového centra. Zákazníci se mohou rozhodnout pro:

- Úložiště v zemi nebo v oblasti pro dodržování předpisů nebo latenci.
- Úložiště mimo zemi/mimo oblast pro účely zabezpečení nebo zotavení po havárii.

Data mohou být replikována v rámci vybrané zeměpisné oblasti pro redundanci, ale nelze je přenášet mimo ni. Zákazníci mají několik možností replikace dat, včetně počtu kopií a počtu a umístění datových center replikace.

Při vytváření účtu úložiště vyberte jednu z následujících možností replikace:

- **Místně redundantní úložiště (LRS):** Místně redundantní úložiště udržuje tři kopie dat. LRS se replikuje třikrát v rámci jednoho zařízení v jedné oblasti. LRS chrání vaše data před běžnými selháními hardwaru, ale ne před selháním jednoho zařízení.
- **Zónově redundantní úložiště (ZRS):** Zónově redundantní úložiště udržuje tři kopie dat. ZRS je replikován třikrát ve dvou až třech zařízeních, aby poskytoval vyšší odolnost než LRS. Replikace probíhá v rámci jedné oblasti nebo ve dvou oblastech. ZRS pomáhá zajistit, že vaše data jsou trvalá v rámci jedné oblasti.
- **Geograficky redundantní úložiště (GRS):** Geograficky redundantní úložiště je při jeho vytváření ve výchozím nastavení povoleno pro váš účet úložiště. GRS udržuje šest kopií dat. S GRS jsou vaše data replikována třikrát v rámci primární oblasti. Data jsou také replikována třikrát v sekundární oblasti stovky kilometrů od primární oblasti, což poskytuje nejvyšší úroveň odolnosti. V případě selhání v primární oblasti Azure Storage převzetí služby při selhání do sekundární oblasti. GRS pomáhá zajistit, že vaše data jsou trvanlivá ve dvou samostatných oblastech.

**Zničení dat**: Když zákazníci odstraní data nebo opustí Azure, Microsoft dodržuje přísné standardy pro přepsání prostředků úložiště před jejich opětovným použitím, stejně jako fyzické zničení vyřazeného hardwaru. Společnost Microsoft provede úplné odstranění dat na žádost zákazníka a po ukončení smlouvy.

## <a name="customer-data-ownership"></a>Vlastnictví zákaznických dat
Společnost Microsoft nekontroluje, neschvaluje ani nemonitoruje aplikace, které zákazníci nasazují do Azure. Microsoft navíc neví, jaký druh dat se zákazníci rozhodnou v Azure ukládat. Společnost Microsoft si nenárokuje vlastnictví dat nad informacemi o zákaznících, které jsou zadány do Azure.

## <a name="records-management"></a>Správa záznamů
Azure zavedla interní požadavky na uchovávání záznamů pro back-endová data. Zákazníci jsou zodpovědní za identifikaci svých vlastních požadavků na uchovávání záznamů. U záznamů, které jsou uložené v Azure, jsou zákazníci zodpovědní za extrahování dat a zachování jejich obsahu mimo Azure po dobu uchovávání zadanou zákazníkem.

Azure umožňuje zákazníkům exportovat data a auditovat sestavy z produktu. Exporty jsou uloženy místně zachovat informace pro zákazníkem definované období uchování.

## <a name="electronic-discovery-e-discovery"></a>Elektronické zjišťování (e-discovery)
Zákazníci Azure jsou zodpovědní za splnění požadavků na elektronické zjišťování při používání služeb Azure. Pokud zákazníci Azure musí zachovat svá zákaznická data, můžou je exportovat a ukládat místně. Zákazníci mohou navíc požadovat exporty svých dat z oddělení podpory zákazníků Azure. Kromě toho, že Azure umožňuje zákazníkům exportovat svá data, provádí rozsáhlé protokolování a interní monitorování.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá pro zabezpečení infrastruktury Azure, najdete v tématu:

- [Zařízení Azure, prostory a fyzické zabezpečení](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Součásti a hranice informačního systému Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Produkční operace a správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
