---
title: Sdílená odpovědnost v cloudu – Microsoft Azure
description: Seznamte se s sdíleným modelem zodpovědnosti a o tom, které úlohy zabezpečení jsou zpracovávány poskytovatelem cloudu a které úlohy jsou zpracovávány vámi.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "72518366"
---
# <a name="shared-responsibility-in-the-cloud"></a>Sdílená odpovědnost v cloudu

Když považujete a vyhodnocujete veřejné cloudové služby, je důležité pochopit model sdílení zodpovědnosti a, které úlohy zabezpečení jsou zpracovávány poskytovatelem cloudu a které úlohy jsou zpracovávány vámi. Odpovědnosti zatížení se liší v závislosti na tom, jestli je zatížení hostované na základě softwaru jako služby (SaaS), platformy jako služby (PaaS), infrastruktury jako služby (IaaS) nebo v místním datacentru.

## <a name="division-of-responsibility"></a>Rozdělení odpovědnosti
V místním datovém centru jste vlastníkem celého zásobníku. Při přechodu do cloudu se některé zodpovědnosti přenesou do Microsoftu. Následující diagram znázorňuje oblasti zodpovědnosti mezi vámi a společností Microsoft podle typu nasazení vašeho zásobníku.

![Zóny odpovědnosti](./media/shared-responsibility/shared-responsibility.png)

U všech typů cloudového nasazení jste vlastníkem dat a identit vy. Zodpovídáte za ochranu zabezpečení vašich dat a identit, místních prostředků a součástí cloudu, které ovládáte (které se liší podle typu služby).

Bez ohledu na typ nasazení se vám vždycky zachovají následující zodpovědnosti:

- Data
- Koncové body
- Účet
- Správa přístupu

## <a name="cloud-security-advantages"></a>Výhody zabezpečení cloudu
Cloud nabízí významné výhody pro řešení problémů se zabezpečením, které jsou náročné. V místním prostředí organizace pravděpodobně mají nesplnění zodpovědnosti a omezené prostředky, které jsou k dispozici pro investici do zabezpečení, což vytváří prostředí, ve kterém můžou útočníci zneužít ohrožení zabezpečení ve všech vrstvách.

Následující diagram znázorňuje tradiční přístup, ve kterém je nesplnění mnoho bezpečnostních povinností v důsledku omezených prostředků. V rámci cloudového přístupu můžete ke svému poskytovateli cloudu přesunout zodpovědnost za den zabezpečení a znovu přidělit prostředky.

![Výhody zabezpečení cloudového prostředí](./media/shared-responsibility/cloud-enabled-security.png)

V rámci cloudového přístupu můžete také využít cloudové možnosti zabezpečení pro zajištění vyšší efektivity a využít cloudovou analýzu ke zlepšení detekce hrozeb a doby odezvy. Díky posunutí zodpovědností poskytovateli cloudu můžou organizace získat větší pokrytí zabezpečení, které jim umožní znovu přidělit prostředky a rozpočet zabezpečení jiným obchodním prioritám.

## <a name="next-steps"></a>Další kroky
Další informace o rozdělení zodpovědnosti mezi vámi a společností Microsoft v nasazení SaaS, PaaS a IaaS najdete v tématu [sdílené odpovědnosti pro cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).
