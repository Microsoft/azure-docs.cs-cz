---
title: Připojit do Průzkumníku dat Azure z Azure Databricks s využitím Pythonu
description: Toto téma ukazuje, jak použít knihovnu Pythonu v Azure Databricks pro přístup k datům z Průzkumníku dat Azure pomocí jedné ze dvou metod ověřování.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 1101a89fd4ddb0e020d0bac237e6119b137fa978
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017493"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Připojit do Průzkumníku dat Azure z Azure Databricks s využitím Pythonu

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) je založená na Apache Sparku analytická platforma optimalizovaná s ohledem na platformě Microsoft Azure. Tento článek ukazuje, jak použít knihovnu Pythonu v Azure Databricks pro přístup k datům z Průzkumníku dat Azure. Existuje několik způsobů, jak ověřovat pomocí Průzkumníka dat služby Azure, včetně přihlašovací údaje zařízení a aplikace Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Požadavky

- [Vytvoření clusteru Průzkumník dat Azure a databáze](/azure/data-explorer/create-cluster-database-portal).
- [Vytvoření pracovního prostoru Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). V části **služba Azure Databricks**v **cenová úroveň** rozevíracího seznamu vyberte **Premium**. Tato volba umožňuje používat Azure Databricks tajné kódy ukládat svoje přihlašovací údaje a odkazovat na ně poznámkových bloků a úlohy.

- [Vytvoření clusteru](https://docs.azuredatabricks.net/user-guide/clusters/create.html) v Azure Databricks s následujícími specifikacemi (minimální nastavení potřebných ke spuštění ukázkové poznámkové bloky):

   ![Specifikace pro vytvoření clusteru](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Nainstalujte knihovnu Pythonu ve vašem clusteru Azure Databricks

Chcete-li nainstalovat [knihovna Python](/azure/kusto/api/python/kusto-python-client-library) v clusteru Azure Databricks:

1. Přejděte do pracovního prostoru Azure Databricks a [vytvořit knihovnu](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Nahrát balíček Python PyPI nebo Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Nahrát, instalaci a připojení knihovny ke clusteru Databricks.
   - Zadejte název PyPi: **dat azure kusto**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Připojit do Průzkumníku dat Azure pomocí přihlašovacích údajů zařízení

[Import poznámkového bloku](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) pomocí [dotazu ADX zařízení přihlášení](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) poznámkového bloku. Pak do Průzkumníku dat Azure můžete připojit pomocí vašich přihlašovacích údajů.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Připojení k ADX s použitím aplikace Azure AD

1. Vytvoření aplikace Azure AD podle [zřizování aplikaci Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Udělení přístupu k aplikaci Azure AD ve vaší databázi Průzkumník dat Azure následujícím způsobem:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | Název databáze |
    | ```AAD App ID``` | ID aplikace Azure AD |
    | ```AAD Tenant ID``` | vaše ID tenanta Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Najít ID vašeho tenanta Azure AD

K ověření aplikace, nastavení používá Průzkumník dat Azure vaše ID tenanta Azure AD. K vyhledání ID vašeho tenanta, použijte následující adresu URL. Nahraďte doménou pro *doména*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Pokud je vaše doména například *contoso.com*, je adresa URL [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Vyberte tuto adresu URL pro zobrazení výsledků. První řádek je následujícím způsobem: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Váš tenant ID je `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Store a zabezpečení Azure AD app ID a klíč 

Store a zabezpečení Azure AD app ID a klíč pomocí Azure Databricks [tajných kódů](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) následujícím způsobem:
1. [Nastavení rozhraní příkazového řádku](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Instalace rozhraní příkazového řádku](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Nastavení ověřování](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurace [tajných kódů](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) pomocí následující ukázkové příkazy:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Import poznámkového bloku
[Import poznámkového bloku](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) pomocí [dotazu ADX AAD aplikace](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) Poznámkový blok jupyter připojit do Průzkumníku dat Azure. Hodnoty zástupných symbolů aktualizujte název clusteru, název databáze a ID tenanta Azure AD
