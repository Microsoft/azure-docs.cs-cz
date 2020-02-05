---
title: Připojení k Azure Průzkumník dat z Azure Databricks pomocí Pythonu
description: V tomto tématu se dozvíte, jak používat knihovnu Pythonu v Azure Databricks k přístupu k datům z Azure Průzkumník dat pomocí jedné ze dvou metod ověřování.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985675"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Připojení k Azure Průzkumník dat z Azure Databricks pomocí Pythonu

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) je analytická platforma založená na Apache Spark, která je optimalizovaná pro Microsoft Azure platformu. V tomto článku se dozvíte, jak používat knihovnu Pythonu v Azure Databricks k přístupu k datům z Azure Průzkumník dat. Existuje několik způsobů, jak ověřit pomocí Průzkumník dat Azure, včetně přihlášení zařízení a aplikace Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Požadavky

- [Vytvořte cluster a databázi Azure Průzkumník dat](/azure/data-explorer/create-cluster-database-portal).
- [Vytvořte pracovní prostor Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). V části **Azure Databricks služba**vyberte v rozevíracím seznamu **cenová úroveň** možnost **Premium**. Tento výběr umožňuje použít Azure Databricks tajných klíčů k ukládání vašich přihlašovacích údajů a jejich odkazování v poznámkových blocích a úlohách.

- V Azure Databricks [vytvořte cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) s následujícími specifikacemi (minimální nastavení potřebná ke spuštění ukázkových poznámkových bloků):

   ![Specifikace pro vytvoření clusteru](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Instalace knihovny Pythonu na Azure Databricks clusteru

Postup instalace [knihovny Pythonu](/azure/kusto/api/python/kusto-python-client-library) na Azure Databricks clusteru:

1. Přejít do svého pracovního prostoru Azure Databricks a [vytvořit knihovnu](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Nahrajte balíček python PyPI nebo vaječný Python](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Nahrajte, nainstalujte a připojte knihovnu k vašemu clusteru datacihly.
   - Zadejte název PyPi: **Azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Připojení k Azure Průzkumník dat pomocí přihlášení zařízení

[Naimportujte Poznámkový](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) blok pomocí poznámkového bloku [Query-ADX-Device-Login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) . Pak se můžete k Azure Průzkumník dat připojit pomocí vašich přihlašovacích údajů.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Připojení k ADX pomocí aplikace Azure AD

1. Vytvořte aplikaci Azure AD tím, že [zřídíte aplikaci Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Udělte přístup k vaší aplikaci Azure AD ve službě Azure Průzkumník dat Database následujícím způsobem:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | název databáze |
    | ```AAD App ID``` | vaše ID aplikace Azure AD |
    | ```AAD Tenant ID``` | ID tenanta Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Najít ID tenanta Azure AD

K ověření aplikace používá Azure Průzkumník dat vaše ID tenanta Azure AD. Pokud chcete zjistit ID tenanta, použijte následující adresu URL. Nahraďte doménu pro *yourdomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Pokud je vaše doména například *contoso.com*, je adresa URL [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Výběrem této adresy URL zobrazíte výsledky. První řádek je následující: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Vaše ID tenanta je `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Uložení a zabezpečení ID a klíče aplikace Azure AD 

Pomocí Azure Databricks [tajných klíčů](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) si uložte a zabezpečte své ID a klíč aplikace Azure AD následujícím způsobem:
1. [Nastavte rozhraní](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)příkazového řádku.
1. [Nainstalujte rozhraní](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)příkazového řádku. 
1. [Nastavte ověřování](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurujte [tajné klíče](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) pomocí následujících ukázkových příkazů:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importovat Poznámkový blok
[Naimportujte Poznámkový blok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) pomocí poznámkového bloku [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) , abyste se připojili k Azure Průzkumník dat. Zástupné hodnoty aktualizujte pomocí názvu clusteru, názvu databáze a ID tenanta Azure AD.
