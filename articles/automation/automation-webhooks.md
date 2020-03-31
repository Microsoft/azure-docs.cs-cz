---
title: Spuštění runbooku Azure Automation pomocí webhooku
description: Webhook, který umožňuje klientovi spustit runbook v Azure Automation z volání HTTP.  Tento článek popisuje, jak vytvořit webhooku a jak volat jeden spustit runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367019"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Spuštění runbooku Azure Automation pomocí webhooku

Webhook umožňuje externí službě spustit konkrétní runbook v Azure Automation prostřednictvím jediného požadavku HTTP. Mezi externí služby patří služby Azure DevOps Services, GitHub, protokoly Azure Monitoru a vlastní aplikace. Taková služba můžete použít webhooku ke spuštění sady Runbook bez implementace úplné řešení pomocí rozhraní API Azure Automation. Webhooky můžete porovnat s jinými metodami spuštění sady Runbook v [aplikaci Spuštění sady Runbook v Azure Automation](automation-starting-a-runbook.md).

> [!NOTE]
> Použití webhooku ke spuštění runbooku Pythonu není podporováno.

![WebhooksPřehled](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="webhook-properties"></a>Vlastnosti webhooku

Následující tabulka popisuje vlastnosti, které je nutné nakonfigurovat pro webhooku.

| Vlastnost | Popis |
|:--- |:--- |
| Name (Název) |Název webhooku. Můžete zadat libovolný název, který chcete, protože není vystaven klientovi. Používá se jenom k identifikaci runbooku v Azure Automation. Jako osvědčený postup byste měli dát webhooku název související s klientem, který jej používá. |
| zprostředkovatele identity |URL webhooku. Toto je jedinečná adresa, kterou klient volá pomocí protokolu HTTP POST ke spuštění sady Runbook propojené s webhookem. Je automaticky generována při vytváření webhooku. Vlastní adresu URL nelze zadat. <br> <br> Adresa URL obsahuje token zabezpečení, který umožňuje systému jiného výrobce vyvolat runbook bez dalšího ověřování. Z tohoto důvodu byste měli adresu URL považovat za heslo. Z bezpečnostních důvodů můžete zobrazit jenom adresu URL na webu Azure Portal při vytváření webhooku. Poznamenejte si adresu URL na bezpečném místě pro budoucí použití. |
| Datum vypršení platnosti | Datum vypršení platnosti webhooku, po kterém již nemůže být použit. Můžete upravit datum vypršení platnosti po vytvoření webhooku, pokud webhooku nevypršela. |
| Povoleno | Nastavení označující, zda je webhook u vytvoření ve výchozím nastavení povolen. Pokud nastavíte tuto vlastnost na Zakázáno, žádný klient může použít webhook. Tuto vlastnost můžete nastavit při vytváření webhooku nebo kdykoli jindy po jeho vytvoření. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametry použité při spuštění webhooku v runbooku

Webhook umíš definovat hodnoty pro parametry sady Runbook, které se používají při spuštění sady Runbook. Webhook musí obsahovat hodnoty pro všechny povinné parametry sady Runbook a může obsahovat hodnoty pro volitelné parametry. Hodnotu parametru nakonfigurovanou na webhooku lze upravit i po vytvoření webhooku. Více webových háčků propojených s jedním runbookem může každý použít jiné hodnoty parametrů sady Runbook. Když klient spustí runbook pomocí webhooku, nemůže přepsat hodnoty parametrů definované v webhooku.

Chcete-li přijímat data z klienta, runbook podporuje jeden parametr s názvem `WebhookData`. Tento parametr definuje objekt obsahující data, která klient obsahuje v požadavku POST.

![Vlastnosti WebhookData](media/automation-webhooks/webhook-data-properties.png)

Parametr `WebhookData` má následující vlastnosti:

| Vlastnost | Popis |
|:--- |:--- |
| `WebhookName` | Název webhooku. |
| `RequestHeader` | Hashtable obsahující záhlaví příchozí post požadavku. |
| `RequestBody` | Tělo příchozí post požadavku. Toto tělo zachová veškeré formátování dat, například řetězec, JSON, XML nebo formulář kódovaný. Runbook musí být zapsán pro práci s očekávaným formátem dat. |

Neexistuje žádná konfigurace webhooku potřebné pro `WebhookData` podporu parametru a runbook není nutné ji přijmout. Pokud runbook nedefinuje parametr, všechny podrobnosti o požadavku odeslané z klienta jsou ignorovány.

> [!NOTE]
> Při volání webhooku by měl klient vždy ukládat všechny hodnoty parametrů v případě, že volání selže. Pokud dojde k výpadku sítě nebo problému s připojením, aplikace nemůže načíst neúspěšná volání webhooku.

Pokud zadáte hodnotu `WebhookData` při vytvoření webhooku, je přepsána, když webhook spustí runbook s daty z požadavku POST klienta. K tomu dochází i v případě, že aplikace neobsahuje žádná data v těle požadavku. 

Pokud spustíte runbook, `WebhookData` který definuje použití jiného mechanismu než webhooku, můžete zadat `WebhookData` hodnotu, kterou sada Runbook rozpozná. Tato hodnota by měla být objekt `WebhookData` se [stejnými vlastnostmi](#webhook-properties) jako parametr tak, aby `WebhookData` sada Runbook může pracovat s ním stejně jako pracuje se skutečnými objekty předanými webhooku.

Například pokud spouštíte následující runbook z portálu Azure a chcete předat některé ukázkové webhooku data pro testování, musíte předat data v JSON v uživatelském rozhraní.

![Parametr WebhookData z uživatelského rozhraní](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Pro další příklad runbooku definujme následující `WebhookData`vlastnosti pro :

* **WebhookName**: MyWebhook
* **RequestBody**:`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Nyní předáme následující objekt JSON v `WebhookData` uI pro parametr. Tento příklad s konce řádku a znaky nového řádku odpovídá formátu, který je předán z webhooku.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Spustit parametr WebhookData z uživatelského rozhraní](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation zaznamenává hodnoty všech vstupních parametrů s úlohou runbooku. Proto je každý vstup poskytnutý klientem v požadavku webhooku zaznamenán a k dispozici všem, kdo mají přístup k automatizaci úlohy. Z tohoto důvodu byste měli být opatrní při zahrnutí citlivých informací do volání webhooku.

## <a name="webhook-security"></a>Zabezpečení webhooku

Zabezpečení webhooku závisí na ochraně osobních údajů jeho adresy URL, která obsahuje token zabezpečení, který umožňuje vyvolání webhooku. Azure Automation neprovádí žádné ověřování na požadavek tak dlouho, dokud je provedena na správnou adresu URL. Z tohoto důvodu by klienti neměli používat webhooky pro sady Runbook, které provádějí vysoce citlivé operace bez použití alternativního prostředku ověření požadavku.

Můžete zahrnout logiku do sady Runbook k určení, pokud je volána webhooku. Nechat runbook zkontrolovat `WebhookName` vlastnost `WebhookData` parametru. Runbook můžete provést další ověření hledáním `RequestHeader` konkrétní `RequestBody` informace v a vlastnosti.

Další strategií je, aby sada Runbook provedla ověření externí podmínky, když obdrží požadavek webhooku. Zvažte například runbook, který se nazývá GitHub kdykoli v případě, že je nový závazek do úložiště GitHub. Runbook se může připojit ke GitHubu a ověřit, že před pokračováním došlo k novému potvrzení.

## <a name="creating-a-webhook"></a>Vytvoření webového háčku

Pomocí následujícího postupu vytvořte nový webhook propojený s runbookem na webu Azure Portal.

1. Na stránce Runbook na portálu Azure klikněte na runbook, který webhook začne zobrazovat podrobnosti runbooku. Ujistěte se, že je pole **Stav** sady Runbook nastaveno na **Publikováno**.
2. Kliknutím na **Webhook** v horní části stránky otevřete stránku Přidat webový hook.
3. Kliknutím na **Vytvořit nový webhook** otevřete stránku Vytvořit webhooku.
4. Vyplňte pole **Název** a **Datum vypršení platnosti** pro webhooka a určete, zda má být povoleno. Další informace o těchto vlastnostech naleznete ve [vlastnostech webhooku.](#webhook-properties)
5. Klikněte na ikonu kopírování a stisknutím kombinace kláves Ctrl+C zkopírujte adresu URL webového háčku. Pak to nahrajte na bezpečném místě. 

    > [!NOTE]
    > Po vytvoření webhooku nelze adresu URL znovu načíst.

   ![Adresa URL webového háku](media/automation-webhooks/copy-webhook-url.png)

1. Kliknutím na **Parametry** zadejte hodnoty parametrů runbooku. Pokud sada Runbook má povinné parametry, nelze vytvořit webhooku, pokud nezadáte hodnoty.
1. Kliknutím na **Vytvořit** webhook vytvořte.

## <a name="using-a-webhook"></a>Použití webového háčku

Chcete-li použít webhooku po jeho vytvoření, `POST` musí klient vydat požadavek HTTP s adresou URL pro webhook. Syntaxe je:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient obdrží jeden z následujících návratových kódů z `POST` požadavku.

| kód | Text | Popis |
|:--- |:--- |:--- |
| 202 |Accepted |Požadavek byl přijat a runbook byl úspěšně zařazen do fronty. |
| 400 |Chybný požadavek |Žádost nebyla přijata z jednoho z následujících důvodů: <ul> <li>Platnost webového háku vypršela.</li> <li>Webhook je zakázán.</li> <li>Token v adrese URL je neplatný.</li>  </ul> |
| 404 |Nebyl nalezen. |Žádost nebyla přijata z jednoho z následujících důvodů: <ul> <li>Webhook nebyl nalezen.</li> <li>Runbook nebyl nalezen.</li> <li>Účet nebyl nalezen.</li>  </ul> |
| 500 |Vnitřní chyba serveru |Adresa URL byla platná, ale došlo k chybě. Odešlete žádost znovu. |

Za předpokladu, že požadavek je úspěšný, odpověď webhooku obsahuje ID úlohy ve formátu JSON, jak je znázorněno níže. Obsahuje jedno ID úlohy, ale formát JSON umožňuje potenciální budoucí vylepšení.

```json
{"JobIds":["<JobId>"]}
```

Klient nemůže určit, kdy se úloha sady Runbook dokončí nebo jeho stav dokončení z webhooku. Tyto informace můžete zjistit pomocí ID úlohy s jiným mechanismem, jako je [například Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) nebo [rozhraní API Azure Automation .](/rest/api/automation/job)

## <a name="renewing-a-webhook"></a><a name="renew-webhook"></a>Obnovení webového háčku

Když je vytvořen webhooku, má dobu platnosti deset let, po které automaticky vyprší. Po vypršení platnosti webového háčku jej nelze znovu aktivovat. Můžete ji odebrat a znovu vytvořit. 

Můžete rozšířit webhook, který nedosáhl doby vypršení platnosti. Rozšíření webového háčku:

1. Přejděte do sady Runbook, která obsahuje webhook. 
2. V části **Zdroje vyberte Možnost Webhooky** . **Resources** 
3. Klikněte na webhook, který chcete rozšířit. 
4. Na stránce Webhook zvolte nové datum a čas vypršení platnosti a klepněte na tlačítko **Uložit**.

## <a name="sample-runbook"></a>Ukázkový runbook

Následující ukázková sada runbook přijímá data webhooku a spouští virtuální počítače zadané v těle požadavku. Chcete-li tuto knihu runbook otestovat, klikněte v účtu Automation v části **Runbook**na **Vytvořit runbook**. Pokud nevíte, jak vytvořit runbook, přečtěte si informace [o vytvoření runbooku](automation-quickstart-create-runbook.md).

> [!NOTE]
> Pro negrafické sady Runbook `Add-AzAccount` `Add-AzureRMAccount` prostředí PowerShell a jsou aliasy pro [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Můžete použít tyto rutiny nebo můžete [aktualizovat moduly](automation-update-azure-modules.md) v účtu Automation na nejnovější verze. Možná budete muset aktualizovat moduly, i když jste právě vytvořili nový účet Automatizace.

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

    # Retrieve VMs from Webhook request body
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
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>Testování vzorku

Následující příklad používá prostředí Windows PowerShell ke spuštění sady Runbook s webovým hákem. Jakýkoli jazyk, který může vytvořit požadavek HTTP, může používat webhook. Jako příklad se zde používá prostředí Windows PowerShell.

Runbook očekává seznam virtuálních počítačů formátovaných v JSON v těle požadavku. Sada Runbook také ověří, zda záhlaví obsahují definovanou zprávu k ověření, že volající webhooku je platný.

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

Následující příklad ukazuje text požadavku, který je k dispozici `RequestBody` pro `WebhookData`runbook ve vlastnosti . Tato hodnota je formátována v JSON, aby byla kompatibilní s formátem obsaženým v textu požadavku.

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

Následující obrázek znázorňuje požadavek odeslaný z prostředí Windows PowerShell a výslednou odpověď. ID úlohy je extrahován z odpovědi a převedenna řetězec.

![Tlačítko Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak pomocí Azure Automation provést akci ve výstrahách Azure, najdete v [tématu Použití výstrahy k aktivaci runbooku Azure Automation](automation-create-alert-triggered-runbook.md).
