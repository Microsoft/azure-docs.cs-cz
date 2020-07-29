---
title: WAF on Azure Application Gateway – přehled ochrany bot
titleSuffix: Azure Web Application Firewall
description: Tento článek poskytuje přehled firewallu webových aplikací (WAF) na Application Gateway ochraně bot
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 05/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e01f9ac8966223e11ad218af7bf6fbb2462f28f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83714895"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Firewall webových aplikací Azure v Azure Application Gateway – přehled ochrany bot

Přibližně 20% veškerého internetového provozu pochází ze špatné robotyy. Dělají věci, jako je například vyřazení, vyhledávání a hledání ohrožení zabezpečení ve vaší webové aplikaci. Když se tyto roboty zastaví na firewallu webových aplikací (WAF), nemůžou vás sami proútočit. Nemůžou také používat vaše prostředky a služby, jako jsou vaše back-endy a jiná základní infrastruktura.

Můžete povolit sadu pravidel spravované ochrany robotů pro WAF k blokování nebo protokolování požadavků ze známých škodlivých IP adres. IP adresy se naúčtují z informačního kanálu Microsoft Threat Intelligence. Intelligent Security Graph využívá Microsoft Threat Intelligence a používá ho víc služeb, včetně Azure Security Center.

> [!IMPORTANT]
> Sada pravidel ochrany robota je aktuálně ve verzi Public Preview a poskytuje verzi Preview smlouvy o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobné informace o Microsoft Azure verzích Preview najdete v tématu s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)   .

## <a name="use-with-owasp-rulesets"></a>Použití s OWASP RuleSets

RuleSet ochranu robota můžete použít vedle kteréhokoli OWASP RuleSets (2.2.9, 3,0 a 3,1). V daném okamžiku může být použita pouze jedna OWASP RuleSet. RuleSet ochrany robota obsahuje další pravidlo, které se zobrazí ve vlastním RuleSet. Má podobu **Microsoft_BotManagerRuleSet_0.1**a můžete ho povolit nebo zakázat podobně jako ostatní pravidla OWASP.

![Robot RuleSet](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>RuleSet aktualizace

Seznam známých neplatných IP adres RuleSet v robotu se aktualizuje několikrát za den z informačního kanálu Microsoft Threat Intelligence, aby zůstal synchronizovaný s roboty. Webové aplikace jsou průběžně chráněny i v případě, že se změní vektory útoku robota.

## <a name="log-example"></a>Příklad protokolu

Tady je příklad položky protokolu pro ochranu robota:

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>Další kroky

- [Konfigurace ochrany bot pro Firewall webových aplikací v Azure Application Gateway (Preview)](bot-protection.md)
