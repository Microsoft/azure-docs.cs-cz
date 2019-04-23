---
title: Ochrana nových prostředků pomocí zamykání prostředků podrobného plánu
description: Naučte se používat zámky prostředků Azure plány jen pro čtení a neodstraňujte k ochraně nově nasazené prostředky.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d315fb5fe3ce7844946e6a9405a9a5f6a0be8b9d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791605"
---
# <a name="protect-new-resources-with-azure-blueprints-resource-locks"></a>Ochrana nové prostředky podle zámky prostředků Azure podrobné plány

Plány Azure [zámky prostředků](../concepts/resource-locking.md) umožňují zabránit změnám, dokonce i pomocí účtu s nově nasazené prostředky _vlastníka_ role. Tato ochrana je přidat do prostředky vytvořené v rámci artefaktem šablony Resource Manageru v definici podrobného plánu.

Jsou zahrnuty následující kroky:

> [!div class="checklist"]
> - Vytvořte novou definici podrobného plánu
> - Označit jako definice podrobného plánu **publikováno**
> - Definice podrobného plánu přiřadit k existujícímu předplatnému
> - Kontrolovat nové skupiny prostředků
> - Zrušit přiřazení podrobného plánu se odebrat zámky.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, je potřeba předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-new-blueprint-definition"></a>Vytvořit novou definici podrobného plánu

Nejprve vytvořte novou definici podrobného plánu.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Z **Začínáme** stránky na levé straně, vyberte **vytvořit** tlačítko _vytvořit podrobný plán_.

1. Najít **prázdný podrobný plán** podrobného plánu ukázka v horní části stránky a vyberte **začínat prázdný podrobný plán**.

1. Zadejte _Základy_ vzorku podrobného plánu:

   - **Název podrobného plánu**: Zadejte název pro vaši kopii ukázky podrobného plánu. V tomto kurzu použijeme název _uzamčen storageaccount_.
   - **Podrobný plán popis**: Popisuje definice podrobného plánu. Použití "pro testovací plán, podle kterého prostředek uzamčení na nasazených prostředků."
   - **Umístění definice**: Použít na tři tečky a vyberte skupiny pro správu nebo předplatného uložíte na definici podrobného plánu.

1. Vyberte _artefakty_ kartě v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Přidání skupiny prostředků v předplatném: Vyberte **+ přidání artefaktu...**  řádek pod **předplatné**.
   Vyberte 'Skupinu zdrojů' pro _Typ artefaktu_. Nastavte _zobrazovaný název artefaktu_ k **RGtoLock**.
   Nechte pole _název skupiny zdrojů_ a _umístění_ prázdná, ale ujistěte se, že na zaškrtávací políčko je zaškrtnuté u každé vlastnosti tak, aby vznikly **dynamické parametry**. Klikněte na tlačítko **přidat** k přidání tohoto artefaktu do podrobného plánu.

1. Přidáte šablonu v rámci skupiny prostředků: Vyberte **+ přidání artefaktu...** řádek pod **RGtoLock** položka. Vyberte 'šablonu Azure Resource Manager' pro _Artefakt typu_, nastavte  _zobrazovaný název Artefaktu_ na 'StorageAccount' a nechte pole _Popis_ prázdné. Na kartu **šablona** v poli editoru, vložte následující šablonu správce prostředků. Po vložení šablony, vyberte **přidat** přidat tento artefakt podrobný plán.

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

1. Vyberte **uložit koncept** v dolní části stránky.

Tento krok vytvoří definici podrobného plánu v vybraná skupina pro správu nebo předplatného.

Jakmile **ukládá definice podrobného plánu se úspěšně** portálu oznámení se zobrazí, přejít k dalšímu kroku.

## <a name="publish-the-blueprint-definition"></a>Publikovat definici podrobného plánu

Definice podrobného plánu byla vytvořena ve vašem prostředí. Vytvoří se v **koncept** režimu a musí být **publikováno** předtím, než může být přiřazena a nasadit.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru k vyhledání _uzamčen storageaccount_ podrobný plán definice a potom ho vyberte.

1. Vyberte **publikovat podrobný plán** v horní části stránky. V novém podokně na pravé straně zadejte **verze** jako _1.0_. Tato vlastnost je užitečná pro, pokud provedete změny později. Zadejte **změnit poznámky** jako "první verze publikována pro zamknutí prostředků podrobného plánu nasazení." Potom vyberte **publikovat** v dolní části stránky.

Tento krok umožňuje přiřadit podrobný plán k předplatnému. Po publikování se můžete stále provádět změny. Další změny, které vyžadují publikování s novou **verze** hodnotu ke sledování rozdíly mezi různé verze stejné definice podrobného plánu.

Jednou **publikování podrobný plán definici úspěšné** portálu oznámení se zobrazí, přejít k dalšímu kroku.

## <a name="assign-the-blueprint-definition"></a>Přiřadit definici podrobného plánu

Jakmile se definice podrobného plánu se úspěšně **publikováno**, je možné přiřadit na předplatné v rámci se uložila do skupiny pro správu. Tento krok je, kde jsou k dispozici parametry aby každého nasazení definice podrobného plánu jedinečný.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru k vyhledání _uzamčen storageaccount_ podrobný plán definice a potom ho vyberte.

1. Vyberte **přiřazení podrobného plánu** v horní části stránky definice podrobného plánu.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná:** Vyberte jednu nebo několik předplatných, které jsou ve skupině pro správu uloží vaše definice podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: Název je předem vyplněna můžete vycházet z názvu definice podrobného plánu. Chceme, aby toto přiřazení představují uzamčení novou skupinu prostředků, takže upravte název přiřazení k _přiřazení uzamčen – účet úložiště – TestingBPLocks_.
     - **Umístění**: Vyberte oblast pro spravovanou identitu, která má být vytvořen v. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro zdroje Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Pro účely tohoto kurzu vyberte _USA – východ 2_.
     - **Verze definice podrobného plánu**: Vyberte si **publikováno** verze _1.0_ definice podrobného plánu.

   - Uzamknout přiřazení

     Vyberte _jen pro čtení_ režim zámku podrobného plánu. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí nastavení _přiřazenou systémem_ možnost. Další informace najdete v tématu [spravovaných identit](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, pod kterým je definována. Tyto parametry jsou [dynamických parametrů](../concepts/parameters.md#dynamic-parameters) protože máte definovaný během přiřazování podrobný plán. Pro každý artefakt, nastavte hodnotu parametru definovaný v **hodnotu** sloupce.

     |Název artefaktu|Typ artefaktu|Název parametru|Hodnota|Popis|
     |-|-|-|-|-|
     |Skupina prostředků RGtoLock|Skupina prostředků|Název|TestingBPLocks|Definuje název nové skupiny prostředků podrobného plánu zámků za účelem použití.|
     |Skupina prostředků RGtoLock|Skupina prostředků|Umístění|Západní USA 2|Definuje umístění novou skupinu prostředků chcete použít podrobný plán zámků za účelem.|
     |StorageAccount|Šablona Resource Manageru|storageAccountType (účet úložiště)|Standard_GRS|Vyberte SKU úložiště. Výchozí hodnota je _Standard_LRS_.|

1. Po zadání všech parametrů se vyberte **přiřadit** v dolní části stránky.

Tento krok definované prostředky nasadí a nakonfiguruje vybraného **zámek přiřazení**. Zámky podrobného plánu může trvat až 30 minut použít.

Jednou **přiřazení definice podrobného plánu se úspěšně** portálu oznámení se zobrazí, přejít k dalšímu kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Zkontrolovat prostředky nasazené podle přiřazení

Přiřazení vytvořili skupinu prostředků _TestingBPLocks_ a účet úložiště, který je nasazený pomocí artefaktů šablony Resource Manageru. Novou skupinu prostředků a stav vybrané zámku jsou zobrazeny na stránce podrobností přiřazení.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **přiřazené podrobné plány** na levé straně stránky. Pomocí filtru k vyhledání _přiřazení uzamčen – účet úložiště – TestingBPLocks_ podrobný plán přiřazení a pak ho vyberte.

   Na této stránce můžeme vidět přiřazení proběhlo úspěšně a prostředky se nasadily pomocí nový stav zámku podrobného plánu. Pokud aktualizaci přiřazení **operace přiřazení** rozevíracím seznamu zobrazí podrobnosti o nasazení každé definici verze. Skupina prostředků dá kliknout, aby přímo otevřete stránku vlastností.

1. Vyberte **TestingBPLocks** skupinu prostředků.

1. Vyberte **řízení přístupu (IAM)** stránky na levé straně a pak **přiřazení rolí** kartu.

   Tady vidíme, že _přiřazení uzamčen – účet úložiště – TestingBPLocks_ se přiřazení podrobného plánu _vlastníka_ role, protože byla použita k nasazení a uzamčení skupinu prostředků.

1. Vyberte **zamítnout přiřazení** kartu.

   Přiřazení podrobného plánu, který je vytvořen [zamítnout přiřazení](../../../role-based-access-control/deny-assignments.md) nasazený prostředek skupiny k vynucení _jen pro čtení_ režim zámku podrobného plánu. Někdo s příslušná oprávnění přiřazení odepřít brání na _přiřazení rolí_ kartu z konkrétní akci. Přiřazení odepřít ovlivňuje _všechny objekty zabezpečení_.

   Informace o vyloučení objektu zabezpečení z přiřazení odepřít najdete v tématu [plány uzamčení prostředků](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Vyberte přiřazení Odepřít a potom **odepření oprávnění** na levé straně stránky.

   Přiřazení odepřít brání všechny operace s **\*** a **akce** konfigurace, ale umožňuje přístup pro čtení vyloučením  **\* /čtení**prostřednictvím **NotActions**.

1. Azure portal navigace s popisem cesty, vyberte **TestingBPLocks – řízení přístupu (IAM)**. Vyberte **přehled** stránky na levé straně a pak **odstranit skupinu prostředků** tlačítko. Zadejte název _TestingBPLocks_ potvrďte odstranění a vyberte **odstranit** v dolní části podokna.

   Na portálu oznámení **odstranit skupinu prostředků se nepovedlo TestingBPLocks** se zobrazí. Chyba, která uvádí, zatímco váš účet má oprávnění k odstranění skupiny prostředků, přístup se zamítl podle přiřazení podrobného plánu. Mějte na paměti, že jsme vybrali _jen pro čtení_ režim zámku podrobného plánu během přiřazení podrobného plánu. Podrobný plán zámek je chrání účtu s oprávněním, dokonce i _vlastníka_, z příslušný prostředek odstraníte. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

Tyto kroky ukazují, že naše nasazené prostředky jsou teď chráněné s podrobného plánu zámků, které brání nežádoucí odstranění, dokonce i z účtu s oprávněním.

## <a name="unassign-the-blueprint"></a>Zrušit přiřazení podrobný plán

Posledním krokem je odebrat přiřazení definice podrobného plánu. Odebrání přiřazení neodebere dokázal artefakty.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **přiřazené podrobné plány** na levé straně stránky. Pomocí filtru k vyhledání _přiřazení uzamčen – účet úložiště – TestingBPLocks_ podrobný plán přiřazení a pak ho vyberte.

1. Vyberte **zrušit přiřazení podrobného plánu** tlačítko v horní části stránky. Přečtěte si upozornění v dialogovém okně potvrzení a pak vyberte **OK**.

   Přiřazení podrobného plánu odebrat se taky odeberou zámky podrobného plánu. Vytvořené prostředky můžete odstranit znovu pomocí účtu s oprávněními.

1. Vyberte **skupiny prostředků** z nabídky Azure vyberte **TestingBPLocks**.

1. Vyberte **řízení přístupu (IAM)** stránky na levé straně a pak **přiřazení rolí** kartu.

Zabezpečení pro skupinu prostředků, ukazuje, že přiřazení podrobného plánu už má _vlastníka_ přístup.

Jakmile **přiřazení podrobného plánu odebrání proběhlo úspěšně.** portálu oznámení se zobrazí, přejít k dalšímu kroku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu, odstraňte následující prostředky:

- Skupina prostředků _TestingBPLocks_
- Podrobný plán definice _uzamčen účet úložiště_

## <a name="next-steps"></a>Další postup

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)