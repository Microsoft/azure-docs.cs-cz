---
title: 'Úvodní příručka: Vytvoření podrobného plánu na portálu'
description: V tomto rychlém startu použijete Azure Blueprints k vytváření, definování a nasazování artefaktů prostřednictvím portálu Azure.
ms.date: 03/25/2020
ms.topic: quickstart
ms.openlocfilehash: 457f4f9c53f45077129b291c904bc1580b6d965e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282048"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Úvodní příručka: Definování a přiřazení podrobného plánu na portálu

Když se dozvíte, jak vytvořit a přiřadit podrobné plány, můžete definovat společné vzory pro vývoj opakovaně použitelných a rychle nasaditelných konfigurací založených na šablonách Azure Resource Manageru, zásadách, zabezpečení a dalších. V tomto kurzu se naučíte používat Azure Blueprints k některým běžným úkolům souvisejícím s vytvářením, publikováním a přiřazování podrobného plánu v rámci vaší organizace. Mezi tyto úkoly patří:

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete.

## <a name="create-a-blueprint"></a>Vytvoření podrobného plánu

Jako první krok při definování standardního vzoru pro dodržování předpisů je sestavení podrobného plánu z dostupných prostředků. V tomto příkladu vytvořte nový podrobný plán s názvem **MyBlueprint** pro konfiguraci role a přiřazení zásad pro předplatné. Potom přidejte novou skupinu prostředků a vytvořte šablonu správce prostředků a přiřazení rolí v nové skupině prostředků.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce vlevo vyberte **definice podrobného plánu** a v horní části stránky vyberte tlačítko **+ Vytvořit podrobný plán.**

   Nebo vyberte **Vytvořit** na stránce **Začínáme** a přejděte přímo k vytvoření podrobného plánu.

   ![Vytvoření podrobného plánu ze stránky Definice podrobného plánu](./media/create-blueprint-portal/create-blueprint-button.png)

1. V horní části seznamu předdefinovaných podrobných plánů vyberte **Začít s prázdným podrobným plánem.**

1. Zadejte **název podrobného plánu,** například **MyBlueprint**. (Používejte až 48 písmen a číslic, ale žádné mezery nebo speciální znaky). Ponechte **popis podrobného plánu** prozatím prázdný.

1. V poli **Umístění definice** vyberte tři tečky vpravo, vyberte [skupinu pro správu](../management-groups/overview.md) nebo předplatné, do které chcete podrobný plán uložit, a zvolte **Vybrat**.

1. Ověřte, zda jsou informace správné. Pole **Název podrobného plánu** a Umístění **definice** nelze později změnit. Pak vyberte **Další : Artefakty** v dolní části stránky nebo na kartě **Artefakty** v horní části stránky.

1. Přidejte přiřazení role na úrovni předplatného:

   1. V části **Odběr**vyberte řádek **+ Přidat artefakt** . Otevře se okno **Přidat artefakt** na pravé straně prohlížeče.

   1. Vyberte **přiřazení role** pro **typ artefaktu**.

   1. V části **Role**vyberte **Přispěvatel**. Ponechte políčko **Přidat uživatele, aplikaci nebo skupinu** se zaškrtávacím políčkem, které označuje dynamický parametr.

   1. Vyberte **Přidat,** chcete-li přidat tento artefakt do podrobného plánu.

   ![Přiřazení role pro artefakt podrobného plánu](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > Většina artefaktů podporuje parametry. Parametr, kterému je přiřazena hodnota při vytváření podrobného plánu, je *statický parametr*. Pokud je parametr přiřazen během přiřazení podrobného plánu, jedná se o *dynamický parametr*. Další informace naleznete v tématu [Blueprint parametry](./concepts/parameters.md).

1. Přidejte přiřazení zásad na úrovni předplatného:

   1. Vyberte řádek **+ Přidat artefakt** pod artefaktem přiřazení role.

   1. Vyberte **přiřazení zásad** pro **typ artefaktu**.

   1. Změnit **typ** na **předdefinovaný**. Do **pole Hledat**zadejte **značku**.

   1. Vyklikněte z **hledání** pro zobrazení filtrování. Vyberte **Připojit značku a její výchozí hodnotu ke skupinám prostředků**.

   1. Vyberte **Přidat,** chcete-li přidat tento artefakt do podrobného plánu.

1. Vyberte řádek značky Apend přiřazení zásad **a její výchozí hodnotu pro skupiny prostředků**.

1. Okno k zadání parametrů s artefaktem jako součást definice podrobného plánu se otevře a povolí nastavení parametrů pro všechna přiřazení (statické parametry)založených na podrobném plánu místo během přiřazení (dynamické parametry). Tento příklad používá dynamické parametry během přiřazení podrobného plánu, takže ponechte výchozí hodnoty a vyberte **Zrušit**.

1. Přidání skupiny prostředků na úrovni předplatného:

   1. V části **Odběr**vyberte řádek **+ Přidat artefakt** .

   1. Vyberte **skupinu prostředků** pro **typ artefaktu**.

   1. Ponechte pole **Název artefaktu**, **Název skupiny prostředků**a **Umístění** prázdná, ale ujistěte se, že je zaškrtnuto políčko pro každou vlastnost parametru, aby byly dynamické parametry.

   1. Vyberte **Přidat,** chcete-li přidat tento artefakt do podrobného plánu.

1. Přidání šablony do skupiny prostředků:

   1. Vyberte řádek **+ Přidat artefakt** pod položkou **ResourceGroup.**

   1. Vyberte **šablonu Správce prostředků Azure** pro **typ artefaktu**, nastavte **zobrazovaný název artefaktu** na **Účet úložiště**a **ponechte popis** prázdný.

   1. Na kartu **šablona** v poli editoru, vložte následující šablonu správce prostředků.
      Po vložení šablony vyberte kartu **Parametry** a všimněte si, že byly zjištěny parametry šablony **storageAccountType** a **umístění.** Každý parametr byl automaticky rozpoznán a naplněn, ale nakonfigurován jako dynamický parametr.

      > [!IMPORTANT]
      > Pokud šablonu importujete, ujistěte se, že soubor je pouze JSON a neobsahuje HTML. Když ukazujete na adresu URL na GitHubu, ujistěte se, že jste vybrali **RAW,** abyste získali čistý soubor JSON a ne ten zabalený html pro zobrazení na GitHubu. Pokud importovaná šablona není čistě JSON, dojde k chybě.

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

   1. Zrušte zaškrtnutí políčka **storageAccountType** a všimněte si, že rozevírací seznam obsahuje pouze hodnoty obsažené v šabloně Správce prostředků v části **allowedValues**. Vyberte pole, chcete-li jej nastavit zpět na dynamický parametr.

   1. Vyberte **Přidat,** chcete-li přidat tento artefakt do podrobného plánu.

   ![Šablona Správce prostředků pro artefakt podrobného plánu](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Váš konečný plán by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že každý artefakt má ** _x_ z y parametry navyplněny _y_ ** ve sloupci **Parametry.** Dynamické parametry se nastavují při každém přiřazení podrobného plánu.

   ![Dokončená definice podrobného plánu](./media/create-blueprint-portal/completed-blueprint.png)

1. Teď, když byly přidány všechny plánované artefakty, vyberte **uložit koncept** v dolní části stránky.

## <a name="edit-a-blueprint"></a>Upravit podrobný plán

V [části Vytvořit podrobný plán](#create-a-blueprint)jste nezadali popis ani nepřidali přiřazení role do nové skupiny prostředků. Obojí můžete opravit následujícími kroky:

1. Na stránce vlevo vyberte **definice podrobného plánu.**

1. V seznamu podrobných plánů klikněte pravým tlačítkem myši na ten, který jste dříve vytvořili, a vyberte **upravit podrobný plán**.

1. V **popisu podrobného plánu**zadejte některé informace o podrobném plánu a artefakty, které jej tvoří. V takovém případě zadejte něco jako: **Tento podrobný plán nastaví zásady značek a přiřazení rolí v předplatném, vytvoří skupinu prostředků a nasadí šablonu prostředků a přiřazení role do této skupiny prostředků.**

1. Vyberte **Další : Artefakty** v dolní části stránky nebo na kartě **Artefakty** v horní části stránky.

1. Přidání přiřazení role do skupiny zdrojů:

   1. Vyberte řádek **+ Přidat artefakt** přímo pod položkou **ResourceGroup.**

   1. Vyberte **přiřazení role** pro **typ artefaktu**.

   1. V části **Role**vyberte **Vlastník**a zrušte zaškrtnutí políčka **Přidat uživatele, aplikaci nebo skupinu.**

   1. Vyhledejte a vyberte uživatele, aplikaci nebo skupinu, které chcete přidat. Tento artefakt používá statický parametr, který je v každém přiřazení tohoto podrobného plánu nastavený stejně.

   e. Vyberte **Přidat,** chcete-li přidat tento artefakt do podrobného plánu.

   ![Přiřazení druhé role pro artefakt podrobného plánu](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Váš konečný plán by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že nově přidané přiřazení role zobrazuje **1 z 1 naplnění parametrů**. To znamená, že je to statický parametr.

   ![Druhá definice dokončeného plánu](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Po aktualizaci **možnosti Uložit pracovní verzi** vyberte nyní možnost Uložit pracovní verzi.

## <a name="publish-a-blueprint"></a>Publikování podrobného plánu

Teď, když jsou do podrobného plánu přidané všechny plánované artefakty, je na čase ho publikovat.
Publikování zpřístupňuje podrobný plán pro přiřazení k předplatnému.

1. Na stránce vlevo vyberte **definice podrobného plánu.**

1. V seznamu podrobných plánů klikněte pravým tlačítkem myši na ten, který jste dříve vytvořili, a vyberte **publikovat podrobný plán**.

1. V podokně, které se otevře, zadejte **version** (písmena, čísla a pomlčky s maximální délkou 20 znaků), například **v1**. Volitelně můžete zadat text do pole **Změnit poznámky**, například **Nejprve publikovat**.

1. V dolní části stránky vyberte **Publikovat.**

## <a name="assign-a-blueprint"></a>Přiřazení podrobného plánu

Po publikování podrobného plánu jej můžete přiřadit k předplatnému. Přiřaďte podrobný plán, který jste vytvořili, k jednomu z předplatných v hierarchii skupiny pro správu. Pokud je podrobný plán uložen do předplatného, lze jej přiřadit pouze k tomuto předplatnému.

1. Na stránce vlevo vyberte **definice podrobného plánu.**

1. V seznamu podrobných plánů klikněte pravým tlačítkem myši na ten, který jste dříve vytvořili (nebo vyberte tři tečky) a vyberte **Přiřadit podrobný plán**.

1. Na stránce **Přiřadit podrobný plán** v rozevíracím seznamu **Odběr** vyberte předplatná, do kterých chcete tento podrobný plán nasadit.

   Pokud jsou k dispozici podporované nabídky Enterprise z [Fakturace Azure](../../billing/index.md), **aktivovat se** v poli **Předplatné** vytvořit nový odkaz. Postupujte následovně:

   1. Chcete-li místo výběru existujících odběrů vybrat stávající, vyberte možnost **Vytvořit nový** odkaz.

   1. Zadejte **zobrazovaný název** nového předplatného.

   1. V rozevíracím seznamu vyberte dostupnou **nabídku.**

   1. Pomocí třísek vyberte [skupinu pro správu,](../management-groups/overview.md) jejíž bude předplatné podřízenou položkou.

   1. V dolní části stránky vyberte **Vytvořit.**

   ![Vytvoření předplatného pro předplatné přiřazení podrobného plánu](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > Nové předplatné je vytvořeno ihned po výběru **možnosti Vytvořit**.

   > [!NOTE]
   > Přiřazení je vytvořeno pro každé vybrané předplatné. Můžete provést změny v jednom přiřazení předplatného později bez vynucení změny na zbytek vybrané odběry.

1. V **případě názvu přiřazení**zadejte jedinečný název tohoto přiřazení.

1. V **umístění**vyberte oblast pro spravované identity a objekt nasazení předplatného, který má být vytvořen v. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Ponechte rozevírací výběr **verze definice podrobného plánu** **publikované** verze v položce **v1.** (Výchozí je naposledy publikovaná verze.)

1. Pro **zámek přiřazení**, ponechte výchozí hodnotu na **bez zámku**. Další informace naleznete v [tématu Blueprints resource locking](./concepts/resource-locking.md).

   ![Uzamčení a spravované identity pro přiřazení](./media/create-blueprint-portal/assignment-locking-mi.png)

1. V části **Spravovaná identita**ponechejte výchozí **hodnotu Systém přiřazen .**

1. Pro přiřazení role na úrovni předplatného **[skupiny uživatelů nebo název aplikace]: Přispěvatel**, vyhledejte a vyberte uživatele, aplikace nebo skupinu.

1. Pro přiřazení zásad na úrovni předplatného nastavte **název značky** na **CostCenter** a **hodnotu značky** na **ContosoIT**.

1. V **části ResourceGroup**zadejte z rozevíracího seznamu **název** **účtu úložiště** a **umístění** **usa 2** ve východní části.

   > [!NOTE]
   > Pro každý artefakt, který jste přidali pod skupinou prostředků během definice podrobného plánu, je tento artefakt odsazen tak, aby byl zarovnán se skupinou prostředků nebo objektem, se kterým jej nasadíte.
   > Artefakty, které buď neberou parametry nebo nemají žádné parametry, které mají být definovány při přiřazení, jsou uvedeny pouze pro kontextové informace.

1. V šabloně Azure Resource Manager **StorageAccount**vyberte **Standard_GRS** **parametru storageAccountType.**

1. Přečtěte si informační pole v dolní části stránky a pak vyberte **Přiřadit**.

## <a name="track-deployment-of-a-blueprint"></a>Sledování aplikace plánu

Pokud byl plán přiřazený k jednomu nebo několika předplatným, stanou dvě věci:

- Podrobný plán se přidá na stránku **Přiřazené podrobné plány** pro každé předplatné.
- Proces nasazení všech artefaktů definovaných podrobným plánem začíná.

Teď, když byl podrobný plán přiřazen k předplatnému, ověřte průběh nasazení:

1. Na stránce vlevo vyberte **Přiřazené podrobné plány.**

1. V seznamu podrobných plánů klikněte pravým tlačítkem myši na ten, který jste dříve přiřadili, a vyberte **zobrazit podrobnosti o přiřazení**.

   ![Zobrazení podrobností o přiřazení na stránce Přiřazené podrobné plány](./media/create-blueprint-portal/view-assignment-details.png)

1. Na stránce **přiřazení podrobného plánu** ověřte, že všechny artefakty byly úspěšně nasazeny a že během nasazení nedošlo k žádným chybám. Pokud došlo k chybám, naleznete [v tématu Poradce při potížích s podrobnými plány,](./troubleshoot/general.md) kde najdete postup, jak zjistit, co se pokazilo.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="unassign-a-blueprint"></a>Zrušení přiřazení plánu

Pokud už přiřazení podrobného plánu nepotřebujete, odeberte ho z předplatného. Podrobný plán mohl být nahrazen novějším plánem s aktualizovanými vzory, zásadami a návrhy. Po odebrání podrobného plánu zůstanou přiřazené artefakty, které byly jeho součástí. Pokud chcete odebrat přiřazení podrobného plánu, postupujte následovně:

1. Na stránce vlevo vyberte **Přiřazené podrobné plány.**

1. V seznamu podrobných plánů vyberte podrobný plán, který chcete zrušit přiřazení. Pak vhorní části stránky vyberte tlačítko **Zrušit přiřazení podrobného** plánu.

1. Přečtěte si potvrzovací zprávu a pak vyberte **OK**.

### <a name="delete-a-blueprint"></a>Smazání podrobného plánu

1. Na stránce vlevo vyberte **definice podrobného plánu.**

1. Klikněte pravým tlačítkem myši na podrobný plán, který chcete odstranit, a vyberte **Odstranit podrobný plán**. Pak v potvrzovacím dialogovém okně vyberte **Ano.**

> [!NOTE]
> Odstranění podrobného plánu v této metodě také odstraní všechny publikované verze vybraného podrobného plánu.
> Chcete-li odstranit jednu verzi, otevřete podrobný plán, vyberte kartu **Publikované verze,** vyberte verzi, kterou chcete odstranit, a pak vyberte **Odstranit tuto verzi**. Podrobný plán také nemůžete odstranit, dokud neodstraníte všechna přiřazení podrobného plánu této definice podrobného plánu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili, přiřadili a odebrali podrobný plán pomocí portálu Azure. Další informace o Azure Blueprints, pokračujte v článku životního cyklu podrobného plánu.

> [!div class="nextstepaction"]
> [Další informace o životním cyklu podrobného plánu](./concepts/lifecycle.md)