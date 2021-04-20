---
title: Kurz rotace pro prostředky s jednou sadou ověřovacích přihlašovacích údajů uložených v Azure Key Vault
description: V tomto kurzu se dozvíte, jak automatizovat rotaci tajného klíče pro prostředky, které používají jednu sadu přihlašovacích údajů pro ověřování.
services: key-vault
author: msmbaldwin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: e66be3b0e3ecae5caa1a76294cc8b8dc11a5f207
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748659"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatizace rotace tajného klíče pro prostředky, které používají jednu sadu ověřovacích přihlašovacích údajů

Nejlepším způsobem, jak ověřit služby Azure, je použití [spravované identity](../general/authentication.md), ale v některých případech se nejedná o možnost. V těchto případech se používají přístupové klíče a tajné kódy. Přístupové klíče a tajné kódy byste měli pravidelně střídat.

V tomto kurzu se dozvíte, jak automatizovat pravidelnou rotaci tajných kódů pro databáze a služby, které používají jednu sadu ověřovacích přihlašovacích údajů. Konkrétně tento kurz otočí SQL Server hesla uložená v Azure Key Vault pomocí funkce aktivované Azure Event Gridm oznámením:


:::image type="content" source="../media/rotate-1.png" alt-text="Diagram řešení rotace":::

1. Třicet dní před datem vypršení platnosti tajného kódu Key Vault pro Event Grid publikovat událost blížící se vypršení platnosti.
1. Event Grid zkontroluje odběry událostí a pomocí HTTP POST zavolá koncový bod aplikace Function App, který se přihlásí k odběru události.
1. Aplikace Function App obdrží tajné informace, vygeneruje nové náhodné heslo a vytvoří novou verzi pro tajný klíč s novým heslem v Key Vault.
1. Aplikace Function App SQL Server aktualizuje pomocí nového hesla.

> [!NOTE]
> Mezi kroky 3 a 4 by mohlo dojít k prodlevě. Během této doby se tajný kód v Key Vault nebude moci ověřit pro SQL Server. V případě selhání některého z kroků Event Grid opakování po dvou hodinách.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault
* SQL Server

Pokud nemáte existující Key Vault a SQL Server, můžete použít odkaz pod nasazením:

[![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. V části **Skupina prostředků** vyberte **vytvořit novou**. Zadejte název skupiny. v tomto kurzu použijeme **akvrotation** .
1. V části **přihlášení správce SQL** zadejte přihlašovací jméno správce SQL. 
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

:::image type="content" source="../media/rotate-2.png" alt-text="Vytvoření skupiny prostředků":::

Nyní budete mít Key Vault a instanci SQL Server. Tuto instalaci můžete ověřit v Azure CLI spuštěním následujícího příkazu:

```azurecli
az resource list -o table -g akvrotation
```

Výsledek bude vypadat přibližně na následujícím výstupu:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Vytvoření a nasazení funkce rotace hesla systému SQL Server
> [!IMPORTANT]
> Pod šablonou se vyžaduje Key Vault, SQL Server a funkce Azure Functions ve stejné skupině prostředků.

Dále vytvořte aplikaci funkcí s identitou spravovanou systémem, kromě dalších požadovaných součástí a nasaďte funkce pro otočení hesla SQL serveru.

Aplikace Function App vyžaduje tyto komponenty:
- Plán Azure App Service
- Function App s funkcemi rotace hesla SQL s triggerem událostí a triggerem protokolu http 
- Účet úložiště vyžadovaný pro správu triggeru aplikace Function App
- Zásady přístupu pro Function App identity pro přístup k tajným klíčům v Key Vault
- Odběr události EventGrid pro událost **SecretNearExpiry**

1. Vyberte odkaz nasazení šablony Azure: 

   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. V seznamu **Skupina prostředků** vyberte **akvrotation**.
1. Do pole **název SQL serveru** zadejte název SQL serveru s heslem pro otočení.
1. Do **Key Vault název** zadejte název trezoru klíčů.
1. Do **Function App název** zadejte název aplikace Function App.
1. Do **název tajného klíče** zadejte tajný název, kde bude uloženo heslo.
1. Do pole **Adresa URL úložiště** zadejte umístění GitHubu pro kód funkce umístění ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git** ).
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

:::image type="content" source="../media/rotate-3.png" alt-text="Vybrat kontrolu + vytvořit":::
  

Po dokončení předchozích kroků budete mít účet úložiště, serverovou farmu a aplikaci Function App. Tuto instalaci můžete ověřit v Azure CLI spuštěním následujícího příkazu:

```azurecli
az resource list -o table -g akvrotation
```

Výsledek bude vypadat přibližně jako následující výstup:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

Informace o tom, jak vytvořit aplikaci funkcí a použít spravovanou identitu pro přístup k Key Vault, najdete v tématu [Vytvoření aplikace Function App z Azure Portal](../../azure-functions/functions-create-function-app-portal.md), [použití spravované identity pro App Service a Azure Functions](../../app-service/overview-managed-identity.md)a [přiřazení zásad Key Vault přístupu pomocí Azure Portal](../general/assign-access-policy-portal.md).

### <a name="rotation-function"></a>Funkce rotace
Funkce nasazená v předchozím kroku používá událost k aktivaci rotace tajného kódu aktualizací Key Vault a SQL Database. 

#### <a name="function-trigger-event"></a>Událost triggeru funkce

Tato funkce čte data událostí a spouští logiku otáčení:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Logika rotace tajných klíčů
Tato metoda otáčení čte informace o databázi z tajného kódu, vytvoří novou verzi tajného klíče a aktualizuje databázi pomocí nového tajného klíče:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
Kompletní kód můžete najít na [GitHubu](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp).

## <a name="add-the-secret-to-key-vault"></a>Přidání tajného klíče do Key Vault
Nastavte zásady přístupu pro udělení oprávnění *Spravovat tajná klíče* uživatelům:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Vytvořte nový tajný klíč pomocí značek, které obsahují ID prostředku SQL Server, SQL Server přihlašovací jméno a dobu platnosti tajného klíče ve dnech. Zadejte název tajného klíče, počáteční heslo z databáze SQL (v našem příkladu "Simple123") a zahrňte datum vypršení platnosti, které je nastavené pro zítřejší.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

Vytvoření tajného klíče s krátkým datem vypršení platnosti způsobí publikování `SecretNearExpiry` události během 15 minut, což zase aktivuje funkci pro otočení tajného klíče.

## <a name="test-and-verify"></a>Testování a ověření

Chcete-li ověřit, zda byl tajný klíč otočen, použijte **Key Vault**  >  **tajných klíčů**:

:::image type="content" source="../media/rotate-8.png" alt-text="Snímek obrazovky, který ukazuje, jak získat přístup k Key Vault > tajných kódů.":::

Otevřete tajný klíč **sqlPassword** a zobrazte původní a otočené verze:

:::image type="content" source="../media/rotate-9.png" alt-text="Přejít k tajným klíčům":::

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

Pokud chcete ověřit přihlašovací údaje SQL, vytvořte webovou aplikaci. Tato webová aplikace získá tajný klíč z Key Vault, extrahuje informace z databáze SQL a přihlašovací údaje z tajného kódu a otestuje připojení k SQL Server.

Webová aplikace vyžaduje tyto komponenty:
- Webová aplikace s identitou spravovanou systémem
- Zásady přístupu pro přístup k tajným klíčům v Key Vault prostřednictvím spravované identity webové aplikace

1. Vyberte odkaz nasazení šablony Azure: 

   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. Vyberte skupinu prostředků **akvrotation** .
1. Do pole **název SQL serveru** zadejte název SQL serveru s heslem pro otočení.
1. Do **Key Vault název** zadejte název trezoru klíčů.
1. Do **název tajného klíče** zadejte tajný název, kde je uložené heslo.
1. Do pole **Adresa URL úložiště** zadejte umístění GitHubu pro kód webové aplikace ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** ).
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.


### <a name="open-the-web-app"></a>Otevření webové aplikace

Přejít na adresu URL nasazené aplikace:
 
'https://akvrotation-app.azurewebsites.net/'

Po otevření aplikace v prohlížeči se zobrazí **vygenerovaná tajná hodnota** a hodnota je **připojená k databázi** *true*.

## <a name="learn-more"></a>Další informace

- Kurz: [rotace pro prostředky se dvěma sadami přihlašovacích údajů](tutorial-rotation-dual.md)
- Přehled: [monitorování Key Vault s](../general/event-grid-overview.md) využitím Azure Event Grid
- Postupy: [příjem e-mailu, když se změní tajný kód trezoru klíčů](../general/event-grid-logicapps.md)
- [Azure Event Grid schéma událostí pro Azure Key Vault](../../event-grid/event-schema-key-vault.md)
