---
title: 'Rychlý Start: připojení k Azure PostgreSQL s akcemi GitHubu'
description: Použití Azure PostgreSQL z pracovního postupu akcí GitHubu
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 2e546801f95d9d884bdfb3f09a18b3fa6e2d78a1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97365113"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>Rychlý Start: použití akcí GitHubu pro připojení k Azure PostgreSQL

<Token>**platí pro:** :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for PostgreSQL – jeden server :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for PostgreSQL – flexibilní Server</Token>

Začněte s [akcemi GitHubu](https://docs.github.com/en/actions) pomocí pracovního postupu k nasazení aktualizací databáze do [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/).

## <a name="prerequisites"></a>Předpoklady

Budete potřebovat:
- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Úložiště GitHub s ukázkovými daty ( `data.sql` ). Pokud nemáte účet GitHubu, [Zaregistrujte se zdarma](https://github.com/join).
- Server Azure Database for PostgreSQL.
    - [Rychlý start: Vytvoření serveru Azure Database for PostgreSQL na webu Azure Portal](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>Přehled souboru pracovního postupu

Pracovní postup akcí GitHubu je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Soubor má dvě části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Nasazení** | 1. Nasaďte databázi. |

## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Spusťte tento příkaz s [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo vyberte tlačítko **vyzkoušet** .

Zástupné symboly nahraďte `server-name` názvem vašeho serveru PostgreSQL hostovaným v Azure. Nahraďte `subscription-id` a `resource-group` pomocí ID předplatného a skupiny prostředků připojené k vašemu serveru PostgreSQL.

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vaší databázi podobně jako v následujícím příkladu. Zkopírujte tento výstupní objekt JSON pro pozdější verzi.

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Je vždy dobrým zvykem udělit minimální přístup. Obor v předchozím příkladu je omezený na konkrétní server, nikoli na celou skupinu prostředků.

## <a name="copy-the-postgresql-connection-string"></a>Zkopírování připojovacího řetězce PostgreSQL

V Azure Portal přejdete na server Azure Database for PostgreSQL a otevřete **Nastavení**  >  **připojovací řetězce**. Zkopírujte připojovací řetězec pro **ADO.NET**. Nahraďte zástupné hodnoty pro `your_database` a `your_password` . Připojovací řetězec bude vypadat podobně jako v tomto příkladu.

> [!IMPORTANT]
> - Pro použití na jednom serveru ```user=adminusername@servername```  . Všimněte si, že ```@servername``` je povinné.
> - V případě flexibilního serveru použijte ```user= adminusername``` bez  ```@servername``` .

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
```

Připojovací řetězec použijete jako tajný klíč GitHubu.

## <a name="configure-the-github-secrets"></a>Konfigurace tajných kódů GitHubu

1. V [GitHubu](https://github.com/)přejděte do úložiště.

1. Vyberte **nastavení > tajných klíčů > nový tajný kód**.

1. Do pole hodnota tajného klíče vložte celý výstup JSON z příkazu Azure CLI. Zadejte název tajného klíče `AZURE_CREDENTIALS` .

    Když později nakonfigurujete soubor pracovního postupu, použijete tajný klíč pro vstup `creds` Akce přihlášení do Azure. Příklad:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Znovu vyberte **nový tajný klíč** .

1. Vložte hodnotu připojovacího řetězce do pole hodnota tajného klíče. Zadejte název tajného klíče `AZURE_POSTGRESQL_CONNECTION_STRING` .


## <a name="add-your-workflow"></a>Přidat pracovní postup

1. Přejít na **Akce** pro úložiště GitHub.

2. Vyberte **nastavit pracovní postup sami**.

2. Odstraňte vše po `on:` části souboru pracovního postupu. Zbývající pracovní postup může vypadat například takto:

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Přejmenujte pracovní postup `PostgreSQL for GitHub Actions` a přidejte akce rezervace a přihlášení. Tyto akce vyřadí kód lokality a ověří ho pomocí Azure pomocí `AZURE_CREDENTIALS` tajného klíče GitHubu, který jste vytvořili dříve.

    ```yaml
    name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

2. Pomocí akce nasazení Azure PostgreSQL se připojte ke své instanci PostgreSQL. Nahraďte `POSTGRESQL_SERVER_NAME` názvem vašeho serveru. Měli byste mít datový soubor PostgreSQL s názvem `data.sql` na kořenové úrovni úložiště.

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. Dokončete pracovní postup přidáním akce k odhlášení Azure. Toto je dokončený pracovní postup. Soubor se zobrazí ve `.github/workflows` složce vašeho úložiště.

    ```yaml
   name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/postgresql@v1
      with:
        server-name: POSTGRESQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Kontrola nasazení

1. Přejít na **Akce** pro úložiště GitHub.

1. Otevřením prvního výsledku zobrazíte podrobné protokoly spuštění pracovního postupu.

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="Protokol spuštění akcí GitHubu":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už databázi a úložiště Azure PostgreSQL nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků a vašeho úložiště GitHub.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o integraci Azure a GitHubu](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [Informace o tom, jak se připojit k serveru](how-to-connect-query-guide.md)
