---
title: Kurz střídání jednoho uživatele a hesla
description: Tento kurz použijte pro automatizaci střídání jednoho uživatele/hesla
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239368"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Automatizace střídání tajných klíčů pro prostředky pomocí ověřování pomocí jednoho uživatele/hesla

I když nejlepší způsob, jak ověřit služby Azure je pomocí [spravované identity](managed-identity.md), existují některé scénáře, kde to není možnost. V těchto případech se používají přístupové klíče nebo tajné klíče. Přístupové klávesy nebo tajné klíče by měly být pravidelně otočeny.

Tento kurz ukazuje, jak automatizovat periodické střídání tajných kódů pro databáze a služby pomocí ověřování pomocí jednoho uživatele/hesla. Konkrétně tento scénář otáčí sql server hesla uložená v trezoru klíčů pomocí funkce spouštěné oznámení mřížce událostí:

![Diagram otáčení](./media/rotate1.png)

1. Třicet dní před datem vypršení platnosti tajného klíče, Trezor klíčů publikovat "téměř vypršení platnosti" událost event grid.
1. Event Grid zkontroluje odběry událostí a pomocí http post volá koncový bod aplikace funkce přihlášeni k odběru této události.
1. Aplikace funkce obdrží tajné informace, vygeneruje nové náhodné heslo a vytvoří novou verzi pro tajný klíč s novým heslem v trezoru klíčů.
1. Aplikace funkce aktualizuje SQL s novým heslem.

> [!NOTE]
> Může být prodleva mezi krokem 3 a 4 a během této doby tajný klíč v trezoru klíčů by nebylo platné k ověření SQL. V případě selhání v některém z kroků Event Grid opakování po dobu 2 hodin.

## <a name="setup"></a>Nastavení

## <a name="create-a-key-vault-and-sql-server"></a>Vytvoření trezoru klíčů a serveru SQL

Než začneme, musíme vytvořit trezor klíčů, vytvořit SQL Server a databázi a uložit heslo správce serveru SQL Server v trezoru klíčů.

Tento kurz používá předem vytvořenou šablonu Azure Resource Manager k vytvoření komponent. Celý kód naleznete zde: [Ukázka šablony základního tajného střídání](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Klikněte na odkaz pro nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Pro "Skupinu prostředků" vyberte "Vytvořit nový" a pojmenujte jej "simplerotation".
1. Vyberte možnost "Nákup".

    ![Vytvořit novou skupinu prostředků](./media/rotate2.png)

Po dokončení těchto kroků budete mít trezor klíčů, sql server a databázi SQL. Můžete ověřit v terminálu Azure CLI spuštěním:

```azurecli
az resource list -o table
```

Výsledky budou vypadat něco takto:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Vytvořit aplikaci funkcí

Vytvořte aplikaci funkcí se systémem spravovanou identitou a další mise: 

Aplikace funkce vyžaduje níže uvedené součásti a konfiguraci:
- Plán služby App Service
- Účet úložiště
- Přístup k zásadám pro přístup k tajným kódům v trezoru klíčů pomocí spravované identity aplikace function

1. Klikněte na odkaz pro nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Pro "Skupinu prostředků" vyberte "simplerotation".
1. Vyberte možnost "Nákup".

   ![Obrazovka nákupu](./media/rotate3.png)

Po dokončení výše uvedených kroků budete mít účet úložiště, serverovou farmu a aplikaci funkce.  Můžete ověřit v terminálu Azure CLI spuštěním:

```azurecli
az resource list -o table
```

Výsledky budou vypadat něco takto:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

Informace o tom, jak vytvořit aplikaci function app a pomocí spravované identity pro přístup k trezoru klíčů, najdete v [tématu Vytvoření aplikace funkcí z webu Azure Portal](../azure-functions/functions-create-function-app-portal.md) a Poskytnutí ověřování [trezoru klíčů se spravovanou identitou.](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>Funkce otáčení a nasazení
Funkce používá událost jako aktivační událost a provádí střídání tajné aktualizace trezoru klíčů a databáze SQL.

#### <a name="function-event-trigger-handler"></a>Obslužná rutina aktivační události funkce

Níže funkce čte data událostí a provádí logiku rotace

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Logika tajného střídání
Tato metoda rotace čte informace o databázi z tajného klíče, vytvoří novou verzi tajného klíče a aktualizuje databázi pomocí nového tajného klíče.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Celý kód naleznete zde:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Nasazení funkcí

1. Stáhnout funkci aplikace zip soubor:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Nahrajte soubor simplerotationsample-fn.zip do Prostředí Azure Cloud Shell.
 
1. Pomocí příkazu pod příkazem CLI nasadíte aplikaci zip file to function:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Obrazovka nákupu](./media/rotate4.png)

Po nasazení byste si měli všimnout dvou funkcí pod simplerotation-fn:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Přidat odběr událostí pro událost "SecretNearExpiry"

Zkopírujte tlačítko aplikace funkce eventgrid_extension.

![Azure Cloud Shell](./media/rotate6.png)

![Testování a ověření](./media/rotate7.png)

Pomocí zkopírovaného rozšiřujícího klíče eventgrid a id předplatného v níže uvedeném příkazu vytvořte odběr mřížky událostí pro události SecretNearExpiry.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Přidání tajného klíče do trezoru klíčů
Nastavte zásady přístupu tak, aby uživatelům ušetdala oprávnění ke správě tajných kódů.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Nyní vytvořte nový tajný klíč se značkami obsahujícími zdroj dat databáze SQL a ID uživatele s datem vypršení platnosti nastaveným na zítřek.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Vytvoření tajného klíče s krátkým datem vypršení platnosti okamžitě zveřejní událost SecretNearExpiry, která zase spustí funkci otočení tajného klíče.

### <a name="test-and-verify"></a>Testování a ověření
Po několika minutách sqluser tajný klíč by měl automaticky otáčet.

Chcete-li ověřit ověření tajného střídání, přejděte na > tajných kódů trezoru klíčů.

![Testování a ověření](./media/rotate8.png)

Otevřete tajný klíč "sqluser" a zobrazte původní a otočenou verzi

![Testování a ověření](./media/rotate9.png)

## <a name="create-web-app"></a>Vytvoření webové aplikace

Chcete-li ověřit pověření SQL, vytvořte webovou aplikaci. Tato webová aplikace získá tajný klíč z trezoru klíčů, extrahuje informace o databázi SQL a pověření z tajného klíče a otestuje připojení k SQL.

Webová aplikace vyžaduje níže uvedené součásti a konfiguraci:
- Webová aplikace se systémem spravovanou identitou
- Přístup k zásadám pro přístup k tajným klíčům v trezoru klíčů pomocí spravované identity webové aplikace

1. Klikněte na odkaz pro nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Vyberte skupinu prostředků **simplerotation**
1. Klikněte na Nákup

### <a name="deploy-web-app"></a>Nasazení webové aplikace

Zdrojový kód pro webovou aplikaci, kterou najdete na https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp webu Pro nasazení webové aplikace, postupujte takto:

1. Stáhněte si soubor zip aplikace funkce zhttps://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Nahrajte `simplerotationsample-app.zip` soubor do Azure Cloud Shell.
1. Pomocí tohoto příkazu Azure CLI nasadit soubor zip do aplikace funkce:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Otevřít webovou aplikaci

Přejděte na nasazenou aplikaci a klikněte na "URL":
 
![Testování a ověření](./media/rotate10.png)

Vygenerovaná tajná hodnota by měla být zobrazena s připojenou databází jako true.

## <a name="learn-more"></a>Další informace:

- Přehled: [Monitorování trezoru klíčů pomocí Azure Event Grid (preview)](event-grid-overview.md)
- Postup: [Příjem e-mailů při změně tajného klíče trezoru](event-grid-logicapps.md)
- [Schéma událostí Azure Event Grid pro Azure Key Vault (preview)](../event-grid/event-schema-key-vault.md)
