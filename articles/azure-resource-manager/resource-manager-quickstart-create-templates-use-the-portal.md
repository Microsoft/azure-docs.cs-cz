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
ms.date: 02/28/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 1db3e5b88560e5f574e01c16d10e95e2d0607cc1
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195451"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Rychlý start: Vytvoření a nasazení šablon Azure Resource Manageru pomocí webu Azure portal

Zjistěte, jak vytvořit šablonu Resource Manageru pomocí webu Azure portal a proces úpravy a nasazení šablony z portálu. Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, podívejte se na téma [Přehled Azure Resource Manageru](resource-group-overview.md).

Po dokončení tohoto kurzu nasadíte účet služby Azure Storage. Stejný postup lze použít k nasazení dalších prostředků Azure.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="generate-a-template-using-the-portal"></a>Vygenerování šablony na portálu

Vytvoření šablony Resource Manageru od začátku není snadný úkol, zejména v případě, že jsou pro vás nové nasazení v Azure a se ještě neznají formátu JSON. Pomocí webu Azure portal, můžete nakonfigurovat prostředek, například účet služby Azure Storage. Před nasazením prostředek, můžete exportovat konfiguraci do šablony Resource Manageru. Šablonu pak můžete uložit, abyste ji mohli v budoucnu znovu použít.

Mnoho vývojářů zkušení šablony pomocí této metody můžete generovat šablony při pokusu o nasazení prostředků Azure, které nejsou obeznámeni s. Další informace o exportu šablony s použitím portálu najdete v tématu [Export skupiny prostředků do šablon](./manage-resource-groups-portal.md#export-resource-groups-to-templates). Další způsob, jak najít šablonu pracovní je z [šablon rychlého startu Azure](https://azure.microsoft.com/resources/templates/).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Vytvořit prostředek** > **Storage** > **Účet úložiště – objekt blob, soubor, tabulka, fronta**.

    ![Vytvoření účtu Azure Storage na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Zadejte následující informace:

    |Název|Hodnota|
    |----|----|
    |**Skupina prostředků**|Vyberte **vytvořit nový**a zadejte název skupiny prostředků podle svého výběru. Na snímku obrazovky má skupina prostředků název *mystorage1016rg*. Skupina prostředků je kontejner pro prostředky Azure. Skupiny prostředků usnadňuje správu prostředků Azure. |
    |**Název**|Zadejte jedinečný název účtu úložiště. Název účtu úložiště musí být jedinečný v rámci všechno, co Azure a obsahovat jenom malá písmena a číslice. Název musí být dlouhý 3 až 24 znaků. Pokud se zobrazí chybová zpráva s oznámením "název účtu úložiště 'mystorage1016' se už používá", zkuste použít  **&lt;vaše_jméno > úložiště&lt;dnešní datum v MMDD >**, například  **johndolestorage1016**. Další informace najdete v tématu [pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions).|

    Pro zbývající vlastnosti můžete použít výchozí hodnoty.

    ![Vytvoření konfigurace účtu Azure Storage na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Některé exportované šablony vyžadují před nasazením nějaké úpravy.

4. V dolní části obrazovky vyberte **Zkontrolovat a vytvořit**. Nesmí být zvolen **vytvořit** v dalším kroku.
5. V dolní části stránky vyberte **Stáhnout šablonu pro automatizaci**. Na portálu se zobrazí vygenerovaná šablona:

    ![Vygenerování šablony na portálu](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    Šablona se zobrazí v hlavním podokně. Představuje soubor JSON s šest nejvyšší úrovně prvky - `schema`, `contentVersion`, `parameters`, `variables`, `resources`, a `output`. Další informace najdete v článku, který se zabývá [strukturou a syntaxí šablon Azure Resource Manageru](./resource-group-authoring-templates.md).

    Existuje šest definovaných parametrů. Jeden z nich má název **storageAccountName**. Druhá část zvýrazněný na předchozím snímku obrazovky ukazuje, jak odkazovat na tento parametr v šabloně. V další části upravíte šablonu tak, aby používala vygenerovaný název účtu úložiště.

    Šablona obsahuje jeden definovaný prostředek Azure. Typ je `Microsoft.Storage/storageAccounts`. Využijte podívat o tom, jak je definován prostředek a struktura definic.
6. Vyberte **Stáhnout** z horní části obrazovky. 
7. Otevřete stažený soubor zip a následně uložte **template.json** k vašemu počítači. V další části šablonu upravíte pomocí nástroje Template deployment.
8. Vyberte kartu **Parametr** a zobrazte zadané hodnoty parametrů. Tyto hodnoty si poznamenejte, protože je budete potřebovat v další části k nasazení šablony.

    ![Vygenerování šablony na portálu](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Pomocí souboru šablony a souborů parametrů, můžete vytvořit prostředek, v tomto kurzu se účet úložiště Azure.

## <a name="edit-and-deploy-the-template"></a>Úprava a nasazení šablony

Na webu Azure Portal je možné provádět několik základních úprav šablon. V tomto rychlém startu použijete nástroj portálu *Template deployment*. *Nasazení šablony* se používá v tomto kurzu, aby mohl dokončit celý kurz pomocí jednoho rozhraní – na webu Azure portal. Chcete-li upravit šablonu pro složitější, zvažte použití [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md), který nabízí bohatší funkce upravit.

Azure vyžaduje, aby každá služba Azure měla jedinečný název. Nasazení může selhat, pokud zadaný název účtu úložiště, který již existuje. K tomuto problému vyhnout, upravte šablonu, kterou chcete použít volání šablony funkce `uniquestring()` ke generování jedinečným názvem účtu úložiště.

1. Na webu Azure Portal vyberte **Vytvořit prostředek**.
2. Do pole **Hledat na Marketplace** zadejte **template deployment** a stiskněte **ENTER**.
3. Vyberte **Template deployment**.

    ![Knihovna šablon Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. Vyberte **Vytvořit**.
5. Vyberte **Vytvořit vlastní šablonu v editoru**.
6. Vyberte **Načíst soubor** a pak podle pokynů načtěte soubor template.json, který jste stáhli v předchozí části.
7. Proveďte následující tři změny v šabloně:

    ![Šablony Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

    - Odeberte **storageAccountName** parametru, jak je znázorněno na předchozím snímku obrazovky.
    - Přidat jednu proměnnou s názvem **storageAccountName** jak je znázorněno na předchozím snímku obrazovky:

        ```json
        "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
        ```

        Zde jsou použity dvě funkce šablony: `concat()` a `uniqueString()`.
    - Aktualizujte element name prostředku **Microsoft.Storage/storageAccounts** tak, aby se místo parametru použila nově definovaná proměnná:

        ```json
        "name": "[variables('storageAccountName')]",
        ```

    Výsledná šablona by měla vypadat takto:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
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
8. Vyberte **Uložit**.
9. Zadejte následující hodnoty:

    |Název|Hodnota|
    |----|----|
    |**Skupina prostředků**|Vyberte název skupiny prostředků, kterou jste vytvořili v předchozí části. |
    |**Umístění**|Vyberte umístění pro účet úložiště. Například **USA (střed)**. |
    |**Typ účtu**|Zadejte **Standard_LRS** pro tento rychlý start. |
    |**Typ**|Zadejte **StorageV2** pro tento rychlý start. |
    |**Access Tier**|Zadejte **Hot** pro tento rychlý start. |
    |**Povolit pouze provoz protokolu HTTPS**| V tomto rychlém startu vyberte **true**. |
    |**Souhlasím s podmínkami a ujednáními uvedenými nahoře**|(Vybrat)|

    Tady je snímek obrazovky s ukázkovým nasazením:

    ![Nasazení šablon Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Vyberte **Koupit**.
11. Stav nasazení zobrazíte tak, že nahoře na obrazovce vyberete ikonu zvonku (oznámení). Měly by se zobrazit **probíhá nasazení**. Počkejte na dokončení nasazení.

    ![Oznámení nasazení šablon Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. V podokně oznámení vyberte **Přejít ke skupině prostředků**. Zobrazí se podobná obrazovka:

    ![Skupina prostředků nasazení šablon Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Jak vidíte, nasazení proběhlo úspěšně a skupina prostředků obsahuje pouze jeden účet úložiště. Název účtu úložiště je jedinečný řetězec vygenerovaný šablonou. Další informace o používání účtů služby Azure storage najdete v tématu [rychlý start: Nahrávání, stahování a výpis objektů BLOB pomocí webu Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků byste měli vidět účet úložiště.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste naučili, jak vygenerovat šablonu na portálu Azure Portal a jak ji pomocí tohoto portálu nasadit. Šablona použitá v tomto rychlém startu představuje jednoduchou šablonu s jedním prostředkem Azure. Pokud je šablona složitá, je snazší šablonu vytvořit v nástroji Visual Studio Code nebo v sadě Visual Studio. V dalším rychlém startu se také dozvíte, jak nasazovat šablony pomocí Azure PowerShellu a rozhraní příkazového řádku Azure (Azure CLI).

> [!div class="nextstepaction"]
> [Vytváření šablon v nástroji Visual Studio](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
