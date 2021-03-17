---
title: Podívejte se na měsíční trend odhadovaných nákladů testovacího prostředí v Azure DevTest Labs
description: Tento článek poskytuje informace o tom, jak sledovat náklady na testovací prostředí (měsíční trend odhadovaných nákladů) v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6a2a9bef9e54ef7deda123aad34cf0c576fd158f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98892333"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Sledovat náklady spojené s testovacím prostředím v Azure DevTest Labs
Tento článek poskytuje informace o tom, jak sledovat náklady na testovací prostředí. Ukazuje, jak zobrazit trend odhadovaných nákladů pro aktuální kalendářní měsíc pro testovací prostředí. Článek také ukazuje, jak zobrazit měsíční náklady na prostředek v testovacím prostředí.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Podívejte se na měsíční trend odhadovaných nákladů testovacího prostředí. 
V této části se dozvíte, jak pomocí grafu **trendu předpokládaných nákladů měsíčně** zobrazit odhadované náklady na aktuální kalendářní měsíc a předpokládané náklady na měsíc v aktuálním kalendářním měsíci. Naučíte se také, jak spravovat náklady na testovací prostředí tím, že nastavíte cíle a prahové hodnoty útraty, které po dosažení těchto výsledků spustí DevTest Labs, které vám nahlásí výsledky.

Chcete-li zobrazit graf trendu odhadovaných nákladů měsíčně, postupujte podle následujících kroků: 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte testovací prostředí.  
4. V nabídce vlevo vyberte **Konfigurace a zásady** .  
4. V části **sledování nákladů** v levé nabídce vyberte **trend nákladů** . Následující snímek obrazovky ukazuje příklad nákladového grafu. 
   
    ![Nákladový graf](./media/devtest-lab-configure-cost-management/graph.png)

    **Odhadovaná hodnota nákladů** je aktuální odhadované náklady v kalendářním měsíci. Předpokládané **náklady** jsou odhadované náklady na celý aktuální kalendářní měsíc, vypočítané pomocí nákladů testovacího prostředí za předchozích pět dnů.

    Náklady se zaokrouhlují na nejbližší celé číslo. Příklad: 

   * 5,01 zaokrouhlí na 6 
   * 5,50 zaokrouhlí na 6
   * 5,99 zaokrouhlí na 6

     Jak je uvedeno výše v grafu, ceny, které se ve výchozím nastavení zobrazí v grafu, jsou *Odhadované* náklady s využitím sazeb za průběžné [platby](https://azure.microsoft.com/offers/ms-azr-0003p/) . Můžete také nastavit vlastní cíle útraty, které se zobrazí v grafech, a to tak, že [se budou řídit náklady na testovací prostředí.](#managing-cost-targets-for-your-lab)

     Kalkulace nákladů *nezahrnuje následující* náklady:

   * Předplatná CSP a DreamSpark se v tuto chvíli nepodporují, protože Azure DevTest Labs používá rozhraní API pro fakturaci Azure k výpočtu nákladů testovacího prostředí, které nepodporují předplatné CSP nebo DreamSpark.
   * Sazby vaší nabídky. V současné době nemůžete použít sazby nabídek (zobrazené v rámci vašeho předplatného), které jste vyjednali s partnery Microsoftu nebo Microsoftu. Používají se jenom tarify průběžných plateb.
   * Vaše daně
   * Vaše slevy
   * Fakturační měna. V současné době se náklady na testovací prostředí zobrazují pouze v měně USD.

### <a name="managing-cost-targets-for-your-lab"></a>Správa cílových nákladů pro testovací prostředí
DevTest Labs vám umožní lépe spravovat náklady v testovacím prostředí tím, že nastavíte cíl útraty, který pak můžete zobrazit v grafu trendu odhadovaných nákladů měsíčně. DevTest Labs vám také může poslat oznámení, když je dosaženo zadané cílové útraty nebo prahové hodnoty. 

1. Na stránce **trend nákladů** vyberte **Spravovat cíl**.

    ![Tlačítko pro správu cíle](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na stránce **Spravovat cíl** zadejte cíl útraty a prahové hodnoty. Můžete také nastavit, zda je každá vybraná prahová hodnota uvedena v grafu trendu nákladů nebo prostřednictvím oznámení Webhooku.

    ![Spravovat cílové podokno](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Vyberte časové období, během kterého chcete sledovat cíle nákladů.
      - **Měsíčně**: cíle nákladů jsou sledovány za měsíc.
      - **Opraveno**: cíle nákladů jsou sledovány pro rozsah dat, který zadáte v počátečním a koncovém datu. Obvykle tyto hodnoty reprezentují, jak dlouho je naplánováno spuštění projektu.
   - Zadejte **cílové náklady**. Například kolik plánujete v tomto testovacím prostředí strávit v časovém období, které jste definovali.
   - Tuto možnost vyberte, pokud chcete povolit nebo zakázat všechny hlášené prahové hodnoty – v přírůstcích po 25% až do 125% zadaných **cílových nákladů**.
      - **Oznámení**: Pokud je tato prahová hodnota splněná, zobrazí se oznámení adresa URL Webhooku, kterou zadáte.
      - **Vykreslení v grafu**: když je tato prahová hodnota splněná, výsledky se vykreslují v grafu trendů nákladů, který můžete zobrazit, jak je popsáno v tématu zobrazení měsíčního trendu odhadovaných nákladů.
   - Pokud se rozhodnete, že při splnění prahové hodnoty budete **upozorněni** , musíte zadat adresu URL Webhooku. V oblasti integrace nákladů vyberte **kliknutím sem přidejte integraci**. V podokně Konfigurace oznámení zadejte **adresu URL Webhooku** a pak vyberte **OK**.

       ![Konfigurovat podokno oznámení](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - Pokud zadáte **Notify**, musíte zadat adresu URL Webhooku.
     - Podobně platí, že pokud definujete adresu URL Webhooku, musíte  nastavit oznámení **v podokně** prahová hodnota nákladů.
     - Webhook je nutné vytvořit před jeho zadáním sem.  

       Další informace o webhookech najdete v tématu [Vytvoření Webhooku nebo rozhraní API Azure Function](../azure-functions/functions-bindings-http-webhook.md). 

## <a name="view-cost-by-resource"></a>Zobrazit náklady podle prostředku 
Funkce trendu měsíčních nákladů v cvičení vám umožní zjistit, kolik jste strávili v aktuálním kalendářním měsíci. Zobrazuje také projekci útraty až do konce měsíce na základě vašich útraty za posledních sedm dní. Abychom vám pomohli pochopit, proč útraty v testovacím prostředí jsou na začátku prahové hodnoty, můžete použít funkci **náklady podle prostředků** , která zobrazuje měsíční náklady na **prostředek** v tabulce.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte požadované testovací prostředí.  
4. V nabídce vlevo vyberte **Konfigurace a zásady** .
5. V části **sledování nákladů** v levé nabídce vyberte **náklady podle prostředků** . Zobrazí se náklady spojené s každým prostředkem přidruženým k testovacímu prostředí. 

    ![Náklady podle prostředků](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Tato funkce vám pomůže snadno identifikovat prostředky, které jsou nejdůležitější, takže můžete provádět akce, které snižují náklady na testovací prostředí. Například náklady na virtuální počítač jsou založené na velikosti virtuálního počítače. Čím větší je velikost virtuálního počítače, tím více jsou náklady. Můžete snadno najít velikost virtuálního počítače a vlastníka, abyste se mohli spojit s vlastníkem virtuálního počítače, abyste zjistili, proč je taková velikost virtuálního počítače potřebná a jestli existuje možnost snížit velikost.

[Zásady automatického vypínání](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) vám pomáhají snížit náklady vypínáním testovacích virtuálních počítačů v určitou dobu dne. Uživatel testovacího prostředí se ale může odhlásit ze zásad vypnutí, což zvyšuje náklady na provoz virtuálního počítače. Můžete vybrat virtuální počítač v tabulce, abyste zjistili, jestli se mu zásady automatického vypínání nerozhodly. V takovém případě se můžete spojit s vlastníkem virtuálního počítače, abyste zjistili, proč se na virtuální počítač nerozhodla zásada.
 
## <a name="next-steps"></a>Další kroky
Tady je několik věcí, které je potřeba vyzkoušet:

* [Definovat zásady testovacího prostředí](devtest-lab-set-lab-policy.md) – Naučte se, jak nastavit různé zásady, pomocí kterých můžete řídit, jak se vaše testovací prostředí a jeho virtuální počítače používají. 
* [Vytvořit vlastní image](devtest-lab-create-template.md) – když vytvoříte virtuální počítač, zadáte základ, který může být buď vlastní image, nebo Image na webu Marketplace. Tento článek ukazuje, jak vytvořit vlastní image ze souboru VHD.
* [Konfigurace imagí Marketplace](devtest-lab-configure-marketplace-images.md) – DevTest Labs podporuje vytváření virtuálních počítačů na základě Azure Marketplace imagí. V tomto článku se naučíte, jak určit, které Azure Marketplace image se můžou použít při vytváření virtuálních počítačů v testovacím prostředí.
* [Vytvoření virtuálního počítače v testovacím prostředí](devtest-lab-add-vm.md) – ukazuje, jak vytvořit virtuální počítač ze základní Image (ať už vlastní nebo tržiště) a jak pracovat s artefakty ve vašem virtuálním počítači.
