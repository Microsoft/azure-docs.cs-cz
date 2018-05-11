---
title: Počínaje webhook, jehož runbook služby automatizace Azure
description: Webhook, která umožňuje klientovi spuštění sady runbook ve službě Azure Automation z volání protokolu HTTP.  Tento článek popisuje, jak vytvořit webhook, jehož a postup volání jednoho spuštění runbooku.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: bb64d0c5d94bb198b6ece2ea50a7fc248b93c7dd
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Počínaje webhook, jehož runbook služby automatizace Azure
A *webhooku* umožňuje spustit konkrétní runbook ve službě Azure Automation prostřednictvím jedné žádosti HTTP. To umožňuje externích služeb, jako je například Visual Studio Team Services, GitHub, analýzy protokolů Azure nebo vlastních aplikací ke spouštění sad runbook bez implementace úplné řešení pomocí rozhraní API služby Azure Automation.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Můžete porovnat webhooky k dalším metodám spuštění sady runbook [spuštění sady runbook ve službě Azure Automation](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Podrobnosti o webhook, jehož
Následující tabulka popisuje vlastnosti, které je nutné nakonfigurovat pro webhook, jehož.

| Vlastnost | Popis |
|:--- |:--- |
| Název |Můžete zadat libovolný název, který chcete použít pro webhook, jehož vzhledem k tomu, že to není vystavený klienta.  Používá se pouze pro vás k identifikaci sady runbook ve službě Azure Automation. <br>  Jako osvědčený postup musíte získat webhooku název související klientovi, který bude používat. |
| URL |Adresa URL webhooku je jedinečnou adresu, která volá klienta pomocí metody POST protokolu HTTP pro spuštění sady runbook propojené s webhooku.  Generuje se automaticky při vytvoření webhooku.  Nelze zadat vlastní adresu URL. <br> <br>  Adresa URL obsahuje token zabezpečení, který umožňuje sady runbook vyvolat systému třetích stran se žádné další ověřování. Z tohoto důvodu by zpracovávat jako heslo.  Z bezpečnostních důvodů můžete jenom zobrazit adresu URL na portálu Azure v době, kdy je vytvoření webhooku. Upozorňujeme ale, adresu URL na bezpečné místo pro budoucí použití. |
| Datum ukončení platnosti |Stejně jako certifikát má každý webhooku datum vypršení platnosti, po kterém již slouží.  Po vytvoření webhooku můžete upravit toto datum vypršení platnosti. |
| Povoleno |Webhook, jehož je ve výchozím nastavení povolena, když je vytvořeno.  Pokud je nastavena na zakázáno, pak žádný klient bude moct používat.  Můžete nastavit **povoleno** vlastnost při vytvoření webhooku nebo kdykoli po jeho vytvoření. |

### <a name="parameters"></a>Parametry
Webhook, jehož můžete definovat hodnoty pro parametry runbooku, které se použijí při spuštění runbooku pomocí tohoto webhooku. Webhook musí obsahovat hodnoty všech povinných parametrů runbooku a můžou obsahovat hodnoty pro volitelné parametry. Hodnotu parametru, který je nakonfigurován tak, aby webhook, jehož lze změnit i po vytvoření webhooku. Více webhooky propojené s jedné sady runbook můžete použít jiné hodnoty parametru.

Při spuštění sady runbook pomocí webhook, jehož klienta, je nejde přepsat hodnot parametrů definovaných v webhooku.  Přijímat data z klienta, sada runbook může přijmout jeden parametr s názvem **$WebhookData** typu [object], která bude obsahovat data, která zahrnuje klient v požadavku POST.

![Vlastnosti Webhookdata](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** objektu bude mít následující vlastnosti:

| Vlastnost | Popis |
|:--- |:--- |
| WebhookName |Název webhooku. |
| RequestHeader |Hodnota hash tabulku obsahující hlavičky příchozí požadavek POST. |
| RequestBody |Text příchozí požadavek POST.  To zachová veškeré formátování, jako je například řetězec, formát JSON, XML, nebo data formuláře kódovaná v řetězci. Sada runbook musí být napsané pro práci s formátem dat, kterou je očekáván. |

Neexistuje žádná konfigurace webhooku potřebné k podpoře **$WebhookData** parametr a sada runbook není potřeba ji přijmout.  Pokud sada runbook nedefinuje parametr, se ignoruje všechny podrobnosti o požadavek odeslaný z klienta.

Pokud zadáte hodnotu pro $WebhookData při vytváření webhooku, že hodnota bude přepsána při webhooku spuštění sady runbook s daty z klienta žádosti, i v případě, že klient neobsahuje žádná data v textu požadavku.  Pokud spustíte runbook, který má $WebhookData pomocí jiné metody než webhook, jehož, je zadat hodnotu pro $Webhookdata, rozpozná pomocí sady runbook.  Tato hodnota by měla být objekt se stejným [vlastnosti](#details-of-a-webhook) jako $Webhookdata tak, aby sada runbook může správně s ním pracovat, jako kdyby byla práce s skutečné WebhookData předaná webhook, jehož.

Například pokud se spouštění následující sady runbook na portálu Azure a chcete předat některé ukázkové WebhookData pro testování, protože WebhookData je objekt, se mají být předány jako JSON v uživatelském rozhraní.

![Parametr WebhookData z uživatelského rozhraní](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Výše uvedené sady runbook, pokud máte následující vlastnosti pro parametr WebhookData:

1. WebhookName: *MyWebhook*
2. RequestHeader: *z = testovacího uživatele*
3. RequestBody: *["VM1", "Virtuálního počítače 2"]*

By pak předejte následující hodnotu JSON v uživatelském rozhraní pro parametr WebhookData:  

* {"WebhookName": "MyWebhook", "RequestHeader": {"Od": "Testovací uživatele"}, "RequestBody": "[\"VM1\",\"virtuálního počítače 2\"]"}

![Parametr WebhookData zahájení z uživatelského rozhraní](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Hodnoty všech vstupních parametrů se protokolují pod úloha sady runbook.  To znamená, že žádný vstup klientem v požadavku webhooku bude protokolu a k dispozici všem uživatelům s přístupem k automatizaci úloh.  Z tohoto důvodu byste měli být opatrní při voláních webhooku včetně citlivé informace.
>

## <a name="security"></a>Zabezpečení
Zabezpečení webhooku spoléhá na ochranu osobních údajů jeho adresa URL, který obsahuje token zabezpečení, který mohou být volána. Služby Azure Automation neprovede žádné ověřování na žádost, dokud se provádí na správnou adresu URL. Z tohoto důvodu webhooky nepoužívejte pro sady runbook, které provádějí vysoce citlivých funkce bez použití alternativní způsob ověření požadavku.

Můžete zahrnout logiky v rámci sady runbook k určení, zda byla volána podle webhook, jehož kontrolou **WebhookName** vlastnost parametru $WebhookData. Sada runbook může provést další ověření tak, že vyhledá konkrétní informace v **RequestHeader** nebo **RequestBody** vlastnosti.

Další strategie se se runbook provést některé ověření externí podmínka v případě, že přijala žádost o webhooku.  Představte si třeba sadu runbook, která je volána metodou Githubu, kdykoli dojde k nové potvrzení úložišti GitHub.  Runbook se může připojit k Githubu k ověření, že nový potvrzení ve skutečnosti jenom vyskytla než budete pokračovat.

## <a name="creating-a-webhook"></a>Vytváření webhooku
Použijte následující postup k vytvoření nové webhooku propojit k sadě runbook na portálu Azure.

1. Z **sady Runbook stránky** na portálu Azure klikněte na tlačítko runbook, která webhooku spustí zobrazíte stránku s jeho podrobnostmi.
2. Klikněte na tlačítko **Webhooku** v horní části stránky otevřete **přidat Webhooku** stránky. <br>
   ![Tlačítko Webhooky](media/automation-webhooks/webhooks-button.png)
3. Klikněte na tlačítko **vytvořit nové webhooku** otevřete **stránka pro vytvoření webhooku**.
4. Zadejte **název**, **datum vypršení platnosti** webhooku a jestli se má povolit. V tématu [podrobnosti o webhook, jehož](#details-of-a-webhook) pro další informace o těchto vlastností.
5. Kliknutím na ikonu kopírování a stisknutím Ctrl + C zkopírujte adresu URL webhooku.  Potom zaznamenejte jej na bezpečném místě.  **Po vytvoření webhooku nelze znovu načíst adresu URL.** <br>
   ![Adresa URL Webhooku](media/automation-webhooks/copy-webhook-url.png)
6. Klikněte na tlačítko **parametry** zadat hodnoty pro parametry runbooku.  Pokud má runbook povinné parametry, pak nebude možné k vytvoření webhooku, pokud jsou zadané hodnoty.
7. Klikněte na tlačítko **vytvořit** k vytvoření webhooku.

## <a name="using-a-webhook"></a>Pomocí webhook, jehož
Klientské aplikace používat webhook, jehož po jeho vytvoření, vydá HTTP POST s adresou URL pro webhooku.  Syntaxe webhooku bude v následujícím formátu.

    http://<Webhook Server>/token?=<Token Value>

Klient se zobrazí jednu z následujících návratové kódy z žádosti.  

| Kód | Text | Popis |
|:--- |:--- |:--- |
| 202 |Přijato |Žádost byla přijata, a sada runbook byla úspěšně zařazených do fronty. |
| 400 |Chybná žádost |Požadavek nebyl přijat pro jednu z následujících důvodů. <ul> <li>Webhook vypršela platnost.</li> <li>Webhook je zakázána.</li> <li>Token v adrese URL je neplatný.</li>  </ul> |
| 404 |Nenalezené |Požadavek nebyl přijat pro jednu z následujících důvodů. <ul> <li>Webhook nebyl nalezen.</li> <li>Sada runbook nebyla nalezena.</li> <li>Účet nebyl nalezen.</li>  </ul> |
| 500 |Vnitřní chyba serveru |Adresa URL byla platná, ale došlo k chybě.  Odešlete požadavek znovu. |

Za předpokladu, že požadavek je úspěšné, webhooku odpovědi obsahuje id úlohy ve formátu JSON následujícím způsobem. Bude obsahovat id jedné úlohy, ale formát JSON umožňuje potenciální budoucí vylepšení.

    {"JobIds":["<JobId>"]}  

Klient nemůže zjistit po dokončení úlohy runbooku nebo její stav dokončení od webhooku.  Může zjistit, tyto informace id úlohy pomocí jiné metody, jako [prostředí Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) nebo [rozhraní API služby Azure Automation](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### <a name="example"></a>Příklad
Následující příklad používá ke spuštění sady runbook s webhook, jehož prostředí Windows PowerShell.  Upozorňujeme, že jakýkoli jazyk, který může odeslat požadavek HTTP, můžete použít webhooku; Prostředí Windows PowerShell se právě používá jako příklad sem.

Sada runbook očekává seznam virtuálních počítačů, které jsou ve formátu JSON v textu požadavku. Můžeme také jsou včetně informací o kdo je spuštění sady runbook a datum a čas, že je právě spuštěna v hlavičce požadavku.      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}

    $vms  = @(
                @{ Name="vm01";ServiceName="vm01"},
                @{ Name="vm02";ServiceName="vm02"}
            )
    $body = ConvertTo-Json -InputObject $vms

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response


Následující obrázek znázorňuje informace hlavičky (pomocí [Fiddler](http://www.telerik.com/fiddler) trasování) z této žádosti. To zahrnuje standardní hlavičky požadavku HTTP kromě vlastní datum a z hlavičky, které jsme přidali.  Každý z těchto hodnot je k dispozici pro sadu runbook v **RequestHeaders** vlastnost **WebhookData**.

![Tlačítko Webhooky](media/automation-webhooks/webhook-request-headers.png)

Následující obrázek ukazuje text žádosti (pomocí [Fiddler](http://www.telerik.com/fiddler) trasování), je k dispozici runbooku **RequestBody** vlastnost **WebhookData**. To je naformátován jako JSON, protože formát, který je zahrnutý v textu požadavku, který byl.     

![Tlačítko Webhooky](media/automation-webhooks/webhook-request-body.png)

Následující obrázek ukazuje žádost odesílány z prostředí Windows PowerShell a výsledné odpovědi.  Id úlohy je extrahována z odpovědi a převedeno na řetězec.

![Tlačítko Webhooky](media/automation-webhooks/webhook-request-response.png)

Následující vzorový runbook přijme předchozí příklad žádost a spustí virtuální počítače zadaný v textu požadavku.

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param (
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {

            # Collect properties of WebhookData
            $WebhookName     =     $WebhookData.WebhookName
            $WebhookHeaders =     $WebhookData.RequestHeader
            $WebhookBody     =     $WebhookData.RequestBody

            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."

            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred

            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook."
        }
    }


## <a name="starting-runbooks-in-response-to-azure-alerts"></a>Spouštění sad runbook v reakci na výstrahy Azure
Webhooku povoleno sady runbook lze reagovat na [Azure výstrahy](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Shromažďování statistik jako výkon, dostupnost a využití pomocí Azure výstrahy můžete sledovat prostředky v Azure. Obdržíte výstrahu založenou na monitorování metriky nebo události pro vaše prostředky Azure, účty služby Automation aktuálně podporují pouze metriky. Pokud hodnota zadané metriky překročí prahovou hodnotu, přiřazené nebo nakonfigurovanou událost se aktivuje oznámení je odeslána do Správce služby nebo spolusprávci tuto výstrahu vyřešíte, další informace o metriky a události naleznete [Azure výstrahy](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Kromě použití Azure výstrahy jako systém oznámení, můžete také ji sady runbook v reakci na výstrahy. Automatizace Azure poskytuje možnost spustit webhooku povoleno sady runbook s Azure výstrahy. Pokud metriky překročí nakonfigurovanou prahovou hodnotu pravidlo výstrahy stane aktivní a aktivuje webhook automatizace, který pak provede sadu runbook.

![Webhooky](media/automation-webhooks/webhook-alert.jpg)

### <a name="alert-context"></a>Kontext výstrahy
Vezměte v úvahu prostředek služby Azure, jako je například virtuální počítač, využití procesoru tohoto počítače je jedním z klíčových metriku. Pokud využití procesoru je 100 % nebo více než určité dlouhou dobu, můžete chtít restartovat virtuální počítač na opravě problému. To lze vyřešit tak, že nakonfigurujete pravidlo výstrahy pro virtuální počítač a toto pravidlo trvá procento využití procesoru jako jeho metriku. Procento využití procesoru v tomto poli je právě prováděné jako příklad, ale existuje mnoho další metriky, které můžete nakonfigurovat na vašich prostředků Azure a restartování virtuálního počítače je akce, která se provede chcete tento problém vyřešit, můžete nakonfigurovat sadu runbook k provádění dalších akcí.

Pokud toto pravidlo výstrahy se změní na aktivní a aktivuje runbook webhooku povolena, odešle kontext výstrahy do sady runbook. [Kontext výstrahy](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) obsahuje podrobnosti, včetně **SubscriptionID**, **ResourceGroupName**, **ResourceName**, **ResourceType**, **ResourceId** a **časové razítko** které jsou požadovány pro sadu runbook k identifikaci prostředku, na kterém je provedením akce. Výstrahy kontextu vložené v části textu **WebhookData** objekt posílá sady runbook a je přístupný pomocí **Webhook.RequestBody** vlastnost

### <a name="example"></a>Příklad
Vytvoření virtuálního počítače Azure ve vašem předplatném a přidružení [výstrahu, kterou chcete sledovat metriku procento procesoru](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Během vytváření výstrahy zkontrolujte, zda že vyplnění pole webhooku s adresou URL webhooku, který byl vygenerován při vytváření webhooku.

Následující vzorový runbook se aktivuje, když pravidlo výstrahy se změní na aktivní a shromáždí parametry kontext výstrahy, které jsou požadovány pro sadu runbook k identifikaci prostředku, na kterém je provedením akce.

    workflow Invoke-RunbookUsingAlerts
    {
        param (      
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData.
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookBody    =   $WebhookData.RequestBody
            $WebhookHeaders =   $WebhookData.RequestHeader

            # Outputs information on the webhook name that called This
            Write-Output "This runbook was started from webhook $WebhookName."


            # Obtain the WebhookBody containing the AlertContext
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody)
            Write-Output "`nWEBHOOK BODY"
            Write-Output "============="
            Write-Output $WebhookBody

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information
            Write-Output "`nALERT CONTEXT DATA"
            Write-Output "==================="
            Write-Output $AlertContext.name
            Write-Output $AlertContext.subscriptionId
            Write-Output $AlertContext.resourceGroupName
            Write-Output $AlertContext.resourceName
            Write-Output $AlertContext.resourceType
            Write-Output $AlertContext.resourceId
            Write-Output $AlertContext.timestamp

            # Act on the AlertContext data, in our case restarting the VM.
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine.
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential"
            Add-AzureAccount -Credential $cred
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN"

            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        }
        else  
        {
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }



## <a name="next-steps"></a>Další postup
* Podrobnosti o různých způsobech spuštění sady runbook najdete v tématu [spuštění sady Runbook](automation-starting-a-runbook.md).
* Informace o zobrazení stavu úlohy Runbooku, najdete v části [spuštění sady Runbook ve službě Azure Automation](automation-runbook-execution.md).
* Naučte se používat Azure Automation provést určitou akci u výstrahy Azure, najdete v tématu [napravit výstrahy virtuálních počítačů Azure pomocí Runbooků Automation](automation-azure-vm-alert-integration.md).
