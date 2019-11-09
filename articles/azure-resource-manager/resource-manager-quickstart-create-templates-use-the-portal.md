---
title: Nasazení šablony Správce prostředků – Azure Portal
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
ms.date: 06/12/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 299e1f3bc5915d547c4b463e770427d480836179
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73834295"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Rychlý start: Vytvoření a nasazení šablony Azure Resource Manageru pomocí portálu Azure Portal

Naučte se generovat šablonu Správce prostředků pomocí Azure Portal a proces úpravy a nasazení šablony z portálu. Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, podívejte se na téma [Přehled Azure Resource Manageru](resource-group-overview.md).

![Diagram portálu pro rychlé zprovoznění šablon Správce prostředků](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po dokončení kurzu nasadíte účet Azure Storage. Stejný postup je možné použít k nasazení dalších prostředků Azure.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="generate-a-template-using-the-portal"></a>Vygenerování šablony na portálu

Vytvoření šablony Správce prostředků od začátku není jednoduchý úkol, zejména pokud s nasazením Azure začínáte a nejste obeznámeni s formátem JSON. Pomocí Azure Portal můžete nakonfigurovat prostředek, například účet Azure Storage. Před nasazením prostředku můžete exportovat konfiguraci do šablony Správce prostředků. Šablonu pak můžete uložit, abyste ji mohli v budoucnu znovu použít.

Mnohé zkušení vývojáři šablon používají tuto metodu k vygenerování šablon při pokusu o nasazení prostředků Azure, které nejsou obeznámené s. Další informace o exportu šablon pomocí portálu najdete v tématu [Export skupin prostředků do šablon](./manage-resource-groups-portal.md#export-resource-groups-to-templates). Další možností, jak najít pracovní šablonu, je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/).

1. Ve webovém prohlížeči přejdete na [Azure Portal](https://portal.azure.com) a přihlaste se.
2. V nabídce Azure Portal vyberte **vytvořit prostředek**.

    ![V nabídce Azure Portal vyberte vytvořit prostředek.](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

3. Vyberte **úložiště** > **účet úložiště**.

    ![Vytvoření účtu úložiště Azure](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
1. Zadejte následující informace:

    |Name (Název)|Hodnota|
    |----|----|
    |**Skupina prostředků**|Vyberte **vytvořit novou**a zadejte název skupiny prostředků podle svého výběru. Na snímku obrazovky má skupina prostředků název *mystorage1016rg*. Skupina prostředků je kontejner pro prostředky Azure. Skupina prostředků usnadňuje správu prostředků Azure. |
    |**Název**|Dejte účtu úložiště jedinečný název. Název účtu úložiště musí být jedinečný v rámci všech Azure a může obsahovat jenom malá písmena a číslice. Název musí být dlouhý 3 až 24 znaků. Pokud se zobrazí chybová zpráva oznamující, že název účtu úložiště mystorage1016 se už používá, zkuste použít **&lt;vaše jméno > úložiště&lt;dnešní datum v > MMDD**, například **johndolestorage1016**. Další informace najdete v tématu [pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|

    Pro zbývající vlastnosti můžete použít výchozí hodnoty.

    ![Vytvoření konfigurace účtu Azure Storage na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Některé exportované šablony vyžadují před nasazením nějaké úpravy.

5. V dolní části obrazovky vyberte **Zkontrolovat a vytvořit**. V dalším kroku nevybírejte **vytvořit** .
6. V dolní části stránky vyberte **Stáhnout šablonu pro automatizaci**. Na portálu se zobrazí vygenerovaná šablona:

    ![Vygenerování šablony na portálu](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    Šablona se zobrazí v hlavním podokně. Jedná se o soubor JSON se šesti elementy nejvyšší úrovně – `schema`, `contentVersion`, `parameters`, `variables`, `resources`a `output`. Další informace najdete v článku, který se zabývá [strukturou a syntaxí šablon Azure Resource Manageru](./resource-group-authoring-templates.md).

    Existuje šest definovaných parametrů. Jeden z nich má název **storageAccountName**. Druhá zvýrazněná část na předchozím snímku obrazovky ukazuje, jak odkazovat na tento parametr v šabloně. V další části upravíte šablonu tak, aby používala vygenerovaný název účtu úložiště.

    Šablona obsahuje jeden definovaný prostředek Azure. Typ je `Microsoft.Storage/storageAccounts`. Podívejte se, jak je definován prostředek, a strukturu definice.
7. V horní části obrazovky vyberte **Stáhnout** .
8. Otevřete stažený soubor zip a uložte soubor **template. JSON** do počítače. V další části šablonu upravíte pomocí nástroje Template deployment.
9. Vyberte kartu **Parametr** a zobrazte zadané hodnoty parametrů. Tyto hodnoty si poznamenejte, protože je budete potřebovat v další části k nasazení šablony.

    ![Vygenerování šablony na portálu](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Pomocí souboru šablony i souboru parametrů můžete vytvořit prostředek v tomto kurzu účet úložiště Azure.

## <a name="edit-and-deploy-the-template"></a>Úprava a nasazení šablony

Na webu Azure Portal je možné provádět několik základních úprav šablon. V tomto rychlém startu použijete nástroj portálu *Template deployment*. V tomto kurzu se používá *nasazení šablony* , abyste mohli celý kurz kompletní pomocí jednoho rozhraní – Azure Portal. Chcete-li upravit složitější šablonu, zvažte použití [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md), která poskytuje bohatší funkce úprav.

> [!IMPORTANT]
> Nasazení šablon poskytuje rozhraní pro testování jednoduchých šablon. Tuto funkci nedoporučujeme používat v produkčním prostředí. Místo toho uložte své šablony do účtu služby Azure Storage nebo do úložiště zdrojového kódu, jako je GitHub.

Azure vyžaduje, aby každá služba Azure měla jedinečný název. Nasazení může selhat, pokud jste zadali název účtu úložiště, který už existuje. Chcete-li se tomuto problému vyhnout, upravte šablonu tak, aby používala volání funkce šablony `uniquestring()` k vygenerování jedinečného názvu účtu úložiště.

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.
2. Do pole **Hledat na Marketplace** zadejte **template deployment** a stiskněte **ENTER**.
3. Vyberte **Template deployment**.

    ![Knihovna šablon Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. Vyberte **Vytvořit**.
5. Vyberte **Vytvořit vlastní šablonu v editoru**.
6. Vyberte **Načíst soubor** a pak podle pokynů načtěte soubor template.json, který jste stáhli v předchozí části.
7. Proveďte následující tři změny v šabloně:

    ![Šablony Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Odeberte parametr **storageAccountName** , jak je znázorněno na předchozím snímku obrazovky.
   - Přidejte jednu proměnnou s názvem **storageAccountName** , jak je znázorněno na předchozím snímku obrazovky:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       Tady se používají dvě funkce šablony: `concat()` a `uniqueString()`.
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

    |Name (Název)|Hodnota|
    |----|----|
    |**Skupina prostředků**|Vyberte název skupiny prostředků, který jste vytvořili v poslední části. |
    |**Umístění**|Vyberte umístění pro účet úložiště. Například **střed USA**. |
    |**Typ účtu**|Zadejte **Standard_LRS** pro tento rychlý Start. |
    |**Plnění**|Pro tento rychlý Start zadejte **StorageV2** . |
    |**Úroveň přístupu**|Pro tento rychlý Start zadejte **Hot** . |
    |**Provoz HTTPS je povolený jenom**| V tomto rychlém startu vyberte **true**. |
    |**Souhlasím s podmínkami a ujednáními uvedenými nahoře**|vybrali|

    Tady je snímek obrazovky s ukázkovým nasazením:

    ![Nasazení šablon Azure Resource Manageru](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Vyberte **Koupit**.
11. Stav nasazení zobrazíte tak, že nahoře na obrazovce vyberete ikonu zvonku (oznámení). Uvidíte, že probíhá **nasazení**. Počkejte na dokončení nasazení.

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

V tomto kurzu jste naučili, jak vygenerovat šablonu na portálu Azure Portal a jak ji pomocí tohoto portálu nasadit. Šablona použitá v tomto rychlém startu představuje jednoduchou šablonu s jedním prostředkem Azure. Pokud je šablona složitá, je snazší šablonu vytvořit v nástroji Visual Studio Code nebo v sadě Visual Studio. Další informace o vývoji šablon najdete v naší nové řadě kurzů pro začátečníky:

> [!div class="nextstepaction"]
> [Začátečník – kurzy](./template-tutorial-create-first-template.md)