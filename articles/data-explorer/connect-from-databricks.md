---
title: Připojit do Průzkumníku dat Azure z Azure Databricks pomocí Pythonu
description: Toto téma se ukazují, jak použít knihovnu Pythonu v Azure Databricks pro přístup k datům z Azure Data Explorer (ADX) pomocí jedné ze dvou metod ověřování.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 53f51db8d1b495f9a6faec86450d2b4e08a4fb72
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428519"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-using-python"></a>Připojit do Průzkumníku dat Azure z Azure Databricks pomocí Pythonu

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) je analytická platforma založená na Apache Spark a optimalizovaná pro platformu cloudových služeb Microsoft Azure. V tomto článku se dozvíte, jak používat knihovnu Pythonu v Azure Databricks pro přístup k datům z Azure Data Explorer (ADX). Existuje několik způsobů, aby ověřoval ADX včetně přihlášení na zařízení a aplikace Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Požadavky

- [Vytvoření clusteru Průzkumník dat Azure a databáze](/azure/data-explorer/create-cluster-database-portal)
- [Vytvoření pracovního prostoru Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)

    V části **služba Azure Databricks**v **cenová úroveň** rozevíracího seznamu vyberte **Premium**. To umožňuje použití tajných kódů Azure Databricks k ukládání svých přihlašovacích údajů a na ně odkazovat v poznámkových bloků a úlohy.

- [Vytvoření clusteru](https://docs.azuredatabricks.net/user-guide/clusters/create.html) v Azure Databricks s následujícími specifikacemi (minimální nastavení potřebných ke spuštění ukázkové poznámkové bloky):

![Vytvoření clusteru](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-python-library-on-your-azure-databricks-cluster"></a>Nainstalujte knihovnu Pythonu ve vašem clusteru Azure Databricks

Chcete-li nainstalovat [knihovna Python](/azure/kusto/api/python/kusto-python-client-library) v clusteru Azure Databricks:

1. Přejděte do pracovního prostoru Azure Databricks a [vytvořit knihovnu](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)
2. [Nahrát balíček Python PyPI nebo Egg Pythonu](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)
    - Nahrát, instalaci a připojení knihovny ke clusteru Databricks.
    - Zadejte název PyPi: *dat azure kusto*

## <a name="connect-to-adx-using-device-login"></a>Připojte se k ADX pomocí přihlášení na zařízení

[Import poznámkového bloku](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) pomocí [dotazu ADX zařízení přihlášení](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) poznámkového bloku pro připojení k ADX pomocí svých přihlašovacích údajů.

## <a name="connect-to-adx-using-azure-ad-app"></a>Připojte se k ADX pomocí aplikace Azure AD

1. Vytvoření aplikace Azure AD podle [zřizování aplikaci AAD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Udělení přístupu k aplikaci Azure AD ve vaší databázi Průzkumník dat Azure následujícím způsobem:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | Název databáze |
    | ```AAD App ID``` | ID aplikace Azure AD |
    | ```AAD Tenant ID``` | ID vašeho Tenanta Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Najít svoje ID Tenanta Azure AD

K ověření aplikace, nastavení používá Průzkumník dat Azure vaše ID tenanta Azure AD. ID tenanta zjistíte pomocí následující adresy URL, ve které *YourDomain* nahradíte svou doménou.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Pokud je vaše doména například *contoso.com*, je adresa URL [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknutím na tuto adresu URL zobrazte výsledky. První řádek vypadá jako v následujícím příkladu. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Váš tenant ID je `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Store a zabezpečení Azure AD App ID a klíč 

Store a zabezpečení Azure AD App ID a klíč pomocí Azure Databricks [tajných kódů](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) následujícím způsobem:
1. [Nastavení rozhraní příkazového řádku](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)
1. [Instalace rozhraní příkazového řádku](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) 
1. [Nastavení ověřování](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurace [tajných kódů](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) pomocí následující ukázkové příkazy:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Import poznámkového bloku
[Import poznámkového bloku](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) pomocí [dotazu ADX AAD aplikace](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) poznámkového bloku pro připojení k ADX. Hodnoty zástupných symbolů aktualizujte název clusteru, název databáze a ID Tenanta služby Azure AD.