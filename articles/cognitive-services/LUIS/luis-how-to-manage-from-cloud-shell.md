---
title: Azure Cloud Shell dat využití pro LUIS
titleSuffix: Azure Cognitive Services
description: Další informace o získání informací o využití ve službě Azure Cloud Shell pro LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: aca81bd529fe6151dce7aba6754874355189fc59
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444380"
---
# <a name="usage-data-for-luis-service-from-azure-cloud-shell"></a>Data o využití pro službu LUIS ze služby Azure Cloud Shell
Na webu Azure portal umožňuje pomocí rutin prostředí PowerShell pro práci s prostředky služby LUIS. 

Tyto rutiny umožňují [vytvořit](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) předplatného služby LUIS, získáte informace o předplatném, včetně [využití](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), a [odebrat](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) předplatné. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Účet úložiště cloud shell a ověřování
Chcete-li použít prostředí PowerShell na webu Azure Portal [cloud shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), musíte mít účet úložiště Azure. Pokud nemáte k dispozici [účtu úložiště](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage), budou vyzváni k jeho vytvoření. Účet úložiště umožňuje uložit skripty prostředí PowerShell ve službě cloud shell.  

Budete potřebovat k ověření ve službě cloud shell pro přístup ke všem prostředkům Azure. 

Jakmile máte účet úložiště a ověření, můžete spustit rutiny prostředí PowerShell.

## <a name="open-cloud-shell"></a>Otevření služby Cloud Shell
Při použití portálu Azure cloud shell, jste vždy na nejnovější verzi prostředí PowerShell. 

Použití **spustit Cloud Shell** tlačítko otevřete Cloud Shell nebo otevřete prohlížeč s [ https://shell.azure.com ](https://shell.azure.com). Vyberte jako prostředí Power Shell. Pokud nemáte účet Azure Storage, je potřeba ho vytvořit. 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>Informace o použití koncového bodu služby LUIS

Rutiny Powershellu 6.x `Get-AzureRmCognitiveServicesAccountUsage`, poskytuje informace o Microsoft Cognitive Services LUIS včetně využití. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) vyžaduje skupinu prostředků a název prostředku služby. 

Syntaxe příkazu je:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

V následujícím příkladu je název skupiny prostředků `luis-westus-rg` a název předplatného služba LUIS je `luis-westus-1`. Oba tyto názvy jsou vybrána při vytvoření službu LUIS. 

Rutina vrátí informace o použití 16 používané 30denního období se období do 7. června 10 000 koncových bodů přístupů:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Příkaz Uložit jako soubor prostředí PowerShell, *.ps1 v účtu úložiště Azure přidružený k službě cloud shell a spusťte v každém okamžiku. 

![Spusťte skript ze služby storage](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

Po uložení skriptu na cloudovou jednotku můžete spustit skript prostředí PowerShell Azure cloud shellu telefonní aplikace. 

![Spusťte skript ze služby storage v aplikaci pro telefon](./media/luis-how-to-manage-from-powershell/phone-app.png)