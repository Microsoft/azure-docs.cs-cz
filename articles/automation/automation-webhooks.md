---
title: Spuštění runbooku Azure Automation s webhooku
description: Webhook, který umožňuje klientovi spuštění runbooku ve službě Azure Automation z volání protokolu HTTP.  Tento článek popisuje, jak vytvořit webhook a volání jednoho spuštění runbooku.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3da4ecb1193959fcc8782f8aa5fdf32c130ee238
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840142"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Spuštění runbooku Azure Automation s webhooku

A *webhooku* umožňuje spuštění konkrétního runbooku ve službě Azure Automation prostřednictvím jednoho požadavku HTTP. To umožňuje externích služeb, jako je Azure DevOps Services, Githubu, Azure Log Analytics nebo vlastních aplikací ke spouštění sad runbook bez implementace celé řešení pomocí rozhraní API služby Azure Automation.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Můžete porovnat webhooky k dalším metodám spuštění sady runbook [spuštění runbooku ve službě Azure Automation](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Podrobnosti o webhooku

Následující tabulka popisuje vlastnosti, které je nutné nakonfigurovat pro webhook.

| Vlastnost | Popis |
|:--- |:--- |
| Název |Můžete zadat libovolný název, který chcete pro webhook, protože to není vystavený klienta. Používá se pouze pro vás k identifikaci sady runbook ve službě Azure Automation. <br> Jako osvědčený postup musíte by měl pojmenovat webhook související klientovi, který ji používá. |
| zprostředkovatele identity |Adresa URL webhooku je jedinečná adresa, která volá klienta pomocí metody POST protokolu HTTP pro spuštění sady runbook propojena k webhooku. Je generována automaticky při vytvoření webhooku. Nelze zadat vlastní adresu URL. <br> <br> Adresa URL obsahuje token zabezpečení, která umožňuje sady runbook, který má být volána systémem třetí strany se nevyžaduje další ověřování. Z tohoto důvodu by zpracovávat jako heslo. Z bezpečnostních důvodů můžete jenom zobrazit adresu URL na webu Azure Portal v době, kdy se webhook vytvoří. Poznačte si adresu URL na bezpečném místě pro budoucí použití. |
| Datum konce platnosti |Stejně jako certifikát má každý webhooku datum vypršení platnosti, po kterém již slouží. Po vytvoření webhooku, je možné upravit toto datum vypršení platnosti. |
| Povoleno |Webhook se ve výchozím nastavení povoleno, při jeho vytvoření. Pokud je nastavena na hodnotu zakázáno, pak žádný klient je moct používat. Můžete nastavit **povoleno** vlastnost při vytváření webhooku nebo kdykoli po jeho vytvoření. |

### <a name="parameters"></a>Parametry

Webhook můžete definovat hodnoty pro parametry sady runbook, které se používají při spuštění runbooku pomocí tohoto webhooku. Webhook musí obsahovat hodnoty všech povinných parametrů runbooku a může obsahovat hodnoty nepovinných parametrů. Hodnotu parametru, který je nakonfigurovaný tak, aby webhook můžete změnit i po vytvoření webhooku. Více webhooky, které jsou propojeny s jedné sady runbook můžete použít různé hodnoty parametrů.

Při spuštění klienta sady runbook pomocí webhooku, nemohou přepsat hodnoty parametrů definované v webhooku. Přijímat data z klienta, můžete sadu runbook přijímat jeden parametr s názvem **$WebhookData** typu [object], který obsahuje data, která obsahuje klient v požadavku POST.

![Parametr Webhookdata vlastnosti](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** objektu má následující vlastnosti:

| Vlastnost | Popis |
|:--- |:--- |
| WebhookName |Název webhooku. |
| RequestHeader |Zatřiďovací tabulku obsahující záhlaví příchozí požadavek POST. |
| Includesearchresults: true |Tělo z příchozí požadavek POST. To zachovává veškeré formátování, jako je například řetězec JSON, XML, nebo data formuláře kódovaná v řetězci. Sada runbook musí být napsané pro práci s formátem dat, která se očekává. |

Neexistuje žádná konfigurace webhooku potřebné k podpoře **$WebhookData** parametr a sada runbook není potřeba ji přijmout. Pokud sada runbook nedefinuje parametr, je ignorován nějaké podrobnosti o žádosti odeslané z klienta.

Pokud zadáte hodnotu pro $WebhookData při vytváření webhooku, že hodnota je přepsána při webhooku spustí runbook s daty z klientského požadavku POST, i v případě, že klient neobsahuje žádná data v textu požadavku. Když spustíte runbook, který má $WebhookData pomocí jiné metody než webhooku, je pro $Webhookdata, který je rozpoznán runbooku zadat hodnotu. Tato hodnota by měla být objekt se stejným [vlastnosti](#details-of-a-webhook) jako $Webhookdata tak, aby sada runbook správně s ním mohli pracovat stejně jako kdybyste se pracovali s skutečný parametr WebhookData předávány webhooku.

Například pokud jsou od webu Azure portal následující sady runbook a chcete předat nějaké ukázkové WebhookData pro testování, protože parametr WebhookData je objekt, je předat jako dokumenty JSON v uživatelském rozhraní.

![Parametr WebhookData z uživatelského rozhraní](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Následující sady runbook, pokud mají následující vlastnosti pro parametr WebhookData:

* WebhookName: *MyWebhook*
* Includesearchresults: TRUE: *[{"ResourceGroup": "myResourceGroup", "Name": "vm01"}, {"ResourceGroup": "myResourceGroup", "Name": "vm02"}]*

Pak by předat následující hodnotu JSON v uživatelském rozhraní pro parametr WebhookData. Vrátí následující příklad se na začátek a znaky nového řádku odpovídá formátu, který je předán z webhooku.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Parametr WebhookData začátku z uživatelského rozhraní](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Hodnoty všechny vstupní parametry jsou protokolovány k úloze runbooku. To znamená, že žádný vstup klientem v požadavku webhooku bude zaznamenané a k dispozici všem uživatelům s přístupem k automatizaci úloh.  Z tohoto důvodu měli byste být opatrní o citlivé informace, včetně ve volání webhooku.

## <a name="security"></a>Zabezpečení

Zabezpečení webhooku spoléhá na ochranu osobních údajů její adresu URL, který obsahuje token zabezpečení, které umožňuje vyvolat. Azure Automation neprovádí ověřování na žádost, tak dlouho, dokud ho tvoří na správnou adresu URL. Z tohoto důvodu by neměl webhooků použít pro sady runbook, které provádějí vysoce citlivé a funkce bez použití alternativní způsob ověření požadavku.

Můžete zahrnout logiky v sadě runbook, chcete-li zjistit, že byla volána webhookem kontrolou **WebhookName** vlastnost parametru $WebhookData. Runbook by mohl provést další ověření tím, že hledají konkrétní informace v **RequestHeader** nebo **includesearchresults: true** vlastnosti.

Jiné strategie je, aby sada runbook ověření některé externí podmínky při přijetí požadavku webhooku. Představte si třeba sadu runbook, která je volána z Githubu, vždycky, když je nová potvrzení změn do úložiště GitHub. Sada runbook může připojení ke Githubu k ověření, že nedošlo k nové potvrzení, než budete pokračovat.

## <a name="creating-a-webhook"></a>Vytvoření webhooku

Pomocí následujícího postupu vytvořte nový webhook propojené k sadě runbook na portálu Azure portal.

1. Z **stránky sady Runbook** na webu Azure Portal, klikněte na runbook, který se spustí se webhook. Chcete-li zobrazit stránku s jeho podrobnostmi. Zkontrolujte sadu runbook **stav** je **publikováno**.
2. Klikněte na tlačítko **Webhooku** v horní části stránky otevřete **přidat Webhook** stránky.
3. Klikněte na tlačítko **vytvořit nový webhook** otevřít **stránka pro vytvoření webhooku**.
4. Zadejte **název**, **datum vypršení platnosti** pro webhook a určuje, zda se funkce má povolit. V tématu [podrobnosti webhooku](#details-of-a-webhook) pro další informace o těchto vlastností.
5. Kliknutím na ikonu kopírování a stisknutím kláves Ctrl + C zkopírujte adresu URL webhooku. Potom uložte ho na bezpečném místě. **Po vytvoření webhooku, nelze znovu načíst adresu URL.**

   ![Adresa URL webhooku](media/automation-webhooks/copy-webhook-url.png)

1. Klikněte na tlačítko **parametry** k poskytnutí hodnot pro parametry runbooku. Pokud má runbook povinné parametry, pak nemůžete vytvořit webhook, pokud jsou k dispozici hodnoty.
1. Klikněte na tlačítko **vytvořit** k vytvoření webhooku.

## <a name="using-a-webhook"></a>Pomocí webhooku

Použití webhooku po jeho vytvoření, musíte vydat klientské aplikace HTTP POST s adresou URL webhooku. Syntaxe webhooku je v následujícím formátu:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient obdrží jednu z následujících návratových kódů z požadavku POST.

| Kód | Text | Popis |
|:--- |:--- |:--- |
| 202 |Přijato |Žádost byla přijata a sada runbook byla úspěšně zařadil do fronty. |
| 400 |Chybný požadavek |Požadavek nebyl přijat pro jednu z následujících důvodů: <ul> <li>Webhooku vypršela platnost.</li> <li>Je webhook zakázaný.</li> <li>Token v adrese URL je neplatný.</li>  </ul> |
| 404 |Nenalezené |Požadavek nebyl přijat pro jednu z následujících důvodů: <ul> <li>Webhook se nenašel.</li> <li>Sada runbook nebyla nalezena.</li> <li>Účet nebyl nalezen.</li>  </ul> |
| 500 |Vnitřní chyba serveru |Adresa URL byla platná, ale došlo k chybě. Požadavek odešlete znovu. |

Za předpokladu, že je žádost úspěšná, odpověď webhooku obsahuje úlohu s ID ve formátu JSON následujícím způsobem. Bude obsahovat ID jedné úlohy, ale umožňuje formát JSON pro potenciální budoucí vylepšení.

```json
{"JobIds":["<JobId>"]}
```

Klient nemůže určit po dokončení úlohy runbooku nebo její stav dokončení od webhooku. Může zjistit tyto informace, ID úlohy pomocí jiné metody, jako [prostředí Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) nebo [rozhraní API služby Azure Automation](/rest/api/automation/job).

## <a name="sample-runbook"></a>Ukázkové sady runbook

Následující vzorový runbook přijme webhook data a spustí virtuální počítače zadaný v textu požadavku. K otestování této sady runbook ve vašem účtu Automation v části **sady Runbook**, klikněte na tlačítko **+ přidat runbook**. Pokud si nejste jisti postup vytvoření sady runbook, přečtěte si téma [vytvoření sady runbook](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)



# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

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

Následující příklad používá prostředí Windows PowerShell pro spuštění sady runbook pomocí webhooku. Webhook; můžete použít libovolný jazyk, který umí vytvářet požadavky HTTP Prostředí Windows PowerShell slouží jako příklad.

Sada runbook očekává seznam virtuálních počítačů ve formátu JSON v textu požadavku. Sada runbook ověřuje také, že obsahují záhlaví konkrétně definované zprávy ověřit volající webhooku je platná.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-RestMethod -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

Následující příklad ukazuje, text, který je k dispozici pro sadu runbook v požadavku **includesearchresults: true** vlastnost **WebhookData**. To je formátovat jako dokumenty JSON, protože bylo formátu, který je zahrnutý v textu požadavku.

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

Žádosti odeslané z prostředí Windows PowerShell a výsledné odpovědi na následujícím obrázku. ID úlohy je extrahován z odpovědi a převedeno na řetězec.

![Tlačítko Webhooků](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Další postup

* Zjistěte, jak reagovat na upozornění Azure pomocí Azure Automation, najdete v článku [pomocí upozornění můžete aktivovat runbooku Azure Automation](automation-create-alert-triggered-runbook.md).
