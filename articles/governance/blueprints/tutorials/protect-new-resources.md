---
title: 'Kurz: ochrana nových prostředků pomocí zámků'
description: V tomto kurzu použijete možnosti zámků prostředků Azure modrotisky jen pro čtení a neodstraňujte je k ochraně nově nasazených prostředků.
ms.date: 03/08/2021
ms.topic: tutorial
ms.openlocfilehash: 87da0f5a1fff2feb103b32533c8d314fb7690f80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102485737"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Kurz: ochrana nových prostředků pomocí zámků prostředků Azure modrotisky

Pomocí [zámků prostředků](../concepts/resource-locking.md)Azure modrotisky můžete chránit nově nasazené prostředky proti poškození, a to i pomocí účtu s rolí _vlastníka_ . Tuto ochranu můžete přidat v definicích podrobného plánu prostředků vytvořených artefaktem šablony Azure Resource Manager (ARM Template). Zámek prostředků podrobného plánu se nastavuje během přiřazení podrobného plánu.

V tomto kurzu provedete tyto kroky:

> [!div class="checklist"]
> - Vytvořit definici podrobného plánu
> - Označení definice podrobného plánu jako **publikované**
> - Přiřazení definice podrobného plánu k existujícímu předplatnému (**nastavení zámků prostředků**)
> - Kontrola nové skupiny prostředků
> - Zrušení přiřazení podrobného plánu k odebrání zámků

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-a-blueprint-definition"></a>Vytvořit definici podrobného plánu

Nejprve vytvořte definici podrobného plánu.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Na stránce **Začínáme** na levé straně vyberte **vytvořit** v části vytvořit podrobný **plán**.

1. V horní části stránky vyhledejte ukázkový ukázkový **podrobný plán.** Vyberte možnost **začít s prázdným plánem**.

1. Na kartě **základy** zadejte tyto informace:

   - **Název** podrobného plánu: zadejte název vaší kopie ukázky podrobného plánu. V tomto kurzu použijeme název **uzamčený-storageaccount**.
   - **Popis** podrobného plánu: přidejte popis definice podrobného plánu. Slouží **k testování uzamykání prostředků podrobného plánu u nasazených prostředků**.
   - **Umístění definice**: vyberte tlačítko se třemi tečkami (...) a pak vyberte skupinu pro správu nebo předplatné, do které chcete uložit definici podrobného plánu.

1. V horní části stránky vyberte kartu **artefakty** nebo vyberte **Další: artefakty** v dolní části stránky.

1. Přidat skupinu prostředků na úrovni předplatného:
   1. V části **předplatné** vyberte řádek **Přidat artefakt** .
   1. V části **Typ artefaktu** vyberte **Skupina prostředků** .
   1. Nastavte **Zobrazovaný název artefaktu** na **RGtoLock**.
   1. Pole **název skupiny prostředků** a **umístění** ponechte prázdné, ale ujistěte se, že je zaškrtnuté políčko u každé vlastnosti, aby byly **dynamické parametry**.
   1. Vyberte **Přidat** a přidejte artefakt do podrobného plánu.

1. Přidejte šablonu do skupiny prostředků:
   1. V položce **RGtoLock** vyberte řádek **Přidat artefakt** .
   1. V části **Typ artefaktu** vyberte **šablonu Azure Resource Manager** , nastavte **Zobrazovaný název artefaktu** na **StorageAccount** a nechejte **Popis** prázdný.
   1. Na kartě **Šablona** vložte do pole Editor následující šablonu ARM. Po vložení do šablony vyberte **Přidat** a přidejte artefakt do podrobného plánu.

      > [!NOTE]
      > Tento krok definuje prostředky, které mají být nasazeny, které jsou uzamčeny zámkem prostředků podrobného plánu, ale nezahrnují zámky prostředků podrobného plánu. Zámky prostředků podrobného plánu jsou nastaveny jako parametr přiřazení podrobného plánu.

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

1. V dolní části stránky vyberte **Uložit koncept** .

Tento krok vytvoří definici podrobného plánu ve vybrané skupině nebo předplatném pro správu.

Po úspěšném zobrazení oznámení na portálu pro **uložení definice** podrobného plánu se dostanete k dalšímu kroku.

## <a name="publish-the-blueprint-definition"></a>Publikovat definici podrobného plánu

Ve vašem prostředí se teď vytvořila vaše definice podrobného plánu. Je vytvořená v režimu **konceptu** a musí být publikována před tím, než bude možné ji přiřadit a nasadit.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů vyhledejte definici **storageaccount podrobného** plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. V pravém podokně vpravo zadejte **1,0** jako **verzi**. Tato vlastnost je užitečná, pokud provedete změnu později. Zadejte **poznámky ke změnám**, jako je například **první verze publikovaná pro uzamykání prostředků nasazených v podrobném** plánu. Na konci stránky pak vyberte **Publikovat**.

Tento krok umožňuje přiřadit podrobný plán k předplatnému. Po publikování definice podrobného plánu můžete provádět změny. Pokud provedete změny, je nutné publikovat definici s novou hodnotou verze ke sledování rozdílů mezi verzemi stejné definice podrobného plánu.

Po **úspěšném** zobrazení oznámení na portálu pro definici podrobného plánu publikování přejdete k dalšímu kroku.

## <a name="assign-the-blueprint-definition"></a>Přiřadit definici podrobného plánu

Po publikování definice podrobného plánu ji můžete přiřadit k předplatnému v rámci skupiny pro správu, do které jste ji uložili. V tomto kroku zadáte parametry, které zajistí, aby každé nasazení definice podrobného plánu bylo jedinečné.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů vyhledejte definici **storageaccount podrobného** plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **Přiřadit podrobný plán**.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - **Základy**

     - **Předplatná**: vyberte jedno nebo více předplatných, které jsou ve skupině pro správu, do které jste uložili definici podrobného plánu. Pokud vyberete více než jedno předplatné, bude pro každé předplatné vytvořeno přiřazení, a to pomocí zadaných parametrů.
     - **Název přiřazení**: název je předem vyplněný na základě názvu definice podrobného plánu. Chceme, aby toto přiřazení představovalo uzamykání nové skupiny prostředků, proto změňte název přiřazení na **přiřazení-Locked-storageaccount-TestingBPLocks**.
     - **Umístění**: Vyberte oblast, ve které chcete vytvořit spravovanou identitu. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Pro tento kurz vyberte **východní USA 2**.
     - **Verze definice** podrobného plánu: vyberte publikovanou verzi **1,0** definice podrobného plánu.

   - **Zamknout přiřazení**

     Vyberte režim zámku **podrobného** plánu. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

     > [!NOTE]
     > Tento krok nakonfiguruje zámek prostředků podrobného plánu u nově nasazených prostředků.

   - **Spravovaná identita**

     Použijte výchozí možnost: **přiřazený systém**. Další informace najdete v tématu [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Parametry artefaktů**

     Parametry definované v této části se vztahují na artefakt, ve kterém jsou definovány. Tyto parametry jsou [dynamické parametry](../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Pro každý artefakt nastavte hodnotu parametru tak, aby se zobrazila ve sloupci **hodnota** .

     |Název artefaktu|Typ artefaktu|Název parametru|Hodnota|Popis|
     |-|-|-|-|-|
     |Skupina prostředků RGtoLock|Skupina prostředků|Name|TestingBPLocks|Definuje název nové skupiny prostředků, na kterou se mají použít zámky podrobného plánu.|
     |Skupina prostředků RGtoLock|Skupina prostředků|Umístění|Západní USA 2|Definuje umístění nové skupiny prostředků, na kterou se mají použít zámky podrobného plánu.|
     |StorageAccount|Šablona Resource Manageru|storageAccountType (StorageAccount)|Standard_GRS|SKU úložiště. Výchozí hodnota je _Standard_LRS_.|

1. Po zadání všech parametrů vyberte **přiřadit** v dolní části stránky.

Tento krok nasadí definované prostředky a nakonfiguruje vybrané **přiřazení zámku**. Použít zámky podrobného plánu může trvat až 30 minut.

Až se zobrazí oznámení na portálu **přiřazení definice** podrobného plánu, přejít k dalšímu kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Kontrola prostředků nasazených přiřazením

Přiřazením se vytvoří skupina prostředků _TestingBPLocks_ a účet úložiště nasazený artefaktem šablony ARM. Nová skupina prostředků a vybraný stav uzamčení se zobrazí na stránce Podrobnosti přiřazení.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Na levé straně vyberte stránku **přiřazené plány** . Pomocí filtrů vyhledejte přiřazení **podrobného plánu přiřazení storageaccount-TestingBPLocks** a pak ho vyberte.

   Na této stránce vidíte, že přiřazení bylo úspěšné a že se prostředky nasadily s novým stavem zámku podrobného plánu. Pokud je přiřazení aktualizováno, rozevírací seznam **operace přiřazení** zobrazí podrobnosti o nasazení každé verze definice. Můžete vybrat skupinu prostředků a otevřít stránku vlastností.

1. Vyberte skupinu prostředků **TestingBPLocks** .

1. Na levé straně vyberte stránku **řízení přístupu (IAM)** . Pak vyberte kartu **přiřazení rolí** .

   Tady vidíte, že přiřazení role vlastníka pro přiřazení _storageaccount-Locked-TestingBPLocks_ má roli _Owner_ . Má tuto roli, protože tato role se použila k nasazení a uzamčení skupiny prostředků.

1. Vyberte kartu **Odepřít přiřazení** .

   Přiřazení podrobného plánu vytvořilo [přiřazení odepřít](../../../role-based-access-control/deny-assignments.md) pro nasazenou skupinu prostředků, aby se vynutil režim zámku **podrobného plánu pro čtení** . Přiřazení zamítnutí brání osobě s odpovídajícími právy na kartě **přiřazení rolí** v tom, aby převzal konkrétní akce. Přiřazení zamítnutí má vliv na _všechny objekty zabezpečení_.

   Informace o vyloučení objektu zabezpečení z přiřazení zamítnutí najdete v tématu [dezamykání prostředků modrotisky](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Vyberte přiřazení odepřít a na levé straně vyberte stránku **Zamítnutá oprávnění** .

   Přiřazení zamítnutí brání všem operacím s konfigurací **\* Akce *_ a _*** , ale umožňuje přístup pro čtení s výjimkou **\* /Read** prostřednictvím **NotActions**.

1. V Azure Portal s popisem cesty vyberte **TestingBPLocks-Access Control (IAM)**. Pak na levé straně vyberte stránku **Přehled** a pak klikněte na tlačítko **Odstranit skupinu prostředků** . Zadáním názvu **TestingBPLocks** potvrďte odstranění a potom v dolní části podokna vyberte **Odstranit** .

   Zobrazí se dialogové okno **Odstranit TestingBPLocks skupinu prostředků** oznámení o portálu. Chyba uvádí, že i když má váš účet oprávnění k odstranění skupiny prostředků, přístup je odepřen přiřazením podrobného plánu. Mějte na paměti, že při přiřazení podrobného plánu jsme vybrali režim zámku podrobný plán **pro čtení** . Zámek podrobného plánu zabraňuje účtu s oprávněním, dokonce i _vlastníkem_, z odstranění prostředku. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

Tyto kroky ukazují, že naše nasazené prostředky jsou teď chráněné pomocí zámků podrobného plánu, které brání nechtěnému odstranění, a to i z účtu, který má oprávnění k odstranění těchto prostředků.

## <a name="unassign-the-blueprint"></a>Zrušit přiřazení podrobného plánu

Posledním krokem je odebrání přiřazení definice podrobného plánu. Odebráním přiřazení nedojde k odebrání přidružených artefaktů.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Na levé straně vyberte stránku **přiřazené plány** . Pomocí filtrů vyhledejte přiřazení **podrobného plánu přiřazení storageaccount-TestingBPLocks** a pak ho vyberte.

1. V horní části stránky vyberte zrušit **přiřazení** podrobného plánu. Přečtěte si upozornění v potvrzovacím dialogovém okně a pak vyberte **OK**.

   Když se odebere přiřazení podrobného plánu, odeberou se taky zámky podrobného plánu. Prostředky můžete znovu odstranit pomocí účtu s příslušnými oprávněními.

1. V nabídce Azure vyberte **skupiny prostředků** a pak vyberte **TestingBPLocks**.

1. Na levé straně vyberte stránku **řízení přístupu (IAM)** a pak vyberte kartu **přiřazení rolí** .

Zabezpečení skupiny prostředků ukazuje na to, že přiřazení podrobného plánu už nemá přístup _vlastníka_ .

Až se zobrazí oznámení o **úspěšném odebrání přiřazení** podrobného plánu, pokračujte na další krok.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až skončíte s tímto kurzem, odstraňte tyto prostředky:

- _TestingBPLocks_ skupiny prostředků
- Definice _podrobného plánu je zamčená – storageaccount_

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s postupem ochrany nových prostředků nasazených pomocí Azure modrotisky. Další informace o plánech Azure najdete v článku o životním cyklu podrobného plánu.

> [!div class="nextstepaction"]
> [Další informace o životním cyklu podrobného plánu](../concepts/lifecycle.md)