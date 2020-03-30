---
title: Vytvoření a použití vlastních pravidel v2
titleSuffix: Azure Web Application Firewall
description: Tento článek obsahuje informace o tom, jak vytvořit vlastní pravidla brány firewall webové aplikace (WAF) v2 v bráně Aplikace Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: bfa6690c636e15fa933f50698cd81359600b5c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368303"
---
# <a name="create-and-use-web-application-firewall-v2-custom-rules-on-application-gateway"></a>Vytvoření a použití vlastních pravidel brány webových aplikací v2 v bráně aplikace

Brána firewall webových aplikací (WAF) v2 v bráně aplikace Azure poskytuje ochranu webových aplikací. Tato ochrana je poskytována základní sadou pravidel projektu Zabezpečení otevřených webových aplikací (OWASP) (CRS). V některých případech může být nutné vytvořit vlastní pravidla, která vyhovují vašim konkrétním potřebám. Další informace o vlastních pravidlech WAF naleznete [v tématu Přehled vlastních pravidel brány firewall webových aplikací](custom-waf-rules-overview.md).

Tento článek ukazuje některé příklady vlastní pravidla, která můžete vytvořit a použít s v2 WAF. Informace o tom, jak nasadit WAF s vlastním pravidlem pomocí Azure PowerShellu, [najdete v tématu Konfigurace vlastních pravidel brány firewall webových aplikací pomocí Azure PowerShellu](configure-waf-custom-rules.md).

>[!NOTE]
> Pokud vaše aplikační brána nepoužívá úroveň WAF, zobrazí se v pravém podokně možnost upgradu aplikační brány na úroveň WAF.

![Povolit WAF][fig1]

## <a name="example-1"></a>Příklad 1

Víte, že je bot s názvem *evilbot,* který chcete blokovat procházení vašich webových stránek. V takovém případě zablokujete user-agent *evilbot* v záhlaví požadavku.

Logika: p

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

A tady je odpovídající JSON:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

Informace o tom, že waf nasazený pomocí tohoto vlastního pravidla, [najdete v tématu Konfigurace vlastního pravidla brány firewall webové aplikace pomocí Azure PowerShellu](configure-waf-custom-rules.md).

### <a name="example-1a"></a>Příklad 1a

Stejnou věc můžete provést pomocí regulárního výrazu:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Regex `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

A odpovídající JSON:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-2"></a>Příklad 2

Chcete povolit provoz z USA pomocí operátoru GeoMatch:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr `

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator GeoMatch `
   -MatchValue "US" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name "allowUS" `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow
```

A odpovídající JSON:

```json
  {
    "customRules": [
      {
        "name": "allowUS",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Allow",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "GeoMatch",
            "matchValues": [
              "US"
            ]
          }
        ]
      }
    ]
  }
```



## <a name="example-3"></a>Příklad 3

Chcete blokovat všechny požadavky z IP adres v rozsahu 198.168.5.0/24.

V tomto příkladu zablokujete veškerý provoz, který pochází z rozsahu IP adres. Název pravidla je *myrule1* a priorita je nastavena na 10.

Logika: p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Zde je odpovídající JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      }
    ]
  }
```

Odpovídající pravidlo CRS:`SecRule REMOTE_ADDR "@ipMatch 192.168.5.0/24" "id:7001,deny"`

## <a name="example-4"></a>Příklad 4

V tomto příkladu chcete blokovat User-Agent *evilbot*a provoz v rozsahu 192.168.5.0/24. Chcete-li to provést, můžete vytvořit dvě samostatné podmínky shody a umístit je do stejného pravidla. Tím je zajištěno, že pokud jsou spárovány *oba evilbot* v záhlaví User-Agent **a** IP adresy z rozsahu 192.168.5.0/24, pak je požadavek blokován.

Logika: p **a** q

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

Zde je odpovídající JSON:

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 10, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": false, 
              "matchValues": [ 
                "192.168.5.0/24" 
              ] 
            }, 
            { 
              "matchVariable": "RequestHeaders", 
              "selector": "User-Agent", 
              "operator": "Contains", 
              "transforms": [ 
                "Lowercase" 
              ], 
              "matchValues": [ 
                "evilbot" 
              ] 
            } 
        ] 
      } 
    ] 
  } 
```

## <a name="example-5"></a>Příklad 5

V tomto příkladu chcete blokovat, pokud je požadavek mimo rozsah IP adres *192.168.5.0/24*, nebo řetězec uživatelského agenta není *chromovaný* (což znamená, že uživatel nepoužívá prohlížeč Chrome). Vzhledem k tomu, že tato logika používá **nebo**, dvě podmínky jsou v samostatných pravidel, jak je vidět v následujícím příkladu. *myrule1* a *myrule2* oba potřebují, aby odpovídaly blokovat provoz.

Logika: **ne** (p **a** q) = **ne** p **nebo ne** q.

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

A odpovídající JSON:

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "selector": "User-Agent",
            "operator": "Contains",
            "negateCondition": true,
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "chrome"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-6"></a>Příklad 6

Chcete blokovat vlastní SQLI. Vzhledem k tomu, že logika zde používá je **nebo**, a všechny hodnoty jsou v *RequestUri*, všechny *MatchValues* může být v seznamu oddělené čárkou.

Logika: p **nebo** q **nebo** r

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri 
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1", "drop tables", "'—" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule4 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Odpovídající JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 10
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1",
              "drop tables",
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

Alternativní Azure PowerShell:

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
-Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "drop tables" `
   -NegationCondition $False

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block

$variable3 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition3 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable3 `
   -Operator Contains `
   -MatchValue "’—" `
   -NegationCondition $False

$rule3 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule3 `
   -Priority 30 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

Odpovídající JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "drop tables"
            ]
          }
        ]
      },
      {
        "name": "myrule3",
        "ruleType": "MatchRule",
        "priority": 30,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="next-steps"></a>Další kroky

Po vytvoření vlastních pravidel se můžete dozvědět, jak zobrazit protokoly WAF. Další informace naleznete v tématu [Diagnostika aplikační brány](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/create-custom-waf-rules/1.png
