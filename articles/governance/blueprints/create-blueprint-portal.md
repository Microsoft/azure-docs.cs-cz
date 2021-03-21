---
title: 'Rychlý Start: vytvoření podrobného plánu na portálu'
description: V tomto rychlém startu použijete Azure modrotisky k vytváření, definování a nasazování artefaktů prostřednictvím Azure Portal.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 75a4e9deca4859ac8b9f302fcb62ecc1d12dd8c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918326"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Rychlý Start: definování a přiřazení podrobného plánu na portálu

Když se naučíte vytvářet a přiřazovat modrotisky, můžete definovat běžné vzory pro vývoj opakovaně použitelných a rychlých nasazení konfigurací založených na Azure Resource Manager šablonách (šablon ARM), zásadách, zabezpečení a dalších. V tomto kurzu se naučíte používat Azure modrotisky k provádění některých běžných úkolů souvisejících s vytvářením, publikováním a přiřazováním podrobného plánu v rámci vaší organizace. Mezi tyto úlohy patří:

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-a-blueprint"></a>Vytvoření podrobného plánu

Jako první krok při definování standardního vzoru pro dodržování předpisů je sestavení podrobného plánu z dostupných prostředků. V tomto příkladu vytvořte nový podrobný plán s názvem **MyBlueprint** , ve kterém nakonfigurujete přiřazení rolí a zásad pro předplatné. Pak přidejte novou skupinu prostředků a vytvořte Správce prostředků šablonu a přiřazení role pro novou skupinu prostředků.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Na stránce vlevo vyberte **definice** podrobného plánu a v horní části stránky vyberte tlačítko **+ vytvořit podrobný plán** .

   Nebo na stránce **Začínáme** vyberte **vytvořit** a pokračujte tak, že vytvoříte podrobný plán.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="Snímek obrazovky s tlačítkem vytvořit podrobné plán na stránce definice podrobného plánu" border="false":::

1. Na kartě v horní části seznamu předdefinovaných modrotisky vyberte **začít s prázdným plánem** .

1. Zadejte **název** podrobného plánu, například **MyBlueprint**. (Použijte až 48 písmen a číslic, ale žádné mezery ani speciální znaky). Pro nyní nechejte **Popis** podrobného plánu prázdný.

1. V poli **umístění definice** vyberte tři tečky na pravé straně, vyberte [skupinu pro správu](../management-groups/overview.md) nebo předplatné, kam chcete uložit podrobný plán, a zvolte **Vybrat**.

1. Ověřte, zda jsou informace správné. Pole **název** a **umístění definice** podrobného plánu nelze později změnit. Pak vyberte **Další: artefakty** ve spodní části stránky nebo na kartě **artefakty** v horní části stránky.

1. Přidejte přiřazení role na úrovni předplatného:

   1. V části **předplatné** vyberte řádek **+ Přidat artefakt** . Otevře se okno **Přidat artefakt** na pravé straně prohlížeče.

   1. Vyberte **přiřazení role** pro **Typ artefaktu**.

   1. V části **role** vyberte **Přispěvatel**. Ponechejte pole **Přidat uživatele, aplikaci nebo skupinu** pomocí zaškrtávacího políčka, které označuje dynamický parametr.

   1. Vyberte **Přidat** a přidejte tento artefakt do podrobného plánu.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="Snímek obrazovky možností artefaktu přiřazení role pro přidání do definice podrobného plánu" border="false":::

   > [!NOTE]
   > Většina artefaktů podporuje parametry. Parametr, kterému je přiřazena hodnota během vytváření podrobného plánu, je _statický parametr_. Pokud je parametr přiřazen během přiřazení podrobného plánu, jedná se o _dynamický parametr_. Další informace najdete v tématu [parametry](./concepts/parameters.md)podrobného plánu.

1. Přidat přiřazení zásady na úrovni předplatného:

   1. V artefaktu přiřazení role vyberte řádek **+ Přidat artefakt** .

   1. Vyberte **přiřazení zásad** pro **Typ artefaktu**.

   1. Změňte **typ** na **předdefinovaný**. Do **vyhledávacího** výrazu zadejte **tag**.

   1. Změňte fokus na **hledání** , které se má provést. Vyberte možnost **připojit značku a její výchozí hodnotu do skupin prostředků**.

   1. Vyberte **Přidat** a přidejte tento artefakt do podrobného plánu.

1. Vyberte řádek s označením přiřazení zásady **připojit a jeho výchozí hodnotu do skupin prostředků**.

1. Okno k zadání parametrů s artefaktem jako součást definice podrobného plánu se otevře a povolí nastavení parametrů pro všechna přiřazení (statické parametry)založených na podrobném plánu místo během přiřazení (dynamické parametry). Tento příklad používá dynamické parametry během přiřazení podrobného plánu, takže ponechte výchozí hodnoty a vyberte **Zrušit**.

1. Přidat skupinu prostředků na úrovni předplatného:

   1. V části **předplatné** vyberte řádek **+ Přidat artefakt** .

   1. Vyberte **skupinu prostředků** pro **Typ artefaktu**.

   1. **Zobrazované názvy artefaktů**, **název skupiny prostředků** a pole **umístění** nechte prázdné, ale ujistěte se, že je zaškrtnuté políčko pro všechny vlastnosti parametrů, aby byly dynamické parametry.

   1. Vyberte **Přidat** a přidejte tento artefakt do podrobného plánu.

1. Přidejte šablonu do skupiny prostředků:

   1. V položce **zdroj dat** vyberte řádek **+ Přidat artefakt** .

   1. Vyberte **šablonu Azure Resource Manager** pro **Typ artefaktu**, nastavte **Zobrazovaný název artefaktu** na **StorageAccount** a nechejte **Popis** prázdný.

   1. Na kartě **Šablona** v poli Editor vložte následující šablonu ARM. Po vložení šablony vyberte kartu **parametry** a Všimněte si, že byly zjištěny parametry šablony **storageAccountType** a **Location** . Každý parametr byl automaticky zjištěn a vyplněn, ale nakonfigurován jako dynamický parametr.

      > [!IMPORTANT]
      > Pokud importujete šablonu, ujistěte se, že je soubor pouze JSON a neobsahuje kód HTML. Když odkazujete na adresu URL na GitHubu, ujistěte se, že jste si vybrali **nezpracované** soubory JSON, a ne ta, která je zabalená do HTML pro zobrazení na GitHubu. Pokud importovaná šablona není čistě JSON, dojde k chybě.

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

   1. Zrušte zaškrtnutí políčka **storageAccountType** a Všimněte si, že rozevírací seznam obsahuje jenom hodnoty obsažené v šabloně ARM v části **allowedValues**. Zaškrtněte políčko pro jeho nastavení zpět na dynamický parametr.

   1. Vyberte **Přidat** a přidejte tento artefakt do podrobného plánu.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="Snímek obrazovky s možnostmi artefaktu šablony Správce prostředků pro přidání do definice podrobného plánu" border="false":::

1. Váš konečný plán by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že každý artefakt má ve sloupci **Parameters** **naplněný parametr _y_** . Dynamické parametry se nastavují při každém přiřazení podrobného plánu.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="Snímek obrazovky dokončené definice podrobného plánu s každým typem artefaktu" border="false":::

1. Teď, když jste přidali všechny plánované artefakty, vyberte **Uložit koncept** ve spodní části stránky.

## <a name="edit-a-blueprint"></a>Upravit podrobný plán

V části [vytvořit podrobný plán](#create-a-blueprint)jste nezadali popis nebo přidáte přiřazení role do nové skupiny prostředků. V obou případech můžete opravit pomocí následujících kroků:

1. Na levé straně stránky vyberte **definice** podrobného plánu.

1. V seznamu modrotisky klikněte pravým tlačítkem na ten, který jste vytvořili dříve, a vyberte **Upravit podrobný plán**.

1. V **popisu** podrobného plánu zadejte nějaké informace o podrobném plánu a artefaktech, které ho tvoří. V takovém případě zadejte něco podobného: **Tento plán nastaví u předplatného zásadu značky a přiřazení role, vytvoří zdroj skupiny a nasadí šablonu prostředků a přiřazení role do této skupiny prostředků.**

1. Vyberte **Další: artefakty** ve spodní části stránky nebo na kartě **artefakty** v horní části stránky.

1. Přidejte přiřazení role pod skupinu prostředků:

   1. Vyberte řádek **+ Přidat artefakt** přímo pod položkou **resourceName** .

   1. Vyberte **přiřazení role** pro **Typ artefaktu**.

   1. V části **role** vyberte **vlastník** a zrušte zaškrtnutí políčka v poli **Přidat uživatele, aplikaci nebo skupinu** .

   1. Vyhledejte a vyberte uživatele, aplikaci nebo skupinu, které chcete přidat. Tento artefakt používá statický parametr, který je v každém přiřazení tohoto podrobného plánu nastavený stejně.

   1. Vyberte **Přidat** a přidejte tento artefakt do podrobného plánu.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="Snímek obrazovky s možnostmi artefaktů přiřazení druhé role pro přidání do definice podrobného plánu" border="false":::

1. Váš konečný plán by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že nově přidané přiřazení role zobrazuje **naplněný 1 z 1 parametrů**. To znamená, že se jedná o statický parametr.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="Snímek obrazovky druhé dokončené definice podrobného plánu s artefaktem přiřazení další role" border="false":::

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

   Pokud jsou v rámci [fakturace Azure](../../cost-management-billing/index.yml)dostupné podporované nabídky Enterprise, v poli **předplatné** se aktivuje odkaz **vytvořit nový** . Postupujte takto:

   1. Vyberte odkaz **vytvořit nový** pro vytvoření nového předplatného místo výběru stávajících.

   1. Zadejte **Zobrazovaný název** pro nové předplatné.

   1. V rozevíracím seznamu vyberte dostupnou **nabídku** .

   1. Pomocí tří teček vyberte [skupinu pro správu](../management-groups/overview.md) , pro kterou bude předplatné podřízeno.

   1. V dolní části stránky vyberte **vytvořit** .

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="Snímek obrazovky okna Vytvořit předplatné a možnosti pro nové předplatné" border="false":::

      > [!IMPORTANT]
      > Nové předplatné se vytvoří ihned po výběru **vytvořit**.

   > [!NOTE]
   > U každého předplatného, které vyberete, se vytvoří přiřazení. Změny v jednom předplatném můžete provádět později bez vynucení změn ve zbývající části vybraných předplatných.

1. Pro **název přiřazení** zadejte jedinečný název pro toto přiřazení.

1. V části **umístění** vyberte oblast, ve které se má vytvořit objekt nasazení spravované identity a předplatného. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Ponechte v rozevíracím seznamu **verze definice** podrobného plánu možnost **publikované** verze v položce **v1** . (Výchozí je naposledy publikovaná verze.)

1. Pro **zámek přiřazení**, ponechte výchozí hodnotu na **bez zámku**. Další informace najdete v tématu [uzamykání prostředků v modrotisky](./concepts/resource-locking.md).

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="Snímek obrazovky s přiřazením zámku a spravovanými možnostmi identity pro přiřazení podrobného plánu" border="false":::

1. V části **spravovaná identita** ponechte výchozí hodnotu **přiřazenou systému**.

1. Pro přiřazení role na úrovni předplatného **[skupiny uživatelů nebo název aplikace]: Přispěvatel**, vyhledejte a vyberte uživatele, aplikace nebo skupinu.

1. U přiřazení zásady na úrovni předplatného nastavte **název značky** na **CostCenter** a **hodnotu značky** na **ContosoIT**.

1. V rozevíracím seznamu pro **zdroj dat** zadejte **název** **StorageAccount** a **umístění** **východní USA 2** .

   > [!NOTE]
   > Pro každý artefakt, který jste přidali do skupiny prostředků během definice podrobného plánu, je tento artefakt odsazený tak, aby se zarovnal se skupinou prostředků nebo objektem, pomocí kterého ho nasadíte.
   > Artefakty, které buď nepřebírají parametry, nebo nemají žádné parametry, které by bylo možné definovat v přiřazení, jsou uvedeny pouze pro kontextové informace.

1. V **StorageAccount** šablony ARM vyberte pro parametr **storageAccountType** možnost **Standard_GRS** .

1. Přečtěte si informační pole v dolní části stránky a potom vyberte **přiřadit**.

## <a name="track-deployment-of-a-blueprint"></a>Sledování aplikace plánu

Pokud byl plán přiřazený k jednomu nebo několika předplatným, stanou dvě věci:

- Podrobný plán se přidá na stránku **přiřazené plány** pro každé předplatné.
- Proces nasazení všech artefaktů definovaných v podrobném plánu začíná.

Po přiřazení podrobného plánu k předplatnému ověřte průběh nasazení:

1. Na levé straně vyberte **přiřazené plány** .

1. V seznamu modrotisky klikněte pravým tlačítkem na ten, který jste předtím přiřadili, a vyberte **Zobrazit podrobnosti přiřazení**.

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="Snímek obrazovky kontextové nabídky přiřazení podrobného plánu se zvolenou možností zobrazit podrobnosti přiřazení" border="false":::

1. Na stránce **přiřazení** podrobného plánu ověřte, zda byly všechny artefakty úspěšně nasazeny a zda během nasazení nedošlo k chybám. Pokud došlo k chybám, přečtěte si téma [řešení potíží s](./troubleshoot/general.md) podrobnými kroky pro určení, co se stalo.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="unassign-a-blueprint"></a>Zrušení přiřazení plánu

Pokud už přiřazení podrobného plánu nepotřebujete, odeberte ho z předplatného. Podrobný plán mohl být nahrazen novějším plánem s aktualizovanými vzorci, zásadami a návrhy. Po odebrání podrobného plánu zůstanou přiřazené artefakty, které byly jeho součástí. Pokud chcete odebrat přiřazení podrobného plánu, postupujte následovně:

1. Na levé straně vyberte **přiřazené plány** .

1. V seznamu modrotisky vyberte plán, který chcete zrušit přiřazení. Pak vyberte tlačítko zrušit **přiřazení** podrobného plánu v horní části stránky.

1. Přečtěte si potvrzovací zprávu a pak vyberte **OK**.

### <a name="delete-a-blueprint"></a>Smazání podrobného plánu

1. Na levé straně stránky vyberte **definice** podrobného plánu.

1. Klikněte pravým tlačítkem na podrobný plán, který chcete odstranit, a vyberte **Odstranit podrobný plán**. Potom v potvrzovacím dialogovém okně vyberte **Ano** .

> [!NOTE]
> Odstraněním podrobného plánu v této metodě se odstraní také všechny publikované verze vybraného podrobného plánu.
> Pokud chcete odstranit jednu verzi, otevřete plán, vyberte kartu **publikované verze** , vyberte verzi, kterou chcete odstranit, a pak vyberte **Odstranit tuto verzi**. Nemůžete také odstranit podrobný plán, dokud neodstraníte všechna přiřazení podrobného plánu této definice podrobného plánu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili, přiřadili a odebrali podrobný plán s Azure Portal. Další informace o plánech Azure najdete v článku o životním cyklu podrobného plánu.

> [!div class="nextstepaction"]
> [Další informace o životním cyklu podrobného plánu](./concepts/lifecycle.md)
