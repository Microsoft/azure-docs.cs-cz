---
title: Sledovat a spravovat úlohy Azure Stream Analytics pomocí prostředí PowerShell
description: Tento článek popisuje, jak používat rutiny prostředí Azure PowerShell a sledovat a spravovat úlohy Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: ed47141c46259007227ed391c1e114932153de38
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Sledovat a spravovat úlohy Stream Analytics pomocí rutin prostředí Azure PowerShell
Zjistěte, jak sledovat a spravovat prostředky Stream Analytics pomocí rutin prostředí Azure PowerShell a skriptů prostředí powershell, které jsou spouštěny základní úlohy Stream Analytics.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Požadavky pro spuštění rutiny prostředí Azure PowerShell pro Stream Analytics
* Vytvořte skupinu prostředků Azure v rámci vašeho předplatného. Toto je ukázkový skript prostředí Azure PowerShell. Prostředí Azure PowerShell informace najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview);  

Azure PowerShell 0.9.8:  

         # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0:  

         # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>



> [!NOTE]
> Úlohy Stream Analytics vytvořené prostřednictvím kódu programu nemají povoleno ve výchozím nastavení monitorování.  Můžete ručně povolit monitorování na portálu Azure tak, že přejdete na stránku úlohy monitorování a kliknutím na tlačítko Povolit nebo to můžete udělat prostřednictvím kódu programu podle kroků v [Azure Stream Analytics – monitorování Stream Analytics úlohy programově](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Rutiny Azure PowerShell pro Stream Analytics
Následující rutiny prostředí Azure PowerShell můžete použít ke sledování a správě úlohy Azure Stream Analytics. Všimněte si, že prostředí Azure PowerShell má různé verze. 
**V uvedených příkladech je první příkaz pro prostředí Azure PowerShell 0.9.8, v druhém příkazu je pro Azure PowerShell 1.0.** Příkazy Azure PowerShell 1.0, bude mít vždy "AzureRM" v příkazu.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Zobrazí všechny úlohy služby Stream Analytics, které jsou definované v předplatného Azure nebo zadaná skupina prostředků, nebo získá úlohy informace o konkrétní úloze ve skupině prostředků.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

Tento příkaz prostředí PowerShell vrátí informace o všechny úlohy služby Stream Analytics v rámci předplatného Azure.

**Příklad 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Tento příkaz prostředí PowerShell vrátí informace o všechny úlohy služby Stream Analytics ve skupině prostředků StreamAnalytics výchozí střed USA.

**Příklad 3**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Tento příkaz prostředí PowerShell vrátí informace o úloze Stream Analytics StreamingJob ve skupině prostředků StreamAnalytics výchozí střed USA.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Zobrazí seznam všech vstupních hodnot, které jsou definované v zadané úloze Stream Analytics, nebo získá informace o specifický vstup.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Tento příkaz prostředí PowerShell vrátí informace o všech vstupů definované v úloze StreamingJob.

**Příklad 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Tento příkaz prostředí PowerShell vrátí informace o vstup s názvem EntryStream definované v úloze StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Zobrazí seznam všech výstupů, které jsou definované v zadané úloze Stream Analytics, nebo získá informace o konkrétní výstup.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Tento příkaz prostředí PowerShell vrátí informace o výstupy definované v úloze StreamingJob.

**Příklad 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Tento příkaz prostředí PowerShell vrátí informace o výstup s názvem definované v úloze StreamingJob výstup.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Získá informace o kvótu počtu jednotek v zadané oblasti streamování.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Tento příkaz prostředí PowerShell vrátí informace o kvóta a využití jednotek streamování v oblasti, střed USA.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Získá informace o konkrétní transformaci definované v úloze Stream Analytics.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Tento příkaz prostředí PowerShell vrátí informace o transformaci názvem StreamingJob v úloze StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzureRMStreamAnalyticsInput
Vytvoří nový vstup v rámci úlohy Stream Analytics nebo aktualizuje existující zadaný vstup.

Název vstupu mohou být zadané v souboru .json nebo na příkazovém řádku. Pokud jsou zadány oba název na příkazovém řádku musí být stejná jako ta, v souboru.

Pokud jste zadejte vstup, který již existuje a není zadán parametr-Force, rutina zobrazí dotaz, zda chcete nahradit existující vstup.

Pokud zadáte – vynutit parametr a zadejte existující zadejte název, vstup se nahradí bez potvrzení.

Podrobné informace o struktuře souboru JSON a obsah, najdete v části [vytvořit vstup (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-input] části [datového proudu Analytics Management REST API referenční dokumentace knihovny][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Tento příkaz prostředí PowerShell vytvoří nový vstup ze souboru Input.json. Pokud stávající vstup se zadaným v souboru definice vstupní názvem je již definován, rutina zobrazí dotaz, zda chcete ho nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Tento příkaz prostředí PowerShell vytvoří nové vstup do úlohy názvem EntryStream. Pokud stávající vstup s tímto názvem je již definován, rutina zobrazí dotaz, zda chcete ho nahradit.

**Příklad 3**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Tento příkaz prostředí PowerShell nahradí definici existující vstupní zdroj s názvem EntryStream s definicí ze souboru.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzureRMStreamAnalyticsJob
Vytvoří novou úlohu služby Stream Analytics v Microsoft Azure nebo aktualizuje definice existující zadanou úlohu.

V souboru .json nebo na příkazovém řádku lze zadat název úlohy. Pokud jsou zadány oba název na příkazovém řádku musí být stejná jako ta, v souboru.

Pokud můžete zadat název úlohy, která již existuje a není zadán parametr-Force, rutina zobrazí dotaz, zda chcete nahradit existující úlohy.

Pokud zadáte – vynutit parametr a zadejte název existující úlohy, definici úlohy se nahradí bez potvrzení.

Podrobné informace o struktuře souboru JSON a obsah, najdete v části [vytvořit úlohy Stream Analytics] [ msdn-rest-api-create-stream-analytics-job] části [datového proudu Analytics Management REST API referenční dokumentace knihovny][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Tento příkaz prostředí PowerShell vytvoří novou úlohu z definice v JobDefinition.json. Pokud stávající úloze s názvem zadaným v souboru definice úlohy je již definován, rutina zobrazí dotaz, zda chcete ho nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Tento příkaz prostředí PowerShell nahrazuje danou definici úlohy pro StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzureRMStreamAnalyticsOutput
Vytvoří nový výstupní v rámci úlohy Stream Analytics nebo aktualizuje existující výstup.  

Název výstupu mohou být zadané v souboru .json nebo na příkazovém řádku. Pokud jsou zadány oba název na příkazovém řádku musí být stejná jako ta, v souboru.

Pokud zadejte výstup, který již existuje a není zadán parametr-Force, rutina zobrazí dotaz, zda chcete nahradit existující výstup.

Pokud zadáte – vynutit parametr a zadejte název existující výstup, výstup se nahradí bez potvrzení.

Podrobné informace o struktuře souboru JSON a obsah, najdete v části [vytvoření výstupu (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-output] části [datového proudu Analytics Management REST API referenční dokumentace knihovny][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Tento příkaz prostředí PowerShell vytvoří nový výstupní názvem "výstupní" v úloze StreamingJob. Pokud existující výstup s tímto názvem je již definován, rutina zobrazí dotaz, zda chcete ho nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Tento příkaz prostředí PowerShell nahradí definici "výstupní" v úloze StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzureRMStreamAnalyticsTransformation
Vytvoří nový transformaci v rámci úlohy Stream Analytics nebo aktualizuje existující transformace.

V souboru .json nebo na příkazovém řádku, může být zadán název transformace. Pokud jsou zadány oba název na příkazovém řádku musí být stejná jako ta, v souboru.

Pokud zadejte transformaci, která již existuje a není zadán parametr-Force, rutina zobrazí dotaz, zda chcete nahradit existující transformace.

Pokud zadáte – vynutit parametr a zadejte název existující transformace, transformaci se nahradí bez potvrzení.

Podrobné informace o struktuře souboru JSON a obsah, najdete v části [vytvořit transformaci (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-transformation] části [datového proudu Analytics Management REST API referenční dokumentace knihovny][stream.analytics.rest.api.reference].

**Příklad 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Tento příkaz prostředí PowerShell vytvoří nový transformace názvem StreamingJobTransform v úloze StreamingJob. Pokud s tímto názvem je již definován existující transformace, rutina zobrazí dotaz, zda chcete ho nahradit.

**Příklad 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Tento příkaz prostředí PowerShell nahrazuje definice StreamingJobTransform v úloze StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzureRMStreamAnalyticsInput
Asynchronně odstraní specifický vstup z úlohy Stream Analytics v Microsoft Azure.  
Zadáte-li parametr-Force, odstraní se vstup bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Tento příkaz prostředí PowerShell odebere vstupní EventStream v úloze StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzureRMStreamAnalyticsJob
Asynchronně odstraní určité úlohy Stream Analytics v Microsoft Azure.  
Zadáte-li parametr-Force, úloha se odstraní bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Tento příkaz prostředí PowerShell odebere úlohy StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzureRMStreamAnalyticsOutput
Asynchronně odstraní konkrétní výstup z úlohy Stream Analytics v Microsoft Azure.  
Zadáte-li parametr-Force, odstraní se výstup bez potvrzení.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Tato odebere příkaz prostředí PowerShell výstup výstup do úlohy StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzureRMStreamAnalyticsJob
Asynchronně nasadí a spustí úlohu služby Stream Analytics v Microsoft Azure.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Tento příkaz prostředí PowerShell spustí úlohu StreamingJob s časem zahájení vlastní výstup nastavená na 12 prosinec 2012 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzureRMStreamAnalyticsJob
Asynchronně Ukončí úlohu služby Stream Analytics z běžící v Microsoft Azure a zrušte přiděluje prostředky, které byly, který se používá. Definice úlohy a metadata bude stále k dispozici v rámci vašeho předplatného prostřednictvím portálu Azure a rozhraní API pro správu tak, aby úlohy můžete upravit a restartovat. Vám nebude nic účtováno pro úlohu v zastaveném stavu.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Tento příkaz prostředí PowerShell zastaví úlohu StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
Testuje schopnost Stream Analytics se připojit k zadaný vstup.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Tento příkaz prostředí PowerShell testů připojení stav vstupní EntryStream v StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
Testuje schopnost Stream Analytics se připojit k zadaným výstupem.

**Příklad 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Tato testy příkaz prostředí PowerShell stav připojení výstupu výstup v StreamingJob.  

## <a name="get-support"></a>Získat podporu
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

