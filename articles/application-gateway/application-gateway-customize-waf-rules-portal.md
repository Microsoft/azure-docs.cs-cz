---
title: Přizpůsobení pravidel firewallu webových aplikací ve službě Azure Application Gateway – Azure portal | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak přizpůsobení pravidel firewallu webových aplikací ve službě Application Gateway pomocí webu Azure portal.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: victorh
ms.openlocfilehash: 30df26dc3a9697d3435779f91c32b2d99a747b88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990463"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Přizpůsobení pravidel firewallu webových aplikací na webu Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI](application-gateway-customize-waf-rules-cli.md)

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

**Nastavení brány webových aplikací** okno umožňuje filtrovat pravidla pomocí fulltextového vyhledávání. Výsledek se zobrazí pouze skupiny pravidel a pravidel, které obsahují text, kterého jste hledali.

![Hledat pravidla][2]

## <a name="disable-rule-groups-and-rules"></a>Zakázat pravidlo skupiny a pravidel

Při vaší při zakázání pravidla, můžete zakázat skupinu celé pravidlo nebo konkrétními pravidly v rámci jedné nebo několika skupin pravidel. 

**Chcete-li zakázat pravidlo skupiny nebo konkrétního pravidla**

   1. Hledat pravidla nebo skupiny pravidel, které chcete zakázat.
   2. Zrušte zaškrtnutí políček u pravidla, která chcete zakázat. 
   2. Vyberte **Uložit**. 

![Uložit změny][3]

## <a name="next-steps"></a>Další postup

Po dokončení konfigurace zakázaná pravidla se dozvíte, jak si chcete zobrazit protokoly WAF. Další informace najdete v tématu [diagnostice služby Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
