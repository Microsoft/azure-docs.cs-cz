---
title: Počínaje webhook, jehož runbook služby automatizace Azure
description: Webhook, která umožňuje klientovi spuštění sady runbook ve službě Azure Automation z volání protokolu HTTP.  Tento článek popisuje, jak vytvořit webhook, jehož a postup volání jednoho spuštění runbooku.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e047dffa86915b0cd6e8829ea27e0335e7f88cb2
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757152"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Počínaje webhook, jehož runbook služby automatizace Azure

A *webhooku* umožňuje spustit konkrétní runbook ve službě Azure Automation prostřednictvím jedné žádosti HTTP. To umožňuje externích služeb, jako je například Visual Studio Team Services, GitHub, analýzy protokolů Azure nebo vlastních aplikací ke spouštění sad runbook bez implementace úplné řešení pomocí rozhraní API služby Azure Automation.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Můžete porovnat webhooky k dalším metodám spuštění sady runbook [spuštění sady runbook ve službě Azure Automation](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Podrobnosti o webhook, jehož

Následující tabulka popisuje vlastnosti, které je nutné nakonfigurovat pro webhook, jehož.

| Vlastnost | Popis |
|:--- |:--- |
| Název |Můžete zadat libovolný název, který chcete použít pro webhook, jehož vzhledem k tomu, že to není vystavený klienta. Používá se pouze pro vás k identifikaci sady runbook ve službě Azure Automation. <br> Jako osvědčený postup musíte získat související klientovi, který se používá název webhooku. |
| zprostředkovatele identity |Adresa URL webhooku je jedinečnou adresu, která volá klienta pomocí metody POST protokolu HTTP pro spuštění sady runbook propojené s webhooku. Generuje se automaticky při vytvoření webhooku. Nelze zadat vlastní adresu URL. <br> <br> Adresa URL obsahuje token zabezpečení, který umožňuje sady runbook vyvolat systému třetích stran se žádné další ověřování. Z tohoto důvodu by zpracovávat jako heslo. Z bezpečnostních důvodů můžete jenom zobrazit adresu URL na portálu Azure v době, kdy je vytvoření webhooku. Poznamenejte si adresu URL na bezpečné místo pro budoucí použití. |
| Datum konce platnosti |Stejně jako certifikát má každý webhooku datum vypršení platnosti, po kterém již slouží. Po vytvoření webhooku můžete upravit toto datum vypršení platnosti. |
| Povoleno |Webhook, jehož je ve výchozím nastavení povolena, když je vytvořeno. Pokud je nastavena na zakázáno, je žádný klient moct používat. Můžete nastavit **povoleno** vlastnost při vytvoření webhooku nebo kdykoli po jeho vytvoření. |

### <a name="parameters"></a>Parametry

Webhook, jehož můžete definovat hodnoty pro parametry runbooku, které se použijí při spuštění runbooku pomocí tohoto webhooku. Webhook musí obsahovat hodnoty všech povinných parametrů runbooku a můžou obsahovat hodnoty pro volitelné parametry. Hodnotu parametru, který je nakonfigurován tak, aby webhook, jehož lze změnit i po vytvoření webhooku. Více webhooky propojené s jedné sady runbook můžete použít jiné hodnoty parametru.

Při spuštění sady runbook pomocí webhook, jehož klienta, je nejde přepsat hodnot parametrů definovaných v webhooku. Přijímat data z klienta, sada runbook může přijmout jeden parametr s názvem **$WebhookData** typu [object], který obsahuje data, která zahrnuje klient v požadavku POST.

![Vlastnosti Webhookdata](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** objekt má následující vlastnosti:

| Vlastnost | Popis |
|:--- |:--- |
| WebhookName |Název webhooku. |
| RequestHeader |Hodnota hash tabulku obsahující hlavičky příchozí požadavek POST. |
| RequestBody |Text příchozí požadavek POST. To zachová veškeré formátování, jako je například řetězec, formát JSON, XML, nebo data formuláře kódovaná v řetězci. Sada runbook musí být napsané pro práci s formátem dat, kterou je očekáván. |

Neexistuje žádná konfigurace webhooku potřebné k podpoře **$WebhookData** parametr a sada runbook není potřeba ji přijmout. Pokud sada runbook nedefinuje parametr, se ignoruje všechny podrobnosti o požadavek odeslaný z klienta.

Pokud zadáte hodnotu pro $WebhookData při vytváření webhooku, že hodnota je přepsat při webhooku spuštění sady runbook s daty z klienta žádosti, i v případě, že klient neobsahuje žádná data v textu požadavku. Pokud spustíte runbook, který má $WebhookData pomocí jiné metody než webhook, jehož, je zadat hodnotu pro $Webhookdata rozpoznávaném sady runbook. Tato hodnota by měla být objekt se stejným [vlastnosti](#details-of-a-webhook) jako $Webhookdata tak, aby sada runbook může správně s ním pracovat, jako kdyby byla práce s skutečné WebhookData předaná webhook, jehož.

Například pokud se spouštění následující sady runbook na portálu Azure a chcete předat některé ukázkové WebhookData pro testování, protože WebhookData je objekt, se mají být předány jako JSON v uživatelském rozhraní.

![Parametr WebhookData z uživatelského rozhraní](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Následující sady runbook, pokud máte následující vlastnosti pro parametr WebhookData:

* WebhookName: *MyWebhook*
* RequestBody: *[{'ResourceGroup': 'myResourceGroup', 'Name': 'vm01'}, {, ResourceGroup': 'myResourceGroup', 'Name': 'vm02'}]*

Následující hodnota JSON by pak předejte v uživatelském rozhraní pro parametr WebhookData. Vrátí následující příklad s návratu na začátek řádku a znaky nového řádku odpovídá formátu, který je předané z webhook, jehož.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Parametr WebhookData zahájení z uživatelského rozhraní](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Hodnoty všech vstupních parametrů se protokolují pod úloha sady runbook. To znamená, že žádný vstup klientem v požadavku webhooku bude protokolu a k dispozici všem uživatelům s přístupem k automatizaci úloh.  Z tohoto důvodu byste měli být opatrní při voláních webhooku včetně citlivé informace.

## <a name="security"></a>Zabezpečení

Zabezpečení webhooku spoléhá na ochranu osobních údajů jeho adresu URL, která obsahuje token zabezpečení, který mohou být volána. Služby Azure Automation neprovede žádné ověřování na žádost, dokud se provádí na správnou adresu URL. Z tohoto důvodu webhooky nepoužívejte pro sady runbook, které provádějí vysoce citlivých funkce bez použití alternativní způsob ověření požadavku.

Můžete zahrnout logiky v rámci sady runbook k určení, zda byla volána podle webhook, jehož kontrolou **WebhookName** vlastnost parametru $WebhookData. Sada runbook může provést další ověření tak, že vyhledá konkrétní informace v **RequestHeader** nebo **RequestBody** vlastnosti.

Další strategie se se runbook provést některé ověření externí podmínka v případě, že přijala žádost o webhooku. Představte si třeba sadu runbook, která je volána metodou Githubu, kdykoli dojde k nové potvrzení úložišti GitHub. Runbook se může připojit k Githubu k ověření, že nový potvrzení vyskytla než budete pokračovat.

## <a name="creating-a-webhook"></a>Vytváření webhooku

Použijte následující postup k vytvoření nové webhooku propojit k sadě runbook na portálu Azure.

1. Z **sady Runbook stránky** na portálu Azure klikněte na tlačítko runbook začínající webhooku zobrazíte stránku s jeho podrobnostmi.
1. Klikněte na tlačítko **Webhooku** v horní části stránky otevřete **přidat Webhooku** stránky.
1. Klikněte na tlačítko **vytvořit nové webhooku** otevřete **stránka pro vytvoření webhooku**.
1. Zadejte **název**, **datum vypršení platnosti** webhooku a jestli se má povolit. V tématu [podrobnosti o webhook, jehož](#details-of-a-webhook) pro další informace o těchto vlastností.
1. Kliknutím na ikonu kopírování a stisknutím Ctrl + C zkopírujte adresu URL webhooku. Potom zaznamenejte jej na bezpečném místě. **Po vytvoření webhooku nelze znovu načíst adresu URL.**

   ![Adresa URL webhooku](media/automation-webhooks/copy-webhook-url.png)

1. Klikněte na tlačítko **parametry** zadat hodnoty pro parametry runbooku. Pokud má runbook povinné parametry, pak nejste k vytvoření webhooku, pokud jsou zadané hodnoty.
1. Klikněte na tlačítko **vytvořit** k vytvoření webhooku.

## <a name="using-a-webhook"></a>Pomocí webhook, jehož

Klientské aplikace používat webhook, jehož po jeho vytvoření, vydá HTTP POST s adresou URL pro webhooku. Syntaxe webhooku je v následujícím formátu:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient obdrží jednu z následujících návratové kódy z žádosti.

| Kód | Text | Popis |
|:--- |:--- |:--- |
| 202 |Přijato |Žádost byla přijata, a sada runbook byla úspěšně zařazených do fronty. |
| 400 |Chybná žádost |Požadavek nebyl přijat pro jednu z následujících důvodů: <ul> <li>Webhook vypršela platnost.</li> <li>Webhook je zakázána.</li> <li>Token v adrese URL je neplatný.</li>  </ul> |
| 404 |Nenalezené |Požadavek nebyl přijat pro jednu z následujících důvodů: <ul> <li>Webhook nebyl nalezen.</li> <li>Sada runbook nebyla nalezena.</li> <li>Účet nebyl nalezen.</li>  </ul> |
| 500 |Vnitřní chyba serveru |Adresa URL byla platná, ale došlo k chybě. Odešlete požadavek znovu. |

Za předpokladu, že požadavek je úspěšné, webhooku odpovědi obsahuje id úlohy ve formátu JSON následujícím způsobem. Bude obsahovat id jedné úlohy, ale formát JSON umožňuje potenciální budoucí vylepšení.

```json
{"JobIds":["<JobId>"]}
```

Klient nemůže zjistit po dokončení úlohy runbooku nebo její stav dokončení od webhooku. Může zjistit, tyto informace id úlohy pomocí jiné metody, jako [prostředí Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) nebo [rozhraní API služby Azure Automation](/rest/api/automation/job).

## <a name="sample-runbook"></a>Ukázkové sady runbook

Následující vzorový runbook přijímá přijímá data webhooku a spustí virtuální počítače zadaný v textu požadavku. K testování této sady runbook ve vašem účtu Automation v části **Runbooky**, klikněte na tlačítko **+ přidat runbook**. Pokud si nejste jisti postup vytvoření sady runbook, přečtěte si téma [vytvoření sady runbook](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName" 

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal." 
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Testování v příkladu

Následující příklad používá ke spuštění sady runbook s webhook, jehož prostředí Windows PowerShell. Jakýkoli jazyk, který může odeslat požadavek HTTP, můžete použít webhooku; Prostředí Windows PowerShell slouží jako příklad sem.

Sada runbook očekává seznam virtuálních počítačů, které jsou ve formátu JSON v textu požadavku.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms

$response = Invoke-RestMethod -Method Post -Uri $uri -Body $body
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

Následující příklad ukazuje tělo žádosti, která je k dispozici v sadě runbook v **RequestBody** vlastnost **WebhookData**. To je naformátován jako JSON, protože formát, který je zahrnutý v textu požadavku, který byl.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

Následující obrázek ukazuje žádost odesílány z prostředí Windows PowerShell a výsledné odpovědi. Id úlohy je extrahována z odpovědi a převedeno na řetězec.

![Tlačítko Webhooky](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Další postup

* Naučte se používat Azure Automation provést určitou akci u výstrahy Azure, najdete v tématu [použít výstrahu pro spuštění runbooku automatizace Azure](automation-create-alert-triggered-runbook.md).
