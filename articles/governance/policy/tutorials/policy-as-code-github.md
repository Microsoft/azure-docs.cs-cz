---
title: 'Kurz: implementace Azure Policy jako kódu pomocí GitHubu'
description: V tomto kurzu implementujete Azure Policy jako pracovní postup kódu s akcemi export, GitHub a GitHub.
ms.date: 03/31/2021
ms.topic: tutorial
ms.openlocfilehash: 64957671597ad6df237f92176e10280dc45018c9
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092751"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>Kurz: implementace Azure Policy jako kódu pomocí GitHubu

Pracovní postup Azure Policy jako kódu umožňuje spravovat definice zásad a přiřazení jako kód, řídit životní cyklus aktualizací těchto definic a automatizovat ověřování výsledků dodržování předpisů. V tomto kurzu se naučíte používat funkce Azure Policy s GitHubem k vytvoření procesu životního cyklu. Mezi tyto úlohy patří:

> [!div class="checklist"]
> - Export definic zásad a přiřazení do GitHubu
> - Objekty nabízených zásad aktualizované v GitHubu do Azure
> - Aktivace kontroly kompatibility z akce GitHubu

Pokud chcete přiřadit zásadu pro identifikaci aktuálního stavu dodržování předpisů ve stávajících prostředcích, vysvětlete si článek rychlý Start.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Přečtěte si téma [návrh pracovního postupu Azure Policy jako kódu](../concepts/policy-as-code.md) , abyste pochopili vzory návrhu používané v tomto kurzu.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Export objektů Azure Policy z Azure Portal

Pokud chcete exportovat definici zásady z Azure Portal, postupujte podle těchto kroků:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na levé straně stránky Azure Policy vyberte **definice** .

1. Použijte tlačítko **exportovat definice** nebo vyberte tři tečky na řádku definice zásady a pak vyberte **Exportovat definici**.

1. Vyberte tlačítko **Přihlásit se pomocí GitHubu** . Pokud jste se ještě neověřili pomocí GitHubu k autorizaci Azure Policy k exportu prostředku, přečtěte si téma přístup k [akci GitHubu](https://github.com/features/actions) v novém okně, které se otevře, a vyberte **autorizovat AzureGitHubActions** , aby bylo možné pokračovat v procesu exportu. Po dokončení se nové okno automaticky zavře.

1. Na kartě **základy** nastavte následující možnosti a potom v dolní části stránky vyberte kartu **zásady** nebo tlačítko **Další: zásady** .

   - **Filtr úložiště**: nastavte na _Moje úložiště_ , abyste viděli jenom úložiště, která vlastníte, nebo _všechna úložiště_ , abyste viděli všechna oprávnění k přístupu k akci GitHubu.
   - **Úložiště**: nastavte na úložiště, do kterého chcete exportovat prostředky Azure Policy.
   - **Větev**: nastavte větev v úložišti. Použití jiné než výchozí větve je dobrým způsobem, jak ověřit aktualizace před tím, než se sloučí do zdrojového kódu.
   - **Adresář**: _Složka kořenové úrovně_ , do které se mají exportovat prostředky Azure Policy. V závislosti na tom, jaké prostředky se exportují, se vytvoří podsložky v tomto adresáři.

1. Na kartě **zásady** nastavte obor pro hledání tak, že vyberete tři tečky a vybíráte kombinaci skupin pro správu, předplatných nebo skupin prostředků.
   
1. Pomocí tlačítka **přidat definice zásad** můžete vyhledat obor, pro který chcete exportovat objekty. V okně, které se otevře, vyberte každý objekt, který chcete exportovat. Vyfiltruje výběr pomocí vyhledávacího pole nebo typu. Po výběru všech objektů k exportu použijte tlačítko **Přidat** v dolní části stránky.

1. U každého vybraného objektu vyberte požadované možnosti exportu, jako je například _jenom definice_ nebo _definice a přiřazení_ pro definici zásady. Pak vyberte kartu **recenze + export** nebo další: v dolní části stránky klikněte na tlačítko **Revize + exportovat** .

   > [!NOTE]
   > Pokud je zvolená _definice a přiřazení_ možnosti, exportují se jenom přiřazení zásad v oboru nastaveném filtrem při přidání definice zásady.

1. Na kartě **Revize + export** Zkontrolujte shodu podrobností a pak použijte tlačítko **exportovat** v dolní části stránky.

1. Zkontrolujte, zda jsou vybrané prostředky nyní exportovány do vašeho zdrojového kódu, ve složce vašeho úložiště GitHub, větve a _kořenové úrovně_ .

Prostředky Azure Policy jsou exportovány do následující struktury v rámci vybraného úložiště GitHub a _složky na kořenové úrovni_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>Objekty nabízených zásad aktualizované v GitHubu do Azure

1. Když se objekty zásad exportují, vytvoří se také soubor [pracovního postupu GitHubu](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) s názvem, který vám `.github/workflows/manage-azure-policy-<randomLetters>.yml` umožní začít.

   > [!NOTE]
   > Při každém použití exportu se vytvoří soubor pracovního postupu GitHubu. Každá instance souboru je specifická pro možnosti při této akci exportu.

1. Tento soubor pracovního postupu používá akci [spravovat Azure Policy](https://github.com/marketplace/actions/manage-azure-policy) k odesílání změn provedených v exportovaných objektech zásad v úložišti GitHub zpět do Azure Policy. Ve výchozím nastavení akce bere v úvahu a synchronizuje pouze ty soubory, které se liší od těch, které jsou v Azure již použity. Můžete také použít `assignments` parametr v akci a synchronizovat pouze změny provedené v konkrétních souborech přiřazení. Tento parametr lze použít k aplikování přiřazení zásad pouze pro konkrétní prostředí. Další informace najdete v [souboru Readme pro správu Azure Policyového úložiště](https://github.com/Azure/manage-azure-policy).

1. Ve výchozím nastavení se pracovní postup musí aktivovat ručně. Provedete to tak, že použijete **Akce** v GitHubu, vyberete `manage-azure-policy-<randomLetters>` pracovní postup, vyberete **Spustit pracovní postup** a znovu **spustíte pracovní postup** .

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="Snímek obrazovky karty akce, pracovního postupu a tlačítka pro spuštění pracovního postupu ve webovém rozhraní GitHubu":::

   > [!NOTE]
   > Soubor pracovního postupu musí být ve výchozí větvi, která se má zjistit a spustit ručně.

1. Pracovní postup synchronizuje změny provedené v objektech zásad pomocí Azure a poskytne vám stav v protokolech.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="Snímek obrazovky pracovního postupu v akci a podrobnosti protokolu k protokolům":::

1. Pracovní postup také přidá podrobnosti v Azure Policy objektů, které `properties.metadata` můžete sledovat.

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Snímek obrazovky definice Azure Policy v Azure Portal aktualizoval metadata, která jsou specifická pro akci GitHubu.":::

### <a name="trigger-compliance-scans-using-github-action"></a>Spustit kontroly dodržování předpisů pomocí akce GitHubu

Pomocí [akce Azure Policy prověřování dodržování předpisů](https://github.com/marketplace/actions/azure-policy-compliance-scan) můžete spustit kontrolu vyhodnocení dodržování předpisů na vyžádání z [pracovního postupu GitHubu](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) na jednom nebo několika prostředcích, skupinách prostředků nebo předplatných a změnit cestu pracovního postupu na základě stavu dodržování předpisů těchto prostředků. Můžete také nakonfigurovat pracovní postup tak, aby běžel v naplánovaném čase, aby získal nejnovější stav dodržování předpisů v pohodlný čas. Tuto akci GitHubu můžete volitelně také vygenerovat zprávu o stavu dodržování předpisů u prověřených prostředků pro další analýzu nebo pro archivaci.

Následující příklad spustí kontrolu dodržování předpisů u předplatného. 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>Opakování

V tomto kurzu jste úspěšně provedli následující úlohy:

> [!div class="checklist"]
> - Exportované definice zásad a přiřazení do GitHubu
> - Předané objekty zásad aktualizované v GitHubu do Azure
> - Aktivovala se kontrola dodržování předpisů z akce GitHubu.

## <a name="next-steps"></a>Další kroky

Další informace o strukturách definic zásad najdete v tomto článku:

> [!div class="nextstepaction"]
> [Struktura definic Azure Policy](../concepts/definition-structure.md)