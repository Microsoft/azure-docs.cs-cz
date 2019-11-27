---
title: Zabezpečení artefaktů v šablonách
description: Zjistěte, jak zabezpečit artefakty, které používají v šablonách Azure Resource Manageru.
author: mumian
ms.date: 10/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b37f7e284b655a362c5a4231a7c1da3719762644
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326439"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Kurz: Zabezpečení artefaktů v nasazení šablon Azure Resource Manageru

Zjistěte, jak zabezpečit artefakty, které používají v šablonách Azure Resource Manageru pomocí sdílených přístupových podpisů (SAS) účtu služby Azure Storage. Artefakty nasazení jsou všechny soubory kromě souborů hlavní šablony, které jsou potřebné k dokončení nasazení. Například v [kurzu: Import souborů SQL BacPac pomocí šablon Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)vytvoří hlavní šablona Azure SQL Database; také zavolá soubor BACPAC a vytvoří tabulky a vloží data. Soubor BACPAC je artefakt. Artefakt je uložená v účtu služby Azure storage s veřejný přístup. V tomto kurzu použijete SAS udělit omezený přístup do souboru BACPAC v účtu úložiště Azure. Další informace o SAS najdete v tématu [použití sdílených přístupových podpisů (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Informace o tom, jak zabezpečit propojenou šablonu, najdete v tématu [kurz: Vytvoření propojených Azure Resource Manager šablon](./resource-manager-tutorial-create-linked-templates.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava souboru BACPAC
> * Otevřete existující šablonu
> * Úprava šablony
> * Nasazení šablony
> * Ověření nasazení

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Pokud [chcete vytvořit Azure Resource Manager šablony](./resource-manager-tools-vs-code.md), přečtěte si téma použití Visual Studio Code.
* [Kurz recenze: Import souborů SQL BacPac pomocí šablon Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). Šablona použitá v tomto kurzu je vyvinutý v tomto kurzu. Odkaz ke stažení dokončené šablony je uvedené v tomto článku.
* Pro zlepšení zabezpečení použijte pro účet správce SQL Serveru vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v [kurzu integrace služby Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="prepare-a-bacpac-file"></a>Příprava souboru BACPAC

V této části Příprava souboru BACPAC, že soubor je bezpečně při nasazení šablony Resource Manageru. V této části je pět postupů:

* Stáhněte si soubor BACPAC.
* Vytvořit účet služby Azure Storage
* Vytvořte kontejner objektů Blob v účtu úložiště.
* Nahrání souboru BACPAC do kontejneru.
* Získat token SAS souboru BACPAC.

Pokud chcete tyto kroky automatizovat pomocí skriptu PowerShellu, přečtěte si [odkaz na skript odeslání propojené šablony](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Stažení souboru BACPAC

Stáhněte si [soubor BacPac](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac)a uložte ho do místního počítače se stejným názvem **SQLDatabaseExtension. BacPac**.

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

1. Vyberte následující obrázek otevřete šablonu Resource Manageru na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Zadejte následující vlastnosti:

    * **Předplatné**: Vyberte své předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou** a pojmenujte ji. Skupina prostředků je kontejner pro prostředky Azure pro účely správy. V tomto kurzu můžete použít stejnou skupinu prostředků pro účet úložiště a Azure SQL Database. Poznamenejte si tento název skupiny prostředků, budete potřebovat při vytváření serveru Azure SQL Database později v kurzech.
    * **Umístění**: Vyberte oblast. Například **střed USA**.
    * **Typ účtu úložiště**: použijte výchozí hodnotu, která je **Standard_LRS**.
    * **Umístění**: použijte výchozí hodnotu, která je **[resourceName (). Location]** . To znamená, že používáte umístění skupiny prostředků pro účet úložiště.
    * **Souhlasím s podmínkami a ujednáními, které byly zahájeny výše**: (vybráno)
3. Vyberte **Koupit**.
4. Vyberte ikonu oznámení (ikona zvonku) v pravém horním rohu portálu, abyste zobrazili stav nasazení.

    ![Podokno kurz portálu oznámení Resource Manageru](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. Po úspěšném nasazení účtu úložiště vyberte v podokně oznámení **Přejít na skupinu prostředků** a otevřete skupinu prostředků.

### <a name="create-a-blob-container"></a>Vytvořte kontejner objektů Blob

Kontejner objektů Blob je potřeba předtím, než můžete nahrát všechny soubory.

1. Výběrem účtu úložiště ho otevřete. Zobrazí se jenom jeden účet úložiště, které jsou uvedené ve skupině prostředků. Název svého účtu úložiště se liší od znázorněné na následujícím snímku obrazovky.

    ![Účet kurz úložiště Resource Manageru](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Vyberte dlaždici **objektů BLOB** .

    ![Kurz objekty BLOB Resource Manageru](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Vyberte **+ kontejner** z horní části a vytvořte nový kontejner.
4. Zadejte následující hodnoty:

    * **Název**: zadejte **sqlbacpac**.
    * **Úroveň veřejného přístupu**: použijte výchozí hodnotu **Private (bez anonymního přístupu)** .
5. Vyberte **OK**.
6. Výběrem **sqlbacpac** otevřete nově vytvořený kontejner.

### <a name="upload-the-bacpac-file-to-the-container"></a>Nahrání souboru BACPAC do kontejneru

1. Vyberte **Nahrát**.
2. Zadejte následující hodnoty:

    * **Soubory**: podle pokynů vyberte soubor BacPac, který jste stáhli dříve. Výchozí název je **SQLDatabaseExtension. BacPac**.
    * **Typ ověřování**: vyberte **SAS**.  Výchozí hodnota je *SAS* .
3. Vyberte **Nahrát**.  Po úspěšném odeslání souboru je název souboru se uvádějí v kontejneru.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />generovat token SAS

1. Klikněte pravým tlačítkem na **SQLDatabaseExtension. BacPac** z kontejneru a pak vyberte **Generovat SAS**.
2. Zadejte následující hodnoty:

    * **Oprávnění**: použijte výchozí, **číst**.
    * **Datum/čas začátku a konce platnosti**: výchozí hodnota poskytuje osm hodin pro použití tokenu SAS. Pokud potřebujete víc času na dokončení tohoto kurzu, aktualizujte **vypršení platnosti**.
    * **Povolené IP adresy**: nechte toto pole prázdné.
    * **Povolené protokoly**: použijte výchozí hodnotu **https**.
    * **Podpisový klíč**: použijte výchozí hodnotu: **klíč 1**.
3. Vyberte **Generovat token SAS objektu BLOB a adresu URL**.
4. Vytvořte kopii **adresy URL SAS objektu BLOB**. Uprostřed adresy URL je název souboru **SQLDatabaseExtension. BacPac**.  Název souboru rozděluje adresu URL do tří částí:

   - **Umístění artefaktu**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Ujistěte se, že umístění končí "/".
   - **Název souboru BacPac**: SQLDatabaseExtension. BacPac.
   - **Umístění artefaktu SAS tokenu**: Ujistěte se, že token předchází "?."

     Tyto tři hodnoty budete potřebovat v [části nasazení šablony](#deploy-the-template).

## <a name="open-an-existing-template"></a>Otevřete existující šablonu

V této relaci upravíte šablonu, kterou jste vytvořili v [kurzu: Import souborů SQL BacPac pomocí šablon Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) pro volání souboru BacPac s tokenem SAS.  Šablona vytvořená v kurzu rozšíření SQL je sdílená na [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.

    Existuje pět prostředky definované v šabloně:

   * `Microsoft.Sql/servers`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Než začnete šablonu přizpůsobovat, je vhodné se s ní nejprve trochu seznámit.
4. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem **azuredeploy.json**.

## <a name="edit-the-template"></a>Úprava šablony

Přidejte následující další parametry:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Kurz zabezpečené artefakty parametrů Resource Manageru](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Aktualizujte hodnotu z následujících dvou prvků:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>Nasazení šablony

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Informace o procesu nasazení najdete v části [Nasazení šablony](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template). Místo toho použijte následující skript PowerShellu pro nasazení:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Použijte vygenerované heslo. Viz [Požadavky](#prerequisites).
Hodnoty _artifactsLocation _artifactsLocationSasToken a bacpacFileName najdete v tématu [vygenerování tokenu SAS](#generate-a-sas-token).

## <a name="verify-the-deployment"></a>Ověření nasazení

Na portálu vyberte databázi SQL z nově nasazené skupiny prostředků. Vyberte **Editor dotazů (Preview)** a zadejte přihlašovací údaje správce. Měly by se zobrazit dvě tabulky importované do databáze:

![Azure Resource Manager – Soubor BACPAC pro nasazení rozšíření SQL](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se nasadit SQL Server, SQL Database a import souboru BACPAC pomocí tokenu SAS. Informace o tom, jak vytvořit kanál Azure pro průběžné vývoj a nasazování šablon Správce prostředků, najdete v tématu.

> [!div class="nextstepaction"]
> [Kontinuální integrace s kanálem Azure](./resource-manager-tutorial-use-azure-pipelines.md)
