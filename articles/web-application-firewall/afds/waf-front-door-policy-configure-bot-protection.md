---
title: Konfigurace ochrany robotů pro Firewall webových aplikací pomocí služby Azure front-dveří (Preview)
description: Zjistěte, jak Firewall webových aplikací (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76934654"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurace ochrany bot pro Firewall webových aplikací (Preview)
V tomto článku se dozvíte, jak nakonfigurovat pravidlo ochrany robota v bráně firewall webových aplikací Azure (WAF) pro přední dveře pomocí Azure Portal. Pravidlo ochrany robota se dá nakonfigurovat taky pomocí šablony CLI, Azure PowerShell nebo Azure Resource Manager.

> [!IMPORTANT]
> Sada pravidel ochrany robota je aktuálně ve verzi Public Preview a poskytuje verzi Preview smlouvy o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Podle pokynů popsaných v tématu [Vytvoření zásad WAF pro přední dveře Azure pomocí Azure Portal](waf-front-door-create-portal.md)vytvořte základní zásady WAF pro přední dveře.

## <a name="enable-bot-protection-rule-set"></a>Povolit sadu pravidel ochrany bot

Na stránce **spravovaná pravidla** při vytváření zásad brány firewall webových aplikací, první části najít **sadu spravovaných pravidel** zaškrtněte políčko u pravidla **Microsoft_BotManager_1 0** z rozevírací nabídky a potom vyberte **zkontrolovat + vytvořit**.

   ![Pravidlo ochrany robota](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Další kroky

- Naučte se [monitorovat WAF](waf-front-door-monitor.md).
