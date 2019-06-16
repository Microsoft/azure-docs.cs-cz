---
title: Přizpůsobení pravidel firewallu webových aplikací ve službě Azure Application Gateway – Azure portal
description: Tento článek obsahuje informace o tom, jak přizpůsobení pravidel firewallu webových aplikací ve službě Application Gateway pomocí webu Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: f7ffb8d6adfd4afc75618834a3fe82cf9a3d0c9f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720395"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Přizpůsobení pravidel firewallu webových aplikací na webu Azure portal

Firewall webových aplikací (WAF) Azure Application Gateway chrání webové aplikace. Tyto ochrany jsou k dispozici ve Open Web Application zabezpečení projektu (OWASP) základní pravidlo nastavte (CRS). Některá pravidla může způsobit, že počet falešně pozitivních výsledků a blokovat skutečný provoz. Z tohoto důvodu Application Gateway poskytuje schopnost přizpůsobit skupin pravidel a pravidla. Další informace o příslušné pravidlo skupiny a pravidel, naleznete v tématu [seznam skupin pravidel CRS firewallu webových aplikací a pravidla](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Pokud vaše brána application gateway úrovně WAF nepoužívá, možnost upgradovat na vrstvu waf služby application gateway se zobrazí v pravém podokně. 

![Povolit WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Zobrazení skupin pravidel a pravidla

**Chcete-li zobrazit skupiny pravidel a pravidla**
   1. Přejděte ke službě application gateway a pak vyberte **firewallu webových aplikací**.  
   2. Vyberte **Rozšířená konfigurace pravidel**.  
   Toto zobrazení uvádí tabulka na stránce skupiny pravidel součástí sady zvolené pravidlo. Jsou vybrány všechny pravidla zaškrtávacích políček.

![Konfigurace zakázaná pravidla][1]

## <a name="search-for-rules-to-disable"></a>Hledat pravidla pro zakázání

**Nastavení brány webových aplikací** stránka poskytuje možnosti filtrování pravidla pomocí fulltextového vyhledávání. Výsledek se zobrazí pouze skupiny pravidel a pravidel, které obsahují text, kterého jste hledali.

![Hledat pravidla][2]

## <a name="disable-rule-groups-and-rules"></a>Zakázat pravidlo skupiny a pravidel

> [!IMPORTANT]
> Buďte opatrní při zakázání pravidla ani pravidla skupiny. To může vystavit na zvýšená rizika zabezpečení.

Pokud se zakázání pravidla, můžete zakázat skupinu celé pravidlo nebo konkrétními pravidly v rámci jedné nebo několika skupin pravidel. 

**Chcete-li zakázat pravidlo skupiny nebo konkrétního pravidla**

   1. Hledat pravidla nebo skupiny pravidel, které chcete zakázat.
   2. Zrušte zaškrtnutí políček u pravidla, která chcete zakázat. 
   2. Vyberte **Uložit**. 

![Uložit změny][3]

## <a name="mandatory-rules"></a>Povinné pravidla

Následující seznam obsahuje podmínky, které způsobí WAF blokování požadavku v režimu ochrany před únikem informací. V režimu detekce jsou přihlášení jako výjimky.

Tyto nelze nakonfigurované nebo zakázané:

* Nepodařilo se analyzovat datovou část požadavku výsledkem požadavku blokován, pokud je text kontroly nevypnuli (XML, JSON, data formuláře)
* Délka dat požadavku textu (spolu s žádné soubory) je větší než nakonfigurovaný limit
* Text (včetně souborů) je větší než limit požadavku
* Došlo k vnitřní chybě v modulu WAF

CRS 3.x konkrétní:

* Příchozí anomálií skóre překročil prahovou hodnotu

## <a name="next-steps"></a>Další postup

Po dokončení konfigurace zakázaná pravidla se dozvíte, jak si chcete zobrazit protokoly WAF. Další informace najdete v tématu [diagnostice služby Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
