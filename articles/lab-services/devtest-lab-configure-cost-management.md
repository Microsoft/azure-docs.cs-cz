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
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: 1f6887a403e03ac11bb080a1d9855daff66ca088
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096771"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Sledujte náklady spojené s testovacího prostředí ve službě Azure DevTest Labs
Tento článek obsahuje informace o tom, jak sledovat náklady testovacího prostředí. Ukazuje, jak chcete zobrazit odhadované náklady trent za aktuální kalendářní měsíc pro testovací prostředí. Tento článek také ukazuje, jak zobrazit náklady za měsíc k datu jednotlivých prostředků v testovacím prostředí.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Zobrazit měsíční trend nákladů odhadované testovacího prostředí 
V této části se dozvíte, jak používat **měsíční Trend odhadovaných nákladů** grafu chcete zobrazit aktuální kalendářní měsíc odhadované náklady k datu a očekávané náklady na konci měsíce pro zbytek aktuálního měsíce. Také se dozvíte, jak spravovat náklady na laboratoře nastavením útraty cílům a prahovým hodnotám, které po dosažení aktivační událost DevTest Labs můžete zprávu o výsledcích.

Chcete-li zobrazit graf měsíční Trend nákladů odhad, postupujte takto: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte vaše testovací prostředí.  
4. Vyberte **konfigurace a zásad** v nabídce vlevo.  
4. Vyberte **trend nákladů** v **sledování nákladů** části v nabídce vlevo. Následující snímek obrazovky ukazuje příklad grafu náklady. 
   
    ![Cenově grafu](./media/devtest-lab-configure-cost-management/graph.png)

    **Odhadované náklady** hodnotou je aktuální kalendářní měsíc odhadované náklady k datu. **Plánované náklady pro** je odhadované náklady pro celou po zbytek aktuálního měsíce, vypočítá náklady testovacího prostředí za posledních pět dnů.

    Částky nákladů se zaokrouhluje na nejbližší celé číslo. Příklad: 

   * 5.01 zaokrouhlí až 6 
   * 5.50 zaokrouhlí až 6
   * 5.99 zaokrouhlí až 6

     Jak se uvádí nad grafem, náklady, které se zobrazí v grafu ve výchozím nastavení se *odhadované* náklady pomocí [s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nabízejí sazby. Můžete také nastavit vlastní útraty cíle, které se zobrazí v grafech podle [Správa cíle nákladů pro vaše testovací prostředí.](#managing-cost-targets-for-your-lab)

     Tyto náklady jsou *není* zahrnutých do výpočtu ceny:

   * Předplatná CSP a Dreamspark se momentálně nepodporují, protože používá Azure DevTest Labs [rozhraní API pro fakturaci Azure](../billing/billing-usage-rate-card-overview.md) pro výpočet nákladů, který nepodporuje předplatná CSP nebo Dreamspark testovacího prostředí.
   * Ceník vaší nabídky. V současné době nemůžete použít plateb (viz obrázek v rámci vašeho předplatného), že máte vyjedná s Microsoftem nebo Microsoft partnery. Se používají pouze tarifů průběžných plateb.
   * Daně
   * Slevy
   * Fakturační Měna. V současné době náklady testovacího prostředí se zobrazí pouze v místní měně USD.

### <a name="managing-cost-targets-for-your-lab"></a>Správa nákladů cíle testovacího prostředí.
DevTest Labs umožňuje lépe spravovat tak, že nastavíte útraty cíl, který pak můžete zobrazit v grafu měsíční Trend nákladů odhad nákladů ve vašem testovacím prostředí. DevTest Labs můžete také pošle oznámení po dosažení zadané cílové útraty nebo prahovou hodnotu. 

1. Na **trend nákladů** stránce **spravovat cíl**.

    ![Spravovat cíl tlačítko](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na **spravovat cíl** stránky, zadejte útraty cílové a prahové hodnoty. Můžete také nastavit, zda každá vybraná prahová hodnota se použije v hlášení v grafu trend nákladů nebo prostřednictvím oznámení webhooku.

    ![Spravovat cíl podokno](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Vyberte časové období, během které chcete sledovat cíle nákladů.
      - **Měsíční**: náklady na cíle jsou sledovány za měsíc.
      - **Oprava**: náklady na cíle jsou sledována pro rozsah dat zadáte počáteční a koncové datum. Tyto hodnoty představují obvykle, jak dlouho je naplánováno spuštění projektu.
   - Zadejte **cílové náklady**. Například, kolik chcete výdajů v časovém období, které jste definovali v tomto testovacím prostředí.
   - Vyberte možnost k povolení nebo zakázání jakékoli prahu chcete hlášené – v přírůstcích po 25 % – až 125 % vaše zadané **cílové náklady**.
      - **Oznámit**: Při splnění této prahové hodnoty jsou upozorněni prostřednictvím zadaná adresa URL webhooku.
      - **Vykreslit v grafu**: Při splnění této prahové hodnoty, výsledky jsou zobrazeny v grafu trend nákladů, které můžete zobrazit, jak je popsáno v [zobrazení grafu měsíční Trend nákladů odhadované](#viewing-the-monthly-estimated-cost-trend-chart).
   - Pokud budete chtít **upozornění** při splnění prahové hodnoty, je nutné zadat adresu URL webhooku. V oblasti integrace nákladů, vyberte **kliknutím sem přidáte integraci**. Zadejte **adresu URL Webhooku** v podokně Konfigurace oznámení a pak vyberte **OK**.

       ![Konfigurace podokno oznámení](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Pokud zadáte **upozornění**, je nutné zadat adresu URL webhooku.
     - Podobně, pokud definujete adresu URL webhooku, je nutné nastavit **oznámení** k **na** v podokně prahové hodnoty nákladů.
     - Je nutné vytvořit webhook před vstupem tady.  

       Další informace o webhooků najdete v tématu [vytvoření webhooku nebo funkce rozhraní API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Zobrazení náklady podle prostředků 
Funkci měsíční trend nákladů v labs můžete zobrazit, kolik jsem strávil po zbytek aktuálního měsíce. Také ukazuje projekci výdaje až do konce měsíce a podle vaší útraty za posledních sedm dní. Vám pomůžou pochopit, proč útraty v testovacím prostředí je splnění prahové hodnoty raném stádiu, můžete použít **náklady podle prostředků** funkce, která ukazuje cenu za měsíc k datu **jednotlivých prostředků** v tabulce.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte požadované prostředí.  
4. Vyberte **konfigurace a zásad** v nabídce vlevo.
5. Vyberte **náklady podle prostředků** v **sledování nákladů** části v nabídce vlevo. Zobrazí se náklady spojené s každou prostředků související s testovacím prostředí. 

    ![Náklady podle prostředků](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Tato funkce vám umožní snadno identifikovat prostředky, které nákladů na maximum, takže můžete provést akce ke snížení výdaje za testovací prostředí. Náklady na virtuální počítač je třeba podle velikosti virtuálního počítače. Čím větší je velikost virtuálního počítače, další náklady. Můžete snadno vyhledat velikost virtuálního počítače a vlastník, takže můžete komunikovat s vlastník virtuálního počítače, abyste pochopili, proč je potřebná velikost virtuálního počítače a zda je pravděpodobné, snížit velikost.

[Automatické vypnutí zásad](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown) vám umožní snížit náklady na vypněte testovacího prostředí virtuálních počítačů v určitou dobu dne. Ale uživatele testovacího prostředí můžete zrušit vypnutí zásad, které zvyšují náklady na provozování virtuálního počítače. Vyberte virtuální počítač v tabulce zobrazíte pokud jeho má byla účast na více instancí automatické vypnutí zásad. Pokud je to tento případ, můžete si promluvit vlastníkovi virtuálního počítače k vyhledání Proč tento virtuální počítač má byla účast na více instancí zásad.
 
## <a name="next-steps"></a>Další postup
Tady jsou dále vyzkoušejte:

* [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) – zjistěte, jak nastavit různé zásady použité k řízení používání testovacího prostředí a jejích virtuálních počítačů. 
* [Vytvoření vlastní image](devtest-lab-create-template.md) – když vytvoříte virtuální počítač, určit základ, který může být buď vlastní image nebo Marketplace image. Tento článek ukazuje, jak vytvořit vlastní image ze souboru VHD.
* [Konfigurace imagí Marketplace](devtest-lab-configure-marketplace-images.md) – DevTest Labs podporuje vytváření virtuálních počítačů založených na imagích Azure Marketplace. Tento článek ukazuje, jak určit, které případně Image Azure Marketplace lze použít při vytváření virtuálních počítačů v testovacím prostředí.
* [Vytvoření virtuálního počítače v testovacím prostředí](devtest-lab-add-vm.md) -ukazuje, jak vytvořit virtuální počítač ze základní image (buď vlastní nebo Marketplace) a způsobu práce s artefakty ve virtuálním počítači.

