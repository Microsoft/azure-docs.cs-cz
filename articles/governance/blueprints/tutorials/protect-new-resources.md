---
title: 'Kurz: Ochrana nových prostředků pomocí zámků'
description: V tomto kurzu použijete možnosti uzamčení prostředků Azure Blueprints pouze pro čtení a Neodstraňujte k ochraně nově nasazených prostředků.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: ee57ff0c08f4fb8aa710dd2fa4dcef664484973d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74327446"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Kurz: Ochrana nových prostředků pomocí zámků prostředků Azure Blueprints

Díky [zámkům prostředků](../concepts/resource-locking.md)Azure Blueprints můžete chránit nově nasazené prostředky před manipulací, a to i pomocí účtu s rolí _Vlastník._ Tuto ochranu můžete přidat do podrobných definic prostředků vytvořených artefaktem šablony Správce prostředků.

V tomto kurzu provedete tyto kroky:

> [!div class="checklist"]
> - Vytvoření definice podrobného plánu
> - Označení definice podrobného plánu jako **publikované**
> - Přiřazení definice podrobného plánu k existujícímu předplatnému
> - Kontrola nové skupiny prostředků
> - Zrušení přiřazení podrobného plánu k odebrání zámků

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete.

## <a name="create-a-blueprint-definition"></a>Vytvoření definice podrobného plánu

Nejprve vytvořte definici podrobného plánu.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce **Začínáme** vlevo vyberte **Vytvořit v** části **Vytvořit podrobný plán**.

1. Ukázka **podrobného** plánu najdete v horní části stránky. Vyberte **Začít s prázdným podrobným plánem**.

1. Tyto informace zadejte na kartě **Základy:**

   - **Název podrobného plánu**: Zadejte název pro kopii ukázky podrobného plánu. Pro účely tohoto kurzu použijeme název **uzamčený účet úložiště**.
   - **Popis podrobného plánu**: Přidejte popis definice podrobného plánu. Použití **pro testování uzamčení prostředků podrobného plánu na nasazených prostředcích**.
   - **Umístění definice**: Vyberte tlačítko se třemi tečkami (...) a pak vyberte skupinu pro správu nebo předplatné, do které chcete uložit definici podrobného plánu.

1. Vyberte kartu **Artefakty** v horní části stránky nebo vyberte **Další: Artefakty** v dolní části stránky.

1. Přidání skupiny prostředků na úrovni předplatného:
   1. V části **Předplatné**vyberte řádek **Přidat artefakt** .
   1. V části **Typ artefaktu**vyberte **Skupina prostředků** .
   1. Nastavte **zobrazovaný název artefaktu** na **RGtoLock**.
   1. Ponechte pole **Název skupiny zdrojů** a **umístění** prázdná, ale ujistěte se, že je u každé vlastnosti zaškrtnuto políčko, aby byly **dynamické parametry**.
   1. Vyberte **Přidat,** chcete-li přidat artefakt do podrobného plánu.

1. Přidání šablony do skupiny prostředků:
   1. Vyberte řádek **Přidat artefakt** pod položkou **RGtoLock.**
   1. V části Typ **artefaktu**vyberte **šablonu Správce prostředků Azure** , nastavte **zobrazovaný název artefaktu** na **Účet úložiště**a **ponechte popis** prázdný.
   1. Na kartě **Šablona** vložte do pole editoru následující šablonu Správce prostředků.
      Po vložení do šablony vyberte **Přidat** a přidejte artefakt do podrobného plánu.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

1. V dolní části stránky vyberte **Uložit koncept.**

Tento krok vytvoří definici podrobného plánu ve vybrané skupině pro správu nebo předplatné.

Po **uložení definice podrobného plánu úspěšné** oznámení portálu, přejděte k dalšímu kroku.

## <a name="publish-the-blueprint-definition"></a>Publikování definice podrobného plánu

Vaše definice podrobného plánu byla nyní vytvořena ve vašem prostředí. Je vytvořen v režimu **konceptu** a musí být publikovánpřed jeho přiřazením a nasazením.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Definice podrobného plánu** vlevo. Pomocí filtrů vyhledejte definici podrobného plánu **účtu uzamčeného úložiště** a vyberte ji.

1. V horní části stránky vyberte **Publikovat podrobný plán.** V novém podokně vpravo zadejte **hodnotu 1.0** jako **verzi**. Tato vlastnost je užitečná, pokud provedete změnu později. Zadejte **poznámky ke změně**, například **První verze publikovaná pro uzamčení prostředků nasazeného podrobného plánu**. Pak vdolní části stránky vyberte **Publikovat.**

Tento krok umožňuje přiřadit podrobný plán k odběru. Po publikování definice podrobného plánu můžete stále provádět změny. Pokud provedete změny, budete muset publikovat definici s novou hodnotu verze sledovat rozdíly mezi verzemi stejné definice podrobného plánu.

Po **publikování definice podrobného plánu úspěšné** oznámení portálu, přejděte k dalšímu kroku.

## <a name="assign-the-blueprint-definition"></a>Přiřazení definice podrobného plánu

Po publikování definice podrobného plánu ji můžete přiřadit k předplatnému v rámci skupiny pro správu, do které jste ji uložili. V tomto kroku zadáte parametry, aby každé nasazení definice podrobného plánu jedinečné.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Definice podrobného plánu** vlevo. Pomocí filtrů vyhledejte definici podrobného plánu **účtu uzamčeného úložiště** a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **Přiřadit podrobný plán.**

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - **Základy**

     - **Odběry:** Vyberte jedno nebo více předplatných, které jsou ve skupině pro správu, do které jste uložili definici podrobného plánu. Pokud vyberete více než jedno předplatné, bude pro každé předplatné vytvořeno přiřazení pomocí zadávaných parametrů.
     - **Název přiřazení**: Název je předem vyplněn ý na základě názvu definice podrobného plánu. Chceme, aby toto přiřazení představovalo uzamčení nové skupiny prostředků, proto změňte název přiřazení na **název účtu uzamčeného úložiště-testingBPLocks**.
     - **Umístění**: Vyberte oblast, ve které chcete vytvořit spravovanou identitu. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       V tomto kurzu vyberte **možnost Východní USA 2**.
     - **Verze definice podrobného plánu**: Vyberte publikovanou verzi **1.0** definice podrobného plánu.

   - **Přiřazení zámku**

     Vyberte režim uzamčení podrobného plánu **jen pro čtení.** Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

   - **Spravovaná identita**

     Použijte výchozí možnost: **Přiřazeno systému**. Další informace naleznete v tématu [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Parametry artefaktů**

     Parametry definované v této části platí pro artefakt, pod kterým jsou definovány. Tyto parametry jsou [dynamické parametry,](../concepts/parameters.md#dynamic-parameters) protože jsou definovány během přiřazení podrobného plánu. Pro každý artefakt nastavte hodnotu parametru na co se zobrazí ve sloupci **Hodnota.**

     |Název artefaktu|Typ artefaktu|Název parametru|Hodnota|Popis|
     |-|-|-|-|-|
     |Skupina prostředků RGtoLock|Skupina prostředků|Name (Název)|TestingBPLocks|Definuje název nové skupiny prostředků, na kterou chcete použít zámky podrobného plánu.|
     |Skupina prostředků RGtoLock|Skupina prostředků|Umístění|USA – západ 2|Definuje umístění nové skupiny prostředků, na které se mají použít zámky podrobného plánu.|
     |StorageAccount|Šablona Resource Manageru|storageAccountType (StorageAccount)|Standard_GRS|Skladová položka úložiště. Výchozí hodnota je _Standard_LRS_.|

1. Po zadání všech parametrů **vyberte** Přiřadit v dolní části stránky.

Tento krok nasadí definované prostředky a nakonfiguruje vybrané **přiřazení zámku**. Použití zámků podrobného plánu může trvat až 30 minut.

Po **přiřazení definice podrobného plánu úspěšné** oznámení portálu, přejděte k dalšímu kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Kontrola prostředků nasazených přiřazením

Přiřazení vytvoří skupinu prostředků _TestingBPLocks_ a účet úložiště nasazený artefaktem šablony Správce prostředků. Nová skupina prostředků a vybraný stav zámku se zobrazí na stránce podrobností o přiřazení.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Přiřazené podrobné plány** vlevo. Pomocí filtrů vyhledejte přiřazení plánu **uzamčeného úložiště-storage-TestingBPLocks** a vyberte ho.

   Na této stránce vidíme, že přiřazení proběhlo úspěšně a že prostředky byly nasazeny s novým stavem zámku podrobného plánu. Pokud je přiřazení aktualizováno, rozevírací seznam **Operace přiřazení** zobrazuje podrobnosti o nasazení každé verze definice. Můžete vybrat skupinu prostředků a otevřít stránku vlastností.

1. Vyberte skupinu prostředků **TestingBPLocks.**

1. Vyberte stránku **řízení přístupu (IAM)** vlevo. Pak vyberte kartu **Přiřazení rolí.**

   Zde vidíme, že _přiřazení uzamčený úložiště account-TestingBPLocks_ podrobného plánu přiřazení má roli _vlastníka._ Má tuto roli, protože tato role byla použita k nasazení a uzamčení skupiny prostředků.

1. Vyberte kartu **Odepřít přiřazení.**

   Přiřazení podrobného plánu vytvořilo [přiřazení odepření](../../../role-based-access-control/deny-assignments.md) v nasazené skupině prostředků k vynucení režimu uzamčení podrobného plánu **jen pro čtení.** Odepřít přiřazení zabrání uživateli s příslušnými **právy** na kartě Přiřazení rolí v přijetí konkrétních akcí. Přiřazení odepřít ovlivňuje _všechny objekty zabezpečení_.

   Informace o vyloučení objektu zabezpečení z přiřazení odepřít naleznete v [tématu podrobné plány uzamčení prostředků](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Vyberte odepřít přiřazení a pak vyberte stránku **Odepřená oprávnění** vlevo.

   Přiřazení odepřít brání všechny operace **\*** s a **akce** konfigurace, ale umožňuje přístup pro čtení vyloučením ** \*/read** via **NotActions**.

1. V popisku cesty portálu Azure vyberte **TestingBPLocks – řízení přístupu (IAM).** Pak vyberte stránku **Přehled** vlevo a potom tlačítko **Odstranit skupinu prostředků.** Zadejte název **TestingBPLocks,** abyste potvrdili odstranění, a pak v dolní části podokna vyberte **Odstranit.**

   Zobrazí se oznámení **portálu Odstranit skupinu prostředků TestingBPLocks.** Chyba uvádí, že i když váš účet má oprávnění k odstranění skupiny prostředků, přístup je odepřen přiřazení podrobného plánu. Nezapomeňte, že jsme během přiřazení podrobného plánu vybrali režim uzamčení podrobného plánu **jen pro čtení.** Zámek podrobného plánu zabrání účtu s oprávněním, a to i _Vlastník_, odstranění prostředku. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

Tyto kroky ukazují, že naše nasazené prostředky jsou nyní chráněny pomocí zámků podrobného plánu, které brání nechtěnému odstranění, a to i z účtu, který má oprávnění k odstranění prostředků.

## <a name="unassign-the-blueprint"></a>Zrušení přiřazení podrobného plánu

Posledním krokem je odebrání přiřazení definice podrobného plánu. Odebránípřiřazení neodebere přidružené artefakty.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Přiřazené podrobné plány** vlevo. Pomocí filtrů vyhledejte přiřazení plánu **uzamčeného úložiště-storage-TestingBPLocks** a vyberte ho.

1. V horní části stránky vyberte **Zrušit přiřazení podrobného** plánu. Přečtěte si upozornění v potvrzovacím dialogovém okně a pak vyberte **OK**.

   Při odebrání přiřazení podrobného plánu jsou odebrány také zámky podrobného plánu. Prostředky lze znovu odstranit pomocí účtu s příslušnými oprávněními.

1. V nabídce Azure vyberte **skupiny prostředků** a pak vyberte **TestingBPLocks**.

1. V levé části vyberte stránku **ovládacího prvku Přístupu (IAM)** a pak vyberte kartu **Přiřazení rolí.**

Zabezpečení pro skupinu prostředků ukazuje, že přiřazení podrobného plánu již nemá přístup _vlastníka._

Po **odebrání přiřazení podrobného plánu úspěšné** oznámení portálu se zobrazí, přejděte k dalšímu kroku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu odstraňte tyto prostředky:

- Skupina prostředků _TestingBPLocks_
- Účet _uzamčení úložiště_ definice podrobného plánu

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak chránit nové prostředky nasazené pomocí Azure Blueprints. Další informace o Azure Blueprints, pokračujte v článku životního cyklu podrobného plánu.

> [!div class="nextstepaction"]
> [Další informace o životním cyklu podrobného plánu](../concepts/lifecycle.md)