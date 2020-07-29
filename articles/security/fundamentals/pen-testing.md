---
title: Testování pera | Microsoft Docs
description: Tento článek poskytuje přehled procesu testování průniku (pentest) a o tom, jak se pentest na vaše aplikace běžící v infrastruktuře Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: db6e25b6304ee9ac41ca95d5a3a6eac0e91eb41b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287772"
---
# <a name="penetration-testing"></a>Testování průniku
Jednou z výhod používání Azure pro testování a nasazení aplikací je, že můžete rychle vytvořit prostředí vytvořená. Nemusíte se starat o přemístění, získání a "rackování a skládání" vašeho vlastního místního hardwaru.

To je skvělé – ale pořád je potřeba zajistit, abyste měli jistotu, že byste měli provést běžnou opatrnost v souvislosti s bezpečností. Jednou z věcí, které pravděpodobně chcete udělat, je testování aplikací, které nasazujete v Azure.

Možná už víte, že Microsoft provádí [testování průniku našeho prostředí Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). To pomáhá řídit vylepšení Azure.

Nemůžeme vám testovat vaši aplikaci, ale chápeme, že budete chtít, abyste mohli provádět testování na svých vlastních aplikacích. To je dobré, protože když vylepšujete zabezpečení vašich aplikací, pomůžete zajistit vyšší zabezpečení celého ekosystému Azure.

Od 15. června 2017 už společnost Microsoft nepotřebuje předběžné schválení k provedení testu průniku na prostředky Azure. Zákazníci, kteří chtějí formálně zdokumentovat nadcházející testování průniku proti Microsoft Azure, jsou doporučovány k vyplnění [formuláře oznámení testování průniku služeb Azure](https://portal.msrc.microsoft.com/en-us/engage/pentest). Tento proces se vztahuje pouze na Microsoft Azure a neplatí pro žádnou jinou službu Microsoft Cloud.

>[!IMPORTANT]
>Zatímco oznamování společnosti Microsoft pro testování pera již nejsou vyžadovány, zákazníci musí stále splňovat pravidla pro [testování průniku v rámci služby Engagement, Microsoft Cloud sjednocení](https://technet.microsoft.com/mt784683).

Mezi standardní testy, které můžete provádět:

* Testuje vaše koncové body, aby se odkryla [5 hlavních chyb zabezpečení projektu webové aplikace (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) .
* [Přibližné testování](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) vašich koncových bodů
* [Kontrola portů](https://en.wikipedia.org/wiki/Port_scanner) v koncových bodech

## <a name="next-steps"></a>Další kroky

- Pokud chcete formálně zdokumentovat nadcházející testování průniku s vašimi aplikacemi, které jsou hostované ve službě Microsoft Azure, přejděte na [pravidla testování průniku zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) a vyplňte formulář pro testování oznámení.
