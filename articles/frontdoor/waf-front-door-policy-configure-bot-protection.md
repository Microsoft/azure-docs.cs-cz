---
title: Konfigurace ochrany robotů pro Firewall webových aplikací pomocí služby Azure front-dveří (Preview)
description: Zjistěte, jak Firewall webových aplikací (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 0f36ea33badad52c55cd11491874db955df5408b
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846346"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurace ochrany bot pro Firewall webových aplikací (Preview)
V tomto článku se dozvíte, jak nakonfigurovat pravidlo ochrany bot v bráně firewall webových aplikací Azure (WAF) pro přední dveře pomocí Azure CLI, Azure PowerShell nebo šablony Azure Resource Manager.

Sada pravidel spravované ochrany robotů může být povolená pro WAF, aby mohla přijímat vlastní akce na žádostech ze známých škodlivých IP adres. IP adresy se naúčtují z informačního kanálu Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) využívá Microsoft Threat Intelligence a používá ho víc služeb, včetně Azure Security Center.

> [!IMPORTANT]
> Sada pravidel ochrany robota je aktuálně ve verzi Public Preview a poskytuje verzi Preview smlouvy o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Podle pokynů popsaných v tématu [Vytvoření zásad WAF pro přední dveře Azure pomocí Azure Portal](waf-front-door-create-portal.md)vytvořte základní zásady WAF pro přední dveře.

## <a name="enable-bot-protection-rule-set"></a>Povolit sadu pravidel ochrany bot

1. Na stránce základní zásady, kterou jste vytvořili v předchozí části, klikněte v části **Nastavení**na **pravidla**.
2. Na stránce s podrobnostmi v části **Spravovat pravidla** v rozevírací nabídce zaškrtněte políčko pravidlo **BotProtection-Preview-0,1**a potom vyberte **Uložit** výše.
    
   ![Pravidlo ochrany robota](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Další postup

- Naučte se [monitorovat WAF](waf-front-door-monitor.md).
