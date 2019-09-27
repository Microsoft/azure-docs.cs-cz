---
title: Vytvořit podrobný plán na portálu
description: Pomocí Azure modrotisky můžete vytvářet, definovat a nasazovat artefakty prostřednictvím Azure Portal.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 225f5cfe36daaafb7f6ab5d64e77c626e52edaac
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338198"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Rychlý start: Definování a přiřazení podrobného plánu na portálu

Když se naučíte vytvářet a přiřazovat modrotisky, můžete definovat běžné vzory pro vývoj opakovaně použitelných a rychle nasazujíných konfigurací na základě Azure Resource Manager šablon, zásad, zabezpečení a dalších. V tomto kurzu se naučíte používat Azure modrotisky k provádění některých běžných úkolů souvisejících s vytvářením, publikováním a přiřazováním podrobného plánu v rámci vaší organizace. Mezi tyto úlohy patří:

> [!div class="checklist"]
> - Vytvoření nového podrobného plánu a přidání různých podporovaných artefaktů
> - Provádění změn stávajícího podrobného plánu ve stavu **Koncept**
> - Označení podrobného plánu připraveného k přiřazení stavem **Publikováno**
> - Přiřazení podrobného plánu ke stávajícímu předplatnému
> - Kontrola stavu a průběhu přiřazeného podrobného plánu
> - Odebrání podrobného plánu přiřazeného k předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-a-blueprint"></a>Vytvořit podrobný plán

Jako první krok při definování standardního vzoru pro dodržování předpisů je sestavení podrobného plánu z dostupných prostředků. V tomto příkladu vytvořte nový podrobný plán s názvem **MyBlueprint** , ve kterém nakonfigurujete přiřazení rolí a zásad pro předplatné. Pak přidejte novou skupinu prostředků a vytvořte Správce prostředků šablonu a přiřazení role pro novou skupinu prostředků.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na stránce vlevo vyberte **definice** podrobného plánu a v horní části stránky vyberte tlačítko **+ vytvořit podrobný plán** .

   Nebo na stránce **Začínáme** vyberte **vytvořit** a pokračujte tak, že vytvoříte podrobný plán.

   ![Vytvořit podrobný plán ze stránky definice podrobného plánu](./media/create-blueprint-portal/create-blueprint-button.png)

1. Zadejte **název** podrobného plánu, například **MyBlueprint**. (Použijte až 48 písmen a číslic, ale žádné mezery ani speciální znaky). Pro nyní nechejte **Popis** podrobného plánu prázdný.

1. V poli **umístění definice** vyberte tři tečky na pravé straně, vyberte [skupinu pro správu](../management-groups/overview.md) nebo předplatné, kam chcete uložit podrobný plán, a zvolte **Vybrat**.

1. Ověřte, zda jsou informace správné. Pole **název** a **umístění definice** podrobného plánu nelze později změnit. Pak vyberte **další: Artefakty v dolní části stránky nebo na kartě artefakty v horní části stránky.**

1. Přidejte přiřazení role na úrovni předplatného:

   1. V části **předplatné**vyberte řádek **+ Přidat artefakt** . Otevře se okno **Přidat artefakt** na pravé straně prohlížeče.

   1. Vyberte **přiřazení role** pro **Typ artefaktu**.

   1. V části **role**vyberte **Přispěvatel**. Ponechejte pole **Přidat uživatele, aplikaci nebo skupinu** pomocí zaškrtávacího políčka, které označuje dynamický parametr.

   1. Vyberte **Přidat** a přidejte tento artefakt do podrobného plánu.

   ![Přiřazení role pro artefakt podrobného plánu](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > Většina artefaktů podporuje parametry. Parametr, kterému je přiřazena hodnota během vytváření podrobného plánu, je *statický parametr*. Pokud je parametr přiřazen během přiřazení podrobného plánu, jedná se o *dynamický parametr*. Další informace najdete v [parametry podrobného plánu](./concepts/parameters.md).

1. Přidat přiřazení zásady na úrovni předplatného:

   1. V artefaktu přiřazení role vyberte řádek **+ Přidat artefakt** .

   1. Vyberte **přiřazení zásad** pro **Typ artefaktu**.

   1. Změňte **typ** na **předdefinovaný**. Do **vyhledávacího**výrazu zadejte **tag**.

   1. Vyklikněte z **hledání** pro zobrazení filtrování. Vyberte možnost **připojit značku a její výchozí hodnotu do skupin prostředků**.

   1. Vyberte **Přidat** a přidejte tento artefakt do podrobného plánu.

1. Vyberte řádek s označením přiřazení zásady **připojit a jeho výchozí hodnotu do skupin prostředků**.

1. Okno pro zadání parametrů artefaktu v rámci definice podrobného plánu se otevře a umožní nastavení parametrů pro všechna přiřazení (statické parametry) na základě tohoto podrobného plánu, nikoli během přiřazení (dynamické parametry). Tento příklad používá dynamické parametry během přiřazení podrobného plánu, takže ponechte výchozí hodnoty a vyberte **Zrušit**.

1. Přidat skupinu prostředků na úrovni předplatného:

   1. V části **předplatné**vyberte řádek **+ Přidat artefakt** .

   1. Vyberte **skupinu prostředků** pro **Typ artefaktu**.

   1. Zobrazované **názvy artefaktů**, **název skupiny prostředků**a pole **umístění** nechte prázdné, ale ujistěte se, že je zaškrtnuté políčko pro všechny vlastnosti parametrů, aby byly dynamické parametry.

   1. Vyberte **Přidat** a přidejte tento artefakt do podrobného plánu.

1. Přidejte šablonu do skupiny prostředků:

   1. V položce **zdroj dat** vyberte řádek **+ Přidat artefakt** .

   1. Vyberte **šablonu Azure Resource Manager** pro **Typ artefaktu**, nastavte **Zobrazovaný název artefaktu** na **StorageAccount**a nechejte **Popis** prázdný.

   1. Na kartu **šablona** v poli editoru, vložte následující šablonu správce prostředků.
      Po vložení šablony vyberte kartu **parametry** a Všimněte si, že byly zjištěny parametry šablony **storageAccountType** a **Location** . Každý parametr byl automaticky zjištěn a vyplněn, ale nakonfigurován jako dynamický parametr.

      > [!IMPORTANT]
      > Pokud importujete šablonu, ujistěte se, že je soubor pouze JSON a neobsahuje kód HTML. Když odkazujete na adresu URL na GitHubu, ujistěte se, že jste si vybrali nezpracované soubory JSON, a ne ta, která je zabalená do HTML pro zobrazení na GitHubu. Pokud importovaná šablona není čistě JSON, dojde k chybě.

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
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
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

   1. Zrušte zaškrtnutí políčka **storageAccountType** a Všimněte si, že rozevírací seznam obsahuje pouze hodnoty obsažené v šabloně správce prostředků v části **allowedValues**. Zaškrtněte políčko pro jeho nastavení zpět na dynamický parametr.

   1. Vyberte **Přidat** a přidejte tento artefakt do podrobného plánu.

   ![Šablona Správce prostředků pro artefakt podrobného plánu](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Váš konečný plán by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že každý artefakt má ve sloupci **Parameters** naplněný **parametr _y_** . Dynamické parametry jsou nastaveny během každého přiřazení podrobného plánu.

   ![Dokončena definice podrobného plánu](./media/create-blueprint-portal/completed-blueprint.png)

1. Teď, když jste přidali všechny plánované artefakty, vyberte **Uložit koncept** ve spodní části stránky.

## <a name="edit-a-blueprint"></a>Upravit podrobný plán

V části [vytvořit podrobný plán](#create-a-blueprint)jste nezadali popis nebo přidáte přiřazení role do nové skupiny prostředků. V obou případech můžete opravit pomocí následujících kroků:

1. Na levé straně stránky vyberte **definice** podrobného plánu.

1. V seznamu modrotisky klikněte pravým tlačítkem na ten, který jste vytvořili dříve, a vyberte **Upravit podrobný plán**.

1. V **popisu**podrobného plánu zadejte nějaké informace o podrobném plánu a artefaktech, které ho tvoří. V takovém případě zadejte něco jako: **Tento plán nastaví přiřazení zásad značek a rolí v předplatném, vytvoří zdroj skupiny a nasadí šablonu prostředků a přiřazení role do této skupiny prostředků.**

1. Vyberte **další: Artefakty v dolní části stránky nebo na kartě artefakty v horní části stránky.**

1. Přidejte přiřazení role pod skupinu prostředků:

   1. Vyberte řádek **+ Přidat artefakt** přímo pod položkou **resourceName** .

   1. Vyberte **přiřazení role** pro **Typ artefaktu**.

   1. V části **role**vyberte **vlastník**a zrušte zaškrtnutí políčka v poli **Přidat uživatele, aplikaci nebo skupinu** .

   1. Vyhledejte a vyberte uživatele, aplikaci nebo skupinu, které chcete přidat. Tento artefakt používá statický parametr nastavený v každém přiřazení tohoto podrobného plánu.

   e. Vyberte **Přidat** a přidejte tento artefakt do podrobného plánu.

   ![Přiřazení druhé role pro artefakt podrobného plánu](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Váš konečný plán by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že nově přidané přiřazení role zobrazuje naplněný **1 z 1 parametrů**. To znamená, že se jedná o statický parametr.

   ![Druhá definice pro dokončený podrobný plán](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Vyberte **Uložit koncept** nyní, že byl aktualizován.

## <a name="publish-a-blueprint"></a>Publikování podrobného plánu

Teď, když jsou do podrobného plánu přidané všechny plánované artefakty, je na čase ho publikovat.
Publikování zpřístupní plán, který se má přiřadit k předplatnému.

1. Na levé straně stránky vyberte **definice** podrobného plánu.

1. V seznamu modrotisky klikněte pravým tlačítkem na ten, který jste dříve vytvořili, a vyberte **publikovat podrobný plán**.

1. V podokně, které se otevře, zadejte **verzi** (písmena, číslice a spojovníky s maximální délkou 20 znaků), například **v1**. V případě potřeby můžete zadat text do **poznámek ke změnám**, jako je například **první publikování**.

1. V dolní části stránky vyberte **publikovat** .

## <a name="assign-a-blueprint"></a>Přiřazení podrobného plánu

Po publikování se plán dá přiřadit k předplatnému. Přiřaďte podrobný plán, který jste vytvořili, do některého z předplatných ve vaší hierarchii skupin pro správu. Pokud je podrobný plán uložen v předplatném, může být přiřazen pouze k tomuto předplatnému.

1. Na levé straně stránky vyberte **definice** podrobného plánu.

1. V seznamu modrotisky klikněte pravým tlačítkem na ten, který jste dříve vytvořili (nebo vyberte tři tečky) a vyberte **přiřadit podrobný plán**.

1. Na stránce **přiřadit podrobný plán** vyberte v rozevíracím seznamu **odběr** odběry, do kterých chcete nasadit tento plán.

   Pokud jsou v rámci [fakturace Azure](../../billing/index.md)dostupné podporované nabídky Enterprise, v poli **předplatné** se aktivuje odkaz **vytvořit nový** . Postupujte následovně:

   1. Vyberte odkaz **vytvořit nový** pro vytvoření nového předplatného místo výběru stávajících.

   1. Zadejte **Zobrazovaný název** pro nové předplatné.

   1. V rozevíracím seznamu vyberte dostupnou **nabídku** .

   1. Pomocí tří teček vyberte [skupinu pro správu](../management-groups/overview.md) , pro kterou bude předplatné podřízeno.

   1. V dolní části stránky vyberte **vytvořit** .

   ![Vytvoření odběru pro odběr přiřazení podrobného plánu](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > Nové předplatné se vytvoří ihned po výběru **vytvořit**.

   > [!NOTE]
   > U každého předplatného, které vyberete, se vytvoří přiřazení. Změny v jednom předplatném můžete provádět později bez vynucení změn ve zbývající části vybraných předplatných.

1. Pro **název přiřazení**zadejte jedinečný název pro toto přiřazení.

1. V části **umístění**vyberte oblast, ve které se má vytvořit objekt nasazení spravované identity a předplatného. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Ponechte v rozevíracím seznamu **verze definice** podrobného plánu možnost **publikované** verze v položce **v1** . (Výchozí je naposledy publikovaná verze.)

1. Pro **zámek přiřazení**, ponechte výchozí hodnotu na **bez zámku**. Další informace najdete v tématu [uzamykání prostředků v modrotisky](./concepts/resource-locking.md).

   ![Uzamykání a spravované identity pro přiřazení](./media/create-blueprint-portal/assignment-locking-mi.png)

1. V části **spravovaná identita**ponechte výchozí hodnotu **přiřazenou systému**.

1. Pro přiřazení role na úrovni předplatného  **[skupina uživatelů nebo název aplikace]: Přispěvatel**, vyhledejte a vyberte uživatele, aplikaci nebo skupinu.

1. U přiřazení zásady na úrovni předplatného nastavte **název značky** na **CostCenter** a **hodnotu značky** na **ContosoIT**.

1. V rozevíracím seznamu pro **zdroj dat**zadejte **název** **StorageAccount** a **umístění** **východní USA 2** .

   > [!NOTE]
   > Pro každý artefakt, který jste přidali do skupiny prostředků během definice podrobného plánu, je tento artefakt odsazený tak, aby se zarovnal se skupinou prostředků nebo objektem, pomocí kterého ho nasadíte.
   > Artefakty, které buď nepřebírají parametry, nebo nemají žádné parametry, které by bylo možné definovat v přiřazení, jsou uvedeny pouze pro kontextové informace.

1. V šabloně Azure Resource Manager **StorageAccount**jako parametr **storageAccountType** vyberte **Standard_GRS** .

1. Přečtěte si informační pole v dolní části stránky a potom vyberte **přiřadit**.

## <a name="track-deployment-of-a-blueprint"></a>Sledování aplikace plánu

Pokud byl plán přiřazený k jednomu nebo několika předplatným, stanou dvě věci:

- Podrobný plán se přidá na stránku **přiřazené plány** pro každé předplatné.
- Proces nasazení všech artefaktů definovaných v podrobném plánu začíná.

Po přiřazení podrobného plánu k předplatnému ověřte průběh nasazení:

1. Na levé straně vyberte **přiřazené plány** .

1. V seznamu modrotisky klikněte pravým tlačítkem na ten, který jste předtím přiřadili, a vyberte **Zobrazit podrobnosti přiřazení**.

   ![Zobrazit podrobnosti o přiřazení z přiřazené stránky modrotisky](./media/create-blueprint-portal/view-assignment-details.png)

1. Na stránce **přiřazení** podrobného plánu ověřte, zda byly všechny artefakty úspěšně nasazeny a zda během nasazení nedošlo k chybám. Pokud došlo k chybám, přečtěte si téma [řešení potíží s](./troubleshoot/general.md) podrobnými kroky pro určení, co se stalo.

## <a name="unassign-a-blueprint"></a>Zrušení přiřazení plánu

Pokud už přiřazení podrobného plánu nepotřebujete, odeberte ho z předplatného. Podrobný plán mohl být nahrazen novějším plánem s aktualizovanými vzorci, zásadami a návrhy. Po odebrání podrobného plánu zůstanou přiřazené artefakty, které byly jeho součástí. Pokud chcete odebrat přiřazení podrobného plánu, postupujte následovně:

1. Na levé straně vyberte **přiřazené plány** .

1. V seznamu modrotisky vyberte plán, který chcete zrušit přiřazení. Pak vyberte tlačítko zrušit **přiřazení** podrobného plánu v horní části stránky.

1. Přečtěte si potvrzovací zprávu a pak vyberte **OK**.

## <a name="delete-a-blueprint"></a>Smazání podrobného plánu

1. Na levé straně stránky vyberte **definice** podrobného plánu.

1. Klikněte pravým tlačítkem na podrobný plán, který chcete odstranit, a vyberte **Odstranit podrobný plán**. Potom v potvrzovacím dialogovém okně vyberte **Ano** .

> [!NOTE]
> Odstraněním podrobného plánu v této metodě se odstraní také všechny publikované verze vybraného podrobného plánu.
> Pokud chcete odstranit jednu verzi, otevřete plán, vyberte kartu **publikované verze** , vyberte verzi, kterou chcete odstranit, a pak vyberte **Odstranit tuto verzi**. Nemůžete také odstranit podrobný plán, dokud neodstraníte všechna přiřazení podrobného plánu této definice podrobného plánu.

## <a name="next-steps"></a>Další kroky

- Další informace o [životním cyklu podrobného plánu](./concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](./concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](./concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](./concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](./how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](./troubleshoot/general.md)