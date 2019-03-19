---
title: Vytvořit podrobný plán na portálu
description: Plány Azure slouží k vytváření, definování a je možné nasazovat artefakty na webu Azure Portal.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: fdf87bff026dee4969b3995b37c31de3ead7714b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004915"
---
# <a name="define-and-assign-an-azure-blueprint-in-the-portal"></a>Definování a přiřazení podrobného plánu Azure na portálu

Seznamte se s principy vytváření a přiřazování podrobných plánů, abyste mohli definovat běžné vzory a vyvíjet opakovaně použitelné a rychle nasaditelné konfigurace založené na šablonách Resource Manageru, zásadách, zabezpečení a dalších. V tomto kurzu se naučíte používat podrobné plány Azure Blueprint k provádění nejčastějších úloh spojených s vytvářením, publikováním a přiřazením podrobného plánu v organizaci, jako je například:

> [!div class="checklist"]
> - Vytvoření nového podrobného plánu a přidání různých podporovaných artefaktů
> - Provádění změn stávajícího podrobného plánu ve stavu **Koncept**
> - Označení podrobného plánu připraveného k přiřazení stavem **Publikováno**
> - Přiřazení podrobného plánu ke stávajícímu předplatnému
> - Kontrola stavu a průběhu přiřazeného podrobného plánu
> - Odebrání podrobného plánu přiřazeného k předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-a-blueprint"></a>Vytvoření podrobného plánu

Jako první krok při definování standardního vzoru pro dodržování předpisů je sestavení podrobného plánu z dostupných prostředků. V tomto příkladu vytvoříte podrobný plán, který se nazývá MyBlueprint a který pro předplatné konfiguruje přiřazení rolí a zásad, přidá skupinu prostředků a vytvoří šablonu Resource Manager a přiřadí skupině prostředků zdroje.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **podrobný plán definice** ze stránky na levé straně a klikněte **+ vytvořit podrobný plán** tlačítko v horní části stránky.

   - Alternativně klepněte na **vytvořit** ze stránky **Začínáme** a přejděte přímo k vytváření podrobného plánu.

   ![Vytvořit podrobný plán](./media/create-blueprint-portal/create-blueprint-button.png)

1. Zadejte **název podrobného plánu** jako je například "MyBlueprint" (písmena a čísla – až 48 znaků, ale žádné mezery ani speciální znaky) pro podrobný plán, ale nechejte **podrobný plán popis** zatím prázdné. V **umístění definice** pole, klikněte na tlačítko se třemi tečkami na pravé straně, vyberte [skupiny pro správu](../management-groups/overview.md) nebo předplatného, ve které chcete uložit podrobný plán a klikněte na tlačítko **vyberte**.

1. Ověřte správnost informací ( **název podrobného plánu** a **umístění definice** pole není možné později změnit) a klikněte na tlačítko **Další: Artefakty** v dolní části stránky nebo **artefakty** kartě v horní části stránky.

1. Přidání přiřazení role v předplatném: Levým tlačítkem myši na **+ přidání artefaktu...**  řádek pod **předplatné** a na pravé straně v prohlížeči se otevře okno "Přidání artefaktu". Vyberte "Přiřazení Role" pro _Typ artefaktu_. V části _Role_, vyberte "Přispěvatel" a nechte _přidat uživatele, aplikace nebo skupina_ pole zaškrtávací políčko označující **dynamického parametru**. Klikněte na tlačítko **přidat** k přidání tohoto artefaktu do podrobného plánu.

   ![Artefakt - přiřazení role](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > Většina _artefaktů_ podporuje parametry. Parametr přiřazený hodnotě během vytváření plánu je **statický parametr**. Pokud je tento parametr přiřazený během přiřazení podrobného plánu, je to **dynamický parametr**. Další informace najdete v [parametry podrobného plánu](./concepts/parameters.md).

1. Přidání přiřazení zásad v předplatném: Levým tlačítkem myši na **+ přidání artefaktu...**  řádek pod artefaktu přiřazení role. Vyberte "Přiřazení zásady" pro _Typ artefaktu_. Změňte _typ_ na "Integrované" a v _hledání_ zadejte 'tag'. Vyklikněte z _hledání_ pro zobrazení filtrování. Zvolte možnost 'Použít značku a její výchozí hodnotu ke skupinám zdrojů' kliknutím na ni. Klikněte na tlačítko **přidat** k přidání tohoto artefaktu do podrobného plánu.

1. Klikněte na řádek přiřazení zásady 'Použít značku a její výchozí hodnotu na skupiny zdrojů'. Okno k zadání parametrů s artefaktem jako součást definice podrobného plánu se otevře a povolí nastavení parametrů pro všechna přiřazení (**statické parametry**)založených na podrobném plánu místo během přiřazení (**dynamické parametry**). V tomto příkladu se během přiřazení podrobného plánu používají **dynamické parametry**, takže ponechte výchozí nastavení a klikněte na **Zrušit**.

1. Přidání skupiny prostředků v předplatném: Levým tlačítkem myši na **+ přidání artefaktu...**  řádek pod **předplatné**. Vyberte 'Skupinu prostředků' pro _Typ artefaktu_. Nechte _zobrazovaný název artefaktu_, _název skupiny prostředků_, a _umístění_ pole prázdná, ale ujistěte se, že je zaškrtnuto políčko pro každou vlastnost parametru tak, aby se daly **dynamických parametrů**. Klikněte na tlačítko **přidat** k přidání tohoto artefaktu do podrobného plánu.

1. Přidáte šablonu v rámci skupiny prostředků: Levým tlačítkem myši na **+ přidání artefaktu...** řádek pod **ResourceGroup** položka. Vyberte 'šablonu Azure Resource Manager' pro _Artefakt typu_, nastavte  _zobrazovaný název Artefaktu_ na 'StorageAccount' a nechte pole _Popis_ prázdné. Na kartu **šablona** v poli editoru, vložte následující šablonu správce prostředků. Po vložení šablony, vyberte **parametry** kartu a Všimněte si, že parametry šablony **storageAccountType** a **umístění** byly zjištěny. Každý parametr byla automaticky zjištěna a vyplní, ale nakonfigurovaný jako **dynamického parametru**. Zrušte zaškrtnutí z **storageAccountType** zaškrtávací políčko a Všimněte si, že rozevírací seznam obsahuje pouze hodnoty, které jsou zahrnuty v šabloně Resource Manageru v rámci **allowedValues**. Zaškrtněte políčko ke zpětnému nastavení na **dynamický parametr**. Klikněte na tlačítko **přidat** k přidání tohoto artefaktu do podrobného plánu.

   > [!IMPORTANT]
   > Pokud importujete šablonu, ujistěte se, že soubor je pouze JSON a neobsahuje HTML. Když přejdete na adresu URL na Github, ujistěte se, že jste klikli **RAW** k získání čistého souboru JSON a ne na zabalený s HTML pro zobrazení na Github. Pokud importovaná šablona není čistě JSON, dojde k chybě.

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
               "defaultValue": "[resourceGroups('ResourceGroup').location]",
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

   ![Šablona Artefakt Resource Manager](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Váš konečný plán by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že každý artefakt má '_x_ z _y_ obsazených parametrů' ve sloupci _Parametry_. **Dynamické parametry** se nastavují při každém přiřazení podrobného plánu.

   ![Dokončený podrobný plán](./media/create-blueprint-portal/completed-blueprint.png)

1. Teď, když byly přidány všechny plánované artefakty, klikněte na tlačítko **uložit koncept** v dolní části stránky.

## <a name="edit-a-blueprint"></a>Upravit podrobný plán

V části [Vytvoření podrobného plánu](#create-a-blueprint) se pro novou skupinu prostředků nezadal popis ani se k ní nepřidalo přiřazení role. Obojí je možné napravit pomocí následujícího postupu:

1. Vyberte **podrobný plán definice** ze stránky na levé straně.

1. V seznamu plány, klikněte pravým tlačítkem na ten, který jste dříve vytvořili a vyberte **upravit podrobný plán**.

1. V **podrobný plán popis**, poskytují několik informací o podrobný plán a artefakty, které tvoří ji. V takovém případě zadejte vypadat: "Tento podrobný plán nastaví značku přiřazení zásady a roli v předplatném, skupina prostředků vytvoří a nasadí prostředků šablony a roli přiřazení do této skupiny prostředků."

1. Klikněte na tlačítko **Další: Artefakty** v dolní části stránky nebo **artefakty** kartě v horní části stránky.

1. Přidání přiřazení role v rámci skupiny prostředků: Levým tlačítkem myši na **+ přidání artefaktu...**  řádek přímo pod **ResourceGroup** položka. Vyberte "Přiřazení Role" pro _Typ artefaktu_. V části _Role_, vyberte "Vlastník" a zrušte zaškrtnutí u _přidat uživatele, aplikace nebo skupina_ pole a vyhledejte a vyberte uživatele, aplikace nebo skupinu přidat. Tento artefakt používá **statický parametr**, který je v každém přiřazení tohoto podrobného plánu nastavený stejně. Klikněte na tlačítko **přidat** k přidání tohoto artefaktu do podrobného plánu.

   ![Artefakt - přiřazení role #2](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Váš konečný plán by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že se zobrazí nově přidaná role přiřazení **naplněných parametrů 1: 1** to znamená, že se jedná **statický parametr**.

   ![Dokončený podrobného plánu #2](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Klikněte na tlačítko **uložit koncept** teď, když se aktualizoval.

## <a name="publish-a-blueprint"></a>Publikování podrobného plánu

Teď, když jsou do podrobného plánu přidané všechny plánované artefakty, je na čase ho publikovat.
Publikováním ho zpřístupní, aby se dalo přiřadit k předplatnému.

1. Vyberte **podrobný plán definice** ze stránky na levé straně.

1. V seznamu plány, klikněte pravým tlačítkem na ten, který jste dříve vytvořili a vyberte **publikovat podrobný plán**.

1. V dialogovém okně, které se otevře, zadejte **verzi** (písmena, číslice a spojovníky s maximální délkou 20 znaků), například „v1“, a **poznámky ke změnám** (volitelné), například „První publikování“.

1. Klikněte na **Publikovat** ve spodní části stránky.

## <a name="assign-a-blueprint"></a>Přiřazení podrobného plánu

Jakmile podrobný plán publikujete, můžete ho přiřadit předplatnému. Přiřaďte vytvořený podrobný plán některému z předplatných v hierarchii skupiny pro správu. Pokud podrobný plán je uložený na předplatné, můžete přiřadit pouze k tomuto předplatnému.

1. Vyberte **podrobný plán definice** ze stránky na levé straně.

1. V seznamu plány, klikněte pravým tlačítkem na jeden jste dříve vytvořili (nebo vyberte tři tečky) a vyberte **přiřazení podrobného plánu**.

1. Na **přiřazení podrobného plánu** vyberte předplatná, kterou chcete nasadit Tento podrobný plán k z **předplatné** rozevíracího seznamu.

   - Pokud nejsou k dispozici z podporovaných nabídky Enterprise [fakturace Azure](../../billing/index.md), **vytvořit nový** odkaz se aktivuje v části **předplatné** pole.

     1. Vyberte **vytvořit nový** odkaz pro vytvoření nového předplatného místo výběru existující aplikace.

        ![Podrobný plán přiřazení - vytvoření odběru](./media/create-blueprint-portal/assignment-create-subscription.png)

     1. Zadejte **zobrazovaný název** pro nové předplatné.

     1. Vyberte dostupných **nabízejí** z rozevíracího seznamu.

     1. Můžete vybrat na tři tečky [skupiny pro správu](../management-groups/index.md) předplatné bude podřízený.

     1. Vyberte **vytvořit** v dolní části stránky.

     > [!IMPORTANT]
     > Nový odběr je okamžitě vytvořen při **vytvořit** zaškrtnuto.

   > [!NOTE]
   > Pro každé vybrané předplatné se vytvoří přiřazení, což umožní pozdější změny přiřazení jednoho předplatného bez nutnosti vynucení změn ve zbývajících vybraných předplatných.

1. Pro **název přiřazení**, zadejte jedinečný název pro toto přiřazení.

1. V **umístění**, vyberte oblast pro spravovaný objekt nasazení identity a předplatné má být vytvořen v. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro zdroje Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Nechte **verze definice podrobného plánu** rozevírací nabídce **publikováno** verze v položce "v1" (výchozí hodnota je naposledy **publikováno** verze).

1. Pro **zámek přiřazení**, ponechte výchozí hodnotu na **bez zámku**. Další informace naleznete v tématu [uzamčení zdrojů plánu](./concepts/resource-locking.md).

   ![Přiřazení - zamykání a spravovaných identit](./media/create-blueprint-portal/assignment-locking-mi.png)

1. V části **Identity spravované**, ponechte výchozí hodnotu **přiřazenou systémem**.

1. Pro přiřazení role na úrovni předplatného **[skupiny uživatelů nebo název aplikace]: Přispěvatel**, vyhledejte a vyberte uživatele, aplikace nebo skupinu.

1. Pro přiřazení zásady úrovně předplatného, nastavte **název tagu** na 'CostCenter ' a **hodnotu tagu** na 'ContosoIT '.

1. Pro 'ResourceGroup ' zadejte **název** ze 'StorageAccount' a **umístění** z 'USA – východ 2 ' z rozevíracího seznamu.

   > [!NOTE]
   > Každý artefakt přidaný v rámci skupiny prostředků během definice podrobného plánu by měl být v souladu se skupinou prostředků nebo objektem, se kterými se má nasadit. Artefakty, které buď nepřijímají parametry, nebo nemají žádné parametry k definování při přiřazení, jsou uvedené pouze kvůli informacím o kontextu.

1. V šabloně Azure Resource Manager 'StorageAccount' vyberte parametr **storageAccountTyp** 'Standard_GRS'.

1. Přečtěte si informace v poli v dolní části stránky a pak klikněte na **přiřadit**.

## <a name="track-deployment-of-a-blueprint"></a>Sledování aplikace plánu

Pokud byl plán přiřazený k jednomu nebo několika předplatným, stanou dvě věci:

- Podrobný plán se přidá do **přiřazené podrobné plány** stranu přiřazeno předplatné
- Proces aplikace všech artefaktů definovaných podrobným plánem začíná

Teď, když se podrobný plán přiřadil k předplatnému, ověřte průběh aplikace.

1. Vyberte **přiřazené podrobné plány** ze stránky na levé straně.

1. V seznamu plány, klikněte pravým tlačítkem na ten, který jste dříve přiřadili a vyberte **zobrazit podrobnosti o přiřazení**.

   ![Zobrazení podrobností přiřazení](./media/create-blueprint-portal/view-assignment-details.png)

1. Na **podrobný plán přiřazení** stránce, ověřte, že všechny artefakty se úspěšně nasadilo a že nebyly žádné chyby během nasazení. Pokud došlo k nějakým chybám, přečtěte si téma [řešení potíží s podrobným plánem](./troubleshoot/general.md), kde najdete postup, jak určit, co se nepovedlo.

## <a name="unassign-a-blueprint"></a>Zrušení přiřazení plánu

Pokud už přiřazení podrobného plánu nepotřebujete, odeberte ho z předplatného. Podrobný plán se mohl nahradit novějším podrobným plánem s aktualizovanými vzory, zásadami a návrhy. Po odebrání podrobného plánu zůstanou přiřazené artefakty, které byly jeho součástí. Pokud chcete odebrat přiřazení podrobného plánu, postupujte následovně:

1. Vyberte **přiřazené podrobné plány** ze stránky na levé straně.

1. V seznamu plány, vyberte plán, který je k přiřazení a pak klikněte na tlačítko **zrušit přiřazení podrobného plánu** tlačítko v horní části stránky.

1. Přečtěte si potvrzovací zprávu a pak klikněte na tlačítko **OK**.

## <a name="delete-a-blueprint"></a>Smazání podrobného plánu

1. Vyberte **podrobný plán definice** ze stránky na levé straně.

1. Klikněte pravým tlačítkem na plán, podle kterého chcete odstranit a vyberte **odstranit podrobný plán**, pak klikněte na tlačítko **Ano** v potvrzovacím dialogovém okně.

> [!NOTE]
> Odstraněním podrobného plánu v této metodě se odstraní také všechny **publikované verze** vybraného podrobného plánu. Pokud chcete odstranit jen jednu verzi, otevřete podrobný plán, klikněte na kartu **publikované verze** vyberte a klikněte na verzi, kterou chcete odstranit a potom klikněte na tlačítko **odstranit tuto verzi**. Kromě toho platí, že není možné odstranit podrobný plán s přiřazeními, dokud se neodstraní všechna přiřazení tohoto podrobného plánu.

## <a name="next-steps"></a>Další postup

- Další informace o [podrobný plán životního cyklu](./concepts/lifecycle.md).
- Vysvětlení použití [statické a dynamické parametry](./concepts/parameters.md).
- Zjistěte, jak přizpůsobit [podrobný plán pořadí řazení](./concepts/sequencing-order.md).
- Zjistěte, jak se využívání [podrobný plán uzamčení prostředků](./concepts/resource-locking.md).
- Zjistěte, jak [aktualizovat existující přiřazení](./how-to/update-existing-assignments.md).
- Řešení problémů při přiřazení podrobného plánu se [obecné řešení potíží s](./troubleshoot/general.md).