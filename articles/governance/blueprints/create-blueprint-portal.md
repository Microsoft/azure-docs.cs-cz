---
title: Vytvořit podrobný plán na portálu
description: Plány Azure slouží k vytváření, definování a je možné nasazovat artefakty na webu Azure portal.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: acee2aabe94f5d015562bd697d08f657895d3b02
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789475"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Rychlý start: Definujte a přiřaďte podrobný plán na portálu

Když se dozvíte, jak vytvořit a přiřadit plány, můžete definovat běžných vzorů pro vývoj konfigurací opakovaně použitelných a rychle nasaditelné založené na Azure Resource Manageru šablony, zásady, zabezpečení a další. V tomto kurzu zjistíte, jak pomocí Azure plány provádět některé běžné úlohy související s vytváření, publikování a přiřazení podrobného plánu v rámci vaší organizace. Mezi tyto úlohy patří:

> [!div class="checklist"]
> - Vytvoření nového podrobného plánu a přidání různých podporovaných artefaktů
> - Provádění změn stávajícího podrobného plánu ve stavu **Koncept**
> - Označení podrobného plánu připraveného k přiřazení stavem **Publikováno**
> - Přiřazení podrobného plánu ke stávajícímu předplatnému
> - Kontrola stavu a průběhu přiřazeného podrobného plánu
> - Odebrání podrobného plánu přiřazeného k předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-a-blueprint"></a>Vytvořit podrobný plán

Jako první krok při definování standardního vzoru pro dodržování předpisů je sestavení podrobného plánu z dostupných prostředků. V tomto příkladu, vytvořte nový plán s názvem **MyBlueprint** ke konfiguraci přiřazení rolí a zásad pro předplatné. A přidejte novou skupinu prostředků a vytvořit přiřazení šablony a role správce prostředků na novou skupinu prostředků.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **podrobný plán definice** ze stránky na levé straně a vyberte **+ vytvořit podrobný plán** tlačítko v horní části stránky.

   Nebo vyberte **vytvořit** z **Začínáme** stránku a přejít přímo k vytváření podrobný plán.

   ![Vytvořit podrobný plán ze stránky definice podrobného plánu](./media/create-blueprint-portal/create-blueprint-button.png)

1. Zadejte **název podrobného plánu** například **MyBlueprint**. (Použijte maximálně 48 písmena a čísla, ale žádné mezery ani speciální znaky). Ponechte **podrobný plán popis** zatím prázdné.

1. V **umístění definice** pole, vyberte tři tečky na pravé straně, vyberte [skupiny pro správu](../management-groups/overview.md) nebo předplatného, ve které chcete uložit podrobný plán a zvolte **vyberte**.

1. Ověřte správnost informací. **Název podrobného plánu** a **umístění definice** pole není možné později změnit. Potom vyberte **Další: Artefakty** v dolní části stránky nebo **artefakty** kartě v horní části stránky.

1. Přidání přiřazení role na úrovni předplatného:

   1. Vyberte **+ přidání artefaktu** řádek pod **předplatné**. **Přidání artefaktu** na pravé straně v prohlížeči se otevře okno.

   1. Vyberte **přiřazení Role** pro **Typ artefaktu**.

   1. V části **Role**vyberte **Přispěvatel**. Nechte **přidat uživatele, aplikace nebo skupinu** pole u zaškrtávacího políčka, která určuje dynamického parametru.

   1. Vyberte **přidat** přidat tento artefakt podrobný plán.

   ![Přiřazení role pro artefakt podrobného plánu](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > Většiny artefaktů, které podporují parametry. Je parametr, který se přiřadí hodnota. při vytváření podrobného plánu *statický parametr*. Pokud tento parametr je přiřazený během přiřazení podrobného plánu se jedná *dynamického parametru*. Další informace najdete v [parametry podrobného plánu](./concepts/parameters.md).

1. Přidáte přiřazení zásad na úrovni předplatného:

   1. Vyberte **+ přidání artefaktu** řádek pod artefaktu přiřazení role.

   1. Vyberte **přiřazení zásady** pro **Typ artefaktu**.

   1. Změna **typ** k **integrované**. V **hledání**, zadejte **značka**.

   1. Vyklikněte z **hledání** pro zobrazení filtrování. Vyberte **použít značku a její výchozí hodnotu pro skupiny prostředků**.

   1. Vyberte **přidat** přidat tento artefakt podrobný plán.

1. Vyberte řádek přiřazení zásady **použít značku a její výchozí hodnotu pro skupiny prostředků**.

1. Otevře se okno k zadání parametrů s artefaktem jako součást definice podrobného plánu a umožňuje nastavení parametrů pro všechna přiřazení (statických parametrů) na tento podrobný plán místo při přiřazení (dynamické parametry). Tento příklad používá dynamických parametrů během přiřazení podrobného plánu, takže políčko nechte výchozí hodnoty a vyberte **zrušit**.

1. Přidáte skupinu prostředků na úrovni předplatného:

   1. Vyberte **+ přidání artefaktu** řádek pod **předplatné**.

   1. Vyberte **skupiny prostředků** pro **Typ artefaktu**.

   1. Nechte **zobrazovaný název artefaktu**, **název skupiny prostředků**, a **umístění** pole prázdná, ale ujistěte se, že je zaškrtnuto zaškrtávací políčko pro každou vlastnost parametru tak, aby se daly dynamické parametry.

   1. Vyberte **přidat** přidat tento artefakt podrobný plán.

1. Přidáte šablonu v rámci skupiny prostředků:

   1. Vyberte **+ přidání artefaktu** řádek pod **ResourceGroup** položka.

   1. Vyberte **šablony Azure Resource Manageru** pro **Typ artefaktu**, nastavte **zobrazovaný název artefaktu** k **StorageAccount**a nechat  **Popis** prázdné.

   1. Na kartu **šablona** v poli editoru, vložte následující šablonu správce prostředků.
      Když vložíte šablony, vyberte **parametry** kartu a Všimněte si, že parametry šablony **storageAccountType** a **umístění** byly zjištěny. Každý parametr byla automaticky zjištěna a vyplní, ale nakonfigurován jako dynamický parametr.

      > [!IMPORTANT]
      > Pokud importujete šablonu, ujistěte se, že soubor je pouze JSON a neobsahuje HTML. V případě, že přejdete na adresu URL na Githubu, ujistěte se, že jste vybrali **RAW** čistý soubor JSON a není ten zabalena HTML pro zobrazení na Githubu. Pokud importovaná šablona není čistě JSON, dojde k chybě.

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

   1. Zrušte **storageAccountType** zaškrtávací políčko a Všimněte si, že rozevírací seznam obsahuje pouze hodnoty, které jsou zahrnuty v šabloně Resource Manageru v rámci **allowedValues**. Vyberte pole a nastavte ji zpět do dynamického parametru.

   1. Vyberte **přidat** přidat tento artefakt podrobný plán.

   ![Šablona Resource Manageru pro artefakt podrobného plánu](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Váš konečný plán by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že má každý artefakt  **_x_ z celkového počtu _y_ naplněných parametrů** v **parametry** sloupce. Dynamické parametry jsou nastaveny při každé přiřazení podrobný plán.

   ![Definice podrobného plánu dokončené](./media/create-blueprint-portal/completed-blueprint.png)

1. Teď, když byly přidány všechny plánované artefakty, vyberte **uložit koncept** v dolní části stránky.

## <a name="edit-a-blueprint"></a>Upravit podrobný plán

V [vytvořit podrobný plán](#create-a-blueprint), nepřidal zadejte popis nebo přidání přiřazení role do nové skupiny prostředků. Můžete je vyřešit i pomocí následujících kroků:

1. Vyberte **podrobný plán definice** ze stránky na levé straně.

1. V seznamu plány, klikněte pravým tlačítkem na ten, který jste dříve vytvořili a vyberte **upravit podrobný plán**.

1. V **podrobný plán popis**, poskytují několik informací o podrobný plán a artefakty, které tvoří ji. V takovém případě zadejte vypadat: **Tento podrobný plán nastaví značku přiřazení zásady a roli v předplatném, skupina prostředků vytvoří a nasadí prostředků šablony a roli přiřazení do této skupiny prostředků.**

1. Vyberte **Další: Artefakty** v dolní části stránky nebo **artefakty** kartě v horní části stránky.

1. Přidání přiřazení role v rámci skupiny prostředků:

   1. Vyberte **+ přidání artefaktu** řádek přímo pod **ResourceGroup** položka.

   1. Vyberte **přiřazení Role** pro **Typ artefaktu**.

   1. V části **Role**vyberte **vlastníka**a zrušte zaškrtnutí políčka v části **přidat uživatele, aplikace nebo skupina** pole.

   1. Vyhledejte a vyberte uživatele, aplikace nebo skupinu přidat. Tento artefakt používá statický parametr sadu stejné v každé přiřazení podrobný plán.

   e. Vyberte **přidat** přidat tento artefakt podrobný plán.

   ![Druhé přiřazení role pro artefakt podrobného plánu](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Váš konečný plán by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že se zobrazí nově přidaná role přiřazení **naplněných parametrů 1: 1**. To znamená, že je statický parametr.

   ![Druhá definice pro dokončené podrobného plánu](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Vyberte **uložit koncept** teď, když se aktualizovala.

## <a name="publish-a-blueprint"></a>Publikování podrobného plánu

Teď, když jsou do podrobného plánu přidané všechny plánované artefakty, je na čase ho publikovat.
Publikování je možné přiřadit k předplatnému podrobného plánu.

1. Vyberte **podrobný plán definice** ze stránky na levé straně.

1. V seznamu plány, klikněte pravým tlačítkem na ten, který jste dříve vytvořili a vyberte **publikovat podrobný plán**.

1. V podokně, které se otevře, zadejte **verze** (písmena, číslice a spojovníky s maximální délkou 20 znaků), jako například **v1**. Volitelně můžete zadat text v **změnit poznámky**, jako například **nejprve publikovat**.

1. Vyberte **publikovat** v dolní části stránky.

## <a name="assign-a-blueprint"></a>Přiřazení podrobného plánu

Po publikování podrobný plán můžete přiřadit k předplatnému. Přiřadíte podrobný plán, který jste vytvořili předplatná v rámci hierarchie skupiny správy. Pokud podrobný plán je uložený na předplatné, můžete přiřadit pouze k tomuto předplatnému.

1. Vyberte **podrobný plán definice** ze stránky na levé straně.

1. V seznamu plány, klikněte pravým tlačítkem na ten, který jste dříve vytvořili (nebo vyberte tři tečky) a vyberte **přiřazení podrobného plánu**.

1. Na **přiřazení podrobného plánu** stránku, **předplatné** rozevíracího seznamu vyberte předplatné, které chcete nasadit Tento podrobný plán k.

   Pokud nejsou k dispozici z podporovaných nabídky Enterprise [fakturace Azure](../../billing/index.md), **vytvořit nový** odkaz se aktivuje v části **předplatné** pole. Postupujte následovně:

   1. Vyberte **vytvořit nový** odkaz pro vytvoření nového předplatného místo výběru existující aplikace.

   1. Zadejte **zobrazovaný název** pro nové předplatné.

   1. Vyberte dostupných **nabízejí** z rozevíracího seznamu.

   1. Můžete vybrat na tři tečky [skupiny pro správu](../management-groups/index.md) , předplatné bude podřízený.

   1. Vyberte **vytvořit** v dolní části stránky.

   ![Vytvoření odběru pro odběr přiřazení podrobného plánu](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > Vytváření nového odběru okamžitě po výběru **vytvořit**.

   > [!NOTE]
   > Přiřazení se vytvoří pro každé předplatné, které jste vybrali. Změny můžete provádět na jedno předplatné přiřazení později bez nutnosti změn ve zbývající části vybraných předplatných.

1. Pro **název přiřazení**, zadejte jedinečný název pro toto přiřazení.

1. V **umístění**, vyberte oblast pro spravovaný objekt nasazení identity a předplatné má být vytvořen v. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Nechte **verze definice podrobného plánu** rozevírací výběr **publikováno** verze **v1** položka. (Výchozí hodnota je naposledy publikované verze.)

1. Pro **zámek přiřazení**, ponechte výchozí hodnotu na **bez zámku**. Další informace najdete v tématu [uzamčení prostředků plány](./concepts/resource-locking.md).

   ![Uzamčení a spravovaných identit pro přiřazení](./media/create-blueprint-portal/assignment-locking-mi.png)

1. V části **Identity spravované**, ponechte výchozí hodnotu **přiřazenou systémem**.

1. Pro přiřazení role na úrovni předplatného **[skupiny uživatelů nebo název aplikace]: Přispěvatel**, vyhledejte a vyberte uživatele, aplikace nebo skupinu.

1. Pro přiřazení zásadou na úrovni předplatného, nastavte **název značky** k **nákladové středisko** a **hodnota značky** k **ContosoIT**.

1. Pro **ResourceGroup**, zadejte **název** z **StorageAccount** a **umístění** z **USA – východ 2** z rozevíracím seznamu.

   > [!NOTE]
   > Pro každý artefakt, který jste přidali v rámci skupiny prostředků během definice podrobného plánu je tento artefakt odsazen bylo v souladu s skupinu prostředků nebo objekt, který nasadíte ji.
   > Artefakty, které buď není přijímají parametry nebo mít žádné parametry definované v přiřazení jsou uvedeny pouze pro kontextové informace.

1. Na šablony Azure Resource Manageru **StorageAccount**vyberte **Standard_GRS** pro **storageAccountType** parametru.

1. Přečtěte si informace o poli v dolní části stránky a pak vyberte **přiřadit**.

## <a name="track-deployment-of-a-blueprint"></a>Sledování aplikace plánu

Pokud byl plán přiřazený k jednomu nebo několika předplatným, stanou dvě věci:

- Podrobný plán se přidá do **přiřazené podrobné plány** stránky pro každé předplatné.
- Zahájí se proces nasazení všechny artefakty definované podrobný plán.

Teď, když podrobný plán se přiřadila k předplatnému, ověřte průběh nasazení:

1. Vyberte **přiřazené podrobné plány** ze stránky na levé straně.

1. V seznamu plány, klikněte pravým tlačítkem na ten, který jste dříve přiřadili a vyberte **zobrazit podrobnosti o přiřazení**.

   ![Zobrazit podrobnosti o přiřazení ze stránky přiřazené podrobné plány.](./media/create-blueprint-portal/view-assignment-details.png)

1. Na **podrobný plán přiřazení** stránce, ověřte, že byly úspěšně nasazeny všechny artefakty a že nebyly zjištěny žádné chyby během nasazení. Pokud došlo k chybám, přečtěte si téma [řešení potíží s plány](./troubleshoot/general.md) pro postupu jak zjistit, co se nepovedlo.

## <a name="unassign-a-blueprint"></a>Zrušení přiřazení plánu

Pokud už nepotřebujete přiřazení podrobného plánu, odeberte ji z odběru. Podrobný plán může byla nahrazena novější plán aktualizace vzory, zásady a návrhy. Po odebrání podrobného plánu zůstanou přiřazené artefakty, které byly jeho součástí. Pokud chcete odebrat přiřazení podrobného plánu, postupujte následovně:

1. Vyberte **přiřazené podrobné plány** ze stránky na levé straně.

1. V seznamu plány vyberte plán, který chcete zrušit přiřazení. Vyberte **zrušit přiřazení podrobného plánu** tlačítko v horní části stránky.

1. Přečtěte si o potvrzení a pak vyberte **OK**.

## <a name="delete-a-blueprint"></a>Smazání podrobného plánu

1. Vyberte **podrobný plán definice** ze stránky na levé straně.

1. Klikněte pravým tlačítkem na podrobný plán, který chcete odstranit a vyberte **odstranit podrobný plán**. Potom vyberte **Ano** v dialogovém okně potvrzení.

> [!NOTE]
> Odstraňuje se plán v této metodě se odstraní také všechny publikované verze vybraného podrobného plánu.
> Pokud chcete odstranit jednu verzi, otevřete podrobný plán, vyberte **publikované verze** kartu, vyberte verzi, kterou chcete odstranit a potom vyberte **odstranit tuto verzi**. Navíc nelze odstranit podrobný plán odstraníte všechna přiřazení podrobného plánu definice podrobného plánu.

## <a name="next-steps"></a>Další postup

- Další informace o [podrobný plán životního cyklu](./concepts/lifecycle.md).
- Principy použití [statických a dynamických parametrů](./concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](./concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](./concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](./how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](./troubleshoot/general.md)