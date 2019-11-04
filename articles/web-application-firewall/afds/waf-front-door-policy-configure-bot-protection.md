---
title: Konfigurace ochrany robotů pro Firewall webových aplikací pomocí služby Azure front-dveří (Preview)
description: Zjistěte, jak Firewall webových aplikací (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 9a8901e3763b10b7ee00c10f4eec08a8325d7dec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512467"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurace ochrany bot pro Firewall webových aplikací (Preview)
V tomto článku se dozvíte, jak nakonfigurovat pravidlo ochrany bot v bráně firewall webových aplikací Azure (WAF) pro přední dveře pomocí Azure CLI, Azure PowerShell nebo šablony Azure Resource Manager.

Sada pravidel spravované ochrany robotů může být povolená pro WAF, aby mohla přijímat vlastní akce na žádostech ze známých škodlivých IP adres. IP adresy se naúčtují z informačního kanálu Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) využívá Microsoft Threat Intelligence a používá ho víc služeb, včetně Azure Security Center.

> [!IMPORTANT]
> Sada pravidel ochrany robota je aktuálně ve verzi Public Preview a poskytuje verzi Preview smlouvy o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Předpoklady

Podle pokynů popsaných v tématu [Vytvoření zásad WAF pro přední dveře Azure pomocí Azure Portal](waf-front-door-create-portal.md)vytvořte základní zásady WAF pro přední dveře.

## <a name="enable-bot-protection-rule-set"></a>Povolit sadu pravidel ochrany bot

1. Na stránce základní zásady, kterou jste vytvořili v předchozí části, klikněte v části **Nastavení**na **pravidla**.
2. Na stránce s podrobnostmi v části **Spravovat pravidla** v rozevírací nabídce zaškrtněte políčko pravidlo **BotProtection-Preview-0,1**a potom vyberte **Uložit** výše.
    
   ![Pravidlo ochrany robota](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Další kroky

- Naučte se [monitorovat WAF](waf-front-door-monitor.md).
