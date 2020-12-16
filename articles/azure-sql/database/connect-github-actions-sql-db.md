---
title: 'Rychlý Start: připojení k Azure SQL Database s akcemi GitHubu'
description: Použití Azure SQL z pracovního postupu akcí GitHubu
author: juliakm
services: sql-database
ms.service: sql-database
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 216658b5f5443409e7bd44cbd29bff40cd56c75f
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606976"
---
# <a name="use-github-actions-to-connect-to-azure-sql-database"></a>Použití akcí GitHubu pro připojení k Azure SQL Database

Začněte s [akcemi GitHubu](https://docs.github.com/en/free-pro-team@latest/actions) pomocí pracovního postupu k nasazení aktualizací databáze do [Azure SQL Database](../azure-sql-iaas-vs-paas-what-is-overview.md). 

## <a name="prerequisites"></a>Předpoklady

Budete potřebovat: 
- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Úložiště GitHub s balíčkem DACPAC ( `Database.dacpac` ). Pokud nemáte účet GitHubu, [Zaregistrujte se zdarma](https://github.com/join).  
- Azure SQL Database.
    - [Rychlý Start: vytvoření samostatné databáze Azure SQL Database](single-database-create-quickstart.md)
    - [Jak vytvořit balíček DACPAC z vaší stávající databáze SQL Server](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="workflow-file-overview"></a>Přehled souboru pracovního postupu

Pracovní postup akcí GitHubu je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Soubor má dvě části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Nasazení** | 1. Nasaďte databázi. |

## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení

[Instanční objekt](../../active-directory/develop/app-objects-and-service-principals.md) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Spusťte tento příkaz s [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo vyberte tlačítko **vyzkoušet** .

Zástupné symboly nahraďte `server-name` názvem vašeho SQL serveru hostovaným v Azure. Nahraďte `subscription-id` a `resource-group` pomocí ID předplatného a skupiny prostředků připojené k vašemu SQL serveru.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vaší databázi podobně jako v tomto příkladu. Zkopírujte výstup objektu JSON pro pozdější verzi.

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

## <a name="copy-the-sql-connection-string"></a>Zkopírování připojovacího řetězce SQL 

V Azure Portal přejdete do Azure SQL Database a otevřete **Nastavení**  >  **připojovací řetězce**. Zkopírujte připojovací řetězec pro **ADO.NET**. Nahraďte zástupné hodnoty pro `your_database` a `your_password` . Připojovací řetězec bude vypadat podobně jako tento výstup. 

```output
    Server=tcp:my-sql-server.database.windows.net,1433;Initial Catalog={your-database};Persist Security Info=False;User ID={admin-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
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

1. Vložte hodnotu připojovacího řetězce do pole hodnota tajného klíče. Zadejte název tajného klíče `AZURE_SQL_CONNECTION_STRING` .


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

1. Přejmenujte pracovní postup `SQL for GitHub Actions` a přidejte akce rezervace a přihlášení. Tyto akce vyřadí kód lokality a ověří ho pomocí Azure pomocí `AZURE_CREDENTIALS` tajného klíče GitHubu, který jste vytvořili dříve. 

    ```yaml
    name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. K připojení ke své instanci SQL použijte akci nasazení Azure SQL. Nahraďte `SQL_SERVER_NAME` názvem vašeho serveru. Měli byste mít balíček DACPAC ( `Database.dacpac` ) na kořenové úrovni úložiště. 

    ```yaml
    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'
    ``` 

1. Dokončete pracovní postup přidáním akce k odhlášení Azure. Toto je dokončený pracovní postup. Soubor se zobrazí ve `.github/workflows` složce vašeho úložiště.

    ```yaml
   name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Kontrola nasazení

1. Přejít na **Akce** pro úložiště GitHub. 

1. Otevřením prvního výsledku zobrazíte podrobné protokoly spuštění pracovního postupu. 
 
   :::image type="content" source="media/quickstart-sql-github-actions/github-actions-run-sql.png" alt-text="Protokol spuštění akcí GitHubu":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už vaše databáze SQL a úložiště Azure nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků a vašeho úložiště GitHub. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o integraci Azure a GitHubu](/azure/developer/github/)