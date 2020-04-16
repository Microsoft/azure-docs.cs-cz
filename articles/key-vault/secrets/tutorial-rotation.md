---
title: Kurz rotace pro jednoho uživatele nebo jednoheslo
description: V tomto kurzu se dozvíte, jak automatizovat střídání tajných klíčů pro prostředky, které používají ověřování jedním uživatelem nebo jedním heslem.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 70eb2449c5c54750831c30ff7d5c948173a38594
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423299"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>Automatizace střídání tajného klíče pro prostředky, které používají ověřování jedním uživatelem nebo jedním heslem

Nejlepší způsob, jak ověřit služby Azure je pomocí [spravované identity](../general/managed-identity.md), ale existují některé scénáře, kde to není možnost. V těchto případech se používají přístupové klíče nebo tajné klíče. Měli byste pravidelně otáčet přístupové klíče nebo tajné klíče.

Tento kurz ukazuje, jak automatizovat periodické střídání tajných kódů pro databáze a služby, které používají ověřování jedním uživatelem nebo jedním heslem. Konkrétně tento kurz otáčí SQL Server hesla uložená v Azure Key Vault pomocí funkce spouštěné oznámení azure event grid:

![Diagram rotačního řešení](../media/rotate1.png)

1. Třicet dní před datem vypršení platnosti tajného klíče, Trezor klíčů publikuje událost "téměř vypršení platnosti" do event gridu.
1. Event Grid zkontroluje odběry událostí a pomocí HTTP POST zavolá koncový bod aplikace funkce, který se k události přihlásí.
1. Aplikace funkce obdrží tajné informace, vygeneruje nové náhodné heslo a vytvoří novou verzi pro tajný klíč s novým heslem v trezoru klíčů.
1. Aplikace funkce aktualizuje SQL Server s novým heslem.

> [!NOTE]
> Mezi kroky 3 a 4 může být prodleva. Během této doby nebude možné tajný klíč v trezoru klíčů ověřit sql server. V případě selhání některého z kroků event grid opakuje po dobu dvou hodin.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Vytvoření trezoru klíčů a instance serveru SQL Server

Prvním krokem je vytvoření trezoru klíčů a instance serveru SQL Server a databáze a uložení hesla správce serveru SQL Server v trezoru klíčů.

Tento kurz používá existující šablonu Azure Resource Manager k vytvoření komponent. Kód naleznete zde: [Ukázka šablony základního tajného střídání](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Vyberte odkaz na nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. V části **Skupina prostředků**vyberte **Vytvořit nový**. Název skupiny **simplerotation**.
1. Vyberte **Koupit**.

    ![Vytvoření skupiny prostředků](../media/rotate2.png)

Nyní budete mít trezor klíčů, instanci serveru SQL a databázi SQL. Toto nastavení můžete ověřit v rozhraní příkazu Konto Azure spuštěním následujícího příkazu:

```azurecli
az resource list -o table
```

Výsledek bude vypadat něco následující výstup:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Vytvoření Function App

Dále vytvořte aplikaci funkcí s identitou spravovanou systémem, kromě dalších požadovaných součástí.

Aplikace funkce vyžaduje tyto součásti:
- Plán služby Azure App Service
- Účet úložiště
- Zásady přístupu pro přístup k tajným kódům v trezoru klíčů prostřednictvím identity spravované aplikací funkcí

1. Vyberte odkaz na nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. V seznamu **Skupina prostředků** vyberte **simplerotation**.
1. Vyberte **Koupit**.

   ![Vybrat nákup](../media/rotate3.png)

Po dokončení předchozích kroků budete mít účet úložiště, serverovou farmu a aplikaci funkcí. Toto nastavení můžete ověřit v rozhraní příkazu Konto Azure spuštěním následujícího příkazu:

```azurecli
az resource list -o table
```

Výsledek bude vypadat podobně jako následující výstup:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

Informace o tom, jak vytvořit aplikaci funkcí a použít spravovanou identitu pro přístup k trezoru klíčů, najdete v [tématu Vytvoření aplikace funkcí z webu Azure Portal](../../azure-functions/functions-create-function-app-portal.md) a Poskytnutí ověřování [trezoru klíčů se spravovanou identitou](../general/managed-identity.md).

### <a name="rotation-function"></a>Funkce otáčení
Funkce používá událost k aktivaci otočení tajného klíče aktualizací trezoru klíčů a databáze SQL.

#### <a name="function-trigger-event"></a>Událost aktivační události funkce

Tato funkce čte data událostí a spouští logiku rotace:

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

#### <a name="secret-rotation-logic"></a>Tajná logika rotace
Tato metoda rotace čte informace o databázi z tajného klíče, vytvoří novou verzi tajného klíče a aktualizuje databázi s novým tajným klíčem:

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Kompletní kód najdete na [GitHubu](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Nasazení funkcí

1. Stáhněte si soubor zip aplikace funkce z [GitHubu](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Nahrajte soubor simplerotationsample-fn.zip do prostředí Azure Cloud Shell.

   ![Nahrání souboru](../media/rotate4.png)
1. Pomocí tohoto příkazu Azure CLI nasadit soubor zip do aplikace funkce:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Po nasazení funkce byste měli vidět dvě funkce pod simplerotation-fn:

![SimpleRotation a SimpleRotationHttpTest funkce](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Přidání odběru událostí pro událost SecretNearExpiry

Zkopírujte `eventgrid_extension` klíč aplikace funkce:

   ![Výběr nastavení aplikace funkcí](../media/rotate6.png)

   ![eventgrid_extension klíč](../media/rotate7.png)

Pomocí zkopírovaného `eventgrid_extension` klíče a ID předplatného v následujícím `SecretNearExpiry` příkazu vytvořte předplatné služby Event Grid pro události:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Přidání tajného klíče do trezoru klíčů
Nastavte zásady přístupu tak, aby uživatelům udělovat oprávnění ke *správě tajných kódů:*

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Vytvořte nový tajný klíč se značkami, které obsahují zdroj dat databáze SQL a ID uživatele. Uveďte datum vypršení platnosti, které je nastaveno na zítřek.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Vytvoření tajného klíče s krátkým `SecretNearExpiry` datem vypršení platnosti okamžitě publikuje událost, která zase spustí funkci otočení tajného klíče.

## <a name="test-and-verify"></a>Testování a ověření
Po několika minutách by se `sqluser` tajemství mělo automaticky otáčet.

Chcete-li ověřit, zda byl tajný klíč otočen, přejděte na > **tajné klíče trezoru** **klíčů**:

![Přejít na Tajemství](../media/rotate8.png)

Otevřete tajný klíč **sqluser** a zobrazte původní a otočené verze:

![Otevření tajného klíče sqluseru](../media/rotate9.png)

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

Chcete-li ověřit přihlašovací údaje SQL, vytvořte webovou aplikaci. Tato webová aplikace získá tajný klíč z trezoru klíčů, extrahuje informace o databázi SQL a přihlašovací údaje z tajného klíče a otestuje připojení k serveru SQL Server.

Webová aplikace vyžaduje tyto součásti:
- Webová aplikace se systémem spravovanou identitou
- Zásady přístupu pro přístup k tajným kódům v trezoru klíčů prostřednictvím identity spravované webovou aplikací

1. Vyberte odkaz na nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Vyberte skupinu prostředků **simplerotation.**
1. Vyberte **Koupit**.

### <a name="deploy-the-web-app"></a>Nasazení webové aplikace

Zdrojový kód pro webovou aplikaci najdete na [GitHubu](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Chcete-li nasadit webovou aplikaci, proveďte tyto kroky:

1. Stáhněte si soubor zip aplikace funkce z [GitHubu](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Nahrajte soubor simplerotationsample-app.zip do prostředí Azure Cloud Shell.
1. Pomocí tohoto příkazu Azure CLI nasadit soubor zip do aplikace funkce:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Otevření webové aplikace

Přejděte na nasazenou aplikaci a vyberte adresu URL:
 
![Vyberte adresu URL](../media/rotate10.png)

Měli byste vidět generovanou tajnou hodnotu s hodnotou připojenou k databázi true.

## <a name="learn-more"></a>Další informace

- Přehled: [Monitorování trezoru klíčů pomocí Azure Event Grid (preview)](../general/event-grid-overview.md)
- Postup: [Příjem e-mailů při změně tajného klíče trezoru](../general/event-grid-logicapps.md)
- [Schéma událostí Azure Event Grid pro Azure Key Vault (preview)](../../event-grid/event-schema-key-vault.md)