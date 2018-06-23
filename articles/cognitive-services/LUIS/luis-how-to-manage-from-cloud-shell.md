---
title: Zobrazit použití LEOŠ z prostředí cloudu Azure | Microsoft Docs
description: Další informace o získání informací o využití v prostředí cloudu Azure pro LEOŠ.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2017
ms.author: v-geberr
ms.openlocfilehash: 2de25645e5377efdd53bcc980695804d34db5ee2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343426"
---
# <a name="manage-luis-service-from-azure-cloud-shell"></a>Spravovat službu LEOŠ z prostředí cloudu Azure
Portál Azure umožňuje pracovat s prostředky LEOŠ pomocí rutin prostředí PowerShell. 

Tyto rutiny umožňují [vytvořit](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) odběru LEOŠ get informace o předplatném, včetně [využití](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), a [odebrat](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) předplatné. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Účet cloudového prostředí úložiště a ověřování
Chcete-li použít PowerShell na portálu Azure [cloudové prostředí](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), musíte mít účet úložiště Azure. Pokud nemáte [účet úložiště](https://docs.microsoft.com/en-us/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share), zobrazí se výzva k jeho vytvoření. Účet úložiště umožňuje uložit skripty prostředí PowerShell v prostředí cloudu.  

Budete také muset ověřování v Azure v prostředí cloudu přístup k prostředkům. 

Jakmile máte účet úložiště a ověření, můžete spustit rutiny prostředí PowerShell.

## <a name="open-cloud-shell"></a>Otevření služby Cloud Shell
Pokud používáte prostředí cloudu portálu Azure, se vždy na nejnovější verzi prostředí PowerShell. 

Použití **spustit prostředí cloudu** tlačítko otevřete prostředí pro Cloud nebo otevřete prohlížeč s [ https://shell.azure.com ](https://shell.azure.com). 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>Informace o využití LEOŠ koncový bod

Rutiny prostředí PowerShell 6.x, `Get-AzureRmCognitiveServicesAccountUsage`, poskytuje informace o kognitivní služby společnosti Microsoft, včetně LEOŠ využití. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) vyžaduje skupiny prostředků a název prostředku služby. 

Syntaxe příkazu je:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

V následujícím příkladu je název skupiny prostředků `luis-westus-rg` a je název odběru služby LEOŠ `luis-westus-1`. Oba tyto názvy jsou vybrali při vytvoření služby LEOŠ. 

Rutina vrátí informace o využití 16 přístupů do 10 000 koncový bod používán období 30 dnů s období do června 7:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Příkaz uložte jako soubor prostředí PowerShell *.ps1 v účtu úložiště Azure přidružený prostředí cloudu a spustit kdykoli. 

![Spusťte skript z úložiště](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

Jakmile skript je uložený na jednotce cloudu, můžete spustit skript prostředí PowerShell z Azure telefonní aplikace cloudové prostředí. 

![Spusťte skript z úložiště v telefonní aplikace](./media/luis-how-to-manage-from-powershell/phone-app.png)