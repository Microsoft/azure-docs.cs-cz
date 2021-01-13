---
title: 'Rychlý Start: připojení k Azure MySQL s akcemi GitHubu'
description: Použití Azure MySQL z pracovního postupu akcí GitHubu
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: f62ca85bd4e027595103b738c23c9997a05cc85c
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132813"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-mysql"></a>Rychlý Start: použití akcí GitHubu pro připojení k Azure MySQL

**Platí pro**: :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for MySQL-Single server :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for MySQL-flexibilní Server

Začněte s [akcemi GitHubu](https://docs.github.com/en/free-pro-team@latest/actions) pomocí pracovního postupu k nasazení aktualizací databáze do [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/).


## <a name="prerequisites"></a>Požadavky

Budete potřebovat: 
- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Úložiště GitHub s ukázkovými daty ( `data.sql` ). Pokud nemáte účet GitHubu, [Zaregistrujte se zdarma](https://github.com/join).  
- Server Azure Database for MySQL.
    - [Rychlý Start: vytvoření serveru Azure Database for MySQL v Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>Přehled souboru pracovního postupu

Pracovní postup akcí GitHubu je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Soubor má dvě části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Nasazení** | 1. Nasaďte databázi. |

## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Spusťte tento příkaz s [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo vyberte tlačítko **vyzkoušet** .

Zástupné symboly nahraďte `server-name` názvem vašeho serveru MySQL hostovaným v Azure. Nahraďte `subscription-id` a `resource-group` pomocí ID předplatného a skupiny prostředků připojené k serveru MySQL.  

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

## <a name="copy-the-mysql-connection-string"></a>Zkopírování připojovacího řetězce MySQL 

V Azure Portal přejdete na server Azure Database for MySQL a otevřete **Nastavení**  >  **připojovací řetězce**. Zkopírujte připojovací řetězec pro **ADO.NET**. Nahraďte zástupné hodnoty pro `your_database` a `your_password` . Připojovací řetězec bude vypadat podobně jako v tomto příkladu. 

> [!IMPORTANT]
> - Pro jeden server použijte **UID = adminusername@servername**. Všimněte si, že **@servername** je povinné.
> - V případě flexibilního serveru použijte **UID = AdminUsername** bez @servername . Mějte na paměti, že server MySQL flexibilní je ve verzi Preview. 


```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
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

1. Vložte hodnotu připojovacího řetězce do pole hodnota tajného klíče. Zadejte název tajného klíče `AZURE_MYSQL_CONNECTION_STRING` .


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

1. Přejmenujte pracovní postup `MySQL for GitHub Actions` a přidejte akce rezervace a přihlášení. Tyto akce vyřadí kód lokality a ověří ho pomocí Azure pomocí `AZURE_CREDENTIALS` tajného klíče GitHubu, který jste vytvořili dříve. 

    ```yaml
    name: MySQL for GitHub Actions

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

1. K připojení ke své instanci MySQL použijte akci nasazení Azure MySQL. Nahraďte `MYSQL_SERVER_NAME` názvem vašeho serveru. Měli byste mít datový soubor MySQL s názvem `data.sql` na kořenové úrovni úložiště. 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. Dokončete pracovní postup přidáním akce k odhlášení Azure. Toto je dokončený pracovní postup. Soubor se zobrazí ve `.github/workflows` složce vašeho úložiště.

    ```yaml
   name: MySQL for GitHub Actions

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

    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Kontrola nasazení

1. Přejít na **Akce** pro úložiště GitHub. 

1. Otevřením prvního výsledku zobrazíte podrobné protokoly spuštění pracovního postupu. 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="Protokol spuštění akcí GitHubu":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už databázi a úložiště Azure MySQL nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků a vašeho úložiště GitHub. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o integraci Azure a GitHubu](/azure/developer/github/)
