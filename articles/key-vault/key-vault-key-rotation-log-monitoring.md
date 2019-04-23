---
title: Nastavení služby Azure Key Vault s začátku do konce obměny klíčů a auditování | Dokumentace Microsoftu
description: Pomocí této příručce s postupy vám pomůžou nastavit obměny klíčů a monitorujte protokoly trezoru klíčů.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: fb3300a45f905eb57fcc4880269e4a9bed9dac0c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789885"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Nastavení služby Azure Key Vault s obměny klíčů a auditování

## <a name="introduction"></a>Úvod

Jakmile máte trezor klíčů, chcete začít, používat k ukládání klíčů a tajných kódů. Vaše aplikace, které už nepotřebujete k uchování vašich klíčů nebo tajných klíčů, ale můžou o ně požádat z trezoru podle potřeby. Služby key vault umožňuje aktualizovat klíče a tajné kódy bez ovlivnění chování aplikace, což otevře širokou škálu možností pro správu tajných kódů a klíče.

>[!IMPORTANT]
> Příklady v tomto článku jsou uvedeny jen jako ukázka. Nejsou určeny pro použití v produkčním prostředí. 

Tento článek vás provede:

- Příklad použití Azure Key Vault k uložíte tajný klíč. Tajný kód uložen v tomto článku je klíč účtu úložiště Azure přistupuje aplikace. 
- Jak implementovat naplánované střídání tohoto klíče účtu úložiště.
- Jak monitorovat klíč trezoru protokoly auditu a vyvolají upozornění při neočekávané požadavky přicházejí.

> [!NOTE]
> Tento článek není podrobně vysvětluje počátečním nastavení trezoru klíčů. Tyto informace v tématu [co je Azure Key Vault?](key-vault-overview.md). Multiplatformní rozhraní příkazového řádku najdete v tématu [Správa služby Key Vault pomocí rozhraní příkazového řádku Azure](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Nastavení služby Key Vault

Povolení aplikace načíst tajného klíče ze služby Key Vault, musíte nejprve vytvořit tajný kód a nahrajte ho do svého trezoru.

Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:

```powershell
Connect-AzAccount
```

V okně prohlížeče rozbalovací zadejte uživatelské jméno a heslo k vašemu účtu Azure. PowerShell získá všechna předplatná, které jsou spojeny s tímto účtem. Prostředí PowerShell použije první předplatné ve výchozím nastavení.

Pokud máte více předplatných, možná budete muset určit ten, který byl použit k vytvoření trezoru klíčů. Zadejte následující příkaz k zobrazení předplatných pro váš účet:

```powershell
Get-AzSubscription
```

Chcete-li specifikovat předplatné přidružené k trezoru klíčů, který budete protokolovat, zadejte:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Protože tento článek ukazuje ukládání klíče účtu úložiště jako tajný klíč, musíte získat tento klíč účtu úložiště.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Po načtení váš tajný klíč (v tomto případě klíč účtu úložiště), musíte převést tento klíč na zabezpečený řetězec a pak vytvoření tajného kódu s touto hodnotou v trezoru klíčů.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Potom získejte identifikátor URI pro tajný klíč, který jste vytvořili. Bude nutné tento identifikátor URI v pozdějším kroku volání služby key vault a načíst váš tajný klíč. Spuštěním následujícího příkazu prostředí PowerShell a poznamenejte si hodnotu ID, což je identifikátor URI tajného klíče:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Nastavení aplikace

Teď, když máte uložené tajného kódu, můžete kód načíst a použít ho po provedení několik kroků.

Nejprve je nutné zaregistrovat aplikaci v Azure Active Directory. Potom sdělí službě Key Vault informace o vaší aplikaci tak, aby ho povolit požadavky od vaší aplikace.

> [!NOTE]
> Aplikace musí být vytvořeny na stejném tenantovi Azure Active Directory jako váš trezor klíčů.

1. Otevřít **Azure Active Directory**.
2. Vyberte **Registrace aplikací**. 
3. Vyberte **registrace nové aplikace** přidání aplikace do Azure Active Directory.

    ![Otevření aplikace v Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. V části **vytvořit**, ponechte typ aplikace jako **webová aplikace / rozhraní API** a zadejte název vaší aplikace. Dejte aplikaci **přihlašovací adresa URL**. Tato adresa URL může být cokoliv, co chcete použít pro tuto ukázku.

    ![Vytvoření registrace aplikace](./media/keyvault-keyrotation/create-app.png)

5. Po přidání aplikace do Azure Active Directory, otevře se stránka aplikace. Vyberte **nastavení**a pak vyberte **vlastnosti**. Kopírovat **ID aplikace** hodnotu. Budete je potřebovat v dalších krocích.

Dále vygenerujte klíč pro vaše aplikace tak může komunikovat se službou Azure Active Directory. Chcete-li vytvořit klíč, vyberte **klíče** pod **nastavení**. Poznamenejte si nově vygenerovaný klíč pro aplikaci Azure Active Directory. Budete je potřebovat později. Klíč nebude k dispozici po opuštění této části. 

![Klíče aplikace Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Před vytvořením všechna volání z aplikace do služby key vault, musíte upozornit služby key vault o vaší aplikaci a její oprávnění. Následující příkaz používá název trezoru a ID aplikace z vaší aplikace Azure Active Directory udělit aplikaci **získat** přístup k trezoru klíčů.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Nyní jste připraveni začít vytvářet vaše aplikace volání. Ve vaší aplikaci je nutné nainstalovat balíčky NuGet, které jsou nutné k interakci s Azure Key Vault a Azure Active Directory. Z konzoly Správce balíčků Visual Studio zadejte následující příkazy. Při psaní tohoto článku, aktuální verze balíčku Azure Active Directory je 3.10.305231913, proto zkontrolujte nejnovější verzi a podle potřeby aktualizujte.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

V kódu aplikace vytvoření třídy k umístění metodu pro ověření služby Azure Active Directory. V tomto příkladu je názvem třídy **Utils**. Přidejte následující `using` – příkaz:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Dále přidejte následující metodu pro načtení tokenu JWT ze služby Azure Active Directory. Údržbu můžete chtít přesunout pevně zakódované řetězcové hodnoty do vaší konfigurace webu nebo aplikace.

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

Přidání nezbytného kódu do volání služby Key Vault a načíst tajná hodnota. Nejprve je nutné přidat následující `using` – příkaz:

```csharp
using Microsoft.Azure.KeyVault;
```

Přidejte volání metody vyvolání služby Key Vault a načítat váš tajný klíč. V této metodě zadejte tajný kód identifikátor URI, který jste si uložili v předchozím kroku. Všimněte si použití **GetToken** metodu z **Utils** třídy, kterou jste předtím vytvořili.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Při spuštění aplikace by měla nyní být ověřování v Azure Active Directory a potom načítání tajná hodnota ze služby Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Obměna klíčů pomocí Azure Automation

> [!IMPORTANT]
> Runbooky služby automatizace Azure stále vyžadují použití `AzureRM` modulu.

Nyní jste připraveni nastavit otočení strategii pro hodnoty, které se ukládají jako tajné klíče služby Key Vault. Tajné kódy lze otočit několika způsoby:

- Jako součást ruční proces
- Programově pomocí volání rozhraní API
- Pomocí skriptu Azure Automation.

Pro účely tohoto článku budete používat Powershellu v kombinaci s Azure Automation. Chcete-li změnit přístupový klíč účtu služby Azure storage. Potom budete aktualizovat tajný kód trezoru klíčů pomocí nového klíče.

Pokud chcete povolit Azure Automation. Chcete-li nastavit hodnoty tajných kódů v trezoru klíčů, musíte získat ID klienta pro připojení s názvem **AzureRunAsConnection**. Toto připojení byl vytvořen při vytvoření instance služby Azure Automation. Chcete-li toto ID najít, vyberte **prostředky** z vaší instance služby Azure Automation. Tam pak vyberete **připojení**a pak vyberte **AzureRunAsConnection** instančního objektu. Poznamenejte si **ApplicationId** hodnotu.

![ID klienta služby Azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

V **prostředky**vyberte **moduly**. Vyberte **Galerie**a poté vyhledejte a import aktualizované verze pro každý z následujících modulů:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Při psaní tohoto článku pouze dříve uvedené moduly museli být aktualizováno pro následující skript. Pokud se nezdaří úlohy automatizace, potvrďte, že jste importovali všechny potřebné moduly a jejich závislosti.

Poté, co jste načetli ID aplikace pro připojení k Azure Automation, je zapotřebí sdělit trezoru klíčů, že tato aplikace má oprávnění k aktualizaci tajným kódům v trezoru. Pomocí následujícího příkazu Powershellu:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

V dalším kroku vyberte **sady Runbook** pod vaší instance služby Azure Automation a pak vyberte **přidat Runbook**. Vyberte možnost **Rychle vytvořit**. Zadejte název vaší sady runbook a vyberte **Powershellu** jako typ runbooku. Můžete přidat popis. Nakonec vyberte **vytvořit**.

![Vytvoření runbooku](./media/keyvault-keyrotation/Create_Runbook.png)

V podokně editor pro nový runbook vložte následující skript prostředí PowerShell:

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

$secret = Set-AzureRmKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Podokna editoru vyberte **testovací podokno** k otestování vašeho skriptu. Po spuštění skriptu bez chyb, můžete vybrat **publikovat**, a pak může použít plán pro runbook v podokně Konfigurace sady runbook.

## <a name="key-vault-auditing-pipeline"></a>Auditování kanálu služby Key Vault

Když nastavíte trezor klíčů, můžete zapnout auditování pro shromažďování protokolů na požadavky na přístup k trezoru klíčů. Tyto protokoly se ukládají v účtu úložiště určené Azure a je mohly vyžádat navýšení kapacity, monitorovat a analyzovat. Následující scénář využívá služba Azure functions, Azure logic apps a protokolů auditu služby key vault k vytvoření kanálu, která odešle e-mail, když aplikaci, která neodpovídá ID aplikace webové aplikace načte tajné kódy z trezoru.

Nejprve je třeba povolit protokolování na váš trezor klíčů. Pomocí následujících příkazů Powershellu. (Můžete zobrazit všechny podrobnosti v [Tento článek o protokolování key vault](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Po povolení protokolování se protokolů auditu start uložené v účtu úložiště určený. Tyto protokoly obsahovat události o tom, jak a kdy přistupují k vašim trezorům klíčů a kým.

> [!NOTE]
> Informace o protokolování můžete přistupovat 10 minut po provedení této operace služby key vault. Často bude k dispozici dříve.

Dalším krokem je [vytvoření fronty služby Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Tato fronta je, kde jsou vloženy protokoly auditu služby key vault. Pokud zprávy protokolu auditu ve frontě, aplikace logiky přebere příspěvky a funguje na nich. Vytvoření instance služby Service Bus pomocí následujících kroků:

1. Vytvoření oboru názvů služby Service Bus (pokud ještě nemáte, kterou chcete použít, přejděte ke kroku 2).
2. Přejděte k instanci služby Service Bus na webu Azure Portal a vyberte obor názvů, které chcete vytvořit frontu.
3. Vyberte **vytvořit prostředek** > **podniková integrace** > **služby Service Bus**a potom zadejte požadované podrobnosti.
4. Najít informace o připojení služby Service Bus výběrem obor názvů a pak vyberete **informace o připojení**. Tyto informace budete potřebovat pro další části.

Dále [vytvořit funkci Azure](../azure-functions/functions-create-first-azure-function.md) dotazovat protokoly trezoru klíčů v rámci účtu úložiště a vyzvednutí nové události. Tato funkce spustí podle plánu.

Chcete-li vytvořit aplikaci Azure function app, vyberte **vytvořit prostředek**, na webu Marketplace vyhledáme **aplikace Function App**a pak vyberte **vytvořit**. Při vytváření můžete použít existující plán hostování nebo vytvořte novou. Můžete se také rozhodnout pro dynamické hostování. Další informace o možnosti hostování pro službu Azure Functions najdete v tématu [postup škálování Azure Functions](../azure-functions/functions-scale.md).

Po vytvoření aplikace funkcí Azure, přejděte do něj a vyberte **časovače** scénář a **C\#**  pro jazyk. Potom vyberte **vytvořit tuto funkci**.

![Okno Azure Functions Start](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Na **vývoj** kartu, nahraďte kód run.csx následujícím kódem:

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
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
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
> Změňte proměnné v předchozím kódu tak, aby odkazoval na svůj účet úložiště, ve kterém jsou zapsány protokoly trezoru klíčů, které jste vytvořili instanci služby Service Bus a na konkrétní cestu k protokoly úložiště služby key vault.

Funkce vybere nejnovější soubor protokolu z účtu úložiště, ve kterém jsou zapsány protokoly trezoru klíčů, získá nejnovější události z tohoto souboru a odesílá je do fronty služby Service Bus. 

Protože jednoho souboru může mít více událostí, můžete vytvořit soubor sync.txt, která také srovnává funkce určit časové razítko poslední události, ke které se nenačítají. Použití tohoto souboru se zajistí, že není vložíte stejnou událost více než jednou. 

Soubor sync.txt obsahuje časové razítko pro události došlo k poslední. Když protokoly jsou načteny, se musí být řada seřazena podle jejich časová razítka zajistit, že jste seřazené správně.

Pro tuto funkci budeme odkazovat na několik dalších knihoven, které nejsou k dispozici ihned v Azure Functions. Zahrnout tyto knihovny, musíte tyto změny pomocí NuGet služby Azure Functions. V části **kód** vyberte **zobrazit soubory**.

![Možnost "Zobrazit soubory"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Přidáte soubor s názvem souboru project.json s následujícím obsahem:

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

Po výběru **Uložit**, Azure Functions se stáhne požadované binární soubory.

Přepněte **integrace** kartu a dejte smysluplný název určený v rámci funkce parametr časovače. V předchozím kódu funkce očekává časovač, která se má volat *myTimer*. Zadejte [výraz CRON](../app-service/webjobs-create.md#CreateScheduledCRON) časovač následujícím způsobem: `0 * * * * *`. Tento výraz způsobí, že funkce pro spuštění jednou za minutu.

Na stejném **integrace** kartu, přidat vstup typu **úložiště objektů Blob v Azure**. Tento vstup bude odkazovat na sync.txt souboru, který obsahuje časové razítko poslední události podívali se na funkce. Tento vstup budou mít přístup v rámci této funkce pomocí názvu parametru. V předchozím kódu vstup úložiště objektů Blob v Azure očekává, že název parametru, který se má *inputBlob*. Vyberte účet úložiště, kde budou umístěné sync.txt souboru (může to být stejný nebo jiný účet úložiště). Do pole Cesta, zadejte cestu k souboru ve formátu `{container-name}/path/to/sync.txt`.

Přidat výstup typu **úložiště objektů Blob v Azure**. Tento výstup bude odkazovat na sync.txt soubor, který jste definovali ve vstupu. Tento výstup se používá funkce k zápisu časové razítko poslední události prohlédli jste si. Předchozí kód očekává, že tento parametrem říkalo *outputBlob*.

Funkce je teď připravený. Ujistěte se, že chcete přepnout zpět **vývoj** kartu a uložte kód. Najdete v okně výstupu chyby při kompilaci a podle potřeby je opravit. Pokud se kompiluje kód, pak kód by měl nyní být kontroluje protokoly trezoru klíčů se každou minutu a doručením (push) libovolný nové události do fronty služby Service Bus, definované. Zobrazí se informace o protokolování zapsat do protokolu okna pokaždé, když se aktivuje funkci.

### <a name="azure-logic-app"></a>Azure logic app

Dále musíte vytvořit Azure logic app, který převezme událostí funkce vkládání do fronty služby Service Bus, analyzuje obsah a odešle e-mailu na základě podmínky je nalezena shoda.

[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) tak, že vyberete **vytvořit prostředek** > **integrace** > **aplikace logiky**.

Po vytvoření aplikace logiky do něj přejděte a vyberte **upravit**. V editoru aplikace logiky, vyberte **frontu služby Service Bus** a zadejte svoje přihlašovací údaje služby Service Bus pro připojení k frontě.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Vyberte **přidat podmínku**. V podmínce přepnout do rozšířeného editoru a zadejte následující kód. Nahraďte *APP_ID* zadejte ID aplikace skutečný vaší webové aplikace:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Tento výraz vrátí v podstatě **false** Pokud *appid* z příchozí události (což je text zprávy služby Service Bus) není *appid* aplikace.

Teď vytvořte akci v rámci **Pokud ne, NEDĚLAT nic**.

![Zvolte akci Azure Logic Apps](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Jako akci vyberte **Office 365 – poslat e-mail**. Vyplňte pole k vytvoření e-mailu k odeslání návratu definované podmínky **false**. Pokud nemáte k dispozici Office 365, hledejte alternativy dosáhnete stejných výsledků.

Teď máte začátku do konce profilace, která hledá nové protokoly auditu služby key vault jednou za minutu. Nové protokoly, které nalezne toho odesílá do fronty služby Service Bus. Aplikace logiky se aktivuje, když jsou novou zprávu ve frontě. Pokud *appid* v rámci události neodpovídá ID aplikace z volající aplikace, odešle e-mailu.
