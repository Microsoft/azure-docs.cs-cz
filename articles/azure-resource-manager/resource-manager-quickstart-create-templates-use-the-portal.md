---
title: Vytvoření a nasazení šablony Azure Resource Manageru pomocí portálu Azure Portal | Microsoft Docs
description: Přečtěte si, jak si můžete na portálu Azure Portal vytvořit první šablonu Azure Resource Manageru a jak ji nasadit.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/16/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 8094105fd14ef13dd5f6b892425608806ca4dbd2
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378029"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Rychlý start: Vytvoření a nasazení šablony Azure Resource Manageru pomocí portálu Azure Portal

Přečtěte si, jak si můžete na webu Azure Portal vygenerovat první šablonu Azure Resource Manageru a jak ji můžete na webu Azure Portal upravit a nasadit. Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Podle pokynů v tomto kurzu vytvoříte účet služby Azure Storage. Stejným postupem můžete vytvořit i další prostředky Azure.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="generate-a-template-using-the-portal"></a>Vygenerování šablony na portálu

V této části vytvoříte účet úložiště pomocí portálu Azure Portal. Než nasadíte účet úložiště, máte možnost prozkoumat šablonu, kterou portál vygeneroval podle vaší konfigurace. Šablonu pak můžete uložit, abyste ji mohli v budoucnu znovu použít.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Vytvořit prostředek** > **Storage** > **Účet úložiště – objekt blob, soubor, tabulka, fronta**.

    ![Vytvoření účtu Azure Storage na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Zadejte následující informace. 

    - **Skupina prostředků**: Vytvořte novou skupinu prostředků Azure s požadovaným názvem. Na snímku obrazovky má skupina prostředků název *mystorage1016rg*.
    - **Název**: Zadejte jedinečný název účtu úložiště. Na snímku obrazovky má účet název *mystorage1016*.

    Pro zbývající vlastnosti můžete použít výchozí hodnoty.

    ![Vytvoření konfigurace účtu Azure Storage na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Některé exportované šablony vyžadují před nasazením nějaké úpravy.

4. V dolní části obrazovky vyberte **Zkontrolovat a vytvořit**. 
5. V dolní části stránky vyberte **Stáhnout šablonu pro automatizaci**. Na portálu se zobrazí vygenerovaná šablona:

    ![Vygenerování šablony na portálu](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    Šablona se zobrazí v hlavním podokně. Jedná se o soubor JSON se čtyřmi elementy nejvyšší úrovně – `schema`, `contentVersion`, `parameters` a `resources`. Další informace najdete v článku, který se zabývá [strukturou a syntaxí šablon Azure Resource Manageru](./resource-group-authoring-templates.md).

    Existuje šest definovaných parametrů. Jeden z nich má název **storageAccountName**. Druhá zvýrazněná část ukazuje použití tohoto parametru v šabloně. V další části upravíte šablonu tak, aby používala vygenerovaný název účtu úložiště.

    Šablona obsahuje jeden definovaný prostředek Azure. Jeho typ je [Microsoft.Storage/storageAccounts]. Prohlédněte si způsob a strukturu definice tohoto prostředku.
6. Vyberte **Download** (Stáhnout). Uložte soubor **template.json** ze staženého balíčku do svého počítače. V další části šablonu upravíte pomocí nástroje Template deployment.
7. Vyberte kartu **Parametr** a zobrazte zadané hodnoty parametrů. Tyto hodnoty si poznamenejte, protože je budete potřebovat v další části k nasazení šablony.

    ![Vygenerování šablony na portálu](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Pomocí šablony a souborů parametrů můžete vytvořit účet služby Azure Storage.

## <a name="edit-and-deploy-the-template"></a>Úprava a nasazení šablony

Na webu Azure Portal je možné provádět několik základních úprav šablon. V tomto rychlém startu použijete nástroj portálu *Template deployment*. Pokud chcete upravit složitější šablonu, zvažte použití editoru [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md), který poskytuje více funkcí úprav.

Azure vyžaduje, aby každá služba Azure měla jedinečný název. Pokud zadáte název účtu úložiště, který už existuje, nasazení selže. Pokud se chcete tomuto problému vyhnout, můžete vygenerovat jedinečný název účtu úložiště zavoláním funkce šablony `uniquestring()`.

1. Na webu Azure Portal vyberte **Vytvořit prostředek**.
2. Do pole **Hledat na Marketplace** zadejte **template deployment** a stiskněte **ENTER**.
3. Vyberte **Template deployment**.

    ![Knihovna šablon Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. Vyberte **Vytvořit**.
5. Vyberte **Vytvořit vlastní šablonu v editoru**.
6. Vyberte **Načíst soubor** a pak podle pokynů načtěte soubor template.json, který jste stáhli v předchozí části.
7. Přidejte jednu proměnnou, jak je znázorněno na následujícím snímku obrazovky:

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
    ```
    ![Šablony Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

    Používají se zde dvě funkce: `concat()` a `uniqueString()`.

8. Odeberte parametr **storageAccountName** zvýrazněný na předchozím snímku obrazovky.
9. Aktualizujte element name prostředku **Microsoft.Storage/storageAccounts** tak, aby se místo parametru použila nově definovaná proměnná:

    ```json
    "name": "[variables('storageAccountName')]",
    ```

    Výsledná šablona by měla vypadat takto:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string"
            },
            "accountType": {
                "type": "string"
            },
            "kind": {
                "type": "string"
            },
            "accessTier": {
                "type": "string"
            },
            "supportsHttpsTrafficOnly": {
                "type": "bool"
            }
        },
        "variables": {
            "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
            {
                "name": "[variables('storageAccountName')]",
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2018-07-01",
                "location": "[parameters('location')]",
                "properties": {
                    "accessTier": "[parameters('accessTier')]",
                    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
                },
                "dependsOn": [],
                "sku": {
                    "name": "[parameters('accountType')]"
                },
                "kind": "[parameters('kind')]"
            }
        ],
        "outputs": {}
    }
    ```
7. Vyberte **Uložit**.
8. Zadejte následující hodnoty:

    - **Skupina prostředků**: Zadejte jedinečný název skupiny prostředků.
    - **Umístění:**: Vyberte umístění pro skupinu prostředků.
    - **Umístění:**: Vyberte umístění pro účet úložiště.  Můžete použít stejné umístění jako u skupiny prostředků.
    - **Typ účtu**: V tomto rychlém startu zadejte **Standard_LRS**.
    - **Druh:** V tomto rychlém startu zadejte **StorageV2**.
    - **Přístup k úrovni:** V tomto rychlém startu zadejte **Horká**.
    - **Povolen pouze přenos HTTPS**:  V tomto rychlém startu vyberte **true**.
    - **Souhlasím s podmínkami a ujednáními uvedenými nahoře**: Toto políčko zaškrtněte.

    Tady je snímek obrazovky s ukázkovým nasazením:

    ![Nasazení šablon Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Vyberte **Koupit**.
11. Stav nasazení zobrazíte tak, že nahoře na obrazovce vyberete ikonu zvonku (oznámení). Počkejte na dokončení nasazení.

    ![Oznámení nasazení šablon Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. V podokně oznámení vyberte **Přejít ke skupině prostředků**. Zobrazí se podobná obrazovka:

    ![Skupina prostředků nasazení šablon Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Jak vidíte, nasazení proběhlo úspěšně a skupina prostředků obsahuje pouze jeden účet úložiště. Název účtu úložiště je jedinečný řetězec vygenerovaný šablonou. Další informace o používání účtů úložiště Azure najdete v tématu [Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí webu Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků byste měli vidět účet úložiště.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste naučili, jak vygenerovat šablonu na portálu Azure Portal a jak ji pomocí tohoto portálu nasadit. Šablona použitá v tomto rychlém startu představuje jednoduchou šablonu s jedním prostředkem Azure. Pokud je šablona složitá, je snazší šablonu vytvořit v nástroji Visual Studio Code nebo v sadě Visual Studio. V dalším rychlém startu se také dozvíte, jak nasazovat šablony pomocí Azure PowerShellu a rozhraní příkazového řádku Azure (Azure CLI).

> [!div class="nextstepaction"]
> [Vytváření šablon v nástroji Visual Studio](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
