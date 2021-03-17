---
title: Šifrování zdroje aplikace v klidovém umístění
description: Zašifrujte data aplikace v Azure Storage a nasaďte je jako soubor balíčku.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 6e4cf16118e748e3ee1d1ff4899730463565ec70
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108027"
---
# <a name="encrypt-your-application-data-at-rest-using-customer-managed-keys"></a>Šifrování dat vaší aplikace v klidovém formátu pomocí klíčů spravovaných zákazníkem

Šifrování dat aplikace Function App v klidovém prostředí vyžaduje účet Azure Storage a Azure Key Vault. Tyto služby se používají při spuštění aplikace z balíčku pro nasazení.

  - [Azure Storage poskytuje šifrování v klidovém umístění](../storage/common/storage-service-encryption.md). Můžete použít klíče poskytované systémem nebo vlastní klíče spravované zákazníkem. V takovém případě se data vaší aplikace ukládají, když nejsou spuštěná v aplikaci Function App v Azure.
  - [Spuštění z balíčku pro nasazení](run-functions-from-deployment-package.md) je funkce nasazení App Service. Umožňuje nasadit obsah webu z Azure Storage účtu pomocí adresy URL sdíleného přístupového podpisu (SAS).
  - [Odkazy na Key Vault](../app-service/app-service-key-vault-references.md) jsou funkcí zabezpečení App Service. Umožňuje import tajných kódů za běhu jako nastavení aplikace. Použijte k zašifrování adresy URL SAS vašeho účtu Azure Storage.

## <a name="set-up-encryption-at-rest"></a>Nastavení šifrování v klidovém umístění

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu Azure Storage

Nejdřív [vytvořte účet Azure Storage](../storage/common/storage-account-create.md) a [Zašifrujte ho pomocí zákaznických spravovaných klíčů](../storage/common/customer-managed-keys-overview.md). Po vytvoření účtu úložiště použijte [Průzkumník služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) k nahrání souborů balíčku.

Dále pomocí Průzkumník služby Storage [vygenerujte SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Uložte tuto adresu URL SAS, použije se později k povolení zabezpečeného přístupu balíčku pro nasazení za běhu.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Konfigurace spuštění z balíčku z účtu úložiště
  
Po nahrání souboru do úložiště objektů BLOB a zadání adresy URL SAS pro tento soubor nastavte `WEBSITE_RUN_FROM_PACKAGE` nastavení aplikace na adresu URL SAS. V následujícím příkladu se používá Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Přidáním tohoto nastavení aplikace dojde k restartování aplikace Function App. Po restartování aplikace ji vyhledejte a ujistěte se, že je aplikace správně spuštěná pomocí balíčku pro nasazení. Pokud se aplikace nespustila správně, přečtěte si [Průvodce řešením potíží se spuštěním z balíčku](run-functions-from-deployment-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Šifrování nastavení aplikace pomocí Key Vaultch odkazů

Nyní můžete nahradit hodnotu `WEBSITE_RUN_FROM_PACKAGE` nastavení aplikace Key Vault odkazem na adresu URL kódovanou pomocí SAS. Tím se zachovává adresa URL SAS zašifrovaná v Key Vault, která poskytuje další vrstvu zabezpečení.

1. [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create)K vytvoření instance Key Vault použijte následující příkaz.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Podle [těchto pokynů udělte aplikaci přístup](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) k vašemu trezoru klíčů:

1. Pomocí následujícího [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) příkazu přidejte svoji externí adresu URL jako tajný klíč do trezoru klíčů:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Pomocí následujícího [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) příkazu vytvořte `WEBSITE_RUN_FROM_PACKAGE` nastavení aplikace s hodnotou jako Key Vault odkaz na externí adresu URL:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`Bude ve výstupu předchozího `az keyvault secret set` příkazu.

Aktualizace nastavení této aplikace způsobí, že se aplikace Function App restartuje. Až se aplikace restartuje, přejděte k ní a ujistěte se, že se správně spustila pomocí odkazu Key Vault.

## <a name="how-to-rotate-the-access-token"></a>Postup otočení přístupového tokenu

Osvědčeným postupem je pravidelně střídat klíč SAS účtu úložiště. Pokud chcete zajistit, aby aplikace Function App nechtěně nevedla přístup, musíte taky aktualizovat adresu URL SAS v Key Vault.

1. Otočte klíč SAS tak, že přejdete na svůj účet úložiště v Azure Portal. V části **Nastavení**  >  **přístupových klíčů**klikněte na ikonu pro otočení klíče SAS.

1. Zkopírujte adresu URL nového SAS a použijte následující příkaz k nastavení aktualizované adresy URL SAS ve vašem trezoru klíčů:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Aktualizujte odkaz na Trezor klíčů v nastavení aplikace na novou tajnou verzi:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`Bude ve výstupu předchozího `az keyvault secret set` příkazu.

## <a name="how-to-revoke-the-function-apps-data-access"></a>Jak odvolat přístup k datům aplikace Function App

Existují dvě metody, jak odvolat přístup k účtu úložiště aplikace Function App. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Otočit klíč SAS pro Azure Storage účet

Pokud se klíč SAS pro účet úložiště otočí, aplikace Function App už nebude mít přístup k účtu úložiště, ale bude i nadále běžet s poslední staženou verzí souboru balíčku. Restartováním aplikace Function App vymažte poslední staženou verzi.

### <a name="remove-the-function-apps-access-to-key-vault"></a>Odeberte přístup aplikace Function App k Key Vault

Přístup aplikace Function App k datům webu můžete odvolat tím, že zakážete přístup aplikace Function App k Key Vault. Uděláte to tak, že odeberete zásady přístupu pro identitu aplikace Function App. Jedná se o stejnou identitu, kterou jste vytvořili dříve při konfiguraci odkazů na Trezor klíčů.

## <a name="summary"></a>Shrnutí

Soubory vaší aplikace jsou teď ve vašem účtu úložiště zašifrované v klidovém stavu. Po spuštění aplikace Function App načte adresu URL SAS z vašeho trezoru klíčů. Nakonec aplikace Function App načte soubory aplikace z účtu úložiště. 

Pokud potřebujete odvolat přístup aplikace Function App k vašemu účtu úložiště, můžete buď odvolat přístup k trezoru klíčů, nebo otočit klíče účtu úložiště, který zruší platnost adresy URL SAS.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Platí se za spuštění aplikace Function App z balíčku pro nasazení nějaké další poplatky?

Účtují se jenom náklady spojené s účtem Azure Storage a příslušnými poplatky za výstup.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>Vliv fungování z balíčku pro nasazení na aplikaci Function App?

- Spuštění aplikace z balíčku pro nasazení zpřístupňuje jen `wwwroot/` pro čtení. Vaše aplikace při pokusu o zápis do tohoto adresáře obdrží chybu.
- Formáty TAR a GZIP se nepodporují.
- Tato funkce není kompatibilní s místní mezipamětí.

## <a name="next-steps"></a>Další kroky

- [Key Vault odkazy na App Service](../app-service/app-service-key-vault-references.md)
- [Šifrování služby Azure Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md)