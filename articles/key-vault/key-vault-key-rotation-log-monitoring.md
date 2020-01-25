---
title: Nastavení Azure Key Vault s koncovým otočením a auditováním klíčů | Microsoft Docs
description: Tento průvodce vám pomůže nastavit rotaci klíčů a monitorovat protokoly trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: a0aa20a8d1ddecfe401a4e099a4f298971779501
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720108"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Nastavení Azure Key Vault s použitím rotace a auditování klíčů

## <a name="introduction"></a>Představení

Po použití trezoru klíčů ho můžete začít používat k ukládání klíčů a tajných kódů. Vaše aplikace už nepotřebují uchovávat vaše klíče nebo tajné kódy, ale podle potřeby je můžou vyžádat z trezoru. Trezor klíčů umožňuje aktualizovat klíče a tajné kódy, aniž by to mělo vliv na chování vaší aplikace. tím se otevře spousta možností pro správu klíčů a tajných kódů.

>[!IMPORTANT]
> Příklady v tomto článku jsou k dispozici pouze pro účely ilustrace. Nejsou určené pro použití v produkčním prostředí. 

Tento článek vás provede:

- Příklad použití Azure Key Vault k uložení tajného kódu. V tomto článku je tajný kód uložený pomocí klíče účtu úložiště Azure, ke kterému má aplikace přistup. 
- Jak implementovat plánované otočení tohoto klíče účtu úložiště.
- Jak monitorovat protokoly auditu trezoru klíčů a vyvolávat výstrahy při neočekávaných žádostech.

> [!NOTE]
> Tento článek podrobně vysvětluje počáteční nastavení vašeho trezoru klíčů. Tyto informace najdete v tématu [co je Azure Key Vault?](key-vault-overview.md). Pokyny k rozhraní příkazového řádku pro různé platformy najdete v tématu [správa Key Vault pomocí Azure CLI](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Nastavení služby Key Vault

Pokud chcete aplikaci povolit načtení tajného klíče z Key Vault, musíte nejdřív vytvořit tajný kód a nahrát ho do svého trezoru.

Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:

```powershell
Connect-AzAccount
```

V automaticky otevíraném okně prohlížeče zadejte uživatelské jméno a heslo pro váš účet Azure. Prostředí PowerShell získá všechna předplatná, která jsou přidružená k tomuto účtu. PowerShell ve výchozím nastavení používá první.

Pokud máte více předplatných, možná budete muset zadat ten, který jste použili k vytvoření trezoru klíčů. Chcete-li zobrazit předplatná pro svůj účet, zadejte následující:

```powershell
Get-AzSubscription
```

Pokud chcete zadat předplatné, které je přidružené k trezoru klíčů, který budete přihlašovat, zadejte:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Vzhledem k tomu, že tento článek ukazuje ukládání klíče účtu úložiště jako tajného klíče, musíte získat tento klíč účtu úložiště.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Po načtení tajného klíče (v tomto případě váš klíč účtu úložiště) musíte tento klíč převést na zabezpečený řetězec a potom v trezoru klíčů vytvořit tajný klíč s touto hodnotou.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Dále získejte identifikátor URI pro tajný klíč, který jste vytvořili. Tento identifikátor URI budete potřebovat v pozdějším kroku pro volání trezoru klíčů a načtení tajného klíče. Spusťte následující příkaz prostředí PowerShell a poznamenejte si hodnotu ID, což je identifikátor URI tajného klíče:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Nastavení aplikace

Teď, když máte uložený tajný kód, můžete pomocí kódu načíst a použít ho po provedení několika dalších kroků.

Nejdřív musíte aplikaci zaregistrovat pomocí Azure Active Directory. Pak sdělte Key Vault informace o vaší aplikaci, aby mohly umožňovat žádosti z vaší aplikace.

> [!NOTE]
> Vaše aplikace musí být vytvořená na stejném Azure Active Directory tenant jako Trezor klíčů.

1. Otevřete **Azure Active Directory**.
2. Vyberte **Registrace aplikací**. 
3. Vyberte **Registrace nové aplikace** a přidejte tak aplikaci do Azure Active Directory.

    ![Otevřete aplikace v Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. V části **vytvořit**ponechte typ aplikace jako **webovou aplikaci nebo rozhraní API** a zadejte název vaší aplikace. Poskytněte aplikaci **přihlašovací adresu URL**. Tato adresa URL může být libovolná, kterou potřebujete pro tuto ukázku.

    ![Vytvořit registraci aplikace](./media/keyvault-keyrotation/create-app.png)

5. Po přidání aplikace do Azure Active Directory se otevře stránka aplikace. Vyberte **Nastavení**a pak vyberte **vlastnosti**. Zkopírujte hodnotu **ID aplikace** . Budete ho potřebovat v pozdějších krocích.

Dále vygenerujte klíč pro vaši aplikaci, aby mohl pracovat s Azure Active Directory. Pokud chcete vytvořit klíč, v části **Nastavení**vyberte **klíče** . Poznamenejte si nově vygenerovaný klíč pro vaši aplikaci Azure Active Directory. Budete je potřebovat později. Po opuštění této části nebude klíč k dispozici. 

![Azure Active Directory klíče aplikace](./media/keyvault-keyrotation/create-key.png)

Před vytvořením volání z vaší aplikace do trezoru klíčů je nutné sdělit Trezor klíčů o vaší aplikaci a její oprávnění. Následující příkaz používá název trezoru a ID aplikace z vaší aplikace Azure Active Directory, aby aplikace **získala** přístup k vašemu trezoru klíčů.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Nyní jste připraveni začít sestavovat volání aplikace. V aplikaci musíte nainstalovat balíčky NuGet, které jsou nutné k interakci s Azure Key Vault a Azure Active Directory. V konzole správce balíčků sady Visual Studio zadejte následující příkazy. Při psaní tohoto článku je aktuální verze balíčku Azure Active Directory 3.10.305231913, proto si podle potřeby potvrďte nejnovější verzi a aktualizaci.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

V kódu aplikace vytvořte třídu, která bude uchovávat metodu pro ověřování Azure Active Directory. V tomto příkladu se tato třída nazývá **utils**. Přidejte následující příkaz `using`:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Dále přidejte následující metodu, která načte token JWT z Azure Active Directory. V případě udržovatelnosti možná budete chtít přesunout pevně kódované řetězcové hodnoty do konfigurace webu nebo aplikace.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Přidejte potřebný kód pro volání Key Vault a načíst tajnou hodnotu. Nejprve je třeba přidat následující příkaz `using`:

```csharp
using Microsoft.Azure.KeyVault;
```

Přidejte volání metody k vyvolání Key Vault a načtení tajného kódu. V této metodě poskytnete tajný identifikátor URI, který jste uložili v předchozím kroku. Všimněte si použití metody **GetToken** z třídy **UTIL** , kterou jste předtím vytvořili.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Po spuštění aplikace byste teď měli ověřit, že se má Azure Active Directory a pak se z Azure Key Vault načítá vaše tajná hodnota.

## <a name="key-rotation-using-azure-automation"></a>Střídání klíčů pomocí Azure Automation

> [!IMPORTANT]
> Azure Automation Runbooky stále vyžadují použití modulu `AzureRM`.

Nyní jste připraveni nastavit strategii otáčení pro hodnoty, které ukládáte jako Key Vault tajných klíčů. Tajné kódy lze otáčet několika způsoby:

- Jako součást ručního procesu
- Programově pomocí volání rozhraní API
- Pomocí skriptu Azure Automation

Pro účely tohoto článku použijete prostředí PowerShell kombinované s Azure Automation ke změně přístupového klíče účtu služby Azure Storage. Pak aktualizujete tajný klíč trezoru klíčů pomocí tohoto nového klíče.

Pokud chcete Azure Automation nastavit tajné hodnoty ve vašem trezoru klíčů, musíte získat ID klienta pro připojení s názvem **AzureRunAsConnection**. Toto připojení se vytvořilo při vytváření instance Azure Automation. Chcete-li zjistit toto ID, vyberte **prostředky** z vaší instance Azure Automation. Odtud vyberte **připojení**a pak vyberte instanční objekt **AzureRunAsConnection** . Poznamenejte si hodnotu **ApplicationId** .

![ID klienta Azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

V **aktivech**vyberte **moduly**. Vyberte možnost **Galerie**a potom vyhledejte a importujte aktualizované verze každého z následujících modulů:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Při psaní tohoto článku se pro následující skript musí aktualizovat jenom dříve uvedené moduly. Pokud se vaše úloha automatizace nezdařila, potvrďte, že jste naimportovali všechny potřebné moduly a jejich závislosti.

Po získání ID aplikace pro připojení Azure Automation musíte sdělit Trezor klíčů, že tato aplikace má oprávnění k aktualizaci tajných klíčů v trezoru. Použijte následující příkaz prostředí PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

V dalším kroku vyberte **Runbooky** pod vaší instancí Azure Automation a pak vyberte **Přidat Runbook**. Vyberte možnost **Rychle vytvořit**. Pojmenujte Runbook a jako typ Runbooku vyberte **PowerShell** . Můžete přidat popis. Nakonec vyberte **vytvořit**.

![Vytvoření runbooku](./media/keyvault-keyrotation/Create_Runbook.png)

Vložte následující skript PowerShellu do podokna editoru pro novou sadu Runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

V podokně editoru vyberte **testovací podokno** pro otestování skriptu. Po spuštění skriptu bez chyby můžete vybrat **publikovat**a pak můžete použít plán pro sadu Runbook v podokně Konfigurace sady Runbook.

## <a name="key-vault-auditing-pipeline"></a>Key Vault kanál auditování

Když nastavíte Trezor klíčů, můžete zapnout auditování a shromažďovat protokoly o požadavcích na přístup k trezoru klíčů. Tyto protokoly se ukládají v určeném účtu služby Azure Storage a je možné je vymezit, monitorovat a analyzovat. Následující scénář používá službu Azure Functions, Azure Logic Apps a protokoly auditu v trezoru klíčů k vytvoření kanálu, který odešle e-mail, když aplikace, která neodpovídá ID aplikace webové aplikace, načte tajné kódy z trezoru.

Nejdřív musíte povolit protokolování do svého trezoru klíčů. Použijte následující příkazy PowerShellu. (V tomto článku se dozvíte, jaké jsou úplné podrobnosti [o protokolování trezoru klíčů](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Po povolení protokolování se začnou protokoly auditu ukládat do určeného účtu úložiště. Tyto protokoly obsahují události týkající se toho, jak a kdy jsou k vašim trezorům klíčů přistupované a kým.

> [!NOTE]
> Po operaci trezoru klíčů můžete získat přístup k informacím o protokolování 10 minut. Bude často k dispozici dříve než to.

Dalším krokem je [vytvoření fronty Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). V této frontě jsou vloženy protokoly auditu trezoru klíčů. Když zprávy protokolu auditu jsou ve frontě, aplikace logiky je vybere a na nich budou pracovat. Vytvořte instanci Service Bus pomocí následujících kroků:

1. Vytvořte obor názvů Service Bus (Pokud již máte nějaké, který chcete použít, přejděte ke kroku 2).
2. V Azure Portal přejděte na instanci Service Bus a vyberte obor názvů, ve kterém chcete vytvořit frontu.
3. Vyberte **vytvořit prostředek** > **Podniková integrace** > **Service Bus**a potom zadejte požadované podrobnosti.
4. Vyhledejte informace o Service Bus připojení, a to tak, že vyberete obor názvů a pak vyberete **informace o připojení**. Tyto informace budete potřebovat pro další část.

Dále [vytvořte funkci Azure](../azure-functions/functions-create-first-azure-function.md) , která bude dotazovat protokoly trezoru klíčů v rámci účtu úložiště a vyzvednout nové události. Tato funkce se aktivuje podle plánu.

Pokud chcete vytvořit aplikaci Azure Function App, vyberte **vytvořit prostředek**, vyhledejte **Function App**na Marketplace a pak vyberte **vytvořit**. Během vytváření můžete použít existující plán hostování nebo vytvořit nový. Můžete se také rozhodnout pro dynamické hostování. Další informace o možnostech hostování pro Azure Functions najdete v tématu [Jak škálovat Azure Functions](../azure-functions/functions-scale.md).

Až se vytvoří aplikace Azure Functions, přejdete na ni a vyberte scénář **časovače** a jazyk **C\#** pro daný jazyk. Pak vyberte **vytvořit tuto funkci**.

![Okno spuštění Azure Functions](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Na kartě **vývoj** nahraďte kód run. csx následujícím kódem:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Změňte proměnné v předchozím kódu tak, aby odkazovaly na účet úložiště, ve kterém jsou zapsány protokoly trezoru klíčů, do instance Service Bus, kterou jste vytvořili dříve, a na konkrétní cestu k protokolům úložiště trezoru klíčů.

Funkce vybere nejnovější soubor protokolu z účtu úložiště, ve kterém jsou zapsány protokoly trezoru klíčů, přiřadí nejnovější události z tohoto souboru a vloží je do fronty Service Bus. 

Vzhledem k tomu, že jeden soubor může mít více událostí, měli byste vytvořit soubor Sync. txt, který funkce také prohlíží při určení časového razítka poslední události, která byla vybrána. Pomocí tohoto souboru zajistíte, že nebudete nahrávat stejnou událost víckrát. 

Soubor Sync. txt obsahuje časové razítko pro poslední nalezenou událost. Po načtení protokolů je třeba je seřadit podle časových razítek a zajistit tak jejich správné pořadí.

Pro tuto funkci odkazujeme na několik dalších knihoven, které nejsou dostupné v Azure Functions. Aby bylo možné tyto knihovny zahrnout, musíme je Azure Functions načíst pomocí NuGet. V poli **kód** vyberte **Zobrazit soubory**.

![Možnost Zobrazit soubory](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Přidejte soubor s názvem Project. JSON s následujícím obsahem:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

Po výběru **uložit**Azure Functions stáhne požadované binární soubory.

Přepněte na kartu **Integration** a udělte parametru Timer smysluplný název, který se má použít v rámci funkce. V předchozím kódu funkce očekává, že časovač bude mít název *myTimer*. Zadejte [výraz cron](../app-service/webjobs-create.md#CreateScheduledCRON) pro časovač následujícím způsobem: `0 * * * * *`. Tento výraz způsobí, že se funkce spustí jednou za minutu.

Na stejné kartě **integrace** přidejte vstup typu **úložiště objektů BLOB v Azure**. Tento vstup odkazuje na soubor Sync. txt, který obsahuje časové razítko poslední události, kterou funkce prohlédla. Tento vstup bude k dispozici v rámci funkce pomocí názvu parametru. V předchozím kódu očekává vstup úložiště Azure Blob Storage název parametru, který se má *inputBlob*. Vyberte účet úložiště, ve kterém se bude nacházet soubor Sync. txt (může to být stejný nebo jiný účet úložiště). Do pole cesta zadejte cestu k souboru ve formátu `{container-name}/path/to/sync.txt`.

Přidejte výstup typu **úložiště objektů BLOB v Azure**. Tento výstup bude ukazovat na soubor Sync. txt, který jste definovali ve vstupu. Tento výstup používá funkce k zápisu časového razítka poslední události, na které se prohlédlo. Předchozí kód očekává, že tento parametr bude mít název *outputBlob*.

Funkce je nyní připravena. Ujistěte se, že jste přešli zpátky na kartu **vývoj** a kód uložíte. V okně výstup vyhledejte chyby kompilace a opravte je podle potřeby. Pokud je kód zkompilován, pak by měl kód nyní kontrolovat protokoly trezorů klíčů každou minutu a vložit nové události do definované fronty Service Bus. Při každém spuštění funkce by se měly zobrazit informace o protokolování, které se mají zapsat do okna protokolu.

### <a name="azure-logic-app"></a>Aplikace logiky Azure

Dále musíte vytvořit aplikaci logiky Azure, která vezme události, které funkce přenáší do fronty Service Bus, analyzuje obsah a pošle e-mail na základě vyhovující podmínky.

[Vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) tak, že vyberete **vytvořit prostředek** > **integraci** > **Aplikace logiky**.

Po vytvoření aplikace logiky se na ni dostanete a vyberte **Upravit**. V editoru aplikace logiky vyberte **Service Bus Queue** a zadejte svoje přihlašovací údaje Service Bus, abyste je připojili ke frontě.

![Sběrnice Azure Logic App Service](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Vyberte **Přidat podmínku**. V podmínce přepněte do rozšířeného editoru a zadejte následující kód. Nahraďte *APP_ID* skutečným ID aplikace vaší webové aplikace:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Tento výraz v podstatě vrátí **hodnotu false** , pokud *AppID* z příchozí události (což je tělo zprávy Service Bus) není *AppID* aplikace.

Teď můžete vytvořit akci, **Pokud ne, nedělat nic**.

![Azure Logic Apps zvolit akci](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

V případě akce vyberte možnost **Office 365 – odeslat e-mail**. Vyplňte pole pro vytvoření e-mailu, který se odešle, když definovaná podmínka vrátí **false**. Pokud nemáte sadu Office 365, hledejte alternativy, abyste dosáhli stejných výsledků.

Teď máte kompletní kanál, který vyhledává nové protokoly auditu v trezoru klíčů jednou za minutu. Vypíše nové protokoly, které najde, do fronty Service Bus. Aplikace logiky se aktivuje při zařazení nové zprávy do fronty. Pokud *AppID* v rámci události neodpovídá ID aplikace volající aplikace, pošle se e-mail.
