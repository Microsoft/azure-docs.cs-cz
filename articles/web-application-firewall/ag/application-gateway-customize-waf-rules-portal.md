---
title: Přizpůsobení pravidel pomocí portálu – Firewall webových aplikací Azure
description: Tento článek poskytuje informace o tom, jak přizpůsobit pravidla firewallu webových aplikací v Application Gateway pomocí Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 04/21/2021
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 0ab122d178e5390a53e5a3a39f1b7763b298dc6d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878321"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Přizpůsobení pravidel firewallu webových aplikací pomocí Azure Portal

Firewall webových aplikací (WAF) služby Azure Application Gateway poskytuje ochranu pro webové aplikace. Tyto ochrany poskytuje základní sadu pravidel OWASP (Open Web Application Security) (počítačový systém). Některá pravidla můžou způsobit falešně pozitivní a blokují skutečný provoz. Z tohoto důvodu Application Gateway poskytuje možnost přizpůsobit skupiny pravidel a pravidla. Další informace o konkrétních skupinách pravidel a pravidlech najdete v tématu [seznam skupin pravidel a pravidel pro Firewall webových aplikací](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Pokud Aplikační brána nepoužívá úroveň WAF, v pravém podokně se zobrazí možnost upgradovat aplikační bránu na úroveň WAF. 

:::image type="content" source="../media/application-gateway-customize-waf-rules-portal/1.png" alt-text="Povolit WAF"::: 

## <a name="view-rule-groups-and-rules"></a>Zobrazení skupin pravidel a pravidel

**Zobrazení skupin pravidel a pravidel**
1. Přejděte do aplikační brány a pak vyberte **Firewall webových aplikací**.  
2. Vyberte **zásady WAF**.
2. Vyberte **spravovaná pravidla**.

   Toto zobrazení ukazuje tabulku na stránce všech skupin pravidel poskytovaných s vybranou sadou pravidel. Všechna zaškrtávací políčka tohoto pravidla jsou vybrána.

## <a name="disable-rule-groups-and-rules"></a>Zakázat skupiny pravidel a pravidla

> [!IMPORTANT]
> Při zakázání libovolných skupin pravidel nebo pravidel buďte opatrní. To vám může vystavit zvýšené bezpečnostní rizika.

**Zakázání skupin pravidel nebo specifických pravidel**

   1. Vyhledejte pravidla nebo skupiny pravidel, které chcete zakázat.
   2. Zaškrtněte políčka pro pravidla, která chcete zakázat. 
   3. Vyberte akci v horní části stránky (Povolit/zakázat) pro vybraná pravidla.
   2. Vyberte **Uložit**.
    :::image type="content" source="../media/application-gateway-customize-waf-rules-portal/figure3.png" alt-text="Uložit zakázaná pravidla"::: 

## <a name="mandatory-rules"></a>Povinná pravidla

Následující seznam obsahuje podmínky, které způsobí, že WAF požadavek zablokuje v režimu prevence. V režimu detekce jsou protokolovány jako výjimky.

Nedají se nakonfigurovat nebo zakázat:

* Při analýze textu žádosti dojde k chybě v blokovaném požadavku, pokud není kontrola těla vypnuta (XML, JSON, data formuláře).
* Text požadavku (bez souborů) Délka dat je větší než nakonfigurovaný limit.
* Text žádosti (včetně souborů) je větší, než je limit.
* V modulu WAF došlo k vnitřní chybě.

Specifické pro počítačový počítač 3. x:

* Skóre příchozí anomálie překročilo prahovou hodnotu.

## <a name="next-steps"></a>Další kroky

Po konfiguraci zakázaných pravidel se můžete dozvědět, jak zobrazit protokoly WAF. Další informace najdete v tématu [diagnostika Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).