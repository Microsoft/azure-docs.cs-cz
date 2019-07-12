---
title: Monitorování a Správa úloh Azure Stream Analytics s využitím Powershellu
description: Tento článek popisuje, jak pomocí Azure Powershellu a rutiny pro monitorování a Správa úloh Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: cdc09973a192924c5b9a81cd4ed49b9f36fc0eb1
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612298"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorování a správa úlohy Stream Analytics s rutinami Azure Powershellu
Zjistěte, jak monitorovat a Stream Analytics prostředky spravovat pomocí rutin prostředí Azure PowerShell a skriptů prostředí powershell, které jsou spouštěny základní úlohy Stream Analytics.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Požadavky na spouštění rutin Azure Powershellu pro Stream Analytics
* Vytvořte skupinu prostředků Azure v rámci vašeho předplatného. Následuje ukázkový skript Azure Powershellu. Informace o Azure Powershellu najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview);  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription –SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Úlohy Stream Analytics vytvořené prostřednictvím kódu programu není nutné monitorování ve výchozím nastavení povolená.  Můžete ručně povolit monitorování na webu Azure Portal přejděte na stránku úlohy monitorování a kliknutím na tlačítko Povolit a můžete to provést prostřednictvím kódu programu pomocí následujících kroků v [Azure Stream Analytics – úlohy Stream Analytics monitorování Programově](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Rutiny Powershellu pro Stream Analytics pro Azure
Následující rutiny prostředí Azure PowerShell slouží k monitorování a Správa úloh Azure Stream Analytics. Všimněte si, že prostředí Azure PowerShell má různé verze. 
**V příklady uvedené první příkaz slouží pro prostředí Azure PowerShell 0.9.8, druhý příkaz je pro Azure PowerShell 1.0.** Příkazy příkazového řádku Azure PowerShell 1.0, bude mít vždy "Az" v příkazu.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Obsahuje seznam všech úloh Stream Analytics, které jsou definované v zadané skupině prostředků nebo předplatného Azure nebo získá informace o úlohách o konkrétní úloze v rámci skupiny prostředků.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Tento příkaz prostředí PowerShell vrátí informace o všech úloh Stream Analytics v rámci předplatného Azure.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Tento příkaz prostředí PowerShell vrátí informace o všech úloh Stream Analytics ve skupině prostředků StreamAnalytics výchozí Central US.

**Příklad 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Tento příkaz prostředí PowerShell vrátí informace o úloze Stream Analytics StreamingJob ve skupině prostředků StreamAnalytics výchozí Central US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Obsahuje seznam všech vstupních hodnot, které jsou definovány v zadané úloze Stream Analytics a získá informace o konkrétní vstup.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Tento příkaz prostředí PowerShell vrátí informace o všech vstupů v úloze StreamingJob.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
```

Tento příkaz prostředí PowerShell vrátí informace o vstup s názvem EntryStream definované v rámci úlohy StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Jsou uvedeny všechny výstupy, které jsou definovány v zadané úloze Stream Analytics a získá informace o konkrétní výstupu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Tento příkaz prostředí PowerShell vrátí informace o definované v rámci úlohy StreamingJob výstupy.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
```

Tento příkaz prostředí PowerShell vrátí informace o výstupu s názvem definované v rámci úlohy StreamingJob výstup.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Získá informace o kvótu počtu jednotek v určité oblasti streamování.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota –Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota –Location "Central US" 
```

Tento příkaz prostředí PowerShell vrátí informace o kvótách a využití jednotek streamování v oblasti USA (střed).

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Získá informace o konkrétní transformace definované v úloze Stream Analytics.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
```

Tento příkaz prostředí PowerShell vrátí informace o transformaci StreamingJob volat v rámci úlohy StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Vytvoří nový vstup do úlohy Stream Analytics nebo aktualizuje existující zadaný vstup.

Název vstupu se dá nastavit v souboru .json nebo na příkazovém řádku. Pokud jsou zadány oba, název na příkazovém řádku musí být stejná jako ta, v souboru.

Pokud zadáte vstup, který již existuje a není zadán parametr-Force, rutina se zeptá, jestli se má nahradit stávající vstup.

Pokud zadáte – vynutí parametr a zadejte existující zadejte název, nahradí se vstup bez potvrzení.

Podrobné informace jak struktura souboru JSON a obsahu [vytvořit vstup (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] section of the [Stream Analytics Management REST API Reference Library][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
```

Tento příkaz prostředí PowerShell vytvoří nový vstup ze souboru Input.JSON vypadá. Pokud stávající vstup s názvem zadaným v definici vstupní soubor je již definován, rutina se zeptá, jestli chcete jej nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
```

Tento příkaz prostředí PowerShell vytvoří nový vstup v úlohu nazvanou EntryStream. Pokud stávající vstup s tímto názvem je již definován, rutina se zeptá, jestli chcete jej nahradit.

**Příklad 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
```

Tento příkaz prostředí PowerShell nahradí definici z existující vstupní zdroj s názvem EntryStream s definicí ze souboru.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Vytvoří novou úlohu Stream Analytics v Microsoft Azure nebo aktualizuje definice existující zadanou úlohu.

V souboru .json nebo na příkazovém řádku lze zadat název úlohy. Pokud jsou zadány oba, název na příkazovém řádku musí být stejná jako ta, v souboru.

Pokud můžete zadat název úlohy, který již existuje a není zadán parametr-Force, rutina se zeptá, jestli chcete nahradit existující úlohy.

Pokud zadáte – vynutí parametr a zadejte název stávající úlohy, nahradí se definice úlohy bez potvrzení.

Podrobné informace jak struktura souboru JSON a obsahu [vytvoření úlohy Stream Analytics][msdn-rest-api-create-stream-analytics-job] section of the [Stream Analytics Management REST API Reference Library][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
```

Tento příkaz prostředí PowerShell vytvoří novou úlohu z definice v JobDefinition.json. Pokud existující úlohy s názvem zadaným v souboru definice úlohy je již definován, rutina se zeptá, jestli chcete jej nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
```

Tento příkaz prostředí PowerShell nahradí definici úlohy pro StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Vytvoří nový výstup úlohy Stream Analytics nebo aktualizuje existující výstup.  

V souboru .json nebo na příkazovém řádku lze zadat název výstupu. Pokud jsou zadány oba, název na příkazovém řádku musí být stejná jako ta, v souboru.

Pokud zadáte výstup, který již existuje a není zadán parametr-Force, rutina se zeptá, jestli chcete nahradit existující výstup.

Pokud zadáte – vynutí parametr a zadejte existující název výstupu, nahradí se výstup bez potvrzení.

Podrobné informace jak struktura souboru JSON a obsahu [vytvořit výstup (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] section of the [Stream Analytics Management REST API Reference Library][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
```

Tento příkaz prostředí PowerShell vytvoří nový výstup nazvaný "výstupní" v rámci úlohy StreamingJob. Pokud stávající výstup s tímto názvem je již definován, rutina se zeptá, jestli chcete jej nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
```

Tento příkaz prostředí PowerShell nahradí definici "výstupní" v rámci úlohy StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Vytvoří nové transformace v rámci úlohy Stream Analytics nebo aktualizuje existující transformace.

V souboru .json nebo na příkazovém řádku lze zadat název transformace. Pokud jsou zadány oba, název na příkazovém řádku musí být stejná jako ta, v souboru.

Pokud zadáte transformace, která již existuje a není zadán parametr-Force, rutina se zeptá, jestli chcete nahradit existující transformace.

Pokud zadáte – vynutí parametr a zadejte název existujícího transformace, nahradí se transformace bez potvrzení.

Podrobné informace jak struktura souboru JSON a obsahu [vytvoření transformace (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] section of the [Stream Analytics Management REST API Reference Library][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
```

Tento příkaz prostředí PowerShell vytvoří nové transformace StreamingJobTransform volat v rámci úlohy StreamingJob. Pokud stávající transformace je již definován s tímto názvem, rutina se zeptá, jestli chcete jej nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
```

 Tento příkaz prostředí PowerShell nahradí definici StreamingJobTransform v úloze StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Asynchronně odstraní specifický vstup z úlohy Stream Analytics v Microsoft Azure.  
Pokud zadáte parametr-Force, odstraní se vstup bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
```

Tento příkaz prostředí PowerShell odebere vstupní EventStream v úloze StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Asynchronně odstraní určité úlohy Stream Analytics v Microsoft Azure.  
Pokud zadáte parametr-Force, úloha se odstraní bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Tento příkaz prostředí PowerShell odebere StreamingJob úlohy.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Asynchronně odstraní konkrétní výstupu z úlohy Stream Analytics v Microsoft Azure.  
Pokud zadáte parametr-Force, výstup se odstraní bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Toto prostředí PowerShell příkaz odebere výstup výstup úlohy StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Asynchronně nasadí a spustí úlohu Stream Analytics v Microsoft Azure.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Tento příkaz prostředí PowerShell spustí úlohu StreamingJob s časem zahájení vlastního výstupu nastavena na 12. prosince 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Asynchronně zastaví úlohu Stream Analytics z běžící v Microsoft Azure a zruší prostředky, které byly, které byly používány. Definice úlohy a metadata zůstanou k dispozici v rámci vašeho předplatného prostřednictvím webu Azure portal a rozhraní API pro správu tak, aby úlohy lze upravit a restartovat. Vám nebude účtovat na úlohu v zastaveném stavu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Tento příkaz prostředí PowerShell zastaví úlohu StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Otestuje schopnost služby Stream Analytics pro připojení k zadaného vstupu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
```

Tento příkaz prostředí PowerShell testů v StreamingJob vstupní EntryStream stav připojení.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Otestuje schopnost služby Stream Analytics pro připojení k zadaným výstupem.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Tato testy příkaz prostředí PowerShell stav připojení výstupu výstup v StreamingJob.  

## <a name="get-support"></a>Získat podporu
Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

