---
title: Spuštění Azure Automation Runbooku z Webhooku
description: V tomto článku se dozvíte, jak pomocí Webhooku spustit Runbook v Azure Automation z volání HTTP.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: df19f32be41b17e13a9da575e828830e29da4e55
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98894758"
---
# <a name="start-a-runbook-from-a-webhook"></a>Spuštění runbooku z webhooku

Webhook umožňuje externí službě spustit konkrétní sadu Runbook v Azure Automation prostřednictvím jedné žádosti HTTP. Mezi externí služby patří Azure DevOps Services, GitHub, protokoly Azure Monitor a vlastní aplikace. Taková služba může pomocí Webhooku spustit Runbook bez implementace kompletního rozhraní Azure Automation API. Webhooky můžete porovnat s jinými metodami spuštění sady Runbook při [Spuštění Runbooku v Azure Automation](./start-runbooks.md).

> [!NOTE]
> Použití Webhooku ke spuštění Runbooku v Pythonu se nepodporuje.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Informace o požadavcích klientů pro TLS 1,2 s Webhooky najdete v tématu [vynucení TLS 1,2 pro Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="webhook-properties"></a>Vlastnosti Webhooku

Následující tabulka popisuje vlastnosti, které musíte nakonfigurovat pro Webhook.

| Vlastnost | Popis |
|:--- |:--- |
| Název |Název Webhooku Můžete zadat jakýkoli název, který chcete, protože není k dispozici klientovi. Používá se pouze k identifikaci Runbooku v Azure Automation. Jako osvědčený postup byste měli Webhook dát název týkající se klienta, který ho používá. |
| URL |Adresa URL Webhooku Jedná se o jedinečnou adresu, kterou klient volá s HTTP POST, aby mohl spustit Runbook propojený s webhookem. Při vytváření Webhooku se automaticky vygeneruje. Nemůžete zadat vlastní adresu URL. <br> <br> Adresa URL obsahuje token zabezpečení, který umožňuje systému třetí strany vyvolat sadu Runbook bez dalšího ověřování. Z tohoto důvodu byste měli zacházet s adresou URL, jako je heslo. Z bezpečnostních důvodů můžete při vytváření Webhooku zobrazit jenom adresu URL v Azure Portal. Poznamenejte si adresu URL v zabezpečeném umístění pro budoucí použití. |
| Datum vypršení platnosti | Datum vypršení platnosti Webhooku, po kterém ho už nebude možné používat. Po vytvoření Webhooku můžete upravit datum vypršení platnosti, dokud nevypršela platnost Webhooku. |
| Povoleno | Nastavení určující, zda je Webhook ve výchozím nastavení povolený, když je vytvořený. Pokud tuto vlastnost nastavíte na zakázáno, nebude moct Webhook používat žádný klient. Tuto vlastnost můžete nastavit při vytváření Webhooku nebo jakékoli jiné doby po jejím vytvoření. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametry používané při spuštění Runbooku pomocí Webhooku

Webhook může definovat hodnoty pro parametry Runbooku, které se použijí při spuštění Runbooku. Webhook musí zahrnovat hodnoty pro všechny povinné parametry Runbooku a může zahrnovat hodnoty pro volitelné parametry. Hodnota parametru konfigurovaná pro Webhook se dá upravit i po vytvoření Webhooku. Jednotlivé Webhooky propojené s jedním runbookm můžou každý použít jiné hodnoty parametrů Runbooku. Když klient spustí Runbook pomocí Webhooku, nemůže přepsat hodnoty parametrů definované ve Webhooku.

Chcete-li přijímat data z klienta, sada Runbook podporuje jeden parametr s názvem `WebhookData` . Tento parametr definuje objekt obsahující data, která klient zahrne do žádosti POST.

![Vlastnosti WebhookData](media/automation-webhooks/webhook-data-properties.png)

`WebhookData`Parametr má následující vlastnosti:

| Vlastnost | Popis |
|:--- |:--- |
| `WebhookName` | Název Webhooku |
| `RequestHeader` | Zatřiďovací tabulka obsahující hlavičky příchozího požadavku POST |
| `RequestBody` | Tělo příchozího požadavku POST Tento text uchovává jakékoli formátování dat, jako je například String, JSON, XML nebo Form-Encoded. Sada Runbook musí být zapsána, aby fungovala s očekávaným formátem dat. |

Pro podporu parametru není k dispozici žádná konfigurace Webhooku `WebhookData` a sada Runbook není pro přijetí nutná. Pokud sada Runbook nedefinuje parametr, budou se všechny podrobnosti žádosti odeslané z klienta ignorovat.

> [!NOTE]
> Při volání Webhooku by měl klient vždycky ukládat hodnoty parametrů pro případ, že volání neproběhne úspěšně. Pokud dojde k výpadku sítě nebo k potížím s připojením, aplikace nemůže načíst neúspěšná volání Webhooku.

Pokud zadáte hodnotu pro `WebhookData` Vytvoření Webhooku, bude přepsána, když Webhook spustí Runbook s daty z požadavku POST klienta. K tomu dojde i v případě, že aplikace neobsahuje žádná data v textu žádosti. 

Pokud spustíte Runbook, který definuje `WebhookData` použití jiného mechanismu než Webhooku, můžete zadat hodnotu `WebhookData` , kterou sada Runbook rozpozná. Tato hodnota by měla být objekt se stejnými [vlastnostmi](#webhook-properties) jako `WebhookData` parametr, aby sada Runbook mohla pracovat stejně, protože funguje se skutečnými `WebhookData` objekty předanými webhookem.

Například pokud spouštíte následující sadu Runbook z Azure Portal a chcete předat ukázková data Webhooku pro testování, je nutné předat data ve formátu JSON v uživatelském rozhraní.

![Parametr WebhookData z uživatelského rozhraní](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Pro další příklad Runbooku nadefinujeme následující vlastnosti pro `WebhookData` :

* **Webhook**: MyWebhook
* **Částmi**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Nyní předáte následující objekt JSON v uživatelském rozhraní pro `WebhookData` parametr. V tomto příkladu se znaky návratu a zalomení řádku shodují s formátem, který je předaný z Webhooku.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Spustit parametr WebhookData z uživatelského rozhraní](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation protokoluje hodnoty všech vstupních parametrů pomocí úlohy Runbooku. Všechny vstupy poskytované klientem v žádosti Webhooku se tak budou protokolovat a k dispozici pro kohokoli, kdo má přístup k úloze automatizace. Z tohoto důvodu byste měli být opatrní v souvislosti s zahrnutím citlivých informací v voláních webhooků.

## <a name="webhook-security"></a>Zabezpečení Webhooku

Zabezpečení Webhooku závisí na ochraně osobních údajů své adresy URL, která obsahuje token zabezpečení, který umožňuje vyvolání Webhooku. Azure Automation neprovádí žádné ověřování u žádosti, pokud je provedena na správnou adresu URL. Z tohoto důvodu by klienti neměli používat Webhooky pro Runbooky, které provádějí vysoce citlivé operace bez použití náhradních způsobů ověření žádosti.

Vezměte v úvahu následující strategie:

* Do sady Runbook můžete zahrnout logiku, která určí, zda je vyvolána webhookem. Nechat sadu Runbook, aby kontrolovala `WebhookName` vlastnost `WebhookData` parametru. Sada Runbook může provést další ověření tím, že hledá konkrétní informace ve `RequestHeader` `RequestBody` vlastnostech a.

* Nechat sadu Runbook při přijetí požadavku Webhooku provést nějaké ověření externí podmínky. Představte si třeba sadu Runbook, která je volána GitHubem, kdykoli je nové potvrzení do úložiště GitHub. Runbook se může připojit k GitHubu a ověřit, že před pokračováním došlo k novému potvrzení.

* Azure Automation podporuje značky služby virtuální sítě Azure, konkrétně [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Pomocí značek služeb můžete definovat řízení přístupu k síti pro [skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md#security-rules) nebo [Azure firewall](../firewall/service-tags.md) a Trigger webhooků z vaší virtuální sítě. Značky služeb lze použít místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby **GuestAndHybridManagement**  v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo zamítnout přenos pro službu Automation. Tato značka služby nepodporuje povolení podrobnějšího řízení tím, že omezí rozsahy IP adres na konkrétní oblast.

## <a name="create-a-webhook"></a>Vytvoření Webhooku

Pomocí následujícího postupu můžete vytvořit nový Webhook propojený s runbookm ve Azure Portal.

1. Na stránce sady Runbook v Azure Portal klikněte na Runbook, který Webhook spustí, aby se zobrazily podrobnosti Runbooku. Ujistěte se, že je pole **stav** Runbooku nastaveno na **Publikováno**.
2. Kliknutím na **Webhook** v horní části stránky otevřete stránku přidat Webhook.
3. Kliknutím na **vytvořit nový Webhook** otevřete stránku vytvořit Webhook.
4. Vyplňte pole **název** a **Datum vypršení platnosti** Webhooku a určete, jestli má být povolený. Další informace o těchto vlastnostech najdete v tématu [vlastnosti Webhooku](#webhook-properties) .
5. Kliknutím na ikonu kopírování a stisknutím kombinace kláves CTRL + C zkopírujte adresu URL Webhooku. Pak záznam uložte na bezpečném místě. 

    > [!NOTE]
    > Jakmile vytvoříte Webhook, nebudete moct znovu načíst adresu URL.

   ![Adresa URL Webhooku](media/automation-webhooks/copy-webhook-url.png)

1. Klikněte na **parametry** a zadejte hodnoty parametrů Runbooku. Pokud má sada Runbook povinné parametry, nelze vytvořit Webhook, pokud nezadáte hodnoty.

2. Kliknutím na **Vytvořit** webhook vytvořte.

## <a name="use-a-webhook"></a>Použití Webhooku

Pro použití Webhooku po jeho vytvoření musí klient vydat `POST` požadavek HTTP s adresou URL Webhooku. Syntaxe je:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient obdrží z požadavku jeden z následujících návratových kódů `POST` .

| Kód | Text | Popis |
|:--- |:--- |:--- |
| 202 |Přijato |Požadavek byl přijat a sada Runbook byla úspěšně zařazena do fronty. |
| 400 |Chybný požadavek |Žádost nebyla přijata z některého z následujících důvodů: <ul> <li>Platnost Webhooku vypršela.</li> <li>Webhook je zakázaný.</li> <li>Token v adrese URL je neplatný.</li>  </ul> |
| 404 |Nenalezeno |Žádost nebyla přijata z některého z následujících důvodů: <ul> <li>Webhook se nenašel.</li> <li>Runbook se nenašel.</li> <li>Účet nebyl nalezen.</li>  </ul> |
| 500 |Vnitřní chyba serveru |Adresa URL byla platná, ale došlo k chybě. Odešlete prosím žádost znovu. |

Za předpokladu, že žádost je úspěšná, odpověď Webhooku obsahuje ID úlohy ve formátu JSON, jak je znázorněno níže. Obsahuje jedno ID úlohy, ale formát JSON umožňuje možné budoucí vylepšení.

```json
{"JobIds":["<JobId>"]}
```

Klient nemůže určit, kdy má být úloha sady Runbook dokončena nebo její stav dokončení z Webhooku. Tyto informace může zjistit s použitím ID úlohy s jiným mechanismem, jako je například [Windows PowerShell](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) nebo [rozhraní Azure Automation API](/rest/api/automation/job).

## <a name="renew-a-webhook"></a>Obnovení Webhooku

Po vytvoření Webhooku má období platnosti dobu 10 let, po jehož uplynutí vyprší platnost automaticky. Po vypršení platnosti Webhooku ho nemůžete znovu aktivovat. Můžete ho odebrat a znovu vytvořit. 

Můžete zvětšit Webhook, který nedosáhl svého času vypršení platnosti. Postup při rozšiřování Webhooku:

1. Přejděte na sadu Runbook, která obsahuje Webhook. 
2. V části **prostředky** vyberte **Webhooky** . 
3. Klikněte na Webhook, který chcete zvětšit. 
4. Na stránce Webhook vyberte nové datum a čas vypršení platnosti a klikněte na **Uložit**.

## <a name="sample-runbook"></a>Ukázkový Runbook

Následující ukázkový Runbook akceptuje data Webhooku a spustí virtuální počítače zadané v textu žádosti. Pokud chcete tento Runbook otestovat, v účtu Automation v části **Runbooky** klikněte na **vytvořit Runbook**. Pokud si nejste jisti, jak vytvořit sadu Runbook, přečtěte si téma [Vytvoření Runbooku](automation-quickstart-create-runbook.md).

> [!NOTE]
> Pro negrafické Runbooky PowerShellu `Add-AzAccount` a `Add-AzureRMAccount` jsou aliasy pro [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Tyto rutiny můžete použít nebo můžete [své moduly](automation-update-azure-modules.md) v účtu Automation aktualizovat na nejnovější verze. Vaše moduly možná budete muset aktualizovat i v případě, že jste právě vytvořili nový účet Automation.

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

## <a name="test-the-sample"></a>Test ukázky

Následující příklad používá Windows PowerShell ke spuštění Runbooku pomocí Webhooku. Libovolný jazyk, který může učinit požadavek HTTP, může používat Webhook. Prostředí Windows PowerShell se tady používá jako příklad.

Sada Runbook očekává v těle žádosti seznam virtuálních počítačů formátovaných ve formátu JSON. Sada Runbook ověří také, že hlavičky obsahují definovanou zprávu pro ověření, že je volající Webhooku platný.

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

Následující příklad ukazuje tělo žádosti, která je k dispozici pro sadu Runbook ve `RequestBody` vlastnosti `WebhookData` . Tato hodnota je ve formátu JSON naformátovaná tak, aby byla kompatibilní s formátem obsaženým v těle žádosti.

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

* Chcete-li spustit sadu Runbook z výstrahy, přečtěte si téma [použití výstrahy ke spuštění sady runbook Azure Automation](automation-create-alert-triggered-runbook.md).