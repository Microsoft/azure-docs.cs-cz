---
title: Spuštění Azure Automation Runbooku pomocí Webhooku
description: Webhook, který umožňuje klientovi spustit Runbook v Azure Automation z volání HTTP.  Tento článek popisuje, jak vytvořit Webhook a jak ho zavolat, aby se spustil Runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: f86193e818a91132f9bbca447acadd7e81747522
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155821"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Spuštění Azure Automation Runbooku pomocí Webhooku

*Webhook* umožňuje spustit konkrétní sadu runbook v Azure Automation prostřednictvím jediného požadavku HTTP. To umožňuje, aby externí služby, jako jsou Azure DevOps Services, GitHub, Azure Monitor protokoly nebo vlastní aplikace, spouštěly Runbooky bez implementace úplného řešení pomocí rozhraní API Azure Automation.
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Webhooky můžete porovnat s jinými metodami spuštění sady Runbook při [Spuštění Runbooku v Azure Automation](automation-starting-a-runbook.md)

> [!NOTE]
> Použití Webhooku ke spuštění Runbooku v Pythonu se nepodporuje.

## <a name="details-of-a-webhook"></a>Podrobnosti Webhooku

Následující tabulka popisuje vlastnosti, které musíte nakonfigurovat pro Webhook.

| Vlastnost | Popis |
|:--- |:--- |
| Name (Název) |Můžete zadat libovolný název, který chcete pro Webhook, protože není k dispozici klientovi. Používá se pouze k identifikaci Runbooku v Azure Automation. <br> Jako osvědčený postup byste měli Webhook dát název týkající se klienta, který ho používá. |
| Adresa URL |Adresa URL Webhooku je jedinečná adresa, kterou klient volá s HTTP POST, aby mohl spustit Runbook propojený s webhookem. Při vytváření Webhooku se automaticky vygeneruje. Nemůžete zadat vlastní adresu URL. <br> <br> Adresa URL obsahuje token zabezpečení, který umožňuje, aby se Runbook vyvolal systémem jiného výrobce bez dalšího ověřování. Z tohoto důvodu by mělo být zacházeno jako s heslem. Z bezpečnostních důvodů můžete adresu URL v Azure Portal zobrazit jenom v době, kdy se Webhook vytvoří. Poznamenejte si adresu URL v zabezpečeném umístění pro budoucí použití. |
| Datum vypršení platnosti |Podobně jako u certifikátu má každý Webhook datum vypršení platnosti, ve kterém už ho nebude možné použít. Toto datum vypršení platnosti lze změnit po vytvoření Webhooku, dokud nevypršela platnost Webhooku. |
| Povoleno |Webhook je ve výchozím nastavení povolený, když se vytvoří. Pokud ho nastavíte na zakázáno, nebude ho moct používat žádný klient. Vlastnost **Enabled** můžete nastavit při vytváření Webhooku nebo kdykoli po jeho vytvoření. |

### <a name="parameters"></a>Parametry

Webhook může definovat hodnoty pro parametry Runbooku, které se použijí, když tento Webhook spustí Runbook. Webhook musí zahrnovat hodnoty pro všechny povinné parametry Runbooku a může zahrnovat hodnoty pro volitelné parametry. Hodnota parametru konfigurovaná pro Webhook se dá upravit i po vytvoření Webhooku. Jednotlivé Webhooky, které jsou propojeny s jednou sadou Runbook, mohou mít různé hodnoty parametrů.

Když klient spustí Runbook pomocí Webhooku, nemůže přepsat hodnoty parametrů definované ve Webhooku. Pro příjem dat z klienta může sada Runbook přijmout jeden parametr s názvem **$WebhookData**. Tento parametr je typu [Object] obsahující data, která klient obsahuje v žádosti POST.

![Vlastnosti Webhookdata](media/automation-webhooks/webhook-data-properties.png)

Objekt **$WebhookData** má následující vlastnosti:

| Vlastnost | Popis |
|:--- |:--- |
| WebhookName |Název Webhooku |
| RequestHeader |Zatřiďovací tabulka obsahující hlavičky příchozího požadavku POST |
| částmi |Tělo příchozího požadavku POST. Tím se zachová formátování, jako je například String, JSON, XML nebo formovaná data s kódováním. Sada Runbook musí být zapsána, aby fungovala s očekávaným formátem dat. |

Není k dispozici žádná konfigurace Webhooku, která by podporovala parametr **$WebhookData** a sada Runbook není nutná k jejímu přijetí. Pokud sada Runbook nedefinuje parametr, budou se všechny podrobnosti žádosti odeslané z klienta ignorovat.

> [!NOTE]
> Při volání Webhooku byste měli vždycky ukládat hodnoty parametrů pro případ, že volání neproběhne úspěšně. Pokud dojde k výpadku sítě nebo k potížím s připojením, nebudete moct načíst neúspěšná volání Webhooku.

Pokud při vytváření Webhooku zadáte hodnotu pro $WebhookData, tato hodnota se přepíše, když Webhook spustí Runbook s daty z požadavku POST klienta, a to i v případě, že klient neobsahuje žádná data v textu žádosti. Pokud spustíte sadu Runbook, která má $WebhookData pomocí jiné metody než Webhooku, můžete zadat hodnotu pro $Webhookdata, kterou sada Runbook rozpozná. Tato hodnota by měla být objekt se stejnými [vlastnostmi](#details-of-a-webhook) jako $Webhookdata tak, aby sada Runbook mohla s ním správně pracovat, jako kdyby pracovala se skutečným Webhookdata předaným webhookem.

Například pokud spouštíte následující sadu Runbook z Azure Portal a chcete předat nějaké ukázkové WebhookData pro testování, protože WebhookData je objekt, měl by být předán jako JSON v uživatelském rozhraní.

![Parametr WebhookData z uživatelského rozhraní](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Pro následující sadu Runbook, pokud máte následující vlastnosti pro parametr WebhookData:

* Webhook: *MyWebhook*
* Částmi: *[{' resourceName ': ' myResourceGroup ', ' name ': ' vm01 '}; {' Resource ': ' myResourceGroup ', ' name ': ' vm02 '}]*

Pak předáte následující hodnotu JSON v uživatelském rozhraní pro parametr WebhookData. V následujícím příkladu se znaky návratu a nového řádku shodují s formátem předaným z Webhooku.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Spustit parametr WebhookData z uživatelského rozhraní](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Hodnoty všech vstupních parametrů jsou protokolovány pomocí úlohy Runbooku. To znamená, že všechny vstupy poskytované klientem v žádosti Webhooku se budou protokolovat a budou k dispozici všem uživatelům, kteří mají přístup k úloze automatizace.  Z tohoto důvodu byste měli být opatrní v souvislosti s zahrnutím citlivých informací v voláních webhooků.

## <a name="security"></a>Zabezpečení

Zabezpečení Webhooku závisí na ochraně osobních údajů své adresy URL, která obsahuje token zabezpečení, který umožňuje jeho vyvolání. Azure Automation neprovádí žádné ověřování na žádosti, pokud je provedena na správnou adresu URL. Z tohoto důvodu by Webhooky neměly být používány pro Runbooky, které provádějí vysoce citlivé funkce bez použití alternativního způsobu ověření žádosti.

Můžete zahrnout logiku v rámci Runbooku, abyste zjistili, že byla vyvolána webhookem, a to tak, že zkontroluje vlastnost **Webhooku** parametru $WebhookData. Sada Runbook může provést další ověření tím, že hledá konkrétní informace ve vlastnostech **RequestHeader** nebo **částmi** .

Další strategií je, aby sada Runbook prováděla při přijetí požadavku Webhooku nějaké ověření externí podmínky. Představte si například sadu Runbook, která je volána GitHubem, kdykoli je k dispozici nové potvrzení do úložiště GitHub. Runbook se může připojit k GitHubu a ověřit, že před pokračováním došlo k novému potvrzení.

## <a name="creating-a-webhook"></a>Vytvoření Webhooku

Pomocí následujícího postupu můžete vytvořit nový Webhook propojený s runbookm ve Azure Portal.

1. Na **stránce sady Runbook** v Azure Portal klikněte na Runbook, který Webhook spustí, a zobrazte jeho stránku s podrobnostmi. Ujistěte se, že je **stav** Runbooku **publikovaný**.
2. Kliknutím na **Webhook** v horní části stránky otevřete stránku **Přidat Webhook** .
3. Kliknutím na **vytvořit nový Webhook** otevřete **stránku vytvořit Webhook**.
4. Zadejte **název**, **Datum vypršení platnosti** Webhooku a určete, jestli má být povolený. Další informace o těchto vlastnostech najdete v [podrobnostech Webhooku](#details-of-a-webhook) .
5. Kliknutím na ikonu kopírování a stisknutím kombinace kláves CTRL + C zkopírujte adresu URL Webhooku. Pak záznam uložte na bezpečném místě. **Jakmile vytvoříte Webhook, nebudete moct znovu načíst adresu URL.**

   ![Adresa URL webhooku](media/automation-webhooks/copy-webhook-url.png)

1. Klikněte na **parametry** a zadejte hodnoty parametrů Runbooku. Pokud má runbook povinné parametry, nebudete moci webhook vytvořit, dokud nezadáte jejich hodnoty.
1. Kliknutím na **Vytvořit** webhook vytvořte.

## <a name="using-a-webhook"></a>Použití Webhooku

Pro použití Webhooku po jeho vytvoření musí klientská aplikace vydat HTTP POST s adresou URL Webhooku. Syntaxe Webhooku je v následujícím formátu:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient obdrží z požadavku POST jeden z následujících návratových kódů.

| kód | Text | Popis |
|:--- |:--- |:--- |
| 202 |Přijato |Požadavek byl přijat a sada Runbook byla úspěšně zařazena do fronty. |
| 400 |Chybný požadavek |Žádost nebyla přijata z některého z následujících důvodů: <ul> <li>Platnost Webhooku vypršela.</li> <li>Webhook je zakázaný.</li> <li>Token v adrese URL je neplatný.</li>  </ul> |
| 404 |Nenalezeno |Žádost nebyla přijata z některého z následujících důvodů: <ul> <li>Webhook se nenašel.</li> <li>Runbook se nenašel.</li> <li>Účet nebyl nalezen.</li>  </ul> |
| 500 |Vnitřní chyba serveru |Adresa URL byla platná, ale došlo k chybě. Odešlete prosím žádost znovu. |

Za předpokladu, že žádost je úspěšná, odpověď Webhooku obsahuje ID úlohy ve formátu JSON následujícím způsobem. Bude obsahovat jedno ID úlohy, ale formát JSON umožňuje možné budoucí vylepšení.

```json
{"JobIds":["<JobId>"]}
```

Klient nemůže určit, kdy má být úloha sady Runbook dokončena nebo její stav dokončení z Webhooku. Tyto informace může zjistit s použitím ID úlohy jinou metodou, jako je [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) nebo [rozhraní Azure Automation API](/rest/api/automation/job).

## <a name="renew-webhook"></a>Obnovení Webhooku

Při vytvoření Webhooku má doba platnosti deset let. Po uplynutí tohoto časového období vyprší platnost Webhooku automaticky. Po vypršení platnosti Webhooku se nedá znovu aktivovat. musí se odebrat a znovu vytvořit. Pokud Webhook nedorazil na svůj čas vypršení platnosti, dá se rozšířit.

Chcete-li Webhook zvětšit, přejděte na sadu Runbook, která obsahuje Webhook. V části **prostředky**vyberte **Webhooky** . Klikněte na Webhook, který chcete zvětšit. Tato akce otevře stránku **Webhooku** .  Vyberte nové datum a čas vypršení platnosti a klikněte na **Uložit**.

## <a name="sample-runbook"></a>Ukázkový Runbook

Následující ukázkový Runbook akceptuje data Webhooku a spustí virtuální počítače zadané v textu žádosti. Pokud chcete tento Runbook otestovat, klikněte v účtu Automation v části **Runbooky**na **+ Přidat Runbook**. Pokud si nejste jisti, jak vytvořit sadu Runbook, přečtěte si téma [Vytvoření Runbooku](automation-quickstart-create-runbook.md).

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

## <a name="test-the-example"></a>Test příkladu

Následující příklad používá Windows PowerShell ke spuštění Runbooku pomocí Webhooku. Libovolný jazyk, který může učinit požadavek HTTP, může používat Webhook. Prostředí Windows PowerShell se tady používá jako příklad.

Sada Runbook očekává v těle žádosti seznam virtuálních počítačů formátovaných ve formátu JSON. Sada Runbook ověří taky, že hlavičky obsahují definovanou zprávu pro ověření, jestli je volající Webhooku platný.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

Následující příklad ukazuje tělo žádosti, která je k dispozici pro sadu Runbook ve vlastnosti **částmi** třídy **WebhookData**. Tato hodnota je formátována jako JSON, protože se jedná o formát, který byl obsažen v těle požadavku.

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

Následující obrázek ukazuje požadavek odesílaný z Windows PowerShellu a výslednou odpověď. ID úlohy se extrahuje z odpovědi a převede na řetězec.

![Tlačítko webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak pomocí Azure Automation provést akci s výstrahami Azure, najdete v tématu [použití výstrahy ke spuštění sady Azure Automation Runbook](automation-create-alert-triggered-runbook.md).

