---
title: Ochrana dat zákazníka v Azure
description: Tento článek popisuje, jak Azure chrání data zákazníků.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 9a3b00e39f78f65b05b7d730447440d481979539
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102172"
---
# <a name="protection-of-customer-data-in-azure"></a>Ochrana dat zákazníka v Azure   
Ve výchozím nastavení byl odepřen přístup k datům zákazníka Microsoft operations a pracovníky podpory. Když jsou udělena přístup k datům zákazníka, je požadováno schválení vedení a poté přístup pečlivě spravované a přihlášení. Požadavky řízení přístupu jsou určeny následující zásady zabezpečení Microsoft Azure:

- Žádný přístup k datům zákazníka ve výchozím nastavení
- Žádné účty uživatele nebo správce na zákazníka virtuální počítače
- Udělit nejnižší oprávnění, které jsou potřebné k dokončení úkolu. audit a protokolování požadavky na přístup

Pracovníky technické podpory Microsoft Azure přiřazené jedinečné účty AD společností Microsoft. Microsoft Azure se spoléhá na Microsoft podnikové služby Active Directory, spravuje MSIT k řízení přístupu k systémům informace o klíči. Služba Multi-Factor authentication je vyžadován, a přístup se poskytuje pouze z konzoly pro zabezpečené.

Všechny pokusy o přístup jsou monitorovány a lze zobrazit pomocí základní sady sestav.

## <a name="data-protection"></a>Ochrana dat
Azure poskytuje zákazníkům silné zabezpečení – ve výchozím nastavení i jako možnosti zákazníka.

**Oddělení dat** -Azure je víceklientské služby, což znamená, že nasazení více zákazníků a virtuální počítače jsou uloženy na stejném fyzickém hardwaru. Azure používá logické izolace oddělit data každého zákazníka z dat jiných. Oddělení poskytuje při pečlivě zákazníkům brání přístupu k datům své škálování a hospodářského výhod víceklientské služby.

**Ochrana dat na rest** -zákazníci jsou zodpovědní za zajištění, že je v souladu s jejich standardy šifrovaná data uložená v Azure. Azure nabízí celou řadu funkcí šifrování, udělíte zákazníkům flexibilitu zvolit si řešení, který bude nejlépe vyhovovat svých potřeb. Azure Key Vault pomáhá zákazníkům snadno zachovat kontrolu nad klíče používané cloudovými aplikacemi a službami šifrovat data. Azure Disk Encryption umožňuje zákazníkům šifrování virtuálních počítačů. Azure šifrování služby úložiště umožňuje šifrování všech dat do účtu úložiště zákazníka.

**Ochrana dat během přenosu** -zákazníkům můžete povolit šifrování pro přenosy mezi své vlastní virtuální počítače a koncoví uživatelé. Azure chrání data při přenosu do nebo z mimo součásti a přenosu dat v interně, například mezeru mezi dvě virtuální sítě. Azure používá daném odvětví standardem zabezpečení TLS (Transport Layer) 1.2 nebo vyšší protokol s 2048 bitů RSA/SHA256 šifrovací klíče, jako je doporučené podle CESG/NCSC, k šifrování komunikace mezi:

- Zákazník a cloud
- interně mezi Azure a datová centra

**Šifrování** -šifrování dat v úložišti a přenosu jde nasadit zákazníci jako osvědčený postup k zajištění důvěrnosti a integritu dat. Je přehledné pro zákazníky, nakonfigurovat jejich cloudových služeb Azure používat protokol SSL k ochraně komunikace z Internetu a i mezi jejich Azure hostované virtuální počítače.

**Redundance datového** -Microsoft zajišťuje ochranu dat, pokud je cyberattack nebo fyzickému poškození do datového centra. Zákazníci zvolit:

- v zemi úložiště z hlediska dodržování předpisů nebo latence
- úložiště na více systémů země pro účely obnovení zabezpečení nebo po havárii

Může být replikovány v rámci vybrané geografické oblasti pro redundanci dat, ale se neodesílá mimo něj. Zákazníci, mají několik možností pro replikaci dat, včetně počtu kopií a počet a umístění datových center replikace.

Při vytváření účtu úložiště, musíte vybrat jednu z těchto možností replikace:

- Místně redundantní úložiště (LRS). Místně redundantní úložiště udržuje tři kopie dat. LRS se replikuje třikrát v rámci jednoho zařízení v jedné oblasti. LRS chrání vaše data před běžnými výpadky hardwaru, ale ne před výpadkem celého zařízení.
- Zónově redundantní úložiště (ZRS). Zónově redundantní úložiště udržuje tři kopie dat. ZRS se replikuje třikrát v zařízení dvě až tři umožňující větší odolnost LRS. V jedné oblasti nebo v rámci dvou oblastí, dojde k replikaci. ZRS zajistí, aby vaše data byla odolná v jedné oblasti.
- Geograficky redundantní úložiště (GRS). Při vytváření účtu úložiště se automaticky vybere geograficky redundantní úložiště. GRS udržuje šest kopií dat. S GRS data se replikují třikrát v rámci primární oblasti. Vaše data také se replikuje třikrát v sekundární oblasti stovky miles od primární oblasti, takže poskytuje nejvyšší úroveň odolnosti. V případě selhání primární oblasti převezme služby pro Azure Storage sekundární oblast. GRS zajistí, aby vaše data byla odolná ve dvou oblastech.

**Odstraňování dat** – Pokud zákazníci odstranit data, nebo ponechejte Azure, Microsoft dodržuje striktní standardy pro přepsání prostředky úložiště před opakované použití, stejně jako fyzický odstraňování vyřazení hardwaru. Microsoft provede kompletní odstranění dat na žádost zákazníka a na ukončení smlouvy.

## <a name="customer-data-ownership"></a>Vlastnictví data zákazníků
Microsoft není zkontrolovat, schválit nebo monitorování aplikací, které zákazníci nasadit do Azure. Kromě toho Microsoft nebude vědět, co druh dat zákazníci zvolit uložení do Azure. Microsoft si nenárokuje vlastnictví dat. přes uvedené informace o zákazníkovi, který je zadán do Azure.

## <a name="records-management"></a>Správa záznamů
Azure má vytvořit interní záznamy uchování požadavky pro data v back-end. Je zodpovědná za identifikaci vlastní uchování záznamu požadavky zákazníků. Záznamy uložené v Azure zákazník zodpovídá za extrahování svoje data a zachovat obsah mimo Azure na dobu uchování zadaná zákazníka.

Azure poskytuje zákazník možnost exportu dat a auditovat sestavy z produktu. Exporty se uloží místně do uchovávat informace pro uchování se zákazník definovaný časové období.

## <a name="electronic-discovery-e-discovery"></a>Elektronické zjišťování (e-discovery)
Je zodpovědná za soulad s požadavky na elektronické zjišťování v jejich používání služeb Azure Azure zákazníků. Pokud Azure zákazníků musí zachovat svá data zákazníků, se mohou exportovat a uložit data v místním počítači. Kromě toho zákazníkům může požádat o export svá data z oddělení zákaznické podpory Azure. Kromě toho, že zákazníci export svá data, provede Azure rozsáhlé protokolování a monitorování interně.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Azure zařízení, místní a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Zabezpečení funkce Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Azure produkční operace a Správa](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integritu infrastruktury Azure](azure-infrastructure-integrity.md)
