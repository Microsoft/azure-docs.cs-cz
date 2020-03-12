---
title: Spuštění Azure Functions z balíčku
description: Modul runtime Azure Functions spouští vaše funkce připojením souboru balíčku pro nasazení, který obsahuje soubory projektu Function App.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: 3ae287939f22469b03f0e10f184f067274464905
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087029"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Spuštění Azure Functions ze souboru balíčku

V Azure můžete spouštět funkce přímo ze souboru balíčku pro nasazení ve vaší aplikaci Function App. Druhou možností je nasadit soubory do adresáře `d:\home\site\wwwroot` aplikace Function App.

Tento článek popisuje výhody spouštění funkcí z balíčku. Také ukazuje, jak povolit tuto funkci ve vaší aplikaci Function App.

> [!IMPORTANT]
> Při nasazení vašich funkcí do aplikace Functions pro Linux v [plánu Premium](functions-scale.md#premium-plan)byste měli vždy spustit ze souboru balíčku a [publikovat aplikaci pomocí Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Výhody spouštění ze souboru balíčku
  
Existuje několik výhod, které je potřeba spustit ze souboru balíčku:

+ Snižuje riziko problémů se zámkem při kopírování souborů.
+ Dá se nasadit do produkční aplikace (s restartováním).
+ Můžete si být jisti soubory, které jsou spuštěny ve vaší aplikaci.
+ Zlepšuje výkon [Azure Resource Manager nasazení](functions-infrastructure-as-code.md).
+ Může snížit dobu studených časů, zejména pro funkce JavaScriptu s velkými stromy balíčků npm.

Další informace najdete v [tomto oznámení](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Povolení spouštění funkcí z balíčku

Pokud chcete povolit spouštění aplikace Function App z balíčku, stačí přidat nastavení `WEBSITE_RUN_FROM_PACKAGE` do nastavení aplikace Function App. Nastavení `WEBSITE_RUN_FROM_PACKAGE` může mít jednu z následujících hodnot:

| Hodnota  | Popis  |
|---------|---------|
| **`1`**  | Doporučuje se pro aplikace Function App běžící v systému Windows. Spusťte ze souboru balíčku ve složce `d:\home\data\SitePackages` vaší aplikace Function App. Pokud nedojde k [nasazení pomocí nástroje zip Deploy](#integration-with-zip-deployment), tato možnost vyžaduje, aby složka měla také soubor s názvem `packagename.txt`. Tento soubor obsahuje pouze název souboru balíčku ve složce bez prázdných znaků. |
|**`<URL>`**  | Umístění konkrétního souboru balíčku, který chcete spustit. Pokud používáte úložiště objektů blob, měli byste použít privátní kontejner se [sdíleným přístupovým podpisem (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) , aby modul runtime Functions mohl přistupovat k balíčku. Pomocí [Průzkumník služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) můžete odeslat soubory balíčku do svého účtu BLOB Storage. Když zadáte adresu URL, musíte [triggery synchronizovat](functions-deployment-technologies.md#trigger-syncing) i po publikování aktualizovaného balíčku. |

> [!CAUTION]
> Při spuštění aplikace Function App ve Windows znamená možnost externí adresy URL horší výkon při studeném startu. Při nasazování aplikace Function App do systému Windows byste měli nastavit `WEBSITE_RUN_FROM_PACKAGE` pro `1` a publikování s nasazením zip.

V následujícím příkladu je aplikace Function App nakonfigurovaná tak, aby běžela ze souboru. zip hostovaného ve službě Azure Blob Storage:

![Nastavení aplikace WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> V současné době jsou podporovány pouze soubory balíčku. zip.

## <a name="integration-with-zip-deployment"></a>Integrace s nasazením zip

[Nasazení zip][Zip deployment for Azure Functions] je funkce Azure App Service, která umožňuje nasadit projekt Function App do adresáře `wwwroot`. Projekt je zabalen jako soubor nasazení. zip. Stejná rozhraní API lze použít k nasazení balíčku do složky `d:\home\data\SitePackages`. Když `WEBSITE_RUN_FROM_PACKAGE` nastavení aplikace `1`, rozhraní API pro nasazení zip zkopírují balíček do složky `d:\home\data\SitePackages` místo extrakce souborů do `d:\home\site\wwwroot`. Vytvoří také soubor `packagename.txt`. Po restartování je balíček připojený k `wwwroot` jako systém souborů jen pro čtení. Další informace o nasazení zip najdete v tématu [nasazení zip pro Azure Functions](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Přidání nastavení WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="use-key-vault-references"></a>Použití Key Vaultch odkazů

Pro zvýšení zabezpečení můžete použít Key Vault odkazy ve spojení s vaší externí adresou URL. Tím se zachovává zašifrovaná adresa URL v klidovém stavu a umožňuje využít Key Vault pro správu a rotaci tajných kódů. Doporučuje se používat úložiště objektů BLOB v Azure, abyste mohli snadno otočit související klíč SAS. Úložiště objektů BLOB v Azure je v klidovém stavu zašifrované, což zajistí zabezpečení dat aplikace, když není nasazená na App Service.

1. Vytvořte Azure Key Vault.

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. Přidejte svoji externí adresu URL jako tajný kód do Key Vault.

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. Vytvořte nastavení aplikace `WEBSITE_RUN_FROM_PACKAGE` a nastavte hodnotu jako Key Vault odkaz na externí adresu URL.

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

Další informace najdete v následujících článcích.

- [Key Vault odkazy na App Service](../app-service/app-service-key-vault-references.md)
- [Azure Storage šifrování dat v klidovém umístění](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Řešení potíží

- Příkaz spustit z balíčku zpřístupní `wwwroot` jen pro čtení, takže při zápisu souborů do tohoto adresáře se zobrazí chyba.
- Formáty tar a gzip se nepodporují.
- Tato funkce nevytváří místní mezipaměť.
- Pro zlepšení výkonu pro studený start použijte místní možnost zip (`WEBSITE_RUN_FROM_PACKAGE`= 1).
- Příkaz spustit z balíčku není kompatibilní s možností přizpůsobení nasazení (`SCM_DO_BUILD_DURING_DEPLOYMENT=true`), krok sestavení bude během nasazení ignorován.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
