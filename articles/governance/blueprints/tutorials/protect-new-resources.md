---
title: Kurz – chránit nové prostředky podle zámky prostředků podrobného plánu
description: V tomto kurzu se naučíte použít jen pro čtení možnosti zámky prostředků Azure plány a neodstraňujte k ochraně nově nasazené prostředky.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 274c437acd8df50d631727fc352c4b9ebecead18
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479970"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Kurz: Ochrana nové prostředky podle zámky prostředků Azure podrobné plány

S plány Azure [zámky prostředků](../concepts/resource-locking.md), nově nasazené prostředky můžete chránit z se manipulovalo, dokonce i pomocí účtu s _vlastníka_ role. Tuto ochranu můžete přidat do definice podrobného plánu prostředky vytvořené v rámci artefaktů šablony Resource Manageru.

V tomto kurzu dokončíte tyto kroky:

> [!div class="checklist"]
> - Vytvořit definici podrobného plánu
> - Označit jako definice podrobného plánu **publikováno**
> - Definice podrobného plánu přiřadit k existujícímu předplatnému
> - Kontrolovat nové skupiny prostředků
> - Zrušit přiřazení podrobného plánu se odebrat zámky.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-blueprint-definition"></a>Vytvořit definici podrobného plánu

Nejprve vytvořte definici podrobného plánu.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Na **Začínáme** stránky na levé straně vyberte **vytvořit** pod **vytvořit podrobný plán**.

1. Najít **prázdný podrobný plán** podrobného plánu ukázka v horní části stránky. Vyberte **začínat prázdný podrobný plán**.

1. Tyto informace zadat **Základy** kartu:

   - **Název podrobného plánu**: Zadejte název pro vaši kopii ukázky podrobného plánu. V tomto kurzu použijeme název **uzamčen storageaccount**.
   - **Podrobný plán popis**: Přidáte popis definice podrobného plánu. Použití **pro testovací plán, podle kterého prostředků uzamčení na nasazených prostředků**.
   - **Umístění definice**: Vyberte tlačítko se třemi tečkami (...) a pak vyberte skupiny pro správu nebo předplatného uložíte na definici podrobného plánu.

1. Vyberte **artefakty** kartě v horní části stránky, nebo vyberte **Další: Artefakty** v dolní části stránky.

1. Přidáte skupinu prostředků na úrovni předplatného:
   1. Vyberte **přidání artefaktu** řádek pod **předplatné**.
   1. Vyberte **skupiny prostředků** pod **Typ artefaktu**.
   1. Nastavte **zobrazovaný název artefaktu** k **RGtoLock**.
   1. Nechte **název skupiny prostředků** a **umístění** pole prázdná, ale ujistěte se, že zaškrtávací políčko zaškrtnuto na každou vlastnost, aby se daly **dynamických parametrů**.
   1. Vyberte **přidat** přidání artefaktu do podrobný plán.

1. Přidáte šablonu v rámci skupiny prostředků:
   1. Vyberte **přidání artefaktu** řádek pod **RGtoLock** položka. 
   1. Vyberte **šablony Azure Resource Manageru** pod **Typ artefaktu**, nastavte **zobrazovaný název artefaktu** k **StorageAccount**a nechat  **Popis** prázdné. 
   1. Na **šablony** kartu, vložte následující šablony Resource Manageru do editoru pole. Po vložení v šabloně vyberte **přidat** přidání artefaktu do podrobný plán.

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

Po **ukládá definice podrobného plánu se úspěšně** portálu oznámení se zobrazí, přejděte k dalšímu kroku.

## <a name="publish-the-blueprint-definition"></a>Publikovat definici podrobného plánu

Definice podrobného plánu byla vytvořena ve vašem prostředí. Vytvoří se v **koncept** režimu a musí být zveřejněna před může být přiřazena a nasadit.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru k vyhledání **uzamčen storageaccount** podrobný plán definice a pak ho vyberte.

1. Vyberte **publikovat podrobný plán** v horní části stránky. V novém podokně na pravé straně zadejte **1.0** jako **verze**. Tato vlastnost je užitečná, pokud později provést změnu. Zadejte **změnit poznámky**, jako například **první verzi publikovaných pro zamknutí prostředků podrobného plánu nasazení**. Potom vyberte **publikovat** v dolní části stránky.

Tento krok umožňuje přiřadit podrobný plán k předplatnému. Po publikování definice podrobného plánu se můžete stále provádět změny. Pokud změníte, budete muset publikovat definice s novou hodnotou verze ke sledování rozdíly mezi verzemi stejné definice podrobného plánu.

Po **publikování podrobný plán definici úspěšné** portálu oznámení se zobrazí, přejděte k dalšímu kroku.

## <a name="assign-the-blueprint-definition"></a>Přiřadit definici podrobného plánu

Po publikování se definice podrobného plánu, ji můžete přiřadit k předplatnému ve skupině správy, kde jste ho uložili. V tomto kroku poskytnete parametrů, aby byl každé nasazení definice podrobného plánu jedinečný.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru k vyhledání **uzamčen storageaccount** podrobný plán definice a pak ho vyberte.

1. Vyberte **přiřazení podrobného plánu** v horní části stránky definice podrobného plánu.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - **Základy**

     - **Předplatná:** Vyberte jednu nebo několik předplatných, které jsou ve skupině správy, kam jste uložili vaší definice podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se přiřazení pro každé předplatné, pomocí parametrů, které zadáte.
     - **Název přiřazení**: Název je předem vyplněna na základě názvu definice podrobného plánu. Chceme, aby toto přiřazení představují uzamčení novou skupinu prostředků, takže upravte název přiřazení k **přiřazení uzamčen – účet úložiště – TestingBPLocks**.
     - **Umístění**: Vyberte oblast, ve kterém chcete vytvořit spravovanou identitu. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro zdroje Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Pro účely tohoto kurzu vyberte **USA – východ 2**.
     - **Verze definice podrobného plánu**: Vyberte publikovanou verzi **1.0** definice podrobného plánu.

   - **Přiřazení zámku**

     Vyberte **jen pro čtení** režim zámku podrobného plánu. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

   - **Spravovaná identita**

     Použijte výchozí možnost: **Systém přiřadil**. Další informace najdete v tématu [spravovaných identit](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Parametry artefaktu**

     Parametry definované v této části se vztahují na artefakt, pod kterým už definované. Tyto parametry jsou [dynamických parametrů](../concepts/parameters.md#dynamic-parameters) vzhledem k tomu, že máte definované při přiřazování podrobný plán. Pro každý artefakt, nastavte hodnotu parametru na co se zobrazí v **hodnotu** sloupce.

     |Název artefaktu|Typ artefaktu|Název parametru|Hodnota|Popis|
     |-|-|-|-|-|
     |Skupina prostředků RGtoLock|Skupina prostředků|Název|TestingBPLocks|Definuje název nové skupiny prostředků podrobného plánu zámků za účelem použití.|
     |Skupina prostředků RGtoLock|Skupina prostředků|Location|Západní USA 2|Definuje umístění novou skupinu prostředků chcete použít podrobný plán zámků za účelem.|
     |StorageAccount|Šablona Resource Manageru|storageAccountType (účet úložiště)|Standard_GRS|SKU úložiště. Výchozí hodnota je _Standard_LRS_.|

1. Po zadání všech parametrů, vyberte **přiřadit** v dolní části stránky.

Tento krok definované prostředky nasadí a nakonfiguruje vybraného **zámek přiřazení**. Může trvat až 30 minut, chcete-li použít podrobný plán zámky.

Po **přiřazení definice podrobného plánu se úspěšně** portálu oznámení se zobrazí, přejděte k dalšímu kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Zkontrolovat prostředky nasazené podle přiřazení

Přiřazení vytvoří skupinu prostředků _TestingBPLocks_ a účet úložiště, který je nasazený pomocí artefaktů šablony Resource Manageru. Novou skupinu prostředků a stav vybrané uzamčení se zobrazí na stránce podrobností přiřazení.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **přiřazené podrobné plány** na levé straně stránky. Pomocí filtru k vyhledání **přiřazení uzamčen – účet úložiště – TestingBPLocks** podrobný plán přiřazení a pak ho vyberte.

   Na této stránce vidíme, že přiřazení proběhlo úspěšně a že prostředky byly nasazeny s nový stav zámku podrobného plánu. Pokud aktualizaci přiřazení **operace přiřazení** rozevíracím seznamu zobrazí podrobnosti o nasazení každé definici verze. Můžete vybrat skupinu prostředků a otevřete stránku vlastností.

1. Vyberte **TestingBPLocks** skupinu prostředků.

1. Vyberte **řízení přístupu (IAM)** na levé straně stránky. Vyberte **přiřazení rolí** kartu.

   Tady vidíme, že _přiřazení uzamčen – účet úložiště – TestingBPLocks_ se přiřazení podrobného plánu _vlastníka_ role. Tato role nemá, protože tato role se používá k nasazení a uzamčení skupinu prostředků.

1. Vyberte **zamítnout přiřazení** kartu.

   Přiřazení podrobného plánu, který je vytvořen [zamítnout přiřazení](../../../role-based-access-control/deny-assignments.md) nasazený prostředek skupiny k vynucení **jen pro čtení** režim zámku podrobného plánu. Někdo s příslušná oprávnění přiřazení odepřít brání na **přiřazení rolí** kartu z konkrétní akci. Přiřazení odepřít ovlivňuje _všechny objekty zabezpečení_.

   Informace o vyloučení objektu zabezpečení z přiřazení odepřít najdete v tématu [plány uzamčení prostředků](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Vyberte přiřazení Odepřít a pak vyberte **odepření oprávnění** na levé straně stránky.

   Přiřazení odepřít brání všechny operace s **\*** a **akce** konfigurace, ale umožňuje přístup pro čtení vyloučením  **\* /čtení**prostřednictvím **NotActions**.

1. V Azure portal navigace s popisem cesty, vyberte **TestingBPLocks – řízení přístupu (IAM)** . Vyberte **přehled** stránky na levé straně a pak **odstranit skupinu prostředků** tlačítko. Zadejte název **TestingBPLocks** potvrďte odstranění a pak vyberte **odstranit** v dolní části podokna.

   Na portálu oznámení **odstranit skupinu prostředků se nepovedlo TestingBPLocks** se zobrazí. Chyba uvádí, že i když váš účet má oprávnění k odstranění skupiny prostředků, přístup se zamítl podle přiřazení podrobného plánu. Mějte na paměti, že jsme vybrali **jen pro čtení** režim zámku podrobného plánu během přiřazení podrobného plánu. Podrobný plán zámek je chrání účtu s oprávněním, dokonce i _vlastníka_, z příslušný prostředek odstraníte. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

Tyto kroky ukazují, že naše nasazené prostředky jsou teď chráněné s podrobného plánu zámky, které znemožňují, aby se nežádoucí, dokonce i z účtu, který má oprávnění k odstranění prostředky.

## <a name="unassign-the-blueprint"></a>Zrušit přiřazení podrobný plán

Posledním krokem je odebrat přiřazení definice podrobného plánu. Odebrání přiřazení neodebere přidružené artefakty.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **přiřazené podrobné plány** na levé straně stránky. Pomocí filtru k vyhledání **přiřazení uzamčen – účet úložiště – TestingBPLocks** podrobný plán přiřazení a pak ho vyberte.

1. Vyberte **zrušit přiřazení podrobného plánu** v horní části stránky. Přečtěte si upozornění v dialogovém okně potvrzení a pak vyberte **OK**.

   Při odebrání přiřazení podrobného plánu se taky odeberou zámky podrobného plánu. Prostředky můžete odstranit znovu pomocí účtu s příslušnými oprávněními.

1. Vyberte **skupiny prostředků** z nabídky na Azure a pak vyberte **TestingBPLocks**.

1. Vyberte **řízení přístupu (IAM)** stránky na levé straně a pak vyberte **přiřazení rolí** kartu.

Zabezpečení pro skupinu prostředků, ukazuje, že přiřazení podrobného plánu už má _vlastníka_ přístup.

Po **přiřazení podrobného plánu odstranění bylo úspěšné** portálu oznámení se zobrazí, přejděte k dalšímu kroku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s tímto kurzem, odstraňte tyto prostředky:

- Skupina prostředků _TestingBPLocks_
- Podrobný plán definice _uzamčen účet úložiště_

## <a name="next-steps"></a>Další postup

- Další informace o [podrobný plán životního cyklu](../concepts/lifecycle.md).
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Zjistěte, jak používat [podrobný plán uzamčení prostředků](../concepts/resource-locking.md).
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- [Řešení potíží s](../troubleshoot/general.md) během přiřazování podrobný plán.
