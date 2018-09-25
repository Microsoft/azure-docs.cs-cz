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
online version: https://docs.microsoft.com/powershell/module/azurerm.frontdoor/remove-azurermfrontdoor
schema: 2.0.0
ms.openlocfilehash: ddc6a40521b951c8083ac185368621ea792b1c85
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047477"
---
# Odebrat AzureRmFrontDoor
Odebrání nástroje pro vyrovnávání zatížení branou

## SYNTAXE

### ByFieldsParameterSet (výchozí)
```
Remove-AzureRmFrontDoor -ResourceGroupName <String> -Name <String> [-PassThru]
 [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### ByObjectParameterSet
```
Remove-AzureRmFrontDoor -InputObject <PSFrontDoor> [-PassThru] [-DefaultProfile <IAzureContextContainer>]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### ResourceIdParameterSet
```
Remove-AzureRmFrontDoor -ResourceId <String> [-PassThru] [-DefaultProfile <IAzureContextContainer>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
```

## POPIS
**Odebrat AzureRmFrontDoor** rutina odebere nástroj pro vyrovnávání zatížení branou v rámci aktuálního předplatného

## PŘÍKLADY

### Příklad 1: Odebrání "frontdoor1" v prostředku skupiny "rg1" v rámci aktuálního předplatného.
```powershell
PS C:\> Remove-AzureRmFrontDoor -Name "frontdoor1" -ResourceGroupName "rg1"
```

Odeberte "frontdoor1" v prostředku skupiny "rg1" v rámci aktuálního předplatného.

### Příklad 2: Odebrání všech FrontDoors v prostředku skupiny "rg1" v rámci aktuálního předplatného.
```powershell
PS C:\> Get-AzureRmFrontDoor -ResourceGroupName "rg1" | Remove-AzureRmFrontDoor
```

Odeberte všechny FrontDoors v prostředku skupiny "rg1" v rámci aktuálního předplatného.

### Příklad 3: Odebrání všech FrontDoors v rámci aktuálního předplatného.
```powershell
PS C:\> Get-AzureRmFrontDoor | Remove-AzureRmFrontDoor
```

Odeberte všechny FrontDoors v rámci aktuálního předplatného.

### Příklad 4: Odebrání všech FrontDoors s názvem "frontdoor1" v rámci aktuálního předplatného.
```powershell
PS C:\> Remove-AzureRmFrontDoor -Name "frontdoor1" | Remove-AzureRmFrontDoor
```

Odeberte všechny FrontDoors s názvem "frontdoor1" v rámci aktuálního předplatného.

## PARAMETRY

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

### -InputObject
Objekt přední dveře k odstranění.

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSFrontDoor
Parameter Sets: ByObjectParameterSet
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: True (ByValue)
Accept wildcard characters: False
```

### – Název
Název přední dveře k lehčí a odstranit.

```yaml
Type: System.String
Parameter Sets: ByFieldsParameterSet
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -PassThru
Vrátí objekt (Pokud je zadaný).

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### – Název skupiny prostředků
Skupina prostředků, do které patří branou.

```yaml
Type: System.String
Parameter Sets: ByFieldsParameterSet
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### – ID prostředku
Id prostředku přední dveře k lehčí a odstranění

```yaml
Type: System.String
Parameter Sets: ResourceIdParameterSet
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### – Potvrzení
Vyzve k potvrzení před spuštěním rutiny.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -WhatIf
Ukazuje, co by se stalo při spuštění rutiny.
Rutina není spuštěna.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable. Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
