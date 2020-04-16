---
title: Nastavení trezoru klíčů Azure s střídáním klíčů od začátku do konce a auditováním | Dokumenty společnosti Microsoft
description: Pomocí tohoto návodu můžete nastavit střídání klíčů a sledovat protokoly trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d2981495a256ce5fb8f8f3584e68ac91541f9d62
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430250"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Nastavení trezoru klíčů Azure s střídáním klíčů a auditováním

## <a name="introduction"></a>Úvod

Po vytvoření trezoru klíčů jej můžete začít používat k ukládání klíčů a tajných klíčů. Aplikace již nemusí uchovávat klíče nebo tajné klíče, ale podle potřeby je mohou vyžádat z trezoru. Trezor klíčů umožňuje aktualizovat klíče a tajné klíče bez ovlivnění chování vaší aplikace, což otevírá širokou část možností pro správu klíčů a tajných kódů.

Tento článek vás provede, jak implementovat naplánované otočení klíčů účtu úložiště, sledovat protokoly auditu trezoru klíčů a vyvolat výstrahy při neočekávaných požadavcích. 

Nejprve je nutné vytvořit trezor klíčů pomocí metody podle vašeho výběru:

- [Nastavení a načtení tajného klíče z azure key vaultu pomocí azure cli](quick-create-cli.md)
- [Nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure PowerShellu](quick-create-powershell.md)
- [Nastavení a načtení tajného klíče z Azure Key Vault pomocí webu Azure Portal](quick-create-portal.md)


## <a name="store-a-secret"></a>Uložení tajného klíče

Chcete-li aplikaci povolit načtení tajného klíče z trezoru klíčů, musíte nejprve vytvořit tajný klíč a nahrát jej do trezoru.

Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:

```powershell
Connect-AzAccount
```

V okně automaticky otevíraný prohlížeč zadejte uživatelské jméno a heslo pro svůj účet Azure. Prostředí PowerShell získá všechna předplatná, která jsou přidružena k tomuto účtu. PowerShell používá první ve výchozím nastavení.

Pokud máte více předplatných, bude pravděpodobně nutné zadat ten, který byl použit k vytvoření trezoru klíčů. Chcete-li zobrazit předplatná svého účtu, zadejte následující:

```powershell
Get-AzSubscription
```

Chcete-li určit předplatné přidružené k trezoru klíčů, který budete protokolovat, zadejte:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Vzhledem k tomu, že tento článek ukazuje ukládání klíče účtu úložiště jako tajný klíč, musíte získat klíč účtu úložiště.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Po načtení tajného klíče (v tomto případě klíče účtu úložiště) je nutné tento klíč převést na zabezpečený řetězec a potom vytvořit tajný klíč s touto hodnotou v trezoru klíčů.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Dále získáte identifikátor URI pro tajný klíč, který jste vytvořili. Tento identifikátor URI budete potřebovat v pozdějším kroku k volání trezoru klíčů a získání tajného klíče. Spusťte následující příkaz Prostředí PowerShell a poznamenejte si hodnotu ID, což je identifikátor URI tajného klíče:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Nastavení aplikace

Nyní, když máte tajný klíč uložený, můžete použít kód k načtení a použití po provedení několika dalších kroků.

Nejprve je nutné zaregistrovat aplikaci ve službě Azure Active Directory. Potom sdělte trezoru klíčů informace o vaší aplikaci, aby mohl povolit požadavky z vaší aplikace.

> [!NOTE]
> Vaše aplikace musí být vytvořena ve stejném tenantovi služby Azure Active Directory jako trezor klíčů.

1. Otevřete **službu Azure Active Directory**.
2. Vyberte **Registrace aplikací**. 
3. Výběrem **možnosti Nová registrace aplikace** přidáte aplikaci do služby Azure Active Directory.

    ![Otevření aplikací ve službě Azure Active Directory](../media/keyvault-keyrotation/azure-ad-application.png)

4. V části **Vytvořit**ponechejte typ aplikace jako **webovou aplikaci / rozhraní API** a pojmenujte aplikaci. Podejte aplikaci **přihlašovací adresu URL**. Tato adresa URL může být cokoliv, co chcete pro tuto ukázku.

    ![Vytvořit registraci aplikace](../media/keyvault-keyrotation/create-app.png)

5. Po přidání aplikace do služby Azure Active Directory se otevře stránka aplikace. Vyberte **Nastavení**a pak vyberte **Vlastnosti**. Zkopírujte hodnotu **ID aplikace.** Budete ji potřebovat v pozdějších krocích.

Dále vygenerujte klíč pro vaši aplikaci, aby mohla pracovat se službou Azure Active Directory. Chcete-li vytvořit klíč, vyberte **možnost Klávesy v** části **Nastavení**. Poznamenejte si nově generovaný klíč pro vaši aplikaci Azure Active Directory. Budete je potřebovat později. Klíč nebude k dispozici po opuštění této části. 

![Klíče aplikací Azure Active Directory](../media/keyvault-keyrotation/create-key.png)

Před vytvořením volání z aplikace do trezoru klíčů, musíte sdělit trezor u klíčů o vaší aplikaci a její oprávnění. Následující příkaz používá název úložiště a ID aplikace z aplikace Azure Active Directory k udělení aplikace **Získat** přístup k trezoru klíčů.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Nyní jste připraveni začít vytvářet volání aplikace. Ve vaší aplikaci je nutné nainstalovat balíčky NuGet, které jsou nutné pro interakci s Azure Key Vault a Azure Active Directory. Z konzoly Správce balíčků sady Visual Studio zadejte následující příkazy. Při psaní tohoto článku aktuální verze balíčku Služby Azure Active Directory je 3.10.305231913, proto potvrďte nejnovější verzi a podle potřeby aktualizujte.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

V kódu aplikace vytvořte třídu, která bude obsahovat metodu ověřování služby Azure Active Directory. V tomto příkladu se tato třída nazývá **Utils**. Přidejte `using` následující příkaz:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Dále přidejte následující metodu pro načtení tokenu JWT z Azure Active Directory. Pro zachování možnosti můžete chtít přesunout pevně zakódované řetězcové hodnoty do konfigurace webu nebo aplikace.

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

Přidejte potřebný kód pro volání trezoru klíčů a načtení tajné hodnoty. Nejprve je nutné `using` přidat následující příkaz:

```csharp
using Microsoft.Azure.KeyVault;
```

Přidejte volání metody k vyvolání trezoru klíčů a načtení tajného klíče. V této metodě zadáte tajný identifikátor URI, který jste uložili v předchozím kroku. Všimněte si použití **GetToken** metoda z **Utils třídy,** kterou jste vytvořili dříve.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Při spuštění aplikace, měli byste teď být ověřování služby Azure Active Directory a potom načítání tajné hodnoty z Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Střídání klíčů pomocí Azure Automation

> [!IMPORTANT]
> Azure Automation runbooky stále `AzureRM` vyžadují použití modulu.

Nyní jste připraveni nastavit strategii střídání hodnot, které uložíte jako tajné klíče úložiště klíčů. Tajemství lze otáčet několika způsoby:

- V rámci ručního procesu
- Programově pomocí volání rozhraní API
- Prostřednictvím skriptu Azure Automation

Pro účely tohoto článku použijete PowerShell v kombinaci s Azure Automation ke změně přístupového klíče účtu úložiště Azure. Poté aktualizujete tajný klíč trezoru klíčů pomocí tohoto nového klíče.

Chcete-li povolit Azure Automation nastavit tajné hodnoty v trezoru klíčů, musíte získat ID klienta pro připojení s názvem **AzureRunAsConnection**. Toto připojení bylo vytvořeno při navázání instance Azure Automation. Pokud chcete najít toto ID, vyberte **prostředky** z instance Azure Automation. Odtud vyberte **Připojení**a pak vyberte instanční objekt služby **AzureRunAsConnection.** Poznamenejte si hodnotu **ApplicationId.**

![ID klienta Azure Automation](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

V **položkách Datové zdroje**vyberte **moduly**. Vyberte **Galerii**a pak vyhledejte a importujte aktualizované verze každého z následujících modulů:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Při psaní tohoto článku bylo třeba aktualizovat pouze dříve uvedené moduly pro následující skript. Pokud se úloha automatizace nezdaří, zkontrolujte, že jste importovali všechny potřebné moduly a jejich závislosti.

Po načtení ID aplikace pro připojení Azure Automation, musíte sdělit trezoru klíčů, že tato aplikace má oprávnění k aktualizaci tajných kódů v trezoru. Použijte následující příkaz Prostředí PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Dále vyberte **Runbook** pod instancí Azure Automation a pak vyberte **Přidat Runbook**. Vyberte možnost **Rychle vytvořit**. Pojmenujte runbook a jako typ runbooku vyberte **PowerShell.** Můžete přidat popis. Nakonec vyberte **vytvořit**.

![Vytvoření runbooku](../media/keyvault-keyrotation/Create_Runbook.png)

Vložte následující skript Prostředí PowerShell do podokna editoru nového runbooku:

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

V podokně editoru vyberte **Podokno testování,** abyste skript otestovali. Po spuštění skriptu bez chyby můžete vybrat **publikovat**a potom můžete použít plán pro runbook v konfiguračním podokně runbooku.

## <a name="key-vault-auditing-pipeline"></a>Kanál auditování trezoru klíčů

Při nastavování trezoru klíčů můžete zapnout auditování a shromažďovat protokoly o požadavcích na přístup k trezoru klíčů. Tyto protokoly jsou uloženy v určeném účtu úložiště Azure a můžete je vyprostit, monitorovat a analyzovat. Následující scénář používá funkce Azure, aplikace logiky Azure a protokoly auditu trezoru klíčů k vytvoření kanálu, který odešle e-mail, když aplikace, která neodpovídá ID aplikace webové aplikace načte tajné kódy z trezoru.

Nejprve je nutné povolit protokolování v trezoru klíčů. Použijte následující příkazy Prostředí PowerShell. (Můžete vidět úplné podrobnosti v [tomto článku o key-vault-protokolování](../general/logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Po povolení protokolování se protokoly auditu začnou ukládat do určeného účtu úložiště. Tyto protokoly obsahují události o tom, jak a kdy jsou k úložištím klíčů přistupovány a kým.

> [!NOTE]
> Přístup k informacím o protokolování můžete získat 10 minut po operaci trezoru klíčů. To bude často k dispozici dříve než to.

Dalším krokem je [vytvoření fronty Azure Service Bus](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Tato fronta je místo, kde jsou nabízeny protokoly auditu trezoru klíčů. Když jsou zprávy protokolu auditu ve frontě, aplikace logiky je vyzvedne a bude s nimi jednat. Vytvořte instanci služby Service Bus pomocí následujících kroků:

1. Vytvořte obor názvů Service Bus (pokud již máte obor, který chcete použít, přejděte ke kroku 2).
2. Přejděte na instanci Service Bus na webu Azure portal a vyberte obor názvů, ve kterém chcete frontu vytvořit.
3. Vyberte **Vytvořit zdroj** > **Enterprise Integration** > **Service Bus**a zadejte požadované podrobnosti.
4. Informace o připojení služby Service Bus najdete tak, že vyberete obor názvů a potom **vyberete Informace o připojení**. Tyto informace budete potřebovat pro další část.

Dále [vytvořte funkci Azure](../../azure-functions/functions-create-first-azure-function.md) k dotazování protokoly trezoru klíčů v rámci účtu úložiště a vyzvednout nové události. Tato funkce bude spuštěna podle plánu.

Pokud chcete vytvořit aplikaci azure funkce, vyberte **Vytvořit prostředek**, vyhledejte na trhu **aplikaci funkce**a pak vyberte **Vytvořit**. Během vytváření můžete použít existující hostingový plán nebo vytvořit nový. Můžete se také rozhodnout pro dynamický hosting. Další informace o možnostech hostování pro funkce Azure najdete v tématu [Jak škálovat funkce Azure](../../azure-functions/functions-scale.md).

Po vytvoření aplikace funkce Azure, přejděte na něj a vyberte **scénář časovač** a **C\# ** pro jazyk. Pak vyberte **Vytvořit tuto funkci**.

![Okno Azure Functions Start](../media/keyvault-keyrotation/Azure_Functions_Start.png)

Na kartě **Vývoj** nahraďte kód run.csx následujícím:

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
> Změňte proměnné v předchozím kódu tak, aby přecózovaly na účet úložiště, kde jsou zapsány protokoly trezoru klíčů, na instanci service bus, kterou jste vytvořili dříve, a na konkrétní cestu k protokolům úložiště trezoru klíčů.

Funkce vyzvedne nejnovější soubor protokolu z účtu úložiště, kde jsou zapsány protokoly trezoru klíčů, uchopí nejnovější události z tohoto souboru a odešle je do fronty service bus. 

Vzhledem k tomu, že jeden soubor může mít více událostí, měli byste vytvořit soubor sync.txt, který funkce také vyhledá, aby určila časové razítko poslední události, která byla zachycena. Použití tohoto souboru zajistí, že nestlačíte stejnou událost vícekrát. 

Soubor sync.txt obsahuje časové razítko pro událost naposledy zjištěné. Při načtení protokolů musí být seřazeny podle jejich časových razítek, aby bylo zajištěno, že jsou správně uspořádány.

Pro tuto funkci odkazujeme na několik dalších knihoven, které nejsou k dispozici po vybalení z krabice v Azure Functions. Chcete-li zahrnout tyto knihovny, potřebujeme Funkce Azure k jejich vytáknutí pomocí NuGet. Pod polem **Kód** vyberte **Zobrazit soubory**.

![Možnost "Zobrazit soubory"](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Přidejte soubor s názvem project.json s následujícím obsahem:

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

Po výběru **uložit**, Azure Functions stáhne požadované binární soubory.

Přepněte na kartu **Integrovat** a pojmenujte parametr timer smysluplný název, který se má použít v rámci funkce. V předchozím kódu funkce očekává, že časovač bude volán *myTimer*. Zadejte [výraz CRON](../../app-service/webjobs-create.md#CreateScheduledCRON) pro časovač `0 * * * * *`následujícím způsobem: . Tento výraz způsobí, že funkce spustit jednou za minutu.

Na stejné **kartě Integrace** přidejte vstup typu **úložiště objektů blob Azure**. Tento vstup bude ukazovat na soubor sync.txt, který obsahuje časové razítko poslední události zjištovaného funkcí. Tento vstup bude přístupný v rámci funkce pomocí názvu parametru. V předchozím kódu vstup úložiště Azure Blob očekává, že název parametru *inputBlob*. Vyberte účet úložiště, ve kterém bude soubor sync.txt umístěn (může to být stejný nebo jiný účet úložiště). V poli cesty zadejte cestu k souboru ve formátu `{container-name}/path/to/sync.txt`.

Přidejte výstup typu **úložiště objektů blob Azure**. Tento výstup bude ukazovat na soubor sync.txt, který jste definovali ve vstupu. Tento výstup je používán funkcí k zápisu časového razítka poslední události, na kterou se díval. Předchozí kód očekává, že tento parametr se nazývá *outputBlob*.

Funkce je nyní připravena. Nezapomeňte přepnout zpět na kartu **Vývoj** a uložit kód. Zkontrolujte, zda ve výstupním okně nenajdete chyby kompilace, a podle potřeby je opravte. Pokud se kód zkompiluje, pak by měl kód nyní každou minutu kontrolovat protokoly trezoru klíčů a zatlačovat všechny nové události do definované fronty služby Service Bus. Měli byste vidět protokolování informace zapisovat do okna protokolu při každém spuštění funkce.

### <a name="azure-logic-app"></a>Aplikace logiky Azure

Dále musíte vytvořit aplikaci logiky Azure, která vyzvedne události, které funkce odesílá do fronty Service Bus, analyzuje obsah a odešle e-mail na základě podmínky, která je spárována.

[Vytvořte aplikaci logiky](../../logic-apps/quickstart-create-first-logic-app-workflow.md) výběrem **vytvořit** > **aplikaci pro****integraci** > prostředků .

Po vytvoření aplikace logiky přejděte na něj a vyberte **Upravit**. V editoru aplikace logiky vyberte **Fronta služby Service Bus** a zadejte pověření služby Service Bus, abyste ji připojili ke frontě.

![Servisní sběrnice aplikace Logiky Azure](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Vyberte **přidat podmínku**. V podmínce přepněte do pokročilého editoru a zadejte následující kód. Nahraďte *APP_ID* skutečným ID aplikace webové aplikace:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Tento výraz v podstatě vrátí **false,** pokud *appid* z příchozí události (což je tělo zprávy service bus) není *appid* aplikace.

Nyní vytvořte akci pod **POKUD NE, NEDĚLAT NIC**.

![Azure Logic Apps zvolit akci](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Pro akci vyberte **Office 365 - odeslat e-mail**. Vyplňte pole, která chcete vytvořit e-mail, který chcete odeslat, když definovaná podmínka vrátí **hodnotu false**. Pokud nemáte Office 365, vyhledejte alternativy, jak dosáhnout stejných výsledků.

Nyní máte kanál dokončení ukončování, který vyhledá nové protokoly auditu trezoru klíčů jednou za minutu. Odešle nové protokoly, které najde do fronty služby Service Bus. Aplikace logiky se aktivuje, když se nová zpráva přistane ve frontě. Pokud *aplikace* v rámci události neodpovídá ID aplikace volající aplikace, odešle e-mail.
