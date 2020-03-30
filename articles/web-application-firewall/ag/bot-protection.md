---
title: Konfigurace ochrany botů pro bránu firewall webových aplikací Azure (WAF)
description: Přečtěte si, jak nakonfigurovat ochranu robotů pro bránu WEBOVÝCH APLIKACÍ (WAF) v bráně Aplikace Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516861"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Konfigurace ochrany botů pro bránu webových aplikací v bráně Aplikace Azure (Preview)

Tento článek ukazuje, jak nakonfigurovat pravidlo ochrany robota v bráně WAF (Azure Web Application Firewall) pro aplikační bránu pomocí portálu Azure. 

Můžete povolit sadu spravovaných pravidel ochrany robota pro waf, která blokuje nebo protokoluje požadavky ze známých škodlivých IP adres. IP adresy jsou získávány z informačního kanálu Microsoft Threat Intelligence. Inteligentní graf zabezpečení pohání microsoftovou analýzu hrozeb a používá ho několik služeb včetně Azure Security Center.

> [!NOTE]
> Sada pravidel ochrany robota je aktuálně ve verzi Public Preview a je vybavena předběžnou smlouvou o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [doplňkových podmínkách použití pro Microsoft Azure Preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 

## <a name="prerequisites"></a>Požadavky

Vytvořte základní zásady WAF pro aplikační bránu podle pokynů popsaných v části [Vytvořit zásady brány firewall webových aplikací pro aplikační bránu](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Povolit sadu pravidel ochrany robota

1. Na stránce **Základní** zásady, kterou jste vytvořili dříve, vyberte v části **Nastavení** **položku Pravidla**.  

2. Na stránce podrobností v rozevírací nabídce v **rozevírací** nabídce v části Správa pravidel zaškrtněte políčko U pravidla ochrany robota a pak vyberte **Uložit**.

> [!div class="mx-imgBorder"]
> ![Ochrana před roboty](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Další kroky

Další informace o vlastních pravidlech najdete [v tématu Vlastní pravidla pro bránu firewall webových aplikací v2 v bráně aplikace Azure](custom-waf-rules-overview.md).