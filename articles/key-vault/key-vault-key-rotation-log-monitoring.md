---
title: Nastavit Azure Key Vault se klíče otočení začátku do konce a auditování | Microsoft Docs
description: Použijte tento postup můžete získat nastavit střídání klíče a monitorování protokoly trezoru klíčů.
services: key-vault
documentationcenter: ''
author: swgriffith
manager: mbaldwin
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: stgriffi
ms.openlocfilehash: 701edc784dcde067e8ec585a3b31d6075207fbbf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Nastavení kompletní obměny klíčů a auditování ve službě Azure Key Vault
## <a name="introduction"></a>Úvod
Po vytvoření trezoru klíčů, budete moct začít používat tento trezor k ukládání klíčů a tajných klíčů. Aplikace už nutné k uchování klíčů nebo tajných klíčů, ale spíše bude o ně požádat z trezoru klíčů podle potřeby. To umožňuje aktualizovat klíče a tajné klíče, aniž by to ovlivnilo chování vaší aplikace, což otevře a široké možnosti kolem vašeho klíče a tajné správy.

>[!IMPORTANT]
> V příkladech v tomto článku jsou uvedené pro pouze pro účely obrázku. Nejsou určeny pro použití v provozním prostředí. 

Tento článek vás provede příklad použití Azure Key Vault pro uložení tajný klíč, v tomto případě klíčem účtu úložiště Azure, který přistupuje aplikaci. Také ukazuje implementaci naplánované oběh tento klíč účtu úložiště. Nakonec provede procesem ukázka sledování protokolů auditu trezoru klíčů a vyvolat výstrahy, když jsou vytvářeny neočekávané požadavky.

> [!NOTE]
> V tomto kurzu není určeno k podrobně popisují počátečním nastavení trezoru klíčů. Další informace naleznete v tématu [Začínáme s Azure Key Vault](key-vault-get-started.md) Pokyny Multiplatformního rozhraní příkazového řádku najdete v tématu [Key Vault spravovat pomocí rozhraní příkazového řádku](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Nastavení služby Key Vault
Chcete-li aplikaci načíst tajného klíče z Key Vault, musíte nejprve vytvořit tajný klíč a nahrajte ho do trezoru. Můžete to provést spusťte relaci prostředí Azure PowerShell a přihlášení k účtu Azure pomocí následujícího příkazu:

```powershell
Login-AzureRmAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. PowerShell získá všechna předplatná, které jsou spojeny s tímto účtem. Prostředí PowerShell použije první, ve výchozím nastavení.

Pokud máte více předplatných, možná muset zadat ten, který byl použit k vytvoření trezoru klíčů. Zadejte následující příkaz pro zobrazení předplatných pro váš účet:

```powershell
Get-AzureRmSubscription
```

Chcete-li zadat odběr, který je spojen s trezoru klíčů, který budete protokolovat, zadejte:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Vzhledem k tomu, že tento článek ukazuje, ukládání klíč účtu úložiště jako tajný klíč, musíte si tento klíč účtu úložiště.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Po načtení váš tajný klíč (v tomto případě klíč účtu úložiště), je potřeba, převést na zabezpečený řetězec a pak vytvořit tajný klíč s touto hodnotou v trezoru klíčů.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
V dalším kroku získáte identifikátor URI pro tajný klíč, který jste vytvořili. Používá se v pozdější fázi při volání trezoru klíčů načíst váš tajný klíč. Spusťte následující příkaz prostředí PowerShell a poznamenejte si hodnotu ID, která je tajný identifikátor URI:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Nastavení aplikace
Teď, když máte uložené tajný klíč, můžete k načtení a jeho použití kódu. Existuje několik kroků potřebných k dosažení tohoto cíle. První a nejdůležitější krok je registrace vaší aplikace v Azure Active Directory a potom že Key Vault informace o vaší aplikaci tak, aby ji můžete povolit požadavky od vaší aplikace.

> [!NOTE]
> Aplikace musí být vytvořeny na stejném tenantovi Azure Active Directory jako váš trezor klíčů.
>
>

Otevřete kartu aplikace služby Azure Active Directory.

![Otevřete aplikace v Azure Active Directory](./media/keyvault-keyrotation/AzureAD_Header.png)

Zvolte **přidat** přidání aplikace do Azure Active Directory.

![Zvolte možnost Přidat](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Ponechat typ aplikace jako **webové aplikace nebo webové rozhraní API** a zadejte název vaší aplikace.

![Název aplikace](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Poskytují aplikace **adresa URL přihlašování** a **identifikátor ID URI aplikace**. Mohou to být nic, co chcete použít pro tuto ukázku, a může změnit později v případě potřeby.

![Zadejte požadované identifikátory URI](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Po přidání aplikace do Azure Active Directory je přesměrován zpět na stránku aplikace. Klikněte **konfigurace** kartě a vyhledejte a zkopírujte **ID klienta** hodnotu. Poznamenejte si ID klienta na pozdější kroky.

V dalším kroku vygenerujte klíč pro vaši aplikaci, můžete spolupracovat s vaší služby Azure Active Directory. Můžete vytvořit ji do **klíče** tématu **konfigurace** kartě. Poznamenejte si nově vygenerovaný klíč z vaší aplikace Azure Active Directory pro použití v pozdější fázi.

![Klíče aplikace služby Azure Active Directory](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Před vytvořením žádné volání z aplikace do trezoru klíčů, se musí zjistit trezoru klíčů o aplikace a její oprávnění. Následující příkaz přebírá název trezoru a ID klienta z aplikací Azure Active Directory a uděluje **získat** přístup k trezoru klíčů pro aplikaci.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

V tomto okamžiku jste připravení začít vytvářet aplikace zavolat. V aplikaci je nutné nainstalovat balíčky NuGet, které jsou potřebné pro interakci s Azure Key Vault a Azure Active Directory. Z konzoly Správce balíčků Visual Studio zadejte následující příkazy. Na zápis tohoto článku, aktuální verzi balíčku, Azure Active Directory je 3.10.305231913, takže můžete chtít potvrďte na nejnovější verzi a aktualizují odpovídajícím způsobem.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

V kódu aplikace vytvořte třídu, pro uložení metodu pro ověřování Azure Active Directory. V tomto příkladu se nazývá třídy **Utils**. Přidejte následující příkaz using:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Dál přidejte následující metodu na JWT token načítat z Azure Active Directory. Pro udržovatelnosti můžete přesunout pevně řetězcové hodnoty do konfiguraci webu nebo aplikace.

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

Přidání nezbytného kódu volání Key Vault a načíst vaše tajná hodnota. Nejprve je nutné přidat následující příkaz using:

```csharp
using Microsoft.Azure.KeyVault;
```

Přidejte volání metody vyvolání Key Vault a načtení váš tajný klíč. Tato metoda zadejte identifikátor URI, který jste uložili v předchozím kroku tajného klíče. Všimněte si použití **gettoken –** metoda z **Utils** třídy, které jste vytvořili dříve.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Při spuštění aplikace teď byste měli být ověřování pro službu Azure Active Directory a potom načítání tajná hodnota z Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Střídání klíče pomocí Azure Automation.
Existují různé možnosti pro implementaci strategie otočení pro hodnoty, které ukládáte jako Azure Key Vault tajných klíčů. Jako součást ruční proces lze otáčet tajné klíče, se může prostřednictvím kódu programu otáčet pomocí volání rozhraní API nebo může být otáčet prostřednictvím skriptu pro automatizaci. Pro účely tohoto článku budete používat prostředí Azure PowerShell v kombinaci s Azure Automation. Chcete-li změnit přístupový klíč účtu úložiště Azure. Pomocí tohoto nového klíče potom aktualizujte tajný klíč trezoru klíčů.

Povolit Azure Automation k nastavení tajný hodnot v trezoru klíčů, musíte získat ID klienta pro připojení s názvem AzureRunAsConnection, která byla vytvořena, když jste vytvořili vaší instanci Azure Automation. Toto ID můžete najít tak, že zvolíte **prostředky** z vaší instanci Azure Automation. Odtud, zvolíte **připojení** a pak vyberte **AzureRunAsConnection** služby zásadu. Poznamenejte si **ID aplikace**.

![ID klienta Azure Automation.](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

V **prostředky**, zvolte **moduly**. Z **moduly**, vyberte **Galerie**a poté vyhledejte a **Import** aktualizované verze pro každý z následujících modulů:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Na zápis tohoto článku, jenom dříve uvedené moduly musela být aktualizováno pro následující skript. Pokud zjistíte, že je možné úlohu automatizace, potvrďte, že jste importovali všechny potřebné moduly a jejich závislosti.
>
>

Po načtení ID aplikace pro připojení k Azure Automation, se musí zjistit trezoru klíčů, tato aplikace má přístup k aktualizaci tajných klíčů v trezoru. Můžete to provést pomocí následujícího příkazu prostředí PowerShell:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Potom vyberte **Runbooky** v rámci vaší instanci Azure Automation a potom vyberte **přidat Runbook**. Vyberte možnost **Rychle vytvořit**. Název vaší sady runbook a vyberte **prostředí PowerShell** jako typ runbooku. Máte možnost přidat její popis. Nakonec klikněte na **vytvořit**.

![Vytvoření runbooku](./media/keyvault-keyrotation/Create_Runbook.png)

V podokně editor pro nový runbook vložte následující skript prostředí PowerShell:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
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

V podokně editor zvolte **testovací podokno** k testování vašeho skriptu. Jakmile se skript spouští bez chyby, můžete vybrat **publikovat**, a pak můžete použít plán pro sady runbook zpět v podokně Konfigurace sady runbook.

## <a name="key-vault-auditing-pipeline"></a>Auditování kanálu Key Vault
Při nastavování trezoru klíčů, můžete zapnout auditování shromažďovat protokoly v žádosti o přístup provedených do trezoru klíčů. Tyto protokoly se ukládají v určené účtu Azure Storage a mohou být vyžádány limitu, monitorovat a analyzovat. Následující scénář používá k vytvoření kanálu pro odeslání e-mailu, když aplikaci, která odpovídají ID aplikace webové aplikace načte tajné klíče z trezoru Azure functions, Azure logic apps a protokoly auditu trezoru klíčů.

Nejprve musíte povolit protokolování na váš trezor klíčů. To lze provést pomocí následujících příkazů Powershellu (úplné podrobnosti si můžete prohlédnout v [protokolování key vault](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Jakmile je tato možnost povolena, protokoly auditu počáteční shromažďování do účtu úložiště určený. Tyto protokoly obsahovat události o tom, jak a kdy vašim trezorům klíčů přistupuje a kým.

> [!NOTE]
> Informace o protokolování můžete přistupovat 10 minut po operaci trezoru klíčů. Obvykle bude rychlejší.
>
>

Dalším krokem je [vytvořit frontu Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Toto je, kde jsou poslat protokoly auditu trezoru klíčů. Když zpráv protokolu auditování ve frontě, aplikaci logiky je převezme a funguje na ně. Vytvoření služby service bus pomocí následujících kroků:

1. Vytvoření oboru názvů Service Bus (Pokud již účet máte, kterou chcete použít pro tento, přejděte ke kroku 2).
2. Přejděte do služby service bus na portálu Azure a vyberte obor názvů, které chcete vytvořit ve frontě v.
3. Vyberte **vytvořit prostředek**, **Enterprise integrace**, **Service Bus**a potom zadejte požadované podrobnosti.
4. Zvolit informace o připojení služby Service Bus výběr obor názvů a **informace o připojení**. Tyto informace budete potřebovat pro další části.

Dále [vytvoření Azure funkce](../azure-functions/functions-create-first-azure-function.md) dotazování protokoly trezoru klíčů v rámci účtu úložiště a vyzvednutí nové události. Bude jím funkci, která se spustí podle plánu.

Pokud chcete vytvořit Azure funkce, vyberte **vytvořit prostředek**, vyhledávání na webu marketplace pro _aplikaci funkce_a klikněte na tlačítko **vytvořit**. Během vytváření můžete použít existující hostingový plán nebo vytvořte novou. Může se rovněž rozhodnout pro dynamické hostování. Další informace o funkce hostování možnosti naleznete na [postup škálování Azure Functions](../azure-functions/functions-scale.md).

Když je vytvořen funkci Azure, přejděte k němu a zvolte časovač funkce a C\#. Pak klikněte na tlačítko **vytvořit tuto funkci**.

![Okno Azure spustit funkce](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Na **vývoj** kartě, run.csx kódu nahraďte následujícím kódem:

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
> Nezapomeňte nahradit proměnné v předchozí kód tak, aby odkazoval na váš účet úložiště, kde se zapisují protokoly trezoru klíčů, sběrnici služby, které jste vytvořili dříve a konkrétní cestu k úložišti protokoly trezoru klíčů.
>
>

Funkce převezme nejnovější soubor protokolu z účtu úložiště, kde se zapisují protokoly trezoru klíčů, získá nejnovější události z tohoto souboru a nabízených oznámení je do fronty Service Bus. Vzhledem k tomu, že jeden soubor může mít více událostí, měli vytvořit sync.txt souboru, který funkce se také vypadá v Chcete-li zjistit časové razítko poslední události, která byla zachyceny. Tím se zajistí, že nemáte nabízené stejnou událost vícekrát. Tento soubor sync.txt obsahuje časové razítko pro poslední došlo k události. Protokoly, při načítání, musí být seřazeny podle časového razítka zajistit, že jsou řazení správně.

Pro tuto funkci jsme odkazovat na několik dalších knihovny, které nejsou k dispozici ihned v Azure Functions. Zahrnout tyto, potřebujeme Azure Functions je načítat pomocí nástroje NuGet. Vyberte **zobrazit soubory** možnost.

![Zobrazení souborů](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

A přidejte do souboru s názvem souboru project.json s následujícím obsahem:

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
Při **Uložit**, Azure Functions stáhnou požadované binární soubory.

Přepnout **integrací** kartě a zadejte parametr časovače smysluplný název pro použití v rámci funkce. V předchozím kódu, se očekává, že časovač k volání *myTimer*. Zadejte [výraz CRON](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON) následujícím způsobem: 0 \* \* \* \* \* pro časovač způsobí, že funkce se má spustit jednou za minutu.

Ve stejném **integrací** přidejte vstup typu **Azure Blob Storage**. To bude odkazovat sync.txt soubor, který obsahuje časové razítko poslední události zvážení funkcí. To bude možné v rámci funkce název parametru. V předchozí kód Azure Blob Storage vstup očekává název parametru, který se má *inputBlob*. Zvolte účet úložiště, kde bude uložený soubor sync.txt (může to být stejný nebo jiný účet úložiště). Do pole Cesta zadejte cestu, kde je umístěn soubor ve formátu {container-name}/path/to/sync.txt.

Přidat výstup typu *Azure Blob Storage* výstup. To bude odkazovat na sync.txt soubor, který jste definovali ve vstupu. To se používá k zápisu časové razítko poslední události po zvážení funkcí. Předchozí kód očekává, že tento parametr má být volána *outputBlob*.

Funkce je nyní připraven. Nezapomeňte přepnout zpět **vývoj** kartě a uložte kód. Ve výstupním okně případné chyby kompilace zkontrolujte a opravte je odpovídajícím způsobem. Pokud kompiluje se kód, pak kód by měl nyní se kontrola protokoly trezoru klíčů každou minutu a vkládání všechny nové události do definované fronty Service Bus. Měli byste vidět zapsat do okna protokolu pokaždé, když se aktivuje funkce informace o protokolování.

### <a name="azure-logic-app"></a>Azure logic Apps
Dále musíte vytvořit aplikaci Azure logiku, která převezme události funkce je vkládání do fronty Service Bus, analyzuje obsah a odešle e-mailu na základě podmínky se shodoval.

[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) přechodem na **nový > aplikace logiky**.

Po vytvoření aplikace logiky, přejděte k němu a zvolte **upravit**. V editoru aplikace logiky, vyberte **frontou Service Bus** a zadejte svá pověření Service Bus pro připojení k frontě.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Dále vyberte **přidat podmínku**. V podmínce přepněte do editoru Upřesnit a zadejte následující kód, nahraďte APP_ID skutečné APP_ID vaší webové aplikace:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Tento výraz v podstatě vrátí **false** Pokud *appid* z příchozí události (což je do těla zprávy služby Service Bus) není *appid* aplikace.

Teď vytvořte akce v rámci **Pokud ne, nedělat nic**.

![Aplikace logiky Azure vybrat akci](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Jako akci vyberte **Office 365 - odesílání e-mailu**. Vyplňte pole pro vytvoření e-mailu k odeslání, když se vrátí definované podmínky **false**. Pokud nemáte Office 365, může se podíváte na alternativy, abyste dosáhli stejné výsledky.

V tuto chvíli máte kanál koncová, které hledá nové protokoly auditu trezoru klíčů jednou za minutu. Vynutí nové protokoly, které najde do fronty service bus. Aplikace logiky se aktivuje, když pojmenováváme novou zprávu ve frontě. Pokud *appid* v rámci událost neodpovídá ID aplikace je volající aplikace, odešle e-mailu.
