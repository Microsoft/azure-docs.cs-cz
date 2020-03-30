---
title: Šifrování zdroje aplikace v klidovém stavu
description: Šifrujte data aplikací ve Službě Azure Storage a nasaďte je jako soubor balíčku.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 62179e900ace0d6d7b8b1f07e8f0ab685508f991
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408720"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Šifrování v klidovém stavu pomocí klíčů spravovaných zákazníkem

Šifrování dat aplikací aplikace funkce v klidovém stavu vyžaduje účet úložiště Azure a trezor klíčů Azure. Tyto služby se používají při spuštění aplikace z balíčku nasazení.

  - [Azure Storage poskytuje šifrování v klidovém stavu](../storage/common/storage-service-encryption.md). Můžete použít klíče poskytované systémem nebo vlastní klíče spravované zákazníkem. Toto je místo, kde se data vaší aplikace ukládají, když se nespouštějí ve funkční aplikaci v Azure.
  - [Spuštění z balíčku nasazení] ((run-functions-from-deployment-package.md) je funkce nasazení služby App Service. Umožňuje nasadit obsah webu z účtu úložiště Azure pomocí adresy URL sdíleného přístupového podpisu (SAS).
  - [Odkazy na úložiště klíčů](../app-service/app-service-key-vault-references.md) jsou funkcí zabezpečení služby App Service. Umožňuje importovat tajné klíče za běhu jako nastavení aplikace. Pomocí této možnosti můžete zašifrovat adresu URL SAS vašeho účtu úložiště Azure.

## <a name="set-up-encryption-at-rest"></a>Nastavení šifrování v klidovém stavu

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

Nejprve [vytvořte účet Azure Storage](../storage/common/storage-account-create.md) a [šifrujte ho pomocí klíčů spravovaných zákazníky](../storage/common/encryption-customer-managed-keys.md). Po vytvoření účtu úložiště použijte [Průzkumníka úložiště Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) k nahrání souborů balíčků.

Dále použijte Průzkumník a úložiště ke [generování SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Uložte tuto adresu URL SAS, která se později používá k povolení zabezpečeného přístupu k balíčku nasazení za běhu.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Konfigurace spuštění z balíčku z účtu úložiště
  
Jakmile soubor nahrajete do úložiště objektů Blob a budete `WEBSITE_RUN_FROM_PACKAGE` mít adresu URL SAS pro soubor, nastavte nastavení aplikace na adresu URL SAS. Následující příklad to provádí pomocí Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Přidání tohoto nastavení aplikace způsobí restartování aplikace funkce. Po restartování aplikace vyhledejte ji a ujistěte se, že aplikace začala správně používat balíček nasazení. Pokud se aplikace nespustila správně, [přečtěte si příručku Spustit z řešení potíží s balíčkem](run-functions-from-deployment-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Šifrování nastavení aplikace pomocí odkazů trezoru klíčů

Nyní můžete nahradit hodnotu `WEBSITE_RUN_FROM_PACKAGE` nastavení aplikace odkazem trezoru klíčů na adresu URL kódovku kódovku SAS. To udržuje adresu URL SAS zašifrovanou v trezoru klíčů, který poskytuje další vrstvu zabezpečení.

1. K vytvoření [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) instance trezoru klíčů použijte následující příkaz.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Podle [těchto pokynů udělte aplikaci přístup](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) do trezoru klíčů:

1. Pomocí následujícího [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) příkazu přidejte externí adresu URL jako tajný klíč do trezoru klíčů:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Pomocí následujícího [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) příkazu `WEBSITE_RUN_FROM_PACKAGE` vytvořte nastavení aplikace s hodnotou jako odkaz key vaultu na externí adresu URL:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Bude `<secret-version>` ve výstupu předchozího `az keyvault secret set` příkazu.

Aktualizace tohoto nastavení aplikace způsobí restartování aplikace funkce. Po restartování aplikace vyhledejte, zda se aplikace spustila správně pomocí odkazu Trezor klíčů.

## <a name="how-to-rotate-the-access-token"></a>Jak otočit přístupový token

Doporučujeme pravidelně otáčet klíč SAS vašeho účtu úložiště. Chcete-li zajistit, aby aplikace funkce neúmyslně neztratila přístup, musíte také aktualizovat adresu URL SAS v trezoru klíčů.

1. Otočte klíč SAS tak, že přejdete na svůj účet úložiště na webu Azure Portal. V části **Nastavení** > **přístupové klávesy**klepněte na ikonu a otočte klávesu SAS.

1. Zkopírujte novou adresu URL SAS a pomocí následujícího příkazu nastavte aktualizovanou adresu URL SAS v trezoru klíčů:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Aktualizujte odkaz na trezor klíčů v nastavení aplikace na novou tajnou verzi:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Bude `<secret-version>` ve výstupu předchozího `az keyvault secret set` příkazu.

## <a name="how-to-revoke-the-function-apps-data-access"></a>Jak odvolat přístup k datům aplikace funkce

Existují dva způsoby, jak odvolat přístup aplikace funkce k účtu úložiště. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Otočení klíče SAS pro účet úložiště Azure

Pokud je klíč SAS pro účet úložiště otočen, aplikace funkce již nebude mít přístup k účtu úložiště, ale bude nadále fungovat s poslední staženou verzí souboru balíčku. Restartujte aplikaci funkcí, chcete-li vymazat poslední staženou verzi.

### <a name="remove-the-function-apps-access-to-key-vault"></a>Odebrání přístupu aplikace pro funkci do trezoru klíčů

Přístup aplikace funkce k datům webu můžete odvolat zakázáním přístupu aplikace funkce k trezoru klíčů. Chcete-li to provést, odeberte zásady přístupu pro identitu aplikace funkce. Jedná se o stejnou identitu, kterou jste vytvořili dříve při konfiguraci odkazů na trezor klíčů.

## <a name="summary"></a>Souhrn

Soubory aplikace jsou nyní šifrovány v klidovém stavu v účtu úložiště. Když se aplikace funkce spustí, načte adresu URL SAS z trezoru klíčů. Nakonec aplikace funkce načte soubory aplikace z účtu úložiště. 

If you need to revoke the function app's access to your storage account, you can either revoke access to the key vault or rotate the storage account keys, which invalidates the SAS URL.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Existuje nějaký další poplatek za spuštění aplikace funkce z balíčku nasazení?

Jenom náklady spojené s účtem úložiště Azure a všechny příslušné odchozí poplatky.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>Jak spuštění z balíčku nasazení ovlivní mou aplikaci funkcí?

- Spuštění aplikace z balíčku `wwwroot/` nasazení umožňuje jen pro čtení. Aplikace obdrží chybu, když se pokusí o zápis do tohoto adresáře.
- Formáty TAR a GZIP nejsou podporovány.
- Tato funkce není kompatibilní s místní mezipamětí.

## <a name="next-steps"></a>Další kroky

- [Odkazy na trezor klíčů pro službu App Service](../app-service/app-service-key-vault-references.md)
- [Šifrování Azure Storage pro data v klidovém stavu](../storage/common/storage-service-encryption.md)
