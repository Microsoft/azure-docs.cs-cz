---
title: Vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, v prostředí Azure | Dokumentace Microsoftu
description: Tento článek vás provede postupem vytvoření definice zásady pro identifikaci prostředků, které nedodržují předpisy.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/18/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 6bb9eddb6a663e1f230c9c46835661ad20c02cfd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, v prostředí Azure
Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. *Neodpovídají* přiřazení zásad.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásad a přiřadíte definici zásady pro *audit virtuálních počítačů bez spravovaných disků*.

1. V levém podokně stránky služby Azure Policy vyberte **Přiřazení**.
2. V horní části podokna **Přiřazení** vyberte **Přiřadit zásadu**.

   ![Přiřazení definice zásady](media/assign-policy-definition/select-assign-policy.png)

3. Na stránce **Přiřadit zásadu** kliknutím na ![tlačítko Definice zásady](media/assign-policy-definition/definitions-button.png) vedle pole **Zásada** otevřete seznam dostupných definic.

   ![Otevření dostupných definic zásad](media/assign-policy-definition/open-policy-definitions.png)

   Služba Azure Policy obsahuje již integrované definice zásad, které můžete použít. Zobrazí se například následující integrované definice zásad:

   - Vynucovat použití značky a její hodnoty
   - Použít značku a její hodnotu
   - Vyžadovat SQL Server verze 12.0

    Úplný seznam všech dostupných předdefinovaných zásad najdete v tématu [Šablony zásad](json-samples.md).

4. Prohledejte definice zásad a najděte definici *Audit virtuálních počítačů, které nepoužívají spravované disky*. Klikněte na tuto zásadu a potom na **Vybrat**.

   ![Vyhledání správné definice zásady](media/assign-policy-definition/select-available-definition.png)

5. Zadejte zobrazovaný **Název** tohoto přiřazení zásady. V tomto případě použijeme *Audit virtuálních počítačů, které nepoužívají spravované disky*. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o způsobu, jakým toto přiřazení zásady identifikuje všechny virtuální počítače, které nepoužívají spravované disky.
6. Změnou cenové úrovně na **Standard** zajistěte, že se zásada použije na stávající prostředky.

   V rámci služby Azure Policy existují dvě cenové úrovně – *Free* a *Standard*. S úrovní Free můžete vynucovat zásady pouze u budoucích prostředků, zatímco s úrovní Standard je můžete vynucovat také u stávajících prostředků, abyste lépe porozuměli vašemu stavu dodržování předpisů. Další informace o cenách najdete v tématu [Ceny služby Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy/).

7. Vyberte **Obor**, na který chcete zásadu použít.  Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků.
8. Vyberte předplatné (nebo skupinu prostředků), které jste dříve zaregistrovali. V tomto příkladu používáme předplatné **Azure Analytics Capacity Dev**, ale vaše možnosti se mohou lišit. Klikněte na **Vybrat**.

   ![Vyhledání správné definice zásady](media/assign-policy-definition/assign-policy.png)

9. Nechte pole **Vyloučení** prázdné a klikněte na **Přiřadit**.

Nyní jste připraveni identifikovat prostředky, které nedodržují předpisy, abyste porozuměli stavu dodržování předpisů ve vašem prostředí.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

V levém podokně vyberte **Dodržování předpisů** a vyhledejte přiřazení zásady, které jste vytvořili.

![Dodržování zásad](media/assign-policy-definition/policy-compliance.png)

Pokud nějaké stávající prostředky nedodržují předpisy tohoto nového přiřazení, zobrazí se na kartě **Nekompatibilní prostředky**.

Pokud se napříč stávajícími prostředky vyhodnotí nějaká podmínka a zjistí hodnotu True, takové prostředky se označí jako nekompatibilní s příslušnou zásadou. Na předchozím obrázku jsou zobrazené nekompatibilní prostředky. Následující tabulka ukazuje, jak různé akce zásad pracují s vyhodnocením podmínek pro zjištění výsledného stavu. Přestože se logika vyhodnocení na webu Azure Portal nezobrazuje, výsledné stavy dodržování předpisů se zobrazují. Výsledný stav je buď kompatibilní, nebo nekompatibilní.

| **Stav prostředku** | **Akce** | **Vyhodnocení zásad** | **Stav dodržování předpisů** |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Odpovídající |
| Nová | Audit, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Nová | Audit, AuditIfNotExist\* | False | Odpovídající |

\* Akce Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE. Tyto akce také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.
## <a name="clean-up-resources"></a>Vyčištění prostředků

Další příručky v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími kurzy, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem na portálu Azure Portal.
1. V levém podokně vyberte **Přiřazení**.
2. Vyhledejte přiřazení, které jste vytvořili, a klikněte na něj pravým tlačítkem.

   ![Odstranění přiřazení](media/assign-policy-definition/delete-assignment.png)

3.  Vyberte **Odstranit přiřazení**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady k oboru prostředků. Definice zásady zajišťuje, že všechny prostředky v příslušném oboru jsou kompatibilní, a určuje ty, které nejsou.

Další informace o přiřazování zásad pro zajištění, aby **budoucí** vytvořené prostředky dodržovaly předpisy, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./create-manage-policy.md)
