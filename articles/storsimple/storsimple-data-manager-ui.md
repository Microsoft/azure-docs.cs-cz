---
title: Uživatelské rozhraní Microsoft Azure StorSimple Data Manager
description: Přečtěte si, jak můžete pomocí uživatelského rozhraní StorSimple Data Manager transformovat data umístěná na zařízeních řady StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 802f732e8d62f5df861be525316b3c31ab4d0655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94957924"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Správa služby StorSimple Data Manager v Azure Portal

V tomto článku se dozvíte, jak můžete pomocí uživatelského rozhraní StorSimple Data Manager transformovat data umístěná na zařízeních řady StorSimple 8000. Transformovaná data pak může využívat jiné služby Azure, jako je Azure Media Services, Azure HDInsight, Azure Machine Learning a Azure Kognitivní hledání.


## <a name="use-storsimple-data-transformation"></a>Použití transformace dat StorSimple

StorSimple Data Manager je prostředek, ve kterém je vytvořena instance transformace dat. Služba transformace dat umožňuje transformovat data z formátu StorSimple do nativního formátu v objektech blob nebo souborech Azure. Pokud chcete transformovat data nativního formátu StorSimple, musíte zadat podrobnosti o zařízení řady StorSimple 8000 a data, která chcete transformovat.

### <a name="create-a-storsimple-data-manager-service"></a>Vytvoření služby StorSimple Data Manager

Chcete-li vytvořit službu StorSimple Data Manager, proveďte následující kroky.

1. Pomocí přihlašovacích údajů účtu Microsoft se přihlaste na webu [Azure Portal](https://portal.azure.com/).

2. Klikněte na **+ vytvořit prostředek** a vyhledejte StorSimple data Manager.

    ![Vytvoření StorSimple Data Manager služby 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Klikněte na StorSimple Data Manager a pak klikněte na **vytvořit**.
    
    ![Vytvoření StorSimple Data Manager služby 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Pro novou službu zadejte následující:

   1. Zadejte jedinečný **název služby** pro váš StorSimple data Manager. Jedná se o popisný název, který můžete použít k identifikaci služby. Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. Název musí začínat a končit písmenem nebo číslicí.

   2. Vyberte **předplatné** z rozevíracího seznamu. Předplatné je propojeno s vaším fakturačním účtem. Pokud máte jenom jedno předplatné, vyplní se toto pole automaticky (a nedá se vybrat).

   3. Vyberte existující skupinu prostředků nebo vytvořte novou skupinu. Další informace najdete v tématu [Skupiny prostředků Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   4. Zadejte **umístění** služby, které slouží k uložení účtů úložiště a služby StorSimple data Manager. Služba StorSimple Správce zařízení, Služba Data Manager a přidružený účet úložiště by měly být všechny v podporovaných oblastech.
    
   5. Pokud chcete na řídicím panelu získat odkaz na tuto službu, vyberte **Připnout na řídicí panel**.
    
   6. Klikněte na **Vytvořit**.

      ![Vytvoření StorSimple Data Manager služby 3](./media/storsimple-data-manager-ui/create-service-4.png)

Vytvoření služby trvá několik minut. Po úspěšném vytvoření služby se zobrazí oznámení a zobrazí se nová služba.

### <a name="create-a-data-transformation-job-definition"></a>Vytvoření definice úlohy transformace dat

V rámci StorSimple Data Manager služby je potřeba vytvořit definici úlohy transformace dat. Definice úlohy určuje podrobnosti o StorSimple datech, která vás zajímá, do účtu úložiště v nativním formátu. Jakmile vytvoříte definici úlohy, můžete tuto úlohu spustit znovu s jiným nastavením modulu runtime.

K vytvoření definice úlohy proveďte následující kroky.

1. Přejděte ke službě, kterou jste vytvořili. Přejít na **Definice úloh > správy**.

2. Klikněte na **+ definice úlohy**.

    ![Kliknout na + definice úlohy](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Zadejte název definice úlohy. Název může být dlouhý 3 až 63 znaků. Název může obsahovat velká a malá písmena, číslice a spojovníky.

4. Zadejte umístění, kde je úloha spuštěna. Toto umístění může být jiné než místo, kde je služba nasazená.

5. Klikněte na **zdroj** a určete zdrojové úložiště dat.

    ![Konfigurace úložiště zdrojových dat](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Vzhledem k tomu, že se jedná o novou Data Manager službu, nejsou nakonfigurovaná žádná úložiště dat. V části **Konfigurovat zdroj dat** zadejte podrobnosti zařízení řady StorSimple 8000 a data, která vás zajímají.

   Pokud chcete přidat Správce zařízení StorSimple jako úložiště dat, klikněte na **Přidat nový** v rozevíracím seznamu úložiště dat a pak klikněte na **Přidat úložiště dat**.

    ![Přidat nové úložiště dat](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Jako typ úložiště dat vyberte **StorSimple 8000 series Manager** .
    
   2. Zadejte popisný název úložiště zdrojových dat.
    
   3. V rozevíracím seznamu vyberte předplatné přidružené k vaší službě StorSimple Správce zařízení.
    
   4. Zadejte název StorSimple Správce zařízení **prostředku**.

   5. Zadejte **šifrovací klíč dat služby** pro službu StorSimple Správce zařízení. 

      ![Konfigurace zdrojového úložiště dat 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Po dokončení klikněte na **OK** . Tím uložíte úložiště dat. Znovu použít tuto StorSimple Správce zařízení v jiných definicích úlohy bez zadání těchto parametrů. Po kliknutí na **OK** může trvat několik sekund, než se nově vytvořené zdrojové úložiště dat zobrazí v rozevíracím seznamu.

7. V rozevíracím seznamu pro **úložiště dat** vyberte úložiště dat, které jste vytvořili. 

   1. Zadejte název zařízení řady StorSimple 8000, které obsahuje data, která vás zajímají.

   2. Zadejte název svazku umístěného na zařízení StorSimple, které má vaše data zájmu.

   3. V podčásti **Filtr** zadejte kořenový adresář, který obsahuje vaše data o vašem zájmu ve formátu _\MyRootDirectory\Data_ . Písmena jednotek, například _\C: \Data_ , nejsou podporována. Sem můžete přidat také libovolné filtry souborů.

   4. Služba transformace dat funguje jenom na posledním snímku dat, která se odešlou do Azure.

   5. Klikněte na **OK**.

      ![Konfigurace zdrojového datového úložiště 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. V dalším kroku je potřeba nakonfigurovat cílové úložiště dat. Vyberte účty úložiště a vložte soubory do objektů BLOB v tomto účtu. V rozevíracím seznamu vyberte **Přidat nové** a pak **nakonfigurujte nastavení**.

9. Vyberte typ cílového úložiště, který chcete přidat, a další parametry přidružené k úložišti.

    Pokud vyberete cíl typu účtu úložiště, můžete zadat popisný název, předplatné (zvolte stejnou možnost jako u služby nebo jiné) a účet úložiště.
        ![Konfigurace cílového úložiště dat 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Při spuštění úlohy se vytvoří fronta úložiště. Tato fronta je naplňována zprávami o transformovaných objektech blob, jakmile jsou připravené. Název této fronty je stejný jako název definice úlohy.
    
10. Po přidání úložiště dat počkejte několik minut.
    
    1. V rozevíracím seznamu v **názvu cílového účtu** vyberte úložiště, které jste vytvořili jako cíl.

    2. Jako objekty blob nebo soubory vyberte typ úložiště. Zadejte název kontejneru úložiště, ve kterém se transformovaná data nacházejí. Klikněte na **OK**.

        ![Konfigurace účtu úložiště cílového datového úložiště](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Můžete také zaškrtnout možnost pro zobrazení odhadu trvání úlohy před spuštěním úlohy. Kliknutím na tlačítko **OK** vytvořte definici úlohy. Vaše definice úlohy je teď dokončená. Tuto definici úlohy můžete použít několikrát přes uživatelské rozhraní s různými nastaveními modulu runtime.

    ![Dokončení definice úlohy](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Nově vytvořená definice úlohy se přidá do seznamu definic úloh pro tuto službu.

### <a name="run-the-job-definition"></a>Spuštění definice úlohy

Kdykoli potřebujete přesunout data z StorSimple do účtu úložiště, který jste zadali v definici úlohy, je nutné ji spustit. V době běhu lze některé parametry zadat odlišně. Kroky jsou následující:

1. Vyberte službu StorSimple Data Manager a pokračujte na **Definice úloh > správy**. Vyberte definici úlohy, kterou chcete spustit, a klikněte na ni.
     
     ![Spustit úlohu 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klikněte na **Spustit nyní**.
     
     ![Spustit spuštění úlohy 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Kliknutím na položku **nastavení spuštění** upravíte všechna nastavení, která můžete pro spuštění úlohy změnit. Klikněte na tlačítko **OK** a potom kliknutím na tlačítko **Spustit** spusťte úlohu.

    ![Spuštění úlohy 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Pokud chcete monitorovat tuto úlohu, přečtěte si v StorSimple Data Manager **úlohy** . Kromě monitorování v okně **úlohy** můžete také naslouchat frontě úložiště, do které se přidá zpráva pokaždé, když se soubor přesune z StorSimple do účtu úložiště.

    ![Spustit úlohu 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Zobrazení protokolů po dokončení úlohy

Po dokončení úlohy můžete zobrazit stav úlohy. Stav úlohy je možné **úspěšně** provést, **částečně úspěšné** a **neúspěšné**. Můžete zobrazit seznam souborů, které byly úspěšně zkopírovány, a soubory, jejichž kopírování se nezdařilo. Tyto seznamy jsou k dispozici v kontejneru s názvem **"StorSimple-data-Manager-joblogs"** v rámci svého cílového účtu úložiště. V rámci tohoto kontejneru můžete vyhledat složku se stejným názvem, jako má vaše definice úlohy. V této složce se vytvoří složka pro každé spuštění úlohy, která bude obsahovat vaše seznamy. Název této složky bude identifikátor GUID úlohy, který můžete získat ze stránky s podrobnostmi úlohy. Případně se ve většině případů na stránce úlohy zobrazí odkaz na protokoly kopírování.
V této složce se zobrazuje 2 sada souborů CSV. Všechny soubory, které začínají na **copiedfilelist...** budou obsahovat seznam úspěšně zkopírovaných souborů. Všechny soubory, které začínají na **failedfilelist...** obsahují soubory, které se nemohly zkopírovat, spolu s chybovou zprávou.


## <a name="next-steps"></a>Další kroky

[Pomocí sady .NET SDK spusťte StorSimple data Manager úlohy](storsimple-data-manager-dotnet-jobs.md).