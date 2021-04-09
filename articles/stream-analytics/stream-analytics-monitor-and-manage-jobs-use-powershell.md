---
title: Monitorování a správa úloh Azure Stream Analytics pomocí prostředí PowerShell
description: Tento článek popisuje, jak pomocí Azure PowerShell a rutin monitorovat a spravovat úlohy Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 6b404516c513dea0888974ffb4fa3d8d43db6c44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015177"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorování a správa úloh Stream Analytics pomocí rutin Azure PowerShell
Naučte se monitorovat a spravovat Stream Analytics prostředky pomocí rutin Azure PowerShell a skriptování prostředí PowerShell, které spouští základní úlohy Stream Analytics.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Předpoklady pro spuštění rutin Azure PowerShell pro Stream Analytics
* Vytvořte ve svém předplatném skupinu prostředků Azure. Následuje ukázkový skript Azure PowerShell. Informace o Azure PowerShell najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/);  

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

Azure PowerShell 1,0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Úlohy Stream Analytics vytvořené programově nemají ve výchozím nastavení povolené monitorování.  Monitorování můžete na portálu Azure povolit ručně tak, že přejdete na stránku monitorování úlohy a kliknete na tlačítko Povolit, nebo to můžete provést programově podle kroků v části [Azure Stream Analytics-monitorovat Stream Analytics úlohy programově](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell rutin pro Stream Analytics
Pomocí následujících rutin Azure PowerShell můžete monitorovat a spravovat Azure Stream Analytics úlohy. Všimněte si, že Azure PowerShell má jiné verze. 
**V příkladech uvedených v prvním příkazu je pro Azure PowerShell 0.9.8, druhý příkaz pro Azure PowerShell 1,0.** Příkazy Azure PowerShell 1,0 budou v příkazu vždy obsahovat "AZ".

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Zobrazí seznam všech úloh Stream Analytics definovaných v předplatném Azure nebo v zadané skupině prostředků nebo získá informace o konkrétní úloze v rámci skupiny prostředků.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob
```

Tento příkaz PowerShellu vrátí informace o všech úlohách Stream Analytics v předplatném Azure.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Tento příkaz PowerShellu vrátí informace o všech úlohách Stream Analytics ve skupině prostředků StreamAnalytics-default-Central-US.

**Příklad 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Tento příkaz PowerShellu vrátí informace o Stream Analytics úlohy StreamingJob ve skupině prostředků StreamAnalytics – výchozí – střední-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Zobrazí seznam všech vstupů, které jsou definovány v zadané Stream Analytics úlohy, nebo získává informace o konkrétním vstupu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Tento příkaz PowerShellu vrátí informace o všech vstupech definovaných v StreamingJob úlohy.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Tento příkaz PowerShellu vrátí informace o vstupu s názvem EntryStream definovaném v StreamingJob úlohy.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Zobrazí seznam všech výstupů, které jsou definovány v zadané Stream Analytics úlohy, nebo získává informace o konkrétním výstupu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Tento příkaz PowerShellu vrátí informace o výstupech definovaných v StreamingJob úlohy.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Tento příkaz PowerShellu vrátí informace o výstupu s názvem Output definovaném v StreamingJob úlohy.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Načte informace o kvótě jednotek streamování v zadané oblasti.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Tento příkaz PowerShellu vrátí informace o kvótě a využití jednotek streamování v oblasti Střed USA.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Načte informace o konkrétní transformaci definované v Stream Analytics úlohy.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Tento příkaz PowerShellu vrátí informace o transformaci s názvem StreamingJob v StreamingJob úlohy.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Vytvoří nový vstup v rámci Stream Analytics úlohy nebo aktualizuje stávající zadaný vstup.

Název vstupu lze zadat v souboru. JSON nebo na příkazovém řádku. Pokud jsou zadány obě, název na příkazovém řádku musí být stejný jako ten v souboru.

Pokud zadáte vstup, který už existuje, a nezadáte parametr-Force, rutina se zeptá, jestli se má existující vstup nahradit.

Zadáte-li parametr-Force a zadáte existující název vstupu, bude vstup nahrazen bez potvrzení.

Podrobné informace o struktuře a obsahu souborů JSON najdete v části věnované [Vytvoření vstupu (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] v [referenční knihovně REST API správy Stream Analytics][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Tento příkaz prostředí PowerShell vytvoří nový vstup ze souboru Input.jsv. Je-li již existující vstup s názvem zadaným ve vstupním souboru definice již definován, rutina se zeptá, zda má být nahrazena.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Tento příkaz PowerShellu vytvoří nový vstup v úloze s názvem EntryStream. Pokud je už existující vstup s tímto názvem definovaný, rutina se zeptá, jestli ho má nahradit.

**Příklad 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Tento příkaz PowerShellu nahrazuje definici stávajícího vstupního zdroje s názvem EntryStream s definicí ze souboru.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Vytvoří novou Stream Analyticsovou úlohu v Microsoft Azure nebo aktualizuje definici existující zadané úlohy.

Název úlohy lze zadat v souboru. JSON nebo na příkazovém řádku. Pokud jsou zadány obě, název na příkazovém řádku musí být stejný jako ten v souboru.

Pokud zadáte název úlohy, která už existuje, a nezadáte parametr-Force, rutina se zeptá, jestli má nahradit existující úlohu.

Zadáte-li parametr-Force a zadáte existující název úlohy, definice úlohy bude nahrazena bez potvrzení.

Podrobné informace o struktuře a obsahu souborů JSON najdete v části [Stream Analytics úlohy][msdn-rest-api-create-stream-analytics-job] v [referenční knihovně REST API pro správu Stream Analytics][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Tento příkaz PowerShellu vytvoří novou úlohu z definice v JobDefinition.js. Pokud je už existující úloha s názvem zadaným v souboru definice úlohy už definovaná, rutina se zeptá, jestli ji má nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Tento příkaz PowerShellu nahrazuje definici úlohy pro StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Vytvoří nový výstup v rámci Stream Analytics úlohy nebo aktualizuje stávající výstup.  

Název výstupu lze zadat v souboru. JSON nebo na příkazovém řádku. Pokud jsou zadány obě, název na příkazovém řádku musí být stejný jako ten v souboru.

Pokud zadáte výstup, který už existuje, a nezadáte parametr-Force, rutina se zeptá, jestli má nahradit stávající výstup.

Pokud zadáte parametr-Force a zadáte existující název výstupu, bude výstup nahrazen bez potvrzení.

Podrobné informace o struktuře a obsahu souborů JSON najdete v části věnované [Vytvoření výstupu (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] v [referenční knihovně REST API správy Stream Analytics][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Tento příkaz PowerShellu vytvoří ve StreamingJob úlohy nový výstup s názvem Output. Pokud je už existující výstup s tímto názvem definovaný, rutina se zeptá, jestli ho má nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Tento příkaz PowerShellu nahrazuje definici pro "Output" ve StreamingJob úlohy.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Vytvoří novou transformaci v rámci Stream Analytics úlohy nebo aktualizuje stávající transformaci.

Název transformace lze zadat v souboru. JSON nebo na příkazovém řádku. Pokud jsou zadány obě, název na příkazovém řádku musí být stejný jako ten v souboru.

Pokud zadáte transformaci, která již existuje, a nezadáte parametr-Force, rutina zobrazí dotaz, zda má nahradit stávající transformaci.

Zadáte-li parametr-Force a zadáte existující název transformace, bude transformace nahrazena bez potvrzení.

Podrobné informace o struktuře a obsahu souborů JSON najdete v části věnované [Vytvoření transformace (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] v [referenční knihovně REST API správy Stream Analytics][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Tento příkaz PowerShellu vytvoří v StreamingJob úlohy novou transformaci s názvem StreamingJobTransform. Pokud je již existující transformace definována s tímto názvem, rutina zobrazí dotaz, zda má být nahrazena.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Tento příkaz PowerShellu nahrazuje definici StreamingJobTransform v StreamingJob úlohy.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Asynchronně odstraní konkrétní vstup z Stream Analytics úlohy v Microsoft Azure.  
Pokud zadáte parametr-Force, vstup se odstraní bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Tento příkaz PowerShellu odstraní vstupní EventStream v StreamingJob úlohy.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Asynchronně odstraní konkrétní úlohu Stream Analytics v Microsoft Azure.  
Pokud zadáte parametr-Force, úloha se odstraní bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Tento příkaz PowerShellu Odebere úlohu StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Asynchronně odstraní určitý výstup z Stream Analytics úlohy v Microsoft Azure.  
Pokud zadáte parametr-Force, výstup se odstraní bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Tento příkaz PowerShellu odstraní výstupní výstup ve StreamingJob úlohy.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Asynchronně nasadí a spustí úlohu Stream Analytics v Microsoft Azure.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1,0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Tento příkaz prostředí PowerShell spustí StreamingJob úlohy s vlastním počátečním časem výstupu nastaveným na 12. prosince 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Asynchronně zastaví spuštění úlohy Stream Analytics v Microsoft Azure a zruší přidělení prostředků, které byly použity. Definice úlohy a metadata zůstanou v rámci vašeho předplatného k dispozici prostřednictvím rozhraní API pro Azure Portal i správu, aby bylo možné úlohu upravovat a restartovat. Úloha se vám nebude účtovat ve stavu Zastaveno.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Tento příkaz PowerShellu zastaví StreamingJob úlohy.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testuje schopnost Stream Analytics připojit k zadanému vstupu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Tento příkaz prostředí PowerShell testuje stav připojení vstupních EntryStream v StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testuje schopnost Stream Analytics připojit k zadanému výstupu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Tento příkaz prostředí PowerShell testuje stav připojení výstupního výstupu v StreamingJob.  

## <a name="get-support"></a>Získání podpory
Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)

[msdn-switch-azuremode]: /previous-versions/azure/dn722470(v=azure.100)
[powershell-install]: /powershell/azure/
[msdn-rest-api-create-stream-analytics-job]: ./stream-analytics-quick-create-portal.md
[msdn-rest-api-create-stream-analytics-input]: ./stream-analytics-define-inputs.md
[msdn-rest-api-create-stream-analytics-output]: ./stream-analytics-define-outputs.md
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/ext/stream-analytics/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/