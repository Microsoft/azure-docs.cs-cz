---
title: Kurz jednoduchého uživatele/otočení hesla
description: Tento kurz slouží k automatizaci rotace jednoho uživatele nebo hesla.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239368"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Automatizace rotace tajného klíče pro prostředky s ověřováním pomocí jednoho uživatele nebo hesla

I když je nejlepší způsob, jak ověřit služby Azure, je použití [spravované identity](managed-identity.md), ale některé scénáře nezpůsobí. V těchto případech se používají přístupové klíče a tajné kódy. Přístupové klíče a tajné kódy by se měly pravidelně střídat.

V tomto kurzu se dozvíte, jak automatizovat pravidelnou rotaci tajných kódů pro databáze a služby s ověřováním pomocí jednoho uživatele nebo hesla. Konkrétně tento scénář otočí hesla systému SQL Server uložená v trezoru klíčů pomocí funkce aktivované Event Grid oznámení:

![Diagram rotace](./media/rotate1.png)

1. Třicet dní před datem vypršení platnosti tajného kódu Key Vault pro Event Grid publikovat událost blížící se vypršení platnosti.
1. Event Grid zkontroluje odběry událostí a pomocí HTTP POST zavolá koncový bod Function App přihlášený k této události.
1. Aplikace Function App obdrží tajné informace, vygeneruje nové náhodné heslo a vytvoří novou verzi pro tajný klíč s novým heslem v Key Vault.
1. Aplikace Function App aktualizuje SQL pomocí nového hesla.

> [!NOTE]
> V této době může být prodleva mezi 3 a 4 a během této doby tajná v Key Vault by nebyla platná pro ověření v SQL. V případě selhání v některém z kroků Event Grid opakované pokusy na 2 hodiny.

## <a name="setup"></a>Nastavení

## <a name="create-a-key-vault-and-sql-server"></a>Vytvoření trezoru klíčů a SQL serveru

Než začneme, je potřeba vytvořit Key Vault, vytvořit SQL Server a databázi a uložit SQL Server heslo správce do Key Vault.

Tento kurz používá předem vytvořenou šablonu Azure Resource Manager k vytváření komponent. Celý kód můžete najít tady: [Ukázka šablony základního tajného klíče pro otočení](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Klikněte na odkaz nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. V části Skupina prostředků vyberte vytvořit novou a sdělte jí název "simplerotation".
1. Vyberte koupit.

    ![Vytvořit novou skupinu prostředků](./media/rotate2.png)

Po dokončení tohoto postupu budete mít Trezor klíčů, SQL Server a SQL Database. To můžete ověřit v terminálu Azure CLI spuštěním:

```azurecli
az resource list -o table
```

Výsledky budou vypadat nějak takto:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Vytvořit Function App

Vytvořte Function App s identitou spravovanou systémem a dalšími požadovanými součástmi: 

Aplikace Function App vyžaduje následující komponenty a konfiguraci:
- Plán služby App Service
- Účet úložiště
- Zásady přístupu pro přístup k tajným klíčům v Key Vault pomocí Function App spravované identity

1. Klikněte na odkaz nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. V části Skupina prostředků vyberte simplerotation.
1. Vyberte koupit.

   ![Koupit obrazovku](./media/rotate3.png)

Po dokončení výše uvedeného postupu budete mít účet úložiště, serverovou farmu a Function App.  To můžete ověřit v terminálu Azure CLI spuštěním:

```azurecli
az resource list -o table
```

Výsledky budou vypadat nějak takto:

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

Informace o tom, jak vytvořit Function App a používat spravovanou identitu pro přístup k Key Vault, najdete v tématu [Vytvoření aplikace Function App z Azure Portal](../azure-functions/functions-create-function-app-portal.md) a [poskytování Key Vault ověřování se spravovanou identitou](managed-identity.md) .

### <a name="rotation-function-and-deployment"></a>Funkce rotace a nasazení
Funkce používá událost jako Trigger a provádí otočení tajné aktualizace Key Vault a SQL Database.

#### <a name="function-event-trigger-handler"></a>Obslužná rutina triggeru události funkce

Níže uvedená funkce čte data události a provádí logiku rotace.

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
Tato metoda otáčení čte informace o databázi z tajného kódu, vytvoří novou verzi tajného klíče a aktualizuje databázi pomocí nového tajného klíče.

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
Celý kód můžete najít tady: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Nasazení funkce

1. Stáhnout soubor zip aplikace Function App: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Nahrání souboru simplerotationsample-FN. zip do Azure Cloud Shell.
 
1. K nasazení souboru zip do aplikace Function App použijte příkaz CLI:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Koupit obrazovku](./media/rotate4.png)

Po nasazení byste měli v simplerotation-FN všimnout dvou funkcí:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Přidat odběr události pro událost SecretNearExpiry

Zkopírujte klíč aplikace Function App eventgrid_extension.

![Azure Cloud Shell](./media/rotate6.png)

![Testování a ověření](./media/rotate7.png)

Pomocí zkopírovaného klíče rozšíření eventgrid a ID vašeho předplatného v níže uvedeném příkazu vytvořte odběr Event gridu pro události SecretNearExpiry.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Přidání tajného klíče do Key Vault
Nastavte zásady přístupu tak, aby uživatelům poskytovala oprávnění "spravovat tajné klíče".

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Nyní vytvořte nový tajný klíč pomocí značek, které obsahují zdroj dat SQL Database a ID uživatele, přičemž datum vypršení platnosti je nastaveno na zítra.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Vytvoření tajného klíče s krátkým datem vypršení platnosti bude okamžitě publikovat událost SecretNearExpiry, která zase aktivuje funkci pro otočení tajného klíče.

### <a name="test-and-verify"></a>Testování a ověření
Po několika minutách by se měl sqluser tajný klíč automaticky otočit.

Pokud chcete ověřit procházení tajných klíčů, navštivte Key Vault > tajné kódy.

![Testování a ověření](./media/rotate8.png)

Otevřete tajný klíč "sqluser" a zobrazte původní a otočenou verzi.

![Testování a ověření](./media/rotate9.png)

## <a name="create-web-app"></a>Vytvoření webové aplikace

K ověření přihlašovacích údajů SQL vytvořte webovou aplikaci. Tato webová aplikace získá tajný klíč z trezoru klíčů, extrahuje informace z databáze SQL a přihlašovací údaje z tajného kódu a otestuje připojení k SQL.

Webová aplikace vyžaduje tyto komponenty a konfiguraci:
- Webová aplikace s identitou spravovanou systémem
- Zásady přístupu pro přístup k tajným klíčům v Key Vault pomocí spravované identity webové aplikace

1. Klikněte na odkaz nasazení šablony Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Vyberte skupinu prostředků **simplerotation** .
1. Kliknout na koupit

### <a name="deploy-web-app"></a>Nasadit webovou aplikaci

Zdrojový kód webové aplikace, který najdete https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp pro nasazení webové aplikace, udělejte toto:

1. Stáhněte si soubor zip aplikace Function App z https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Nahrajte soubor `simplerotationsample-app.zip` do Azure Cloud Shell.
1. Pomocí tohoto příkazu rozhraní příkazového řádku Azure nasaďte soubor zip do aplikace Function App:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Otevřít webovou aplikaci

Přejděte do nasazené aplikace a klikněte na "URL":
 
![Testování a ověření](./media/rotate10.png)

Vygenerovaná tajná hodnota by měla být zobrazena s databází připojenou jako true.

## <a name="learn-more"></a>Další informace:

- Přehled: [Key Vault monitorování pomocí Azure Event Grid (Preview)](event-grid-overview.md)
- Postupy: [příjem e-mailu, když se změní tajný kód trezoru klíčů](event-grid-logicapps.md)
- [Azure Event Grid schéma událostí pro Azure Key Vault (Preview)](../event-grid/event-schema-key-vault.md)
