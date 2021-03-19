---
title: Konfigurace aplikace bot Protection pro Firewall webových aplikací Azure (WAF)
description: Přečtěte si, jak nakonfigurovat ochranu robota pro Firewall webových aplikací (WAF) v Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "73516861"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Konfigurace ochrany před roboty pro Firewall webových aplikací ve službě Azure Application Gateway (Preview)

V tomto článku se dozvíte, jak nakonfigurovat pravidlo ochrany robota v bráně firewall webových aplikací Azure (WAF) pro Application Gateway pomocí Azure Portal. 

Pro WAF můžete povolit spravovanou sadu pravidel ochrany před roboty, která bude blokovat nebo protokolovat požadavky ze známých škodlivých IP adres. Zdrojem těchto IP adres je kanál analýzy hrozeb Microsoftu. Analýza hrozeb Microsoftu využívá systém Intelligent Security Graph, který využívá celá řada služeb, včetně služby Azure Security Center.

> [!NOTE]
> Sada pravidel ochrany robota je aktuálně ve verzi Public Preview a poskytuje verzi Preview smlouvy o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobné informace o Microsoft Azure verzích Preview najdete v tématu s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)   .

## <a name="prerequisites"></a>Předpoklady

Pomocí pokynů popsaných v tématu [Vytvoření zásad firewallu webových aplikací pro Application Gateway](create-waf-policy-ag.md)vytvořte základní zásady WAF pro Application Gateway.

## <a name="enable-bot-protection-rule-set"></a>Povolit sadu pravidel ochrany bot

1. Na stránce **základní** zásady, kterou jste vytvořili dříve, v části **Nastavení** vyberte **pravidla**.  

2. Na stránce podrobností v části  **Spravovat pravidla** v   rozevírací nabídce zaškrtněte políčko pro pravidlo ochrany robota a pak vyberte **Uložit**.

> [!div class="mx-imgBorder"]
> ![Ochrana před roboty](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Další kroky

Další informace o vlastních pravidlech najdete v tématu [vlastní pravidla pro webový Application firewall V2 v Azure Application Gateway](custom-waf-rules-overview.md).