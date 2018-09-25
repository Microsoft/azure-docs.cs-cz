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
online version: https://docs.microsoft.com/powershell/module/azurerm.frontdoor/new-azurermfrontdoorloadbalancingsettingobject
schema: 2.0.0
ms.openlocfilehash: 3bea921d66d974c1aa0894f79339d4bbd6491432
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047476"
---
# Nové AzureRmFrontDoorLoadBalancingSettingObject
Vytvoření PSLoadBalancingSetting objektu pro vytváření branou

## SYNTAXE

```
New-AzureRmFrontDoorLoadBalancingSettingObject -Name <String> [-SampleSize <Int32>]
 [-SuccessfulSamplesRequired <Int32>] [-AdditionalLatencyInMilliseconds <Int32>]
 [-DefaultProfile <IAzureContextContainer>] [<CommonParameters>]
```

## POPIS
Vytvoření PSLoadBalancingSetting objektu pro vytváření branou

## PŘÍKLADY

### Příklad 1
```powershell
PS C:\> New-AzureRmFrontDoorLoadBalancingSettingObject -Name "loadbalancingsetting1"


SampleSize                    : 4
AdditionalLatencyMilliseconds : 0
SuccessfulSamplesRequired     : 2
ResourceState                 :
Id                            :
Name                          : loadbalancingsetting1
Type                          :
```

Vytvoření PSLoadBalancingSetting objektu pro vytváření branou

## PARAMETRY

### -AdditionalLatencyInMilliseconds
Další latence v milisekundách pro testy spadá do nejnižší latenci oblasti. Výchozí hodnota je 0

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

### – Název
Název nastavení sondy stavu.

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

### -Hodnotu SampleSize
Počet vzorků k zohlednění pro načtení vyrovnávání rozhodnutí.
Výchozí hodnota je 4

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

### -SuccessfulSamplesRequired
Počet vzorků v rámci ukázkové období, ve kterém musí být úspěšně výchozí hodnota je 2

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

### CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable. Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
