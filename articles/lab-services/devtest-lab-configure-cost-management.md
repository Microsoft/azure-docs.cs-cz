---
title: Zobrazit měsíční náklady na trend odhadované testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Další informace o graf trendů Azure DevTest Labs měsíční odhadované náklady.
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
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787630"
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Zobrazit měsíční náklady na trend odhadované testovacího prostředí v Azure DevTest Labs
Náklady na správu funkce DevTest Labs vám pomůže sledovat náklady na vašem testovacím prostředí. Tento článek ukazuje, jak používat **měsíční odhadované náklady Trend** grafu zobrazíte aktuální kalendářní měsíc odhadované náklady na začátku a předpokládané náklady na konci měsíce pro aktuální měsíc kalendáře. Tento článek také ukazuje, jak Správa testovacího prostředí náklady nastavením útraty cíle a prahové hodnoty, když dosaženo, aktivační události DevTest Labs nahlásit výsledky pro vás.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Zobrazení grafu měsíční odhadované náklady trendů
Chcete-li zobrazit graf měsíční odhadované náklady trendů, postupujte takto: 

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu. (Testovacího prostředí může zobrazit již na řídicím panelu v části **všechny prostředky**).
1. Ze seznamu labs vyberte požadované testovací prostředí.  
1. V tomto prostředí **přehled** oblasti, vyberte **konfiguraci a zásady**.   
1. Na levé straně v části **sledování nákladů**, vyberte **náklady trend**.

   Následující snímek obrazovky ukazuje příklad grafu náklady. 
   
    ![Cenově grafu](./media/devtest-lab-configure-cost-management/graph.png)

**Odhadované náklady na** hodnota je aktuální kalendářní měsíc odhadované náklady na začátku. **Plánované náklady** je odhadované náklady pro celou aktuální kalendářní měsíc vypočítává pomocí náklady na testovacím předchozí pět dní.

Objemy náklady jsou zaokrouhlený nahoru na nejbližší celé číslo. Příklad: 

* 5.01 zaokrouhlí až 6 
* 5.50 zaokrouhlí až 6
* 5.99 zaokrouhlí až 6

Jak je uvedeno výše grafu, náklady, uvidíte v grafu ve výchozím nastavení jsou *odhadované* stojí pomocí [průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/) nabízejí sazby. Můžete také nastavit vlastní výdaje cíle, které se zobrazí v grafech podle [Správa nákladů cíle pro testovací prostředí.](#managing-cost-targets-for-your-lab)

Kromě toho jsou následující *není* zahrnutých do výpočtu náklady na:

* Předplatné Dreamspark a CSP nejsou aktuálně podporovány jako používá Azure DevTest Labs [rozhraní API Azure fakturace](../billing/billing-usage-rate-card-overview.md) vypočítat náklady, který nepodporuje zprostředkovatele kryptografických služeb nebo Dreamspark odběry testovací prostředí.
* Nabídka sazby. V současné době nelze použít sazby nabídka (zobrazené v rámci svého předplatného), že máte vyjedná se společnosti Microsoft nebo Microsoft partnery. Se používají pouze průběžné platby sazby.
* Vaše daně
* Vaše slevy
* Vaše fakturační Měna. V současné době náklady na testovacího prostředí se zobrazí jenom v USD měny.

## <a name="managing-cost-targets-for-your-lab"></a>Správa nákladů cíle pro testovací prostředí
DevTest Labs umožňuje lepší nastavením útraty cíl, který potom můžete zobrazit v grafu měsíční Trend odhadované náklady na správu nákladů ve vašem testovacím prostředí. DevTest Labs vám taky může odeslat oznámení po dosažení zadané cílové výdaje nebo prahovou hodnotu. 

1. Na vašem testovacím prostředí **přehled** podokně, vyberte **konfiguraci a zásady**.
1. Na levé straně v části **sledování nákladů**, vyberte **náklady trend**.

    ![Spravovat tlačítkem cíle](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. V **náklady trend** podokně, vyberte **spravovat cílový**.

    ![Spravovat tlačítkem cíle](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. V podokně Správa cíl zadejte požadované cílové útraty a prahové hodnoty. Můžete také nastavit, zda každý vybraná prahová hodnota se použije v hlášení na graf trendů náklady nebo prostřednictvím webhooku oznámení.

    ![Spravovat podokně cíl](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Vyberte časové období, které se mají sledovat cíle náklady.
      - **Měsíční**: cíle náklady jsou sledovány za měsíc.
      - **Opravené**: cíle náklady jsou sledovány pro rozsah zadejte počáteční datum a koncovým datem. Obvykle to odpovídají jak dlouho má naplánované spuštění projektu.
   - Zadejte **cíle náklady**. To může být například kolik máte v úmyslu vynaložit na tomto testovacím prostředí v časovém období, které jste definovali.
   - Vyberte k povolení nebo zakázání všechny prahové hodnoty chcete hlášené – v přírůstcích po 25 % – až 125 % vaše zadané **cíle náklady**.
      - **Oznámit**: při splnění této prahové hodnoty upozornění od zadaná adresa URL webhooku.
      - **Vykreslení v grafu**: při splnění této prahové hodnoty výsledky jsou zobrazeny v grafu trend náklady, který si můžete zobrazit, jak je popsáno v [zobrazení grafu měsíční odhadované náklady Trend](#viewing-the-monthly-estimated-cost-trend-chart).
   - Pokud zvolíte možnost **oznámení** při splnění prahovou hodnotu, musíte zadat adresu URL webhooku. V oblasti integrace náklady vyberte **kliknutím sem přidejte integrační**.

      Zadejte adresu URL Webhooku v podokně Konfigurace oznámení a potom vyberte **OK**.

       ![Konfigurace podokno oznámení](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Pokud zadáte **oznámení**, je nutné zadat adresu URL webhooku.
      - Podobně pokud definujete URL webhooku se nenačetla, je nutné nastavit **oznámení** k **na** v podokně náklady prahovou hodnotu.
      - Je nutné vytvořit webhooku před zadáním ho sem.  

      Další informace o webhooky najdete v tématu [vytvoření webhooku nebo funkce rozhraní API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Příspěvky blogu související
* [Dva kroky zachovat vaše náklady na sledování v DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Proč náklady prahové hodnoty?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Další postup
Zde jsou další vyzkoušejte:

* [Definování zásad testovacího](devtest-lab-set-lab-policy.md) – zjistěte, jak nastavit různé zásady použité k řízení použití testovacího prostředí a jeho virtuální počítače. 
* [Vytvořit vlastní image](devtest-lab-create-template.md) – při vytváření virtuálního počítače, zadejte základ, který může být buď vlastní image nebo image pořízenou prostřednictvím Marketplace. Tento článek ukazuje, jak vytvořit vlastní obrázek ze souboru virtuálního pevného disku.
* [Konfigurace Marketplace image](devtest-lab-configure-marketplace-images.md) – DevTest Labs podporuje vytváření virtuálních počítačů založené na imagích Azure Marketplace. Tento článek ukazuje, jak určit, které, pokud existuje, může být Azure Marketplace Image používá při vytváření virtuálních počítačů v testovacím prostředí.
* [Vytvoření virtuálního počítače v testovacím prostředí](devtest-lab-add-vm.md) -znázorňuje, jak vytvořit virtuální počítač z bitové kopie základní (buď vlastní nebo Marketplace) a postup práce s artefakty ve vašem virtuálním počítači.

