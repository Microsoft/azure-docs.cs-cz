---
title: "Ochrana osobních údajů v Microsoft Azure | Microsoft Docs"
description: "Tento článek by vám pomohou při použití Azure k ochraně osobních údajů a v souladu s obecné Data Protection nařízení (GDPR)"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 741fb17be315faacef6483cbaaa565136622cb45
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-in-microsoft-azure"></a>Ochrana osobních údajů v Microsoft Azure

Tento článek představuje řadu články, které vám pomohou používat Azure bezpečnostních technologií a služeb na ochranu osobních údajů. Toto je klíčovým požadavkem pro mnoho společností a dodržování předpisů a zásad správného řízení iniciativy odvětví. Například můžete informací uvedených v této série článků pro dosažení souladu s obecné Data Protection nařízení (GDPR). Scénář, problém prohlášení a společnosti cíle jsou zahrnuté v tomto poli.

## <a name="scenario-and-problem-statement"></a>Scénáře a problému

Velké výletních společnosti, centrálou ve Spojených státech amerických, je rozšířit jeho operací a nabídnout itineráře v Středomoří, Jaderského a baltský moři, jakož i Britské ostrovy. Pro podporu těchto úsilí, získala menší výletních Víceřádkový na základě v Itálii, Německo, Dánsko a Spojeném království

Společnost používá Microsoft Azure k ukládání firemních dat v cloudu. Například to může zahrnovat informace zákazníků nebo zaměstnanců:

- adresy
- telefonní čísla
- daň identifikační čísla
- informace o kreditní kartě

Společnosti musí ochrany osobních údajů zákazníků a dat při zpřístupnění dat pro tyto oddělení, které je třeba ji. (například oddělení mzdy a rezervace)

## <a name="company-goals"></a>Cíle společnosti 

- Zdroje dat, které obsahují osobní data se šifrují, pokud umístěný do cloudového úložiště.

- Během přenosu šifrovány osobní data, která se přenáší z jednoho umístění do druhého. Toto je hodnota true, pokud se data mezi podnikovém datovém centru a cloudu Azure cestě přes virtuální síť nebo přes Internet.

- Důvěrnost a integrita osobních údajů je chráněný před neoprávněným přístupem správu silné identit a technologie pro řízení přístupu.

- Osobní data je chráněný před přes porušení zabezpečení dat prostřednictvím monitorování ohrožení zabezpečení a hrozeb.

- Stav zabezpečení služby Azure, které uložení nebo na přenos osobních dat bude posouzeno identifikovat příležitosti k lepší ochraně osobních údajů.

## <a name="data-protection-guidance"></a>Pokyny pro ochranu dat

V následujících článcích obsahovat technické postupy pokyny, které vám pomohou dosáhnout cíle ochrany osobních údajů uvedených výše:

- [Azure šifrovací technologie](protect-personal-data-at-rest.md)

- [Azure šifrovací technologie](protect-personal-data-in-transit-encryption.md)

- [Azure technologie identit a přístupu](protect-personal-data-identity-access-controls.md)

- [Technologie zabezpečení sítě Azure](protect-personal-data-network-security.md)

- [Azure Security Center](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>Další postup

- [Server s informacemi o zabezpečení Azure](https://aka.ms/AzureSecInfo)

- [Centrum zabezpečení Microsoft](https://www.microsoft.com/TrustCenter/default.aspx)

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

- [Blog týmu zabezpečení Azure](https://www.azuresecurityorg)

- [Azure.com Blog - zabezpečení](https://azure.microsoft.com/blog/topics/security/)
