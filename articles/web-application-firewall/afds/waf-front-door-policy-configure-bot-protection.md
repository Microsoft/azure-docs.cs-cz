---
title: Konfigurace ochrany robotů pro bránu firewall webových aplikací pomocí předních dveří Azure (preview)
description: Naučte se bránu firewall webových aplikací (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934654"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurace ochrany botů pro bránu firewall webových aplikací (preview)
Tento článek ukazuje, jak nakonfigurovat pravidlo ochrany botů v bráně WAF (Azure Web Application Firewall) pro front door pomocí portálu Azure Portal. Pravidlo ochrany botů lze taky nakonfigurovat pomocí příkazového příkazu cli, Azure PowerShellu nebo šablony Azure Resource Manager.

> [!IMPORTANT]
> Sada pravidel ochrany botů je aktuálně ve verzi Public Preview a je vybavena předběžnou smlouvou o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Vytvořte základní zásady WAF pro přední dveře podle pokynů popsaných v části [Vytvoření zásadwaf pro Azure Front Door pomocí portálu Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Povolit sadu pravidel ochrany robota

Na stránce **Spravovaná pravidla** při vytváření zásad brány firewall webových aplikací nejprve vyhledejte oddíl **Sada spravovaných pravidel,** zaškrtněte políčko před pravidlem **Microsoft_BotManager_1.0** v rozevírací nabídce a pak vyberte **Zkontrolovat + Vytvořit**.

   ![Pravidlo ochrany botů](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [sledovat WAF](waf-front-door-monitor.md).
