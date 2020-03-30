---
title: Sdílená odpovědnost v cloudu – Microsoft Azure
description: Seznamte se s modelem sdílené odpovědnosti a tím, které úkoly zabezpečení zpracovává poskytovatel cloudu a které úkoly zpracováváte vy.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518366"
---
# <a name="shared-responsibility-in-the-cloud"></a>Sdílená odpovědnost v cloudu

Při zvažování a vyhodnocování veřejných cloudových služeb je důležité porozumět modelu sdílené odpovědnosti a tomu, které úkoly zabezpečení zpracovává poskytovatel cloudu a které úkoly zpracováváte vy. Odpovědnost za pracovní vytížení se liší v závislosti na tom, zda je úloha hostovaná na software jako služba (SaaS), platformě jako službě (PaaS), infrastruktuře jako službě (IaaS) nebo v místním datovém centru

## <a name="division-of-responsibility"></a>Rozdělení odpovědnosti
V místním datovém centru vlastníte celý zásobník. Při přechodu do cloudu se některé povinnosti přenesou do Microsoftu. Následující diagram znázorňuje oblasti odpovědnosti mezi vámi a společností Microsoft podle typu nasazení zásobníku.

![Zóny odpovědnosti](./media/shared-responsibility/shared-responsibility.png)

U všech typů cloudového nasazení jste vlastníkem dat a identit vy. Jste zodpovědní za ochranu zabezpečení vašich dat a identit, místních prostředků a cloudových součástí, které řídíte (což se liší podle typu služby).

Bez ohledu na typ nasazení si vždy ponecháte následující povinnosti:

- Data
- Koncové body
- Účet
- Správa přístupu

## <a name="cloud-security-advantages"></a>Výhody zabezpečení cloudu
Cloud nabízí významné výhody pro řešení dlouhodobých problémů v oblasti zabezpečení informací. V místním prostředí mají organizace pravděpodobně k dispozici nesplněné povinnosti a omezené prostředky, které mohou investovat do zabezpečení, což vytváří prostředí, ve kterém útočníci mohou zneužít chyby zabezpečení ve všech vrstvách.

Následující diagram znázorňuje tradiční přístup, kdy mnoho odpovědností za zabezpečení není splněno z důvodu omezených prostředků. V přístupu s podporou cloudu můžete přesunout každodenní povinnosti zabezpečení na svého poskytovatele cloudu a přerozdělit vaše prostředky.

![Výhody zabezpečení cloudové éry](./media/shared-responsibility/cloud-enabled-security.png)

V přístupu s podporou cloudu můžete také využít cloudové možnosti zabezpečení pro větší efektivitu a používat cloudové inteligence ke zlepšení detekce hrozeb a doby odezvy. Přesunutím odpovědnosti na poskytovatele cloudu mohou organizace získat větší pokrytí zabezpečení, což jim umožní přerozdělit zdroje zabezpečení a rozpočet na jiné obchodní priority.

## <a name="next-steps"></a>Další kroky
Další informace o rozdělení odpovědnosti mezi vámi a Microsoftem v nasazení SaaS, PaaS a IaaS najdete v [tématu Sdílené odpovědnosti za cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).
