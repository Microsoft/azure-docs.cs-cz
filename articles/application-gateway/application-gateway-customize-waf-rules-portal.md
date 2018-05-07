---
title: Přizpůsobení pravidel brány firewall webových aplikací v Azure Application Gateway - portálu Azure | Microsoft Docs
description: Tento článek obsahuje informace o tom, jak přizpůsobit webovou pravidla aplikace brány firewall v Application Gateway pomocí portálu Azure.
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
ms.openlocfilehash: ae61e3a8308e95c16ccde71de37fb10666ef0df9
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Přizpůsobení pravidel brány firewall webových aplikací prostřednictvím portálu Azure

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Azure Application Gateway brány firewall webových aplikací (firewall webových aplikací) poskytuje ochranu pro webové aplikace. Tyto ochrany jsou poskytovány pomocí aplikace otevřete webový projekt zabezpečení (OWASP) základní pravidlo nastavit CRS (). Některá pravidla můžete způsobit falešně pozitivních zjištění a blokování skutečné provozu. Z tohoto důvodu Application Gateway poskytuje schopnost přizpůsobit skupiny pravidla a pravidla. Další informace o konkrétní pravidlo skupiny a pravidel najdete v tématu [seznam webových aplikací brány firewall řádku pravidlo skupiny a pravidel](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Pokud svoji službu application gateway nepoužívá vrstvě firewall webových aplikací, se zobrazí v pravém podokně možnost upgradu služby application gateway k vrstvě firewall webových aplikací. 

![Povolit firewall webových aplikací][fig1]

## <a name="view-rule-groups-and-rules"></a>Zobrazit skupiny pravidla a pravidla

**Chcete-li zobrazit skupiny pravidla a pravidla**
   1. Přejděte do služby application gateway a potom vyberte **brány firewall webových aplikací**.  
   2. Vyberte **rozšířeného pravidla konfigurace**.  
   Toto zobrazení uvádí tabulku na stránce skupiny pravidel zadat sadou vybrané pravidlo. Jsou vybrány všechny pravidla zaškrtávacích políček.

![Konfigurace zakázaná pravidla][1]

## <a name="search-for-rules-to-disable"></a>Vyhledejte pravidla mají být zakázána

**Webové aplikace, nastavení brány firewall** okno poskytuje možnost filtrovat pravidla prostřednictvím fulltextové vyhledávání. Výsledek zobrazí pouze skupiny pravidla a pravidla, které obsahují text, který jste hledali.

![Vyhledejte pravidla][2]

## <a name="disable-rule-groups-and-rules"></a>Zakázat pravidlo skupiny a pravidel

Pokud vaše jste zakázání pravidla, můžete zakázat skupinu celé pravidlo nebo konkrétní pravidla v rámci jedné nebo více skupin pravidlo. 

**Chcete-li zakázat pravidlo skupiny nebo konkrétní pravidla**

   1. Vyhledat pravidla nebo skupiny pravidel, které chcete zakázat.
   2. Zrušte zaškrtnutí políčka pro pravidla, která chcete zakázat. 
   2. Vyberte **Uložit**. 

![Uložit změny][3]

## <a name="next-steps"></a>Další postup

Po dokončení konfigurace zakázaná pravidla, můžete naučit k zobrazení protokolů firewall webových aplikací. Další informace najdete v tématu [diagnostics Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
