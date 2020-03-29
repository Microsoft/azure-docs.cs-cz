---
title: Zobrazení měsíčního odhadovaného trendu nákladů na testovací prostředí v laboratořích Azure DevTest Labs
description: Tento článek obsahuje informace o tom, jak sledovat náklady na testovací prostředí (měsíční graf trendů odhadovaných nákladů) v laboratořích Azure DevTest Labs.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2e4fe55fac5edf73e16df05bd38cc2712a94377d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721723"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Sledování nákladů spojených s testovacím prostředím v laboratořích Azure DevTest Labs
Tento článek obsahuje informace o tom, jak sledovat náklady na testovací prostředí. Ukazuje, jak zobrazit odhadované náklady trent pro aktuální kalendářní měsíc pro testovací prostředí. Článek také ukazuje, jak zobrazit náklady od měsíce do data na zdroj v testovacím prostředí.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Zobrazení měsíčního odhadovaného trendu nákladů na testovací prostředí 
V této části se dozvíte, jak pomocí grafu **Trend odhadovaných nákladů měsíce** zobrazit odhadované náklady aktuálního kalendářního měsíce a předpokládané náklady na konec měsíce pro aktuální kalendářní měsíc. Můžete také zjistit, jak spravovat náklady na testovací prostředí nastavením cílů výdajů a prahových hodnot, které po dosažení aktivační událost DevTest Labs hlásit výsledky na vás.

Chcete-li zobrazit graf Trend odhadovaných měsíčních nákladů, postupujte takto: 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte testovací prostředí.  
4. V levé nabídce vyberte **Možnost Konfigurace a zásady.**  
4. V levé nabídce vyberte **Trend nákladů** v části **Sledování nákladů.** Následující snímek obrazovky ukazuje příklad nákladového grafu. 
   
    ![Graf nákladů](./media/devtest-lab-configure-cost-management/graph.png)

    Hodnota **odhadovaných nákladů** je odhadované náklady aktuálního kalendářního měsíce k datu. **Předpokládané náklady** jsou odhadované náklady na celý aktuální kalendářní měsíc vypočítané pomocí nákladů testovacího prostředí za předchozích pět dní.

    Částky nákladů jsou zaokrouhleny nahoru na další celé číslo. Například: 

   * 5.01 zaokrouhluje nahoru na 6 
   * 5.50 zaokrouhluje až na 6
   * 5.99 zaokrouhluje nahoru na 6

     Jak je uvedeno nad grafem, náklady, které vidíte ve výchozím nastavení v *grafu,* jsou odhadované náklady pomocí sazeb nabídek [průběžných](https://azure.microsoft.com/offers/ms-azr-0003p/) plateb. Můžete také nastavit vlastní cíle výdajů, které jsou zobrazeny v grafech [pomocí správy cílů nákladů pro vaše testovací prostředí.](#managing-cost-targets-for-your-lab)

     Do výpočtu nákladů *nejsou* zahrnuty tyto náklady:

   * Předplatná CSP a Dreamspark nejsou aktuálně podporována, protože Azure DevTest Labs používá [fakturační API Azure](../cost-management-billing/manage/usage-rate-card-overview.md) k výpočtu nákladů na testovací prostředí, které nepodporují předplatná CSP nebo Dreamspark.
   * Vaše nabídkové sazby. V současné době nelze použít sazby za nabídku (uvedené v rámci předplatného), které jste vyjednali s partnery společnosti Microsoft nebo společnosti Microsoft. Používají se pouze sazby průběžných plateb.
   * Vaše daně
   * Vaše slevy
   * Vaše fakturační měna. V současné době jsou náklady na testovací prostředí zobrazeny pouze v měně USD.

### <a name="managing-cost-targets-for-your-lab"></a>Správa cílů nákladů pro vaši laboratoř
DevTest Labs vám umožní lépe spravovat náklady v testovacím prostředí nastavením cíle výdajů, který pak můžete zobrazit v grafu Trend měsíčních odhadovaných nákladů. DevTest Labs můžete také poslat oznámení, když je dosaženo zadané cílové výdaje nebo prahové hodnoty. 

1. Na stránce **Trend nákladů** vyberte **Spravovat cíl**.

    ![Tlačítko Spravovat cíl](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na stránce **Spravovat cíl** zadejte cíl výdajů a prahové hodnoty. Můžete také nastavit, zda je každá vybraná prahová hodnota uvedena v grafu trendu nákladů nebo prostřednictvím oznámení webhooku.

    ![Spravovat cílové podokno](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Vyberte časové období, během kterého chcete sledovat nákladové cíle.
      - **Měsíčně**: nákladové cíle jsou sledovány za měsíc.
      - **Opraveno**: nákladové cíle jsou sledovány pro rozsah dat, který zadáte v počátečním a koncovém datu. Tyto hodnoty obvykle představují, jak dlouho je naplánováno spuštění projektu.
   - Zadejte **cílovou cenu**. Například kolik plánujete utratit v tomto testovacím prostředí v časovém období, které jste definovali.
   - Tuto možnost vyberte, chcete-li povolit nebo zakázat jakoukoli prahovou hodnotu, kterou chcete nahlásit – v krocích po 25 % – až do výše 125 % zadané **cílové ceny**.
      - **Upozornit:** Pokud je tato prahová hodnota splněna, budete upozorněni adresou URL webhooku, kterou zadáte.
      - **Vykreslení v grafu**: Pokud je tato prahová hodnota splněna, výsledky jsou vykresleny v grafu trendu nákladů, který můžete zobrazit, jak je popsáno v zobrazení grafu Trend měsíčních odhadovaných nákladů.
   - Pokud se rozhodnete **upozornit** při splnění prahové hodnoty, je nutné zadat adresu URL webhooku. V oblasti Integrace nákladů vyberte **klepnutím sem, chcete-li přidat integraci**. Do podokna konfigurovat oznámení zadejte **adresu URL webhooku** a pak vyberte **OK**.

       ![Konfigurovat podokno oznámení](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Pokud zadáte **Upozornit**, musíte definovat adresu URL webhooku.
     - Podobně pokud definujete adresu URL webhooku, musíte nastavit **oznámení** **na Zapnuto** v podokně Prahová hodnota nákladů.
     - Před zadáním sem je nutné vytvořit webový hák.  

       Další informace o webhooky najdete [v tématu vytvoření webhooku nebo funkce Azure rozhraní API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Zobrazit náklady podle zdroje 
Funkce trendu měsíčních nákladů v testovacích prostředích umožňuje zjistit, kolik jste v aktuálním kalendářním měsíci utratili. Zobrazuje také odhad výdajů do konce měsíce na základě vašich výdajů za posledních sedm dní. Abyste lépe pochopili, proč výdaje v testovacím prostředí splňují prahové hodnoty dříve, můžete použít funkci **nákladů podle zdrojů,** která zobrazuje náklady **na zdroj** od začátku měsíce v tabulce.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  
4. V levé nabídce vyberte **Možnost Konfigurace a zásady.**
5. V levé nabídce vyberte **Náklady podle zdroje** v části Sledování **nákladů.** Zobrazí se náklady spojené s každým zdrojem přidruženým k testovacímu prostředí. 

    ![Náklady podle prostředků](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Tato funkce vám pomůže snadno identifikovat prostředky, které stojí nejvíce, takže můžete provést akce ke snížení výdajů testovacího prostředí. Například náklady na virtuální počítač je založen na velikosti virtuálního počítače. Čím větší je velikost virtuálního počítače, tím více je cena. Můžete snadno najít velikost virtuálního počítače a vlastníka, takže můžete mluvit s vlastníkem virtuálního počítače, abyste pochopili, proč je taková velikost virtuálního počítače potřebná a zda existuje možnost snížit velikost.

[Zásady automatického vypnutí](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) vám pomůže snížit náklady vypnutím virtuálních počítače testovacího prostředí v určitou denní dobu. Uživatel testovacího prostředí se však může odhlásit ze zásad vypnutí, což zvyšuje náklady na spuštění virtuálního počítače. Můžete vybrat virtuální ho v tabulce a zjistit, jestli byl odhlásil ze zásad automatického vypnutí. V takovém případě můžete mluvit s vlastníkem virtuálního média a zjistit, proč byl virtuální virtuální hotel odhlásil ze zásad.
 
## <a name="next-steps"></a>Další kroky
Zde je několik věcí, které můžete vyzkoušet:

* [Definujte zásady testovacího prostředí](devtest-lab-set-lab-policy.md) – naučte se, jak nastavit různé zásady používané k řízení, jak se vaše testovací prostředí a jeho virtuální počítače používají. 
* [Vytvořit vlastní image](devtest-lab-create-template.md) – Při vytváření virtuálního virtuálního montova, zadáte základnu, která může být buď vlastní image nebo image Marketplace. Tento článek ukazuje, jak vytvořit vlastní obrázek ze souboru VHD.
* [Konfigurace image Marketplace](devtest-lab-configure-marketplace-images.md) – DevTest Labs podporuje vytváření virtuálních počítačů na základě ibi Azure Marketplace. Tento článek ukazuje, jak určit, které image Azure Marketplace se dá použít při vytváření virtuálních počítačů v testovacím prostředí.
* [Vytvoření virtuálního počítače v testovacím prostředí](devtest-lab-add-vm.md) – ukazuje, jak vytvořit virtuální počítač ze základní image (vlastní nebo Marketplace) a jak pracovat s artefakty ve vašem virtuálním počítači.

