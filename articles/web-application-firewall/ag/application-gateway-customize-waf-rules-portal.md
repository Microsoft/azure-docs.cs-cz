---
title: Přizpůsobení pravidel pomocí portálu – Brána firewall pro webové aplikace Azure
description: Tento článek obsahuje informace o tom, jak přizpůsobit pravidla brány firewall webových aplikací v application gateway s portálem Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048383"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Přizpůsobení pravidel brány firewall webových aplikací pomocí portálu Azure

Brána webových aplikací Azure Application Gateway (WAF) poskytuje ochranu webových aplikací. Tyto ochrany jsou poskytovány základní sadou pravidel projektu Zabezpečení otevřených webových aplikací (OWASP) (CRS). Některá pravidla mohou způsobit falešně pozitivní výsledky a blokovat skutečný provoz. Z tohoto důvodu aplikace Gateway poskytuje možnost přizpůsobit skupiny pravidel a pravidla. Další informace o konkrétních skupinách pravidel a pravidlech naleznete v [tématu Seznam skupin a pravidel souborů crs](application-gateway-crs-rulegroups-rules.md)brány FIREWALL webové aplikace .

>[!NOTE]
> Pokud vaše aplikační brána nepoužívá úroveň WAF, zobrazí se v pravém podokně možnost upgradu aplikační brány na úroveň WAF. 

![Povolit WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Zobrazení skupin pravidel a pravidel

**Zobrazení skupin pravidel a pravidel**
1. Přejděte k bráně aplikace a vyberte **bránu firewall webové aplikace**.  
2. Vyberte **zásady WAF**.
2. Vyberte **spravovaná pravidla**.

   Toto zobrazení zobrazuje tabulku na stránce všech skupin pravidel, které jsou součástí zvolené sady pravidel. Jsou zaškrtnuta všechna zaškrtávací políčka pravidla.

## <a name="disable-rule-groups-and-rules"></a>Zakázání skupin pravidel a pravidel

> [!IMPORTANT]
> Buďte opatrní při zakázání skupin pravidel nebo pravidel. To vás může vystavit zvýšeným bezpečnostním rizikům.

Při zakázání pravidel můžete zakázat celou skupinu pravidel nebo určitá pravidla v rámci jedné nebo více skupin pravidel. 

**Zakázání skupin pravidel nebo konkrétních pravidel**

   1. Vyhledejte pravidla nebo skupiny pravidel, které chcete zakázat.
   2. Zaškrtněte políčka u pravidel, která chcete zakázat. 
   3. Vyberte akci v horní části stránky (povolit/zakázat) pro vybraná pravidla.
   2. Vyberte **Uložit**. 

![Uložit změny][3]

## <a name="mandatory-rules"></a>Závazná pravidla

Následující seznam obsahuje podmínky, které způsobují WAF blokovat požadavek v režimu prevence. V režimu zjišťování jsou zaznamenány jako výjimky.

Nelze je konfigurovat ani zakázat:

* Pokud se nepodaří analyzovat tělo požadavku, bude požadavek blokován, pokud není vypnutá kontrola těla (XML, JSON, data formuláře)
* Tělo požadavku (bez souborů) délka dat je větší než nakonfigurovaný limit
* Tělo požadavku (včetně souborů) je větší než limit
* V motoru WAF došlo k vnitřní chybě.

Specifické pro CRS 3.x:

* Překročení limitu vstupní anomálie

## <a name="next-steps"></a>Další kroky

Po konfiguraci zakázaných pravidel se můžete dozvědět, jak zobrazit protokoly WAF. Další informace naleznete v tématu [Diagnostika aplikační brány](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
