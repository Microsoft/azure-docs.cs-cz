---
title: Jak odstranit a exportovat osobní údaje z Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak odstranit a exportovat osobní údaje ze služby Azure DevLast Labs k podpoře vaší povinností v rámci obecného ochrany Regulation (GDPR).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51642418"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Export nebo odstranění osobních údajů v Azure DevTest Labs
Tento článek popisuje kroky pro odstranění a exportování osobních údajů ze služby Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Jaké osobní data shromažďuje nástroj DevTest Labs?
DevTest Labs shromažďuje dva hlavní druhy osobních údajů od uživatele. Jsou: uživatel e-mailovou adresu a ID objektu uživatele. Tyto informace jsou zásadní pro službu a zajistit tak provozu funkce do testovacího prostředí správci a uživatelé testovacího prostředí.

### <a name="user-email-address"></a>E-mailovou adresu uživatele
DevTest Labs používá e-mailová adresa uživatele pro automatické vypnutí e-mailová oznámení odesílat uživatelům testovacího prostředí. E-mailu upozorní uživatele jejich počítače vypíná. Uživatelé můžete buď posunout nebo přeskočit vypnutí, když budou chtít udělat. Konfigurujete e-mailovou adresu na úrovni testovacího prostředí nebo na úrovni virtuálního počítače.

**Nastavení e-mailu v testovacím prostředí:**

![Nastavení e-mailu na úrovni testovacího prostředí](./media/personal-data-delete-export/lab-user-email.png)

**Nastavení e-mailové adrese virtuálního počítače:**

![Nastavení e-mailu na úrovni virtuálního počítače](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID objektu uživatele
DevTest Labs používá ID objektu uživatele zobrazit trendy nákladů měsíce na měsíc a náklady podle prostředků informace pro správce testovacího prostředí. Umožňuje sledování nákladů a správa prahové hodnoty pro své testovací prostředí. 

**Trend odhad nákladů pro aktuální kalendářní měsíc:**
![trend odhad nákladů pro aktuální kalendářní měsíc](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Odhadované náklady za měsíc k datu prostředkem:**
![odhadované náklady za měsíc k datu podle prostředku](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Proč potřebujeme tyto osobní údaje?
Služba DevTest Labs používá osobních údajů pro provozní účely. Tato data jsou důležité pro službu k doručování klíčové funkce. Pokud nastavíte zásady uchovávání informací pro e-mailovou adresu uživatele, uživatelé testovacího prostředí neobdrží včas automatické vypnutí e-mailová oznámení po odstranění jejich e-mailovou adresu v našem systému. Podobně správce testovacího prostředí není možné zobrazit měsíce na měsíc trendů ve vývoji nákladů a náklady podle prostředků pro počítače v jejich labs, pokud jsou odstraněny ID objektu uživatele podle zásady uchovávání informací. Proto musí tato data se uchovávají po dobu, za předpokladu, prostředku daného uživatele je aktivní v testovacím prostředí.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Jak může mít systém zapomenout mé osobní údaje?
Jako uživatel, který testovací prostředí Pokud chcete mít tyto osobní údaje se odstraní, můžete k tomu odstranění odpovídající prostředek v testovacím prostředí. Služba DevTest Labs anonymizes Odstraněná osobní data 30 dní po odstranění uživatelem.

Například pokud odstraníte virtuální počítač nebo odebrán e-mailovou adresu, službu DevTest Labs anonymizes tato data 30 dní po odstranění prostředku. Zásady uchovávání informací 30 dní po odstranění se ujistěte, že poskytujeme odhadu nákladů přesné měsíce na měsíc správci testovacího prostředí.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Jak můžete požádat o export mé osobní údaje?
Jako uživatel, který testovací prostředí můžete požádat o export s osobními údaji, která ukládá služba DevTest Labs. Žádosti o export, přejděte **osobní údaje** možnost **přehled** stránku vašeho testovacího prostředí. Vyberte **žádost o export** tlačítko zahajuje vytvoření souboru ke stažení Excelu v účtu úložiště správce vašeho testovacího prostředí. Potom můžete kontaktovat správce vašeho testovacího prostředí, chcete-li zobrazit tato data.

1. Vyberte **osobní údaje** v nabídce vlevo. 

    ![Osobní data stránky](./media/personal-data-delete-export/personal-data-page.png)
2. Vyberte **skupiny prostředků** , který obsahuje testovací prostředí.

    ![Výběr skupiny prostředků](./media/personal-data-delete-export/select-resource-group.png)
3. Vyberte **účtu úložiště** ve skupině prostředků.
4. Na **účtu úložiště** stránce **objekty BLOB**.

    ![Vyberte dlaždici objekty BLOB](./media/personal-data-delete-export/select-blobs-tile.png)
5. Vyberte kontejner s názvem **labresourceusage** v seznamu kontejnerů.

    ![Vyberte kontejner objektů blob.](./media/personal-data-delete-export/select-blob-container.png)
6. Vyberte **složky** pojmenované po vašem testovacím prostředí. Můžete najít **sdíleného svazku clusteru** soubory pro **disky** a **virtuálních počítačů** ve vaší laboratoři v této složce. Můžete stáhnout tyto soubory sdíleného svazku clusteru, filtrování obsahu pro testovací prostředí uživatele, který požaduje přístup a s nimi sdílet.

    ![Stáhněte si soubor CSV](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>Další postup
Viz následující články: 

- [Nastavení zásad pro testovací prostředí](devtest-lab-get-started-with-lab-policies.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
