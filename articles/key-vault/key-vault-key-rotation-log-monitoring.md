---
title: Nastavení služby Azure Key Vault s začátku do konce obměny klíčů a auditování – Azure Key Vault | Dokumentace Microsoftu
description: Použijte tento postup při seznamování s obměny klíčů a monitorování protokolů služby key vault.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 4dbfd993a8464c569d30f11e305d4bae000a778f
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077704"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Nastavení služby Azure Key Vault s obměny klíčů a auditování

## <a name="introduction"></a>Úvod

Jakmile máte trezor klíčů, chcete začít, používat k ukládání klíčů a tajných kódů. Vaše aplikace, které už nepotřebujete k uchování vašich klíčů nebo tajných klíčů, ale můžou o ně požádat z trezoru podle potřeby. Umožňuje aktualizovat klíče a tajné kódy bez ovlivnění chování aplikace, která otevírá široké možnosti týkající se klíč a správa tajných kódů.

>[!IMPORTANT]
> Příklady v tomto článku jsou uvedeny jen jako ukázka. Nejsou určeny pro použití v produkčním prostředí. 

Tento článek vás provede:

- Příklad použití Azure Key Vault k uložíte tajný klíč. Tajný kód uložené v tomto kurzu je klíč účtu úložiště Azure přistupuje aplikace. 
- Také ukazuje implementaci naplánované otáčení tohoto klíče účtu úložiště.
- Ukazuje, jak monitorovat protokoly auditu služby key vault a vyvolají upozornění při neočekávané požadavky přicházejí.

> [!NOTE]
> V tomto kurzu není určena pro podrobně vysvětluje počátečním nastavení trezoru klíčů. Další informace naleznete v tématu [Začínáme s Azure Key Vault](key-vault-get-started.md) Multiplatformní rozhraní příkazového řádku najdete v tématu [Správa služby Key Vault pomocí rozhraní příkazového řádku](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Nastavení služby Key Vault

Povolení aplikace načíst tajného klíče ze služby Key Vault, musíte nejprve vytvořit tajný kód a nahrajte ho do svého trezoru. To můžete provést spuštěním relaci Azure Powershellu a přihlášení k účtu Azure pomocí následujícího příkazu:

```powershell
Connect-AzureRmAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. PowerShell získá všechna předplatná, které jsou spojeny s tímto účtem. Prostředí PowerShell použije první předplatné ve výchozím nastavení.

Pokud máte více předplatných, možná budete muset určit ten, který byl použit k vytvoření trezoru klíčů. Zadejte následující příkaz k zobrazení předplatných pro váš účet:

```powershell
Get-AzureRmSubscription
```

Chcete-li specifikovat předplatné přidružené k trezoru klíčů, který budete protokolovat, zadejte:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Protože tento článek ukazuje ukládání klíče účtu úložiště jako tajný klíč, musíte získat tento klíč účtu úložiště.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Po načtení váš tajný klíč (v tomto případě klíč účtu úložiště), musíte převést, který na zabezpečený řetězec a pak vytvořte tajného kódu s touto hodnotou v trezoru klíčů.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Potom získejte identifikátor URI pro tajný klíč, který jste vytvořili. Používá se v pozdějším kroku při volání načíst vaše tajný kód trezoru klíčů. Spuštěním následujícího příkazu prostředí PowerShell a poznamenejte si hodnotu ID, což je identifikátor URI tajného kódu:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Nastavení aplikace

Teď, když máte uložené tajného kódu, můžete kód načíst a použít ho. Existuje několik kroků potřebných k dosažení tohoto cíle. První a nejdůležitější krok je registrace aplikace v Azure Active Directory a potom oznamovat služby Key Vault informace o vaší aplikaci tak, aby ho povolit požadavky od vaší aplikace.

> [!NOTE]
> Aplikace musí být vytvořeny na stejném tenantovi Azure Active Directory jako váš trezor klíčů.
>
>

1. Přejděte do služby Azure Active Directory.
2. Zvolte **registrace aplikací** 
3. Zvolte **registrace nové aplikace** přidání aplikace do služby Azure Active Directory.

    ![Otevření aplikace v Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. V **vytvořit** oddílu ponechat typ aplikace jako **webové aplikace nebo webové rozhraní API** a zadejte název vaší aplikace. Dejte aplikaci **PŘIHLAŠOVACÍ adresu URL**. To může být cokoliv, co chcete použít pro tuto ukázku.

    ![Vytvoření registrace aplikace](./media/keyvault-keyrotation/create-app.png)

5. Po přidání aplikace do Azure Active Directory, je možné zařazení do systému stránky aplikace. Vyberte **nastavení** a vyberte možnost Vlastnosti. Kopírovat **ID aplikace** hodnotu. Budete ji potřebovat v dalších krocích.

Dále vygenerujte klíč pro vaše aplikace tak může komunikovat s Azure Active Directory. Klíč v části můžete vytvořit tak, že přejdete na **klíče** části **nastavení**. Poznamenejte si nově vygenerovaný klíč z vaší aplikace Azure Active Directory pro použití v pozdějším kroku. Všimněte si, že klíč nebude k dispozici po přejít z této části. 

![Klíče aplikace Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Před vytvořením všechna volání z aplikace do služby key vault, musíte upozornit služby key vault o vaší aplikaci a její oprávnění. Následující příkaz má název trezoru a ID aplikace z aplikace Azure Active Directory a udělení **získat** přístup k trezoru klíčů pro aplikaci.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

V tomto okamžiku jste připravení začít vytvářet vaše aplikace volání. Ve vaší aplikaci je nutné nainstalovat balíčky NuGet, které jsou nutné k interakci s Azure Key Vault a Azure Active Directory. Z konzoly Správce balíčků Visual Studio zadejte následující příkazy. Při psaní tohoto článku je aktuální verze balíčku Azure Active Directory 3.10.305231913, proto je vhodné k potvrzení na nejnovější verzi a příslušně aktualizovat.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

V kódu aplikace vytvoření třídy k umístění metodu pro ověření služby Azure Active Directory. V tomto příkladu je názvem třídy **Utils**. Přidejte následující příkaz using:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Dále přidejte následující metodu pro načtení tokenu JWT ze služby Azure Active Directory. Údržbu můžete přesunout do vaší konfigurace webu nebo aplikace pevně zakódované řetězcové hodnoty.

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

Přidání nezbytného kódu do volání služby Key Vault a načíst tajná hodnota. Nejprve je nutné přidat následující příkaz using:

```csharp
using Microsoft.Azure.KeyVault;
```

Přidejte volání metody vyvolání služby Key Vault a načítat váš tajný klíč. V této metodě zadejte tajný kód identifikátor URI, který jste si uložili v předchozím kroku. Všimněte si použití **GetToken** metodu z **Utils** třídy, které jste předtím vytvořili.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Při spuštění aplikace by měla nyní být ověřování v Azure Active Directory a potom načítání tajná hodnota ze služby Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Obměna klíčů pomocí Azure Automation

Existují různé možnosti pro implementaci otočení strategie pro hodnoty, které se ukládají jako tajné kódy služby Azure Key Vault. Jako součást ruční proces lze otočit tajné kódy, se může prostřednictvím kódu programu otáčet s použitím volání rozhraní API nebo může být otočit pomocí skriptu pro automatizaci. Pro účely tohoto článku budete používat Azure Powershellu v kombinaci s Azure Automation. Chcete-li změnit přístupový klíč účtu úložiště Azure. Potom budete aktualizovat tajný kód trezoru klíčů pomocí nového klíče.

Pokud chcete povolit Azure Automation. Chcete-li nastavit hodnoty tajných kódů v trezoru klíčů, musíte získat ID klienta pro připojení s názvem AzureRunAsConnection, který byl vytvořen při vytvoření instance služby Azure Automation. Toto ID můžete najít výběrem **prostředky** z vaší instance služby Azure Automation. Tady zvolíte **připojení** a pak vyberte **AzureRunAsConnection** služby zásadě. Poznamenejte si **ID aplikace**.

![ID klienta služby Azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

V **prostředky**, zvolte **moduly**. Z **moduly**vyberte **Galerie**a poté vyhledejte a **Import** aktualizované verze každého z následujících modulů:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Při psaní tohoto článku pouze dříve uvedené moduly museli být aktualizováno pro následující skript. Pokud zjistíte, že se nedaří úlohy automatizace, potvrďte, že jste naimportovali všechny potřebné moduly a jejich závislosti.
>
>

Po načtení ID aplikací pro připojení k Azure Automation, je zapotřebí sdělit trezoru klíčů, že tato aplikace má přístup k aktualizaci tajným kódům v trezoru. Můžete to provést pomocí následujícího příkazu Powershellu:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

V dalším kroku vyberte **sady Runbook** pod vaší instance služby Azure Automation a pak vyberte **přidat Runbook**. Vyberte možnost **Rychle vytvořit**. Zadejte název vaší sady runbook a vyberte **Powershellu** jako typ runbooku. Máte možnost přidat její popis. Nakonec klikněte na tlačítko **vytvořit**.

![Vytvoření runbooku](./media/keyvault-keyrotation/Create_Runbook.png)

V podokně editor pro nový runbook vložte následující skript prostředí PowerShell:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
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

#Optionally you may set the following as parameters
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

Z podokna editoru zvolte **testovací podokno** k otestování vašeho skriptu. Po bez chyb je spuštěný skript, můžete vybrat **publikovat**, a pak může použít plán pro runbook zpátky v podokně Konfigurace sady runbook.

## <a name="key-vault-auditing-pipeline"></a>Auditování kanálu služby Key Vault
Když nastavíte trezor klíčů, můžete zapnout auditování pro shromažďování protokolů na požadavky na přístup k trezoru klíčů. Tyto protokoly se ukládají v určené účtu Azure Storage a mohou být vyžádány, monitorovat a analyzovat. Následující scénář využívá služba Azure functions, Azure logic apps a protokolů auditu služby key vault k vytvoření kanálu pro odeslání e-mailu, když aplikaci, která se neshoduje s ID aplikace webové aplikace načte tajné kódy z trezoru.

Nejprve je třeba povolit protokolování na váš trezor klíčů. To můžete udělat pomocí následujících příkazů Powershellu (úplné podrobnosti lze zobrazit na [protokolování key vault](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Jakmile je tato možnost povolena, protokoly auditu zahájení shromažďování do účtu úložiště určený. Tyto protokoly obsahovat události o tom, jak a kdy přistupují k vašim trezorům klíčů a kým.

> [!NOTE]
> Informace o protokolování můžete přistupovat 10 minut po provedení této operace služby key vault. Obvykle bude rychlejší.
>
>

Dalším krokem je [vytvoření fronty služby Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). To je, kde jsou vloženy protokoly auditu služby key vault. Když ve frontě zpráv protokolu auditu, aplikace logiky přebere příspěvky a funguje na nich. Vytvoření služby Service bus pomocí následujících kroků:

1. Vytvoření oboru názvů služby Service Bus (pokud ještě nemáte, kterou chcete použít pro tento, přejděte ke kroku 2).
2. Přejděte do služby Service bus na webu Azure Portal a vyberte obor názvů, které chcete vytvořit frontu.
3. Vyberte **vytvořit prostředek**, **podniková integrace**, **služby Service Bus**a potom zadejte požadované podrobnosti.
4. Vyberte Výběr oboru názvů a kliknutím na informace o připojení služby Service Bus **informace o připojení**. Tyto informace budete potřebovat pro další části.

Dále [vytvořit funkci Azure](../azure-functions/functions-create-first-azure-function.md) dotazovat protokoly trezoru klíčů v rámci účtu úložiště a vyzvednutí nové události. Toto bude funkce, která se spouští podle plánu.

Chcete-li vytvořit funkci Azure, zvolte **vytvořit prostředek**, na webu Marketplace vyhledáme _aplikace Function App_a klikněte na tlačítko **vytvořit**. Při vytváření můžete použít existující plán hostování nebo vytvořte novou. Může se také rozhodnout pro dynamické hostování. Další informace o funkci Možnosti hostování najdete v [postup škálování Azure Functions](../azure-functions/functions-scale.md).

Po vytvoření funkce Azure Functions do něj přejděte a vyberte časovač, funkce a C\#. Pak klikněte na tlačítko **vytvořit tuto funkci**.

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

            //required to order by time as they may not be in the file
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
> Ujistěte se, že k nahrazení proměnné v předchozím kódu tak, aby odkazovala na váš účet úložiště, ve kterém jsou zapsány protokoly trezoru klíčů, service bus, které jste vytvořili dříve a konkrétní cestu do úložiště protokolů služby key vault.
>
>

Funkce vybere nejnovější soubor protokolu z účtu úložiště, ve kterém jsou zapsány protokoly trezoru klíčů, získá nejnovější události z tohoto souboru a odesílá je do fronty služby Service Bus. Od jednoho souboru může mít více událostí, můžete vytvořit soubor sync.txt, která také srovnává funkce určit časové razítko poslední události, ke které se nenačítají. Tím se zajistí, že není vložíte stejnou událost více než jednou. Tento soubor sync.txt obsahuje časové razítko poslední události došlo k chybě. Protokoly, při načtení, třeba seřazeny podle časové razítko. Ujistěte se, že jsou řazeny správně.

Pro tuto funkci budeme odkazovat na několik dalších knihoven, které nejsou k dispozici ihned v Azure Functions. Pro ty pak uvádějte, potřebujeme, aby tyto změny služby Azure Functions pomocí nástroje NuGet. Zvolte **zobrazit soubory** možnost.

![Možnost zobrazit soubory](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

A přidejte soubor s názvem souboru project.json s následujícím obsahem:

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

Při **Uložit**, Azure Functions se stáhne požadované binární soubory.

Přepněte **integrace** kartu a dejte smysluplný název určený v rámci funkce parametr časovače. V předchozím kódu se očekává, že na časovač a volat *myTimer*. Zadejte [výraz CRON](../app-service/webjobs-create.md#CreateScheduledCRON) následujícím způsobem: 0 \* \* \* \* \* časovač, který způsobí, že funkce pro spuštění jednou za minutu.

Na stejném **integrace** kartu, přidat vstup typu **Azure Blob Storage**. To bude odkazovat na sync.txt souboru, který obsahuje časové razítko poslední události podívali se na funkce. To bude možné v rámci funkce podle názvu parametru. V předchozím kódu vstup úložiště objektů Blob v Azure očekává, že název parametru, který se má *inputBlob*. Zvolte účet úložiště, ve kterém bude uložený soubor sync.txt (může to být stejný nebo jiný účet úložiště). V poli Cesta zadejte cestu, kam se soubor nachází ve formátu {container-name}/path/to/sync.txt.

Přidat výstup typu *Azure Blob Storage* výstup. To bude odkazovat na sync.txt soubor, který jste definovali ve vstupu. To se používá k zápisu časové razítko poslední události podívali se na funkce. Předchozí kód očekává, že tento parametrem říkalo *outputBlob*.

Funkce je v tuto chvíli připravena. Ujistěte se, že chcete přepnout zpět **vývoj** kartu a uložte kód. Najdete v okně výstupu chyby při kompilaci a opravte je odpovídajícím způsobem. Pokud se kompiluje kód, pak kód by měl nyní být kontroluje protokoly trezoru klíčů se každou minutu a doručením (push) libovolný nové události do definované frontu služby Service Bus. Zobrazí se informace o protokolování zapsat do protokolu okna pokaždé, když se aktivuje funkci.

### <a name="azure-logic-app"></a>Azure logic app

Dále musíte vytvořit Azure logic app, který převezme událostí funkce vkládání do fronty služby Service Bus, analyzuje obsah a odešle e-mailu na základě podmínky je nalezena shoda.

[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) tak, že přejdete do **nový > aplikace logiky**.

Po vytvoření aplikace logiky do něj přejděte a vyberte **upravit**. V editoru aplikace logiky zvolte **frontu služby Service Bus** a zadejte svoje přihlašovací údaje služby Service Bus pro připojení k frontě.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Dále vyberte **přidat podmínku**. V podmínce přepnout do rozšířeného editoru a zadejte následující kód a nahraďte APP_ID s skutečné APP_ID vaší webové aplikace:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Tento výraz vrátí v podstatě **false** Pokud *appid* z příchozí události (což je text zprávy služby Service Bus) není *appid* aplikace.

Teď vytvořte akci v rámci **Pokud ne, nedělat nic**.

![Zvolte akci Azure Logic App](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Akce, můžete zvolit **Office 365 – poslat e-mail**. Vyplňte pole k vytvoření e-mailu k odeslání návratu definované podmínky **false**. Pokud nemáte Office 365, je vyhledat v alternativy k dosáhnete stejných výsledků.

V tomto okamžiku máte za kompletní kanál, který hledá nové protokoly auditu služby key vault jednou za minutu. Nové protokoly, které nalezne ho odešle do fronty service bus. Aplikace logiky se aktivuje, když jsou novou zprávu ve frontě. Pokud *appid* v rámci události se neshoduje s ID aplikace z volající aplikace, odešle e-mailu.
