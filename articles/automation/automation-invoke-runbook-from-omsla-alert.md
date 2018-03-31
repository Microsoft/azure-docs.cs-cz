---
title: Volání runbooku Azure Automation z upozornění Log Analytics
description: Tento článek obsahuje přehled o tom, jak vyvolat runbook služby automatizace výstraze analýzy protokolů v Azure.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 2a0e497535f783cbffc21004331ccd2a50ab8eef
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>Volání runbooku Azure Automation z upozornění Log Analytics

Ve službě Azure Log Analytics můžete nakonfigurovat upozornění, které vytvoří záznam upozornění, když výsledky odpovídají zadaným kritériím. Upozornění pak ve snaze automaticky napravit problém může automaticky spustit runbook Azure Automation. 

Upozornění může například signalizovat dlouhodobý prudký nárůst zatížení procesoru. Nebo může značit, že selhal proces, který je klíčový pro fungování některé firemní aplikace. Runbook pak může zapsat příslušnou událost do protokolu událostí Windows.  

V konfiguraci upozornění existují dvě možnosti volání runbooku:

* Pomocí webhooku.
   * Toto je jedinou možností, které jsou k dispozici, pokud není propojený pracovní prostor analýzy protokolů pro účet Automation.
   * Pokud již máte účet Automation propojit s pracovní prostor analýzy protokolů, tato možnost je stále k dispozici.  

* Přímým výběrem runbooku.
   * Tato možnost je dostupná pouze v případě, že je propojený pracovní prostor analýzy protokolů pro účet Automation.

## <a name="calling-a-runbook-by-using-a-webhook"></a>Volání runbooku pomocí webhooku

Webhook můžete použít ke spuštění konkrétního runbooku v Azure Automation prostřednictvím jednoho požadavku HTTP. Než nakonfigurujete [upozornění Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules), aby jako akci upozornění volalo runbook pomocí webhooku, je potřeba nejprve [vytvořit webhook](automation-webhooks.md#creating-a-webhook) pro runbook, který bude touto metodou volán. Nezapomeňte si poznamenat adresu URL webhooku, abyste na něj mohli odkazovat při konfiguraci pravidla upozornění.   

## <a name="calling-a-runbook-directly"></a>Přímé volání runbooku

Můžete nainstalovat a nakonfigurovat automatizace a řízení nabídky v pracovním prostoru analýzy protokolů. Při konfiguraci možnosti akcí runbooku pro upozornění můžete všechny runbooky zobrazit v rozevíracím seznamu **Vybrat sadu runbook** a zvolit konkrétní runbook, který chcete v reakci na upozornění spustit. Vybraný runbook se může spustit v pracovním prostoru Azure nebo v procesu Hybrid Runbook Worker. 

Když vytvoříte upozornění s možností runbooku, pro tento runbook se vytvoří webhook. Webhook zobrazíte tak, že přejdete do účtu Automation a pak otevřete podokno webhooku vybraného runbooku. 

Pokud odstraníte dané upozornění, webhook zůstane zachovaný. To nepředstavuje žádný problém. Webhook se změní jen na osamocenou položku, kterou byste měli nakonec odstranit ručně, abyste účet Automation udržovali v pořádku.  

## <a name="characteristics-of-a-runbook"></a>Vlastnosti runbooku

Obě metody volání runbooku z upozornění Log Analytics mají charakteristiky, kterým musíte před konfigurací pravidel upozornění porozumět. 

Data upozornění jsou ve formátu JSON v jedné vlastnosti **SearchResult**. Tento formát platí pro akce runbooku a webhooku se standardní datovou částí. Pro akce webhooku s vlastními datovými částmi, které v **textu žádosti** zahrnují **IncludeSearchResults:True**, je tato vlastnost **SearchResults**.

Musíte mít vstupní parametr runbooku s názvem **WebhookData**, který je typu **Objekt**. Může být povinný nebo volitelný. Upozornění předává výsledky hledání do runbooku pomocí tohoto vstupního parametru.

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
Také musíte mít kód, který převede **WebhookData** na objekt prostředí PowerShell.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

**$SearchResult** představuje pole objektů. Každý z nich obsahuje pole s hodnotami z jednoho výsledku hledání.


## <a name="example-walkthrough"></a>Ukázka podrobného postupu

Následující ukázka grafického runbooku předvede, jak tento proces funguje. Spustí službu pro Windows.

![Grafický runbook pro spouštění služby systému Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

Runbook má jeden vstupní parametr typu **Objekt** s názvem **WebhookData**. Zahrnuje data webhooku předaná z výstrahy, která obsahuje vlastnost **SearchResult**.

![Vstupní parametry runbooku](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

V našem příkladu jsme v Log Analytics vytvořili dvě vlastní pole: **SvcDisplayName_CF** a **SvcState_CF**. Tato pole extrahují zobrazovaný název služby a stav služby (jestli je spuštěná, nebo zastavená) z události zapsané do protokolu událostí systému. Následně vytvoříme pravidlo upozornění s následujícím vyhledávacím dotazem, abychom mohli detekovat zastavení služby zařazování tisku v systému Windows:

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

Může se jednat o jakoukoliv službu, která vás zajímá. Pro tento příklad používáme jednu z již existujících služeb, které jsou součástí operačního systému Windows. Akce upozornění je nakonfigurována ke spouštění runbooku použitého v tomto příkladu v procesu Hybrid Runbook Worker, který je na cílovém systému povolený.   

Aktivita kódu runbooku **Get Service Name from LA** (Načíst název služby z LA) konvertuje řetězec ve formátu JSON na typ objektu a vyfiltrujte položku **SvcDisplayName_CF**. Extrahuje zobrazovaný název služby systému Windows a tuto hodnotu předá následující aktivitě, která ověří, zda je služba zastavená, a potom se ji pokusí restartovat. **SvcDisplayName_CF** je [vlastní pole](../log-analytics/log-analytics-custom-fields.md), které jsme vytvořili v Log Analytics pro účely tohoto příkladu.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Když se služba zastaví, pravidlo upozornění v Log Analytics zjistí shodu a aktivuje runbook, do kterého odešle kontext upozornění. Runbook se pokusí ověřit, že je služba zastavená. Pokud ano, runbook se ji pokusí restartovat, ověří, zda se spustila správně, a zobrazí výsledky.     

Nemáte účtu Automation propojit s pracovní prostor analýzy protokolů, které můžete nakonfigurovat pravidlo výstrahy se akce webhooku. Akce webhooku spustí runbook. Také nakonfiguruje runbook tak, aby podle kroků uvedených výše konvertoval řetězec ve formátu JSON a vyfiltroval hodnotu **SearchResult**.    

>[!NOTE]
> Pokud byl váš pracovní prostor upgradován na [nový dotazovací jazyk Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), datová část webhooku se změnila. Podrobnosti o formátu najdete v tématu [Rozhraní REST API služby Azure Log Analytics](https://aka.ms/loganalyticsapiresponse).

## <a name="next-steps"></a>Další postup

* Další informace o upozorněních v Log Analytics a jejich vytváření najdete v tématu [Upozornění v Log Analytics](../log-analytics/log-analytics-alerts.md).

* Informace o tom, jak aktivovat runbooky pomocí webhooku, najdete v tématu [Webhooky Azure Automation](automation-webhooks.md).
