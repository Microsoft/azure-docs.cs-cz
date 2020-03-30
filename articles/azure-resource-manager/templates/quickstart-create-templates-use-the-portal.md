---
title: Nasazení šablony – portál Azure
description: Přečtěte si, jak si můžete na portálu Azure Portal vytvořit první šablonu Azure Resource Manageru a jak ji nasadit.
author: mumian
ms.date: 06/12/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: dd3d9caa8184b8637b509fc3318851751b211405
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80131872"
---
# <a name="quickstart-create-and-deploy-arm-templates-by-using-the-azure-portal"></a>Úvodní příručka: Vytvoření a nasazení šablon ARM pomocí portálu Azure

Zjistěte, jak generovat šablonu Azure Resource Manager (ARM) pomocí portálu Azure a proces úprav a nasazení šablony z portálu. Arm šablony jsou soubory JSON, které definují prostředky, které potřebujete k nasazení pro vaše řešení. Informace o konceptech spojených s nasazením a správou řešení Azure najdete v [tématu přehled nasazení šablon](overview.md).

![Diagram portálu rychlého spuštění šablony Správce prostředků](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po dokončení kurzu nasadíte účet Azure Storage. Stejný proces lze použít k nasazení jiných prostředků Azure.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="generate-a-template-using-the-portal"></a>Vygenerování šablony na portálu

Vytvoření šablony ARM od začátku není snadný úkol, zejména pokud jste novým nasazením Azure a nejste obeznámeni s formátem JSON. Pomocí portálu Azure můžete nakonfigurovat prostředek, například účet Azure Storage. Před nasazením prostředku můžete exportovat konfiguraci do šablony. Šablonu pak můžete uložit, abyste ji mohli v budoucnu znovu použít.

Mnoho zkušených vývojářů šablon používá tuto metodu ke generování šablon při pokusu o nasazení prostředků Azure, které nejsou obeznámeni s. Další informace o exportu šablon pomocí portálu naleznete v tématu [Export skupin prostředků do šablon](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Druhý způsob, jak najít pracovní šablonu, je ze [šablon Azure Quickstart](https://azure.microsoft.com/resources/templates/).

1. Ve webovém prohlížeči přejděte na [portál Azure](https://portal.azure.com) a přihlaste se.
1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.

    ![Vyberte Vytvořit prostředek z nabídky portálu Azure.](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Vyberte**účet úložiště**. **Storage** > 

    ![Vytvoření účtu úložiště Azure](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
1. Zadejte následující informace:

    |Name (Název)|Hodnota|
    |----|----|
    |**Skupina prostředků**|Vyberte **Vytvořit nový**a zadejte název skupiny prostředků podle vašeho výběru. Na snímku obrazovky má skupina prostředků název *mystorage1016rg*. Skupina prostředků je kontejner pro prostředky Azure. Skupina prostředků usnadňuje správu prostředků Azure. |
    |**Název**|Pojmenujte účet úložiště jedinečným názvem. Název účtu úložiště musí být jedinečný ve všech Azure a obsahuje pouze malá písmena a čísla. Název musí být mezi 3 a 24 znaky. Pokud se zobrazí chybová zpráva "Název účtu úložiště 'mystorage1016' je již přijata", zkuste použít ** &lt;své jméno>&lt;úložiště Dnešní datum v MMDD>**, například **johndolestorage1016**. Další informace naleznete v [tématu Zásady pojmenování a omezení](/azure/architecture/best-practices/resource-naming).|

    Pro zbývající vlastnosti můžete použít výchozí hodnoty.

    ![Vytvoření konfigurace účtu Azure Storage na portálu Azure Portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Některé exportované šablony vyžadují před nasazením nějaké úpravy.

1. V dolní části obrazovky vyberte **Zkontrolovat a vytvořit**. V dalším kroku nevybírejte **Vytvořit.**
1. V dolní části stránky vyberte **Stáhnout šablonu pro automatizaci**. Na portálu se zobrazí vygenerovaná šablona:

    ![Vygenerování šablony na portálu](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    Šablona se zobrazí v hlavním podokně. Jedná se o soubor JSON se `schema`šesti `contentVersion` `parameters`prvky `resources`nejvyšší `output`úrovně - , , , `variables`, a . Další informace naleznete [v tématu Understand the structure and syntax of ARM templates](./template-syntax.md)

    Existuje šest definovaných parametrů. Jeden z nich má název **storageAccountName**. Druhá zvýrazněná část na předchozím snímku obrazovky ukazuje, jak odkazovat na tento parametr v šabloně. V další části upravíte šablonu tak, aby používala vygenerovaný název účtu úložiště.

    Šablona obsahuje jeden definovaný prostředek Azure. Typ je `Microsoft.Storage/storageAccounts`. Podívejte se na způsob definování prostředku a definiční strukturu.
1. V horní části obrazovky vyberte **Stáhnout.**
1. Otevřete stažený soubor ZIP a uložte **soubor template.json** do počítače. V další části šablonu upravíte pomocí nástroje Template deployment.
1. Vyberte kartu **Parametr** a zobrazte zadané hodnoty parametrů. Tyto hodnoty si poznamenejte, protože je budete potřebovat v další části k nasazení šablony.

    ![Vygenerování šablony na portálu](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Pomocí souboru šablony a souboru parametrů můžete vytvořit prostředek v tomto kurzu účet úložiště Azure.

## <a name="edit-and-deploy-the-template"></a>Úprava a nasazení šablony

Na webu Azure Portal je možné provádět několik základních úprav šablon. V tomto rychlém startu použijete nástroj portálu *Template deployment*. *Nasazení šablony se* používá v tomto kurzu, takže můžete dokončit celý kurz pomocí jednoho rozhraní – portálu Azure. Chcete-li upravit složitější šablonu, zvažte použití [kódu sady Visual Studio](quickstart-create-templates-use-visual-studio-code.md)Code , který poskytuje bohatší funkce úprav.

> [!IMPORTANT]
> Nasazení šablony poskytuje rozhraní pro testování jednoduchých šablon. Nedoporučuje se používat tuto funkci v produkčním prostředí. Místo toho uložte šablony v účtu úložiště Azure nebo úložiště zdrojového kódu, jako je GitHub.

Azure vyžaduje, aby každá služba Azure měla jedinečný název. Nasazení může selhat, pokud jste zadali název účtu úložiště, který již existuje. Chcete-li se tomuto problému vyhnout, upravte šablonu tak, aby používala volání `uniquestring()` funkce šablony ke generování jedinečného názvu účtu úložiště.

1. V nabídce Portál Azure nebo na **domovské** stránce vyberte **Vytvořit prostředek**.
1. Do pole **Hledat na Marketplace** zadejte **template deployment** a stiskněte **ENTER**.
1. Vyberte **Template deployment**.

    ![Knihovna šablon Azure Resource Manageru](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
1. Vyberte **Vytvořit**.
1. Vyberte **Vytvořit vlastní šablonu v editoru**.
1. Vyberte **Načíst soubor** a pak podle pokynů načtěte soubor template.json, který jste stáhli v předchozí části.
1. Proveďte v šabloně následující tři změny:

    ![Šablony Azure Resource Manageru](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Odeberte parametr **storageAccountName,** jak je znázorněno na předchozím snímku obrazovky.
   - Přidejte jednu proměnnou nazvanou **storageAccountName,** jak je znázorněno na předchozím snímku obrazovky:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       Zde se používají dvě `concat()` `uniqueString()`funkce šablony: a .
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
1. Vyberte **Uložit**.
1. Zadejte následující hodnoty:

    |Name (Název)|Hodnota|
    |----|----|
    |**Skupina prostředků**|Vyberte název skupiny prostředků, který jste vytvořili v posledním oddílu. |
    |**Umístění**|Vyberte umístění pro účet úložiště. Například **střední USA**. |
    |**Typ účtu**|Zadejte **Standard_LRS** pro tento rychlý start. |
    |**Druhu**|Zadejte **StorageV2** pro tento rychlý start. |
    |**Úroveň přístupu**|Zadejte **Hot** pro tento rychlý start. |
    |**Povolen pouze provoz https**| V tomto rychlém startu vyberte **true**. |
    |**Souhlasím s výše uvedenými podmínkami**|(vyberte)|

    Tady je snímek obrazovky s ukázkovým nasazením:

    ![Nasazení šablon Azure Resource Manageru](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

1. Vyberte **Koupit**.
1. Stav nasazení zobrazíte tak, že nahoře na obrazovce vyberete ikonu zvonku (oznámení). Uvidíte **probíhající nasazení**. Počkejte na dokončení nasazení.

    ![Oznámení nasazení šablon Azure Resource Manageru](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

1. V podokně oznámení vyberte **Přejít ke skupině prostředků**. Zobrazí se podobná obrazovka:

    ![Skupina prostředků nasazení šablon Azure Resource Manageru](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Jak vidíte, nasazení proběhlo úspěšně a skupina prostředků obsahuje pouze jeden účet úložiště. Název účtu úložiště je jedinečný řetězec vygenerovaný šablonou. Další informace o používání účtů úložiště Azure najdete v tématu [Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí webu Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
1. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
1. Vyberte název skupiny prostředků.  Ve skupině prostředků byste měli vidět účet úložiště.
1. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste naučili, jak vygenerovat šablonu na portálu Azure Portal a jak ji pomocí tohoto portálu nasadit. Šablona použitá v tomto rychlém startu představuje jednoduchou šablonu s jedním prostředkem Azure. Pokud je šablona složitá, je snazší šablonu vytvořit v nástroji Visual Studio Code nebo v sadě Visual Studio. Další informace o vývoji šablon najdete v našem novém výukovém programu pro začátečníky:

> [!div class="nextstepaction"]
> [Kurzy pro začátečníky](./template-tutorial-create-first-template.md)