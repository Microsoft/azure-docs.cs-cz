---
title: Služba Azure branou – referenční informace prostředí PowerShell | Dokumentace Microsoftu
description: Tento odkaz na podrobnosti o různých rutin Powershellu nepodporuje branou služby Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2018
ms.author: sharadag
Module Name: AzureRM.FrontDoor
online version: https://docs.microsoft.com/powershell/module/azurerm.frontdoor/new-azurermfrontdoorcustomruleobject
schema: 2.0.0
ms.openlocfilehash: 498751617625de42c6b0de04d2657076d7758121
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047307"
---
# Nové AzureRmFrontDoorCustomRuleObject
Vytvořit CustomRule objekt pro vytváření zásad WAF

## SYNTAXE

```
New-AzureRmFrontDoorCustomRuleObject -Name <String> -RuleType <PSCustomRuleType>
 -MatchCondition <PSMatchCondition[]> -Action <PSAction> -Priority <Int32>
 [-RateLimitDurationInMinutes <Int32>] [-RateLimitThreshold <Int32>] [-Transform <String[]>]
 [-DefaultProfile <IAzureContextContainer>] [<CommonParameters>]
```

## POPIS
Vytvořit CustomRule objekt pro vytváření zásad WAF

## PŘÍKLADY

### Příklad 1
```powershell
PS C:\> New-AzureRmFrontDoorCustomRuleObject -Name "Rule1" -RuleType MatchRule -MatchCondition $matchCondition1 -Action Block -Priority 2

RuleType                   : MatchRule
Action                     : Block
MatchConditions            : {Microsoft.Azure.Commands.FrontDoor.Models.PSMatchCondition}
Priority                   : 2
RateLimitDurationInMinutes : 1
RateLimitThreshold         :
Name                       : Rule1
Etag                       :
Transforms                 :
```

Vytvořte objekt CustomRule

## PARAMETRY

### -Akce
Typ akce.
Možné hodnoty zahrnují: 'Povolit', "Block", "protokolu.

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSAction
Parameter Sets: (All)
Aliases:
Accepted values: Allow, Block, Log

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -DefaultProfile
Pověření, účet, tenanta a předplatné, které slouží ke komunikaci s Azure.

```yaml
Type: Microsoft.Azure.Commands.Common.Authentication.Abstractions.IAzureContextContainer
Parameter Sets: (All)
Aliases: AzureRmContext, AzureCredential

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -MatchCondition
Seznam podmínek shody.

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSMatchCondition[]
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### – Název
Název pravidla

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Priority
Popisuje prioritu pravidla.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -RateLimitDurationInMinutes
Míra limit duration. Výchozí hodnota - 1 minuta

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -RateLimitThreshold
Míra limit thresold

```yaml
Type: System.Nullable`1[System.Int32]
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Typu pravidla
Typ pravidla.
Možné hodnoty zahrnují: "MatchRule", "RateLimitRule.

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSCustomRuleType
Parameter Sets: (All)
Aliases:
Accepted values: RateLimitRule, MatchRule

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Transformace
Seznam transformací

```yaml
Type: System.String[]
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable. Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
