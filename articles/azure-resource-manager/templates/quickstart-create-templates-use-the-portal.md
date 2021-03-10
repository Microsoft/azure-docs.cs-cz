---
title: Nasadit šablonu – Azure Portal
description: Naučte se, jak vytvořit první šablonu Azure Resource Manager (šablonu ARM) pomocí Azure Portal a jak ji nasadit.
author: mumian
ms.date: 03/09/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 20b1bf47ae2fd63e91a11c8cccd1f03cf3464899
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548121"
---
# <a name="quickstart-create-and-deploy-arm-templates-by-using-the-azure-portal"></a>Rychlý Start: vytvoření a nasazení šablon ARM pomocí Azure Portal

Naučte se generovat šablonu Azure Resource Manager (šablonu ARM) pomocí Azure Portal a proces úpravy a nasazení šablony z portálu. Šablony ARM jsou soubory JSON definující prostředky, které pro vaše řešení potřebujete nasadit. Informace o konceptech spojených s nasazením a správou řešení Azure najdete v tématu [Přehled nasazení šablon](overview.md).

![Diagram portálu pro rychlé zprovoznění šablon Správce prostředků](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po dokončení kurzu nasadíte účet Azure Storage. Stejný postup je možné použít k nasazení dalších prostředků Azure.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="generate-a-template-using-the-portal"></a>Vygenerování šablony na portálu

Vytvoření šablony ARM od začátku není jednoduchý úkol, zejména pokud s nasazením Azure začínáte a nejste obeznámeni s formátem JSON. Pomocí Azure Portal můžete nakonfigurovat prostředek, například účet Azure Storage. Před nasazením prostředku můžete exportovat konfiguraci do šablony. Šablonu pak můžete uložit, abyste ji mohli v budoucnu znovu použít.

Mnohé zkušení vývojáři šablon používají tuto metodu k vygenerování šablon při pokusu o nasazení prostředků Azure, které nejsou obeznámené s. Další informace o exportu šablon pomocí portálu najdete v tématu [Export skupin prostředků do šablon](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Další možností, jak najít pracovní šablonu, je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/).

1. Ve webovém prohlížeči přejdete na [Azure Portal](https://portal.azure.com) a přihlaste se.
1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.

    ![V nabídce Azure Portal vyberte vytvořit prostředek.](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Do vyhledávacího pole zadejte **účet úložiště** a potom stiskněte **[ENTER]**.
1. Vyberte šipku dolů vedle tlačítka **vytvořit** a pak vyberte **účet úložiště**.

    ![Vytvoření účtu úložiště Azure](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)

1. Zadejte následující informace:

    |Name|Hodnota|
    |----|----|
    |**Skupina prostředků**|Vyberte **vytvořit novou** a zadejte název skupiny prostředků podle svého výběru. Na snímku obrazovky má skupina prostředků název *mystorage1016rg*. Skupina prostředků je kontejner pro prostředky Azure. Skupina prostředků usnadňuje správu prostředků Azure. |
    |**Název**|Dejte účtu úložiště jedinečný název. Název účtu úložiště musí být jedinečný v rámci všech Azure a může obsahovat jenom malá písmena a číslice. Název musí být dlouhý 3 až 24 znaků. Pokud se zobrazí chybová zpráva s názvem "název účtu úložiště" mystorage1016 se už používá ", zkuste použít **&lt; své jméno>&lt; dnešní datum v>MMDD**, například **johndolestorage1016**. Další informace najdete v tématu [pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|

    Pro zbývající vlastnosti můžete použít výchozí hodnoty.

    ![Vytvoření konfigurace účtu Azure Storage na portálu Azure Portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Některé exportované šablony vyžadují před nasazením nějaké úpravy.

1. V dolní části obrazovky vyberte **Zkontrolovat a vytvořit**. V dalším kroku nevybírejte **vytvořit** .
1. V dolní části stránky vyberte **Stáhnout šablonu pro automatizaci**. Na portálu se zobrazí vygenerovaná šablona:

    ![Vygenerování šablony na portálu](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    Šablona se zobrazí v hlavním podokně. Jedná se o soubor JSON se šesti elementy nejvyšší úrovně,,,,, `schema` `contentVersion` `parameters` `variables` `resources` a `output` . Další informace najdete v tématu [pochopení struktury a syntaxe šablon ARM](./template-syntax.md) .

    Je definováno devět parametrů. Jeden z nich má název **storageAccountName**. Druhá zvýrazněná část na předchozím snímku obrazovky ukazuje, jak odkazovat na tento parametr v šabloně. V další části upravíte šablonu tak, aby používala vygenerovaný název účtu úložiště.

    Šablona obsahuje jeden definovaný prostředek Azure. Typ je `Microsoft.Storage/storageAccounts` . Podívejte se, jak je definován prostředek, a strukturu definice.
1. V horní části obrazovky vyberte **Stáhnout** .
1. Otevřete stažený soubor zip a uložte **template.js** do svého počítače. V další části šablonu upravíte pomocí nástroje Template deployment.
1. Vyberte kartu **Parametr** a zobrazte zadané hodnoty parametrů. Tyto hodnoty si poznamenejte, protože je budete potřebovat v další části k nasazení šablony.

    ![Snímek obrazovky, který zvýrazní kartu parametru zobrazující hodnoty, které jste zadali.](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Pomocí souboru šablony i souboru parametrů můžete vytvořit prostředek v tomto kurzu účet úložiště Azure.

## <a name="edit-and-deploy-the-template"></a>Úprava a nasazení šablony

Na webu Azure Portal je možné provádět několik základních úprav šablon. V tomto rychlém startu použijete nástroj portálu *Template deployment*. V tomto kurzu se používá *nasazení šablony* , abyste mohli celý kurz kompletní pomocí jednoho rozhraní – Azure Portal. Chcete-li upravit složitější šablonu, zvažte použití [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md), která poskytuje bohatší funkce úprav.

> [!IMPORTANT]
> Nasazení šablon poskytuje rozhraní pro testování jednoduchých šablon. Tuto funkci nedoporučujeme používat v produkčním prostředí. Místo toho uložte své šablony do účtu služby Azure Storage nebo do úložiště zdrojového kódu, jako je GitHub.

Azure vyžaduje, aby každá služba Azure měla jedinečný název. Nasazení může selhat, pokud jste zadali název účtu úložiště, který už existuje. Chcete-li se tomuto problému vyhnout, upravte šablonu tak, aby používala volání funkce šablony `uniquestring()` k vygenerování jedinečného názvu účtu úložiště.

1. Z nabídky Azure Portal do vyhledávacího pole zadejte **Deploy** a pak vyberte **nasadit vlastní šablonu**.

    ![Knihovna šablon Azure Resource Manageru](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)

1. **V editoru vyberte vytvořit vlastní šablonu**.
1. Vyberte **Načíst soubor** a pak podle pokynů načtěte soubor template.json, který jste stáhli v předchozí části.
1. Proveďte následující tři změny v šabloně:

    ![Šablony Azure Resource Manageru](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Odeberte parametr **storageAccountName** , jak je znázorněno na předchozím snímku obrazovky.
   - Přidejte jednu proměnnou s názvem **storageAccountName** , jak je znázorněno na předchozím snímku obrazovky:

      ```json
      "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
      ```

      Tady se používají dvě funkce šablony: `concat()` a `uniqueString()` .
   - Aktualizujte element name prostředku **Microsoft.Storage/storageAccounts** tak, aby se místo parametru použila nově definovaná proměnná:

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
          "minimumTlsVersion": {
            "type": "string"
          },
          "supportsHttpsTrafficOnly": {
            "type": "bool"
          },
          "allowBlobPublicAccess": {
            "type": "bool"
          },
          "allowSharedKeyAccess": {
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
            "apiVersion": "2019-06-01",
            "location": "[parameters('location')]",
            "properties": {
              "accessTier": "[parameters('accessTier')]",
              "minimumTlsVersion": "[parameters('minimumTlsVersion')]",
              "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
              "allowBlobPublicAccess": "[parameters('allowBlobPublicAccess')]",
              "allowSharedKeyAccess": "[parameters('allowSharedKeyAccess')]"
            },
            "dependsOn": [],
            "sku": {
              "name": "[parameters('accountType')]"
            },
            "kind": "[parameters('kind')]",
            "tags": {}
          }
        ],
        "outputs": {}
      }
      ```

1. Vyberte **Uložit**.
1. Zadejte tyto hodnoty:

    |Name|Hodnota|
    |----|----|
    |**Skupina prostředků**|Vyberte název skupiny prostředků, který jste vytvořili v poslední části. |
    |**Oblast**|Vyberte umístění skupiny prostředků. Například **USA – střed**. |
    |**Umístění**|Vyberte umístění pro účet úložiště. Například **USA – střed**. |
    |**Typ účtu**|Zadejte **Standard_LRS** pro tento rychlý Start. |
    |**Druh**|Pro tento rychlý Start zadejte **StorageV2** . |
    |**Úroveň přístupu**|Pro tento rychlý Start zadejte **Hot** . |
    |**Minimální verze protokolu TLS**|Zadejte **TLS1_0**. |
    |**Podporuje jenom přenosy https.**| V tomto rychlém startu vyberte **true**. |
    |**Povolení veřejného přístupu objektu BLOB**| V tomto rychlém startu vyberte **false**. |
    |**Povolení přístupu ke sdíleným klíčům**| V tomto rychlém startu vyberte **true**. |

1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.
1. Stav nasazení zobrazíte tak, že nahoře na obrazovce vyberete ikonu zvonku (oznámení). Uvidíte, že probíhá **nasazení**. Počkejte na dokončení nasazení.

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

V tomto kurzu jste naučili, jak vygenerovat šablonu na portálu Azure Portal a jak ji pomocí tohoto portálu nasadit. Šablona použitá v tomto rychlém startu představuje jednoduchou šablonu s jedním prostředkem Azure. Pokud je šablona složitá, je snazší šablonu vytvořit v nástroji Visual Studio Code nebo v sadě Visual Studio. Další informace o vývoji šablon najdete v naší nové řadě kurzů pro začátečníky:

> [!div class="nextstepaction"]
> [Kurzy pro začátečníky](./template-tutorial-create-first-template.md)
