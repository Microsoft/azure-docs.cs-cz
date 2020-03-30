---
title: Připojení k Azure Data Exploreru z Azure Databricks pomocí Pythonu
description: Toto téma ukazuje, jak používat knihovnu Pythonu v Azure Databricks pro přístup k datům z Průzkumníka dat Azure pomocí jedné ze dvou metod ověřování.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76985675"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Připojení k Azure Data Exploreru z Azure Databricks pomocí Pythonu

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) je analytická platforma založená na Apache Spark, která je optimalizovaná pro platformu Microsoft Azure. Tento článek ukazuje, jak používat knihovnu Pythonu v Azure Databricks pro přístup k datům z Azure Data Exploreru. Existuje několik způsobů, jak ověřit pomocí Azure Data Explorer, včetně přihlášení zařízení a Azure Active Directory (Azure AD) aplikace.

## <a name="prerequisites"></a>Požadavky

- [Vytvořte cluster a databázi Průzkumníka dat Azure](/azure/data-explorer/create-cluster-database-portal).
- [Vytvořte pracovní prostor Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). V části **Azure Databricks Service**vyberte v rozevíracím seznamu **Cenová úroveň** možnost **Premium**. Tento výběr umožňuje používat tajné kódy Azure Databricks k ukládání přihlašovacích údajů a odkazování na ně v poznámkových blocích a úlohách.

- [Vytvořte cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) v Azure Databricks s následujícími specifikacemi (minimální nastavení potřebná ke spuštění ukázkových poznámkových bloků):

   ![Specifikace pro vytvoření clusteru](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Instalace knihovny Pythonu do clusteru Azure Databricks

Instalace [knihovny Pythonu](/azure/kusto/api/python/kusto-python-client-library) do clusteru Azure Databricks:

1. Přejděte do pracovního prostoru Azure Databricks a [vytvořte knihovnu](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Nahrajte balíček Python PyPI nebo Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Nahrajte, nainstalujte a připojte knihovnu ke clusteru Databricks.
   - Zadejte název PyPi: **azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Připojení k Azure Data Exploreru pomocí přihlášení k zařízení

[Importujte poznámkový blok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) pomocí poznámkového bloku [přihlášení k zařízení Query-ADX.](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) Potom se můžete připojit k Průzkumníku dat Azure pomocí svých přihlašovacích údajů.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Připojení k ADX pomocí aplikace Azure AD

1. Vytvořte aplikaci Azure AD [zřízením aplikace Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Udělte přístup k aplikaci Azure AD v databázi Azure Data Exploreru následujícím způsobem:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | název databáze |
    | ```AAD App ID``` | ID aplikace Azure AD |
    | ```AAD Tenant ID``` | ID klienta Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Vyhledání ID klienta Azure AD

K ověření aplikace používá Azure Data Explorer id vašeho klienta Azure AD. Pokud chcete najít ID klienta, použijte následující adresu URL. Nahraďte doménu *yourdomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Pokud je vaše doména například *contoso.com*, je adresa URL [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Výběrem této adresy URL zobrazíte výsledky. První řádek je následující: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

ID vašeho `6babcaad-604b-40ac-a9d7-9fd97c0b779f`klienta je . 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Ukládání a zabezpečení ID a klíče aplikace Azure AD 

Uložte a zabezpečte ID a klíč aplikace Azure AD pomocí [tajných kódů](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) Azure Databricks následujícím způsobem:
1. [Nastavte cli](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Nainstalujte cli](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Nastavte ověřování](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Nakonfigurujte [tajné klíče](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) pomocí následujících ukázkových příkazů:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Import poznámkového bloku
[Importujte poznámkový blok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) pomocí poznámkového bloku [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) pro připojení k Průzkumníku dat Azure. Aktualizujte zástupné hodnoty s názvem clusteru, názvem databáze a ID klienta Azure AD.
