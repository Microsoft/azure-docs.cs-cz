---
title: Zobrazit měsíční trend nákladů odhadované testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Další informace o službě Azure DevTest Labs grafu měsíční trend odhadovaných nákladů.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 13535dae82ef2c8896dad7d6221553d15e4e6a95
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38573807"
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Zobrazit měsíční trend nákladů odhadované testovacího prostředí ve službě Azure DevTest Labs
Funkci náklady na správu služby DevTest Labs pomáhá sledovat náklady testovacího prostředí. Tento článek ukazuje, jak používat **měsíční Trend odhadovaných nákladů** grafu chcete zobrazit aktuální kalendářní měsíc odhadované náklady k datu a očekávané náklady na konci měsíce pro zbytek aktuálního měsíce. Tento článek také ukazuje, jak lépe spravovat náklady na laboratoře nastavením útraty cílům a prahovým hodnotám, které po dosažení aktivační událost DevTest Labs můžete zprávu o výsledcích.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Zobrazení grafu měsíční Trend odhad nákladů
Chcete-li zobrazit graf měsíční Trend nákladů odhad, postupujte takto: 

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu. (Testovací prostředí může již zobrazený na řídicím panelu v části **všechny prostředky**).
1. V seznamu testovacích prostředí vyberte požadované prostředí.  
1. Cvičení **přehled** vyberte **konfigurace a zásad**.   
1. Na levé straně v části **sledování nákladů**vyberte **trend nákladů**.

   Následující snímek obrazovky ukazuje příklad grafu náklady. 
   
    ![Cenově grafu](./media/devtest-lab-configure-cost-management/graph.png)

**Odhadované náklady** hodnotou je aktuální kalendářní měsíc odhadované náklady k datu. **Plánované náklady pro** je odhadované náklady pro celou po zbytek aktuálního měsíce, vypočítá náklady testovacího prostředí za posledních pět dnů.

Částky nákladů se zaokrouhluje na nejbližší celé číslo. Příklad: 

* 5.01 zaokrouhlí až 6 
* 5.50 zaokrouhlí až 6
* 5.99 zaokrouhlí až 6

Jak se uvádí nad grafem, náklady, které se zobrazí v grafu ve výchozím nastavení se *odhadované* náklady pomocí [s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nabízejí sazby. Můžete také nastavit vlastní útraty cíle, které se zobrazí v grafech podle [Správa cíle nákladů pro vaše testovací prostředí.](#managing-cost-targets-for-your-lab)

Kromě toho jsou následující *není* zahrnutých do výpočtu ceny:

* Předplatná CSP a Dreamspark se momentálně nepodporují, protože používá Azure DevTest Labs [rozhraní API pro fakturaci Azure](../billing/billing-usage-rate-card-overview.md) pro výpočet nákladů, který nepodporuje předplatná CSP nebo Dreamspark testovacího prostředí.
* Ceník vaší nabídky. V současné době nemůžete použít plateb (viz obrázek v rámci vašeho předplatného), že máte vyjedná s Microsoftem nebo Microsoft partnery. Se používají pouze tarifů průběžných plateb.
* Daně
* Slevy
* Fakturační Měna. V současné době náklady testovacího prostředí se zobrazí pouze v místní měně USD.

## <a name="managing-cost-targets-for-your-lab"></a>Správa nákladů cíle testovacího prostředí.
DevTest Labs umožňuje lépe spravovat tak, že nastavíte útraty cíl, který pak můžete zobrazit v grafu měsíční Trend nákladů odhad nákladů ve vašem testovacím prostředí. DevTest Labs můžete také pošle oznámení po dosažení zadané cílové útraty nebo prahovou hodnotu. 

1. Na vašem testovacím prostředí **přehled** vyberte **konfigurace a zásad**.
1. Na levé straně v části **sledování nákladů**vyberte **trend nákladů**.

    ![Spravovat cíl tlačítko](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. V **trend nákladů** vyberte **spravovat cíl**.

    ![Spravovat cíl tlačítko](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. V podokně Správa cíl zadejte požadovanou cílovou útraty a prahové hodnoty. Můžete také nastavit, zda každá vybraná prahová hodnota se použije v hlášení v grafu trend nákladů nebo prostřednictvím oznámení webhooku.

    ![Spravovat cíl podokno](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Vyberte časové období, během které chcete sledovat cíle nákladů.
      - **Měsíční**: náklady na cíle jsou sledovány za měsíc.
      - **Oprava**: náklady na cíle jsou sledována pro rozsah dat zadáte datum zahájení a ukončení datová pole. Obvykle to odpovídají jak dlouho je naplánováno spuštění projektu.
   - Zadejte **cílové náklady**. Například to může být, kolik chcete věnovat v časovém období, které jste definovali v tomto testovacím prostředí.
   - Vyberte možnost k povolení nebo zakázání jakékoli prahu chcete hlášené – v přírůstcích po 25 % – až 125 % vaše zadané **cílové náklady**.
      - **Oznámit**: při splnění této prahové hodnoty, se zobrazí oznámení o zadaná adresa URL webhooku.
      - **Vykreslit v grafu**: při splnění této prahové hodnoty, výsledky jsou zobrazeny v grafu trend nákladů, které můžete zobrazit, jak je popsáno v [zobrazení grafu měsíční Trend nákladů odhadované](#viewing-the-monthly-estimated-cost-trend-chart).
   - Pokud budete chtít **upozornění** při splnění prahové hodnoty, je nutné zadat adresu URL webhooku. V oblasti integrace nákladů, vyberte **kliknutím sem přidáte integraci**.

      Zadejte adresu URL Webhooku v podokně Konfigurace oznámení a pak vyberte **OK**.

       ![Konfigurace podokno oznámení](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Pokud zadáte **upozornění**, je nutné zadat adresu URL webhooku.
      - Podobně, pokud definujete adresu URL webhooku, je nutné nastavit **oznámení** k **na** v podokně prahové hodnoty nákladů.
      - Je nutné vytvořit webhook před vstupem tady.  

      Další informace o webhooků najdete v tématu [vytvoření webhooku nebo funkce rozhraní API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky
* [Dva kroky k zajištění vaše náklady na správné cestě ve službě DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Proč prahových hodnot nákladů?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Další postup
Tady jsou dále vyzkoušejte:

* [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) – zjistěte, jak nastavit různé zásady použité k řízení používání testovacího prostředí a jejích virtuálních počítačů. 
* [Vytvoření vlastní image](devtest-lab-create-template.md) – když vytvoříte virtuální počítač, určit základ, který může být buď vlastní image nebo Marketplace image. Tento článek ukazuje, jak vytvořit vlastní image ze souboru VHD.
* [Konfigurace imagí Marketplace](devtest-lab-configure-marketplace-images.md) – DevTest Labs podporuje vytváření virtuálních počítačů založených na imagích Azure Marketplace. Tento článek ukazuje, jak určit, které případně Image Azure Marketplace lze použít při vytváření virtuálních počítačů v testovacím prostředí.
* [Vytvoření virtuálního počítače v testovacím prostředí](devtest-lab-add-vm.md) -ukazuje, jak vytvořit virtuální počítač ze základní image (buď vlastní nebo Marketplace) a způsobu práce s artefakty ve virtuálním počítači.

