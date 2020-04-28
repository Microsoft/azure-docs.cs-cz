---
title: Kurz rotace jednoho uživatele nebo jednoduchého hesla
description: V tomto kurzu se dozvíte, jak automatizovat rotaci tajného klíče pro prostředky, které používají ověřování jedním uživatelem a jedním heslem.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f9c0dca29d173eb2c7893a20b2ab41dd31522e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183207"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>Automatizace rotace tajného klíče pro prostředky, které používají ověřování jedním uživatelem a jedním heslem

Nejlepším způsobem, jak ověřit služby Azure, je použití [spravované identity](../general/managed-identity.md), ale v některých případech se nejedná o možnost. V těchto případech se používají přístupové klíče a tajné kódy. Přístupové klíče a tajné kódy byste měli pravidelně střídat.

V tomto kurzu se dozvíte, jak automatizovat pravidelnou rotaci tajných kódů pro databáze a služby, které používají ověřování jedním uživatelem a jedním heslem. Konkrétně tento kurz otočí SQL Server hesla uložená v Azure Key Vault pomocí funkce aktivované Azure Event Gridm oznámením:

![Diagram řešení rotace](../media/rotate1.png)

1. Třicet dní před datem vypršení platnosti tajného kódu Key Vault pro Event Grid publikovat událost blížící se vypršení platnosti.
1. Event Grid zkontroluje odběry událostí a pomocí HTTP POST zavolá koncový bod aplikace Function App, který se přihlásí k odběru události.
1. Aplikace Function App obdrží tajné informace, vygeneruje nové náhodné heslo a vytvoří novou verzi pro tajný klíč s novým heslem v Key Vault.
1. Aplikace Function App SQL Server aktualizuje pomocí nového hesla.

> [!NOTE]
> Mezi kroky 3 a 4 by mohlo dojít k prodlevě. Během této doby se tajný kód v Key Vault nebude moci ověřit pro SQL Server. V případě selhání některého z kroků Event Grid opakování po dvou hodinách.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Vytvoření trezoru klíčů a instance SQL Server

Prvním krokem je vytvoření trezoru klíčů a SQL Server instance a databáze a ukládání hesla SQL Server správce do Key Vault.

V tomto kurzu se k vytváření komponent používá existující šablona Azure Resource Manager. Kód najdete tady: [Ukázka šablony základního tajného klíče pro otočení](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Vyberte odkaz nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. V části **Skupina prostředků**vyberte **vytvořit novou**. Pojmenujte skupinu **simplerotation**.
1. Vyberte **Koupit**.

    ![Vytvoření skupiny prostředků](../media/rotate2.png)

Teď budete mít Trezor klíčů, instanci SQL Server a SQL Database. Tuto instalaci můžete ověřit v Azure CLI spuštěním následujícího příkazu:

```azurecli
az resource list -o table
```

Výsledek bude vypadat přibližně na následujícím výstupu:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Vytvoření Function App

Dále můžete kromě dalších požadovaných součástí vytvořit aplikaci funkcí s identitou spravovanou systémem.

Aplikace Function App vyžaduje tyto komponenty:
- Plán Azure App Service
- Účet úložiště
- Zásady přístupu pro přístup k tajným klíčům v Key Vault prostřednictvím spravované identity aplikace Function App

1. Vyberte odkaz nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. V seznamu **Skupina prostředků** vyberte **simplerotation**.
1. Vyberte **Koupit**.

   ![Vybrat nákup](../media/rotate3.png)

Po dokončení předchozích kroků budete mít účet úložiště, serverovou farmu a aplikaci Function App. Tuto instalaci můžete ověřit v Azure CLI spuštěním následujícího příkazu:

```azurecli
az resource list -o table
```

Výsledek bude vypadat přibližně jako následující výstup:

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

Informace o tom, jak vytvořit aplikaci funkcí a použít spravovanou identitu pro přístup k Key Vault, najdete v tématu [Vytvoření aplikace Function App z Azure Portal](../../azure-functions/functions-create-function-app-portal.md) a [poskytnutí Key Vaultho ověřování se spravovanou identitou](../general/managed-identity.md).

### <a name="rotation-function"></a>Funkce rotace
Funkce používá událost k aktivaci rotace tajného kódu aktualizací Key Vault a databáze SQL.

#### <a name="function-trigger-event"></a>Událost triggeru funkce

Tato funkce čte data událostí a spouští logiku otáčení:

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

#### <a name="secret-rotation-logic"></a>Logika rotace tajných klíčů
Tato metoda otáčení čte informace o databázi z tajného kódu, vytvoří novou verzi tajného klíče a aktualizuje databázi pomocí nového tajného klíče:

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
Kompletní kód můžete najít na [GitHubu](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Nasazení funkce

1. Stáhněte si soubor zip aplikace Function App z [GitHubu](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Nahrajte soubor simplerotationsample-FN. zip do Azure Cloud Shell.

   ![Nahrání souboru](../media/rotate4.png)
1. Pomocí tohoto příkazu rozhraní příkazového řádku Azure nasaďte soubor zip do aplikace Function App:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Po nasazení funkce by se v simplerotation-FN měla zobrazit dvě funkce:

![Funkce SimpleRotation a SimpleRotationHttpTest](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Přidat odběr události pro událost SecretNearExpiry

Zkopírujte `eventgrid_extension` klíč aplikace Function App:

   ![Vybrat nastavení aplikace Function App](../media/rotate6.png)

   ![eventgrid_extension klíč](../media/rotate7.png)

Pomocí zkopírovaného `eventgrid_extension` klíče a ID předplatného v následujícím příkazu vytvořte Event Grid předplatné pro `SecretNearExpiry` události:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Přidání tajného klíče do Key Vault
Nastavte zásady přístupu pro udělení oprávnění *Spravovat tajná klíče* uživatelům:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Vytvořte nový tajný klíč pomocí značek, které obsahují zdroj dat databáze SQL a ID uživatele. Zahrňte datum vypršení platnosti, které je nastaveno na zítra.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Při vytváření tajného klíče s krátkým datem vypršení platnosti `SecretNearExpiry` se okamžitě publikuje událost, která zase aktivuje funkci pro otočení tajného klíče.

## <a name="test-and-verify"></a>Testování a ověření
Po několika minutách by se `sqluser` měl tajný klíč automaticky otočit.

Chcete-li ověřit, zda byl tajný klíč otočen, použijte **Key Vault** > **tajných klíčů**:

![Přejít k tajným klíčům](../media/rotate8.png)

Otevřete tajný klíč **sqluser** a zobrazte původní a otočené verze:

![Otevřete tajný klíč sqluser.](../media/rotate9.png)

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

Pokud chcete ověřit přihlašovací údaje SQL, vytvořte webovou aplikaci. Tato webová aplikace získá tajný klíč z Key Vault, extrahuje informace z databáze SQL a přihlašovací údaje z tajného kódu a otestuje připojení k SQL Server.

Webová aplikace vyžaduje tyto komponenty:
- Webová aplikace s identitou spravovanou systémem
- Zásady přístupu pro přístup k tajným klíčům v Key Vault prostřednictvím spravované identity webové aplikace

1. Vyberte odkaz nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Vyberte skupinu prostředků **simplerotation** .
1. Vyberte **Koupit**.

### <a name="deploy-the-web-app"></a>Nasazení webové aplikace

Zdrojový kód webové aplikace můžete najít na [GitHubu](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Pokud chcete nasadit webovou aplikaci, proveďte tyto kroky:

1. Stáhněte si soubor zip aplikace Function App z [GitHubu](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Nahrajte soubor simplerotationsample-App. zip do Azure Cloud Shell.
1. Pomocí tohoto příkazu rozhraní příkazového řádku Azure nasaďte soubor zip do aplikace Function App:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Otevření webové aplikace

Přejít na nasazenou aplikaci a vybrat adresu URL:
 
![Vyberte adresu URL.](../media/rotate10.png)

Po otevření aplikace v prohlížeči se zobrazí **vygenerovaná tajná hodnota** a hodnota je **připojená k databázi** *true*.

## <a name="learn-more"></a>Další informace

- Přehled: [Key Vault monitorování pomocí Azure Event Grid (Preview)](../general/event-grid-overview.md)
- Postupy: [příjem e-mailu, když se změní tajný kód trezoru klíčů](../general/event-grid-logicapps.md)
- [Azure Event Grid schéma událostí pro Azure Key Vault (Preview)](../../event-grid/event-schema-key-vault.md)
