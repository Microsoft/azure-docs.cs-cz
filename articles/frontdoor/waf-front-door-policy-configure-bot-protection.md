---
title: Konfigurace ochrany bot za firewall webových aplikací s Azure branou (Preview)
description: Přečtěte si firewall webových aplikací (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481621"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurace ochrany bot za firewall webových aplikací (Preview)
Tento článek ukazuje postup při konfiguraci pravidla ochrany bot ve firewallu webových aplikací (WAF) pro branou pomocí rozhraní příkazového řádku Azure, Azure Powershellu nebo Azure Resource Manageru šablony.

Sadu pravidel ochrany spravované Bot je možné povolit pro svou bránu WAF provést vlastní akce na požadavky známé škodlivé IP adresy. IP adresy pocházejí z analýzy hrozeb Microsoft informačního kanálu. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) využívá Microsoft analýzu hrozeb a používá víc služeb, včetně Azure Security Center.

> [!IMPORTANT]
> Sada pravidel ochrany bot je aktuálně ve verzi public preview a je k dispozici ve verzi preview smlouvu o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Vytvoření základní zásady WAF pro branou podle pokynů v tématu [vytvořit zásadu WAF pro Azure branou pomocí webu Azure portal](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Povolit sadu pravidel ochrany robota

1. Na stránce základní zásady, kterou jste vytvořili v předchozí části, v části **nastavení**, klikněte na tlačítko **pravidla**.
2. Na stránce podrobností pod **spravovat pravidla** části, z rozevírací nabídky, zaškrtněte políčko u pravidla **BotProtection. ve verzi preview 0,1**a pak vyberte **Uložit**výše.
    
   ![Pravidla ochrany robota](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [monitorování WAF](waf-front-door-monitor.md).
