---
title: Monitorování a správa úloh Azure Stream Analytics pomocí PowerShellu
description: Tento článek popisuje, jak pomocí Azure PowerShellu a rutin ke sledování a správě úloh Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 7781f35fe7c17e4a0f307f559945caf648b23f6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431707"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Sledování a správa úloh Stream Analytics pomocí rutin Azure PowerShell
Zjistěte, jak monitorovat a spravovat prostředky Stream Analytics pomocí rutin Azure PowerShell a skriptování powershellu, které spouštějí základní úlohy Stream Analytics.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Požadavky pro spuštění rutin Azure PowerShellpro Stream Analytics
* Vytvořte skupinu prostředků Azure ve vašem předplatném. Následuje ukázkový skript Azure PowerShellu. Informace o Azure PowerShellu najdete v [tématu Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview);  

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
Get-AzSubscription �SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Úlohy Služby Stream Analytics vytvořené programově nemají ve výchozím nastavení povoleno monitorování.  Monitorování můžete na webu Azure Portal povolit ručně tak, že přejdete na stránku sledování úloh a kliknete na tlačítko Povolit nebo to můžete provést programově podle kroků umístěných na [webu Azure Stream Analytics – monitorovat úlohy analýzy datového](stream-analytics-monitor-jobs.md)proudu programově .
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Rutiny Prostředí Azure PowerShell pro analýzu datových proudů
Následující rutiny Azure PowerShellu se můžou použít ke sledování a správě úloh Azure Stream Analytics. Všimněte si, že Azure PowerShell má různé verze. 
**V uvedených příkladech je první příkaz pro Azure PowerShell 0.9.8, druhý příkaz je pro Azure PowerShell 1.0.** Příkazy Azure PowerShell 1.0 bude mít vždy "Az" v příkazu.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Zobrazí seznam všech úloh Stream Analytics definovaných v předplatném Azure nebo v zadané skupině prostředků nebo získá informace o konkrétní úloze v rámci skupiny prostředků.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Tento příkaz PowerShellvrátí informace o všech úlohách Stream Analytics v předplatném Azure.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Tento příkaz Prostředí PowerShell vrátí informace o všech úlohách Stream Analytics ve skupině prostředků StreamAnalytics-Default-Central-US.

**Příklad 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Tento příkaz Prostředí PowerShell vrátí informace o úloze Stream Analytics StreamingJob ve skupině prostředků StreamAnalytics-Default-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Zobrazí seznam všech vstupů, které jsou definovány v zadané úloze Služby Stream Analytics, nebo získá informace o konkrétním vstupu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Tento příkaz prostředí PowerShell vrátí informace o všech vstupech definovaných v úloze StreamingJob.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name EntryStream
```

Tento příkaz prostředí PowerShell vrátí informace o vstupu s názvem EntryStream definovanév úloze StreamingJob úlohy.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Zobrazí seznam všech výstupů, které jsou definovány v zadané úloze Služby Stream Analytics, nebo získá informace o konkrétním výstupu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Tento příkaz prostředí PowerShell vrátí informace o výstupech definovaných v úloze StreamingJob.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name Output
```

Tento příkaz prostředí PowerShell vrátí informace o výstupu s názvem Výstup definovaný v úloze StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Získá informace o kvótě jednotek streamování v zadané oblasti.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota �Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota �Location "Central US" 
```

Tento příkaz prostředí PowerShell vrátí informace o kvótě a využití jednotek streamování v oblasti Střední USA.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformace | Get-AzStreamAnalyticsTransformace
Získá informace o konkrétní transformaci definované v úloze Stream Analytics.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name StreamingJob
```

Tento příkaz prostředí PowerShell vrátí informace o transformaci s názvem StreamingJob v úloze StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>Nový-AzureStreamAnalyticsInput | Nový-AzStreamAnalyticsInput
Vytvoří nový vstup v rámci úlohy Stream Analytics nebo aktualizuje existující zadaný vstup.

Název vstupu lze zadat v souboru JSON nebo na příkazovém řádku. Pokud jsou zadány obě, název na příkazovém řádku musí být stejný jako název v souboru.

Pokud zadáte vstup, který již existuje, a nezadáte parametr Force, rutina se zeptá, zda má být existující vstup nahrazen.

Pokud zadáte parametr Force a existující vstupní název, vstup bude nahrazen bez potvrzení.

Podrobné informace o struktuře a obsahu souboru JSON najdete v části [Vytvořit vstup (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] [v referenční knihovně rozhraní REST ROZHRANÍ API služby Stream Analytics][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" 
```

Tento příkaz prostředí PowerShell vytvoří nový vstup ze souboru Input.json. Pokud je již definován existující vstup s názvem zadaným v souboru definice vstupu, rutina se zeptá, zda má být nahrazen.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream
```

Tento příkaz prostředí PowerShell vytvoří nový vstup v úloze s názvem EntryStream. Pokud je již definován existující vstup s tímto názvem, rutina se zeptá, zda jej má nahradit.

**Příklad 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream -Force
```

Tento příkaz prostředí PowerShell nahradí definici existujícího vstupního zdroje nazvaného EntryStream definicí ze souboru.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>Nová azurestreamanalyticsjob | Nová-AzStreamAnalyticsJob
Vytvoří novou úlohu Stream Analytics v Microsoft Azure nebo aktualizuje definici existující zadané úlohy.

Název úlohy lze zadat v souboru JSON nebo na příkazovém řádku. Pokud jsou zadány obě, název na příkazovém řádku musí být stejný jako název v souboru.

Pokud zadáte název úlohy, která již existuje, a nezadáte parametr Force, rutina se zeptá, zda má být existující úloha nahrazena.

Pokud zadáte parametr Force a existující název úlohy, definice úlohy bude nahrazena bez potvrzení.

Podrobné informace o struktuře a obsahu souboru JSON najdete v části [Vytvořit úlohu analýzy datového proudu][msdn-rest-api-create-stream-analytics-job] v referenční knihovně rozhraní REST ROZHRANÍ API [služby Stream Analytics][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" 
```

Tento příkaz prostředí PowerShell vytvoří novou úlohu z definice v jobdefinition.json. Pokud je již definována existující úloha s názvem zadaným v souboru definice úlohy, rutina se zeptá, zda ji má nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" �Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" �Name StreamingJob -Force
```

Tento příkaz prostředí PowerShell nahrazuje definici úlohy pro StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>Nový výstup AzureStreamAnalytics | Nový-AzStreamAnalyticsOutput
Vytvoří nový výstup v rámci úlohy Stream Analytics nebo aktualizuje existující výstup.  

Název výstupu lze zadat v souboru JSON nebo na příkazovém řádku. Pokud jsou zadány obě, název na příkazovém řádku musí být stejný jako název v souboru.

Pokud zadáte výstup, který již existuje, a nezadáte parametr Force, rutina se zeptá, zda má být nahrazen existující výstup.

Pokud zadáte parametr Force a existující název výstupu, bude výstup nahrazen bez potvrzení.

Podrobné informace o struktuře a obsahu souboru JSON najdete v části [Vytvořit výstup (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] [v referenční knihovně rozhraní REST ROZHRANÍ API služby Stream Analytics .][stream.analytics.rest.api.reference]

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output
```

Tento příkaz prostředí PowerShell vytvoří nový výstup s názvem "výstup" v úloze StreamingJob. Pokud je již definován existující výstup s tímto názvem, rutina se zeptá, zda jej má nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output -Force
```

Tento příkaz prostředí PowerShell nahrazuje definici "výstupu" v úloze StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>Nová transformace AzureStreamAnalytics | Nová-AzStreamAnalyticsTransformace
Vytvoří novou transformaci v rámci úlohy Stream Analytics nebo aktualizuje existující transformaci.

Název transformace lze zadat v souboru JSON nebo na příkazovém řádku. Pokud jsou zadány obě, název na příkazovém řádku musí být stejný jako název v souboru.

Pokud zadáte transformaci, která již existuje a nezadáte Force parametr, rutina se zeptá, zda nahradit existující transformace.

Pokud zadáte parametr Force a zadáte existující název transformace, transformace bude nahrazena bez potvrzení.

Podrobné informace o struktuře a obsahu souboru JSON najdete v části [Vytvořit transformaci (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] [v referenční knihovně rozhraní REST ROZHRANÍ API služby Stream Analytics .][stream.analytics.rest.api.reference]

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform
```

Tento příkaz Prostředí PowerShell vytvoří novou transformaci nazvanou StreamingJobTransform v úloze StreamingJob. Pokud existující transformace je již definována s tímto názvem, rutina se zeptá, zda ji nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform -Force
```

 Tento příkaz prostředí PowerShell nahrazuje definici Položky StreamingJobTransform v úloze StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Odebrat-AzureStreamAnalyticsInput | Odebrat-AzStreamAnalyticsInput
Asynchronně odstraní konkrétní vstup z úlohy Stream Analytics v Microsoft Azure.  
Pokud zadáte parametr Force, vstup bude odstraněn bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EventStream
```

Tento příkaz prostředí PowerShell odebere vstupní EventStream v úloze StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Odebrat AzureStreamAnalyticsJob | Odebrat-AzStreamAnalyticsJob
Asynchronně odstraní konkrétní úlohu Stream Analytics v Microsoft Azure.  
Pokud zadáte parametr Force, úloha bude odstraněna bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Tento příkaz prostředí PowerShell odebere úlohu StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Odebrat-AzureStreamAnalyticsOutput | Odebrat-AzStreamAnalyticsOutput
Asynchronně odstraní konkrétní výstup z úlohy Stream Analytics v Microsoft Azure.  
Pokud zadáte parametr Force, výstup bude odstraněn bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Tento příkaz prostředí PowerShell odebere výstupní výstup v úloze StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Asynchronně nasazuje a spouští úlohu Stream Analytics v Microsoft Azure.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Tento příkaz Prostředí PowerShell spustí úlohu StreamingJob s vlastní výstupní čas zahájení nastavena na 12 prosince 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Asynchronně zastaví spuštění úlohy Stream Analytics v Microsoft Azure a zruší přidělení prostředků, které byly používány. Definice úlohy a metadata zůstanou dostupné v rámci vašeho předplatného prostřednictvím portálu Azure a rozhraní API pro správu, takže úlohu lze upravit a restartovat. Za úlohu v zastaveném stavu vám nebude účtován poplatek.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Tento příkaz prostředí PowerShell zastaví úlohu StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testuje schopnost Stream Analytics připojit se k zadanému vstupu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EntryStream
```

Tento příkaz prostředí PowerShell testuje stav připojení vstupního EntryStream v streamingjobu.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testuje schopnost Stream Analytics připojit se k zadanému výstupu.

**Příklad 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Tento příkaz prostředí PowerShell testuje stav připojení výstupního výstupu v streamingjobu.  

## <a name="get-support"></a>Získat podporu
Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
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

