---
title: Ui správce dat Microsoft Azure StorSimple
description: Popisuje způsob použití uj.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 85be49ad88ac62d90235c3da6b89b0da6a11487c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933748"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Správa služby StorSimple Data Manager na webu Azure Portal

Tento článek vysvětluje, jak můžete použít StorSimple Data Manager uI transformovat data s bydlištěm na zařízení řady StorSimple 8000. Transformovaná data pak můžou spotřebovat další služby Azure, jako jsou Azure Media Services, Azure HDInsight, Azure Machine Learning a Azure Cognitive Search.


## <a name="use-storsimple-data-transformation"></a>Použití transformace dat StorSimple

Správce dat StorSimple je prostředek, ve kterém je vytvořena instance transformace dat. Služba Transformace dat umožňuje transformovat data z formátu StorSimple do nativního formátu v objektech BLOB nebo Soubory Azure. Chcete-li transformovat data nativního formátu StorSimple, je třeba zadat podrobnosti o zařízení řady StorSimple 8000 a data, která chcete transformovat.

### <a name="create-a-storsimple-data-manager-service"></a>Vytvoření služby StorSimple Data Manager

Chcete-li vytvořit službu StorSimple Data Manager, proveďte následující kroky.

1. Pomocí přihlašovacích údajů účtu Microsoft se přihlaste na webu [Azure Portal](https://portal.azure.com/).

2. Klikněte **na + Vytvořit prostředek** a vyhledejte StorSimple Data Manager.

    ![Vytvoření služby StorSimple Data Manager 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Klepněte na položku StorSimple Data Manager a potom klepněte na tlačítko **Vytvořit**.
    
    ![Vytvoření služby StorSimple Data Manager 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Pro novou službu zadejte následující:

   1. Zadejte jedinečný **název služby** pro správce dat StorSimple. Jedná se o popisný název, který můžete použít k identifikaci služby. Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. Název musí začínat a končit písmenem nebo číslicí.

   2. Z rozevíracího seznamu vyberte **předplatné.** Předplatné je propojeno s vaším fakturačním účtem. Toto pole je automaticky vyplněno (a nelze vybrat), pokud máte pouze jedno předplatné.

   3. Vyberte existující skupinu prostředků nebo vytvořte novou skupinu. Další informace najdete v tématu [Skupiny prostředků Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

   4. Zadejte **umístění** pro vaši službu, ve které jsou umístěny vaše účty úložiště a služba StorSimple Data Manager. Vaše služba StorSimple Device Manager, služba Data Manager a přidružený účet úložiště by měly být v podporovaných oblastech.
    
   5. Chcete-li na řídicí panel získat odkaz na tuto službu, vyberte **připnout na řídicí panel**.
    
   6. Klikněte na **Vytvořit**.

      ![Vytvoření služby StorSimple Data Manager 3](./media/storsimple-data-manager-ui/create-service-4.png)

Vytvoření služby trvá několik minut. Zobrazí se oznámení po úspěšném vytvoření služby a zobrazí se nová služba.

### <a name="create-a-data-transformation-job-definition"></a>Vytvoření definice úlohy transformace dat

V rámci služby StorSimple Data Manager je třeba vytvořit definici úlohy transformace dat. Definice úlohy určuje podrobnosti o datech StorSimple, která vás zajímají o přesunutí do účtu úložiště v nativním formátu. Jakmile vytvoříte definici úlohy, můžete tuto úlohu spustit znovu s různými nastaveními běhu.

Chcete-li vytvořit definici úlohy, proveďte následující kroky.

1. Přejděte na službu, kterou jste vytvořili. Přejděte na **definice úloh řízení >**.

2. Klepněte na tlačítko **+ Definice úlohy**.

    ![Klikněte na +Definice zakázky.](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Zadejte název definice pracovní ho dispozičního projektu. Název může být mezi 3 a 63 znaky. Název může obsahovat velká a malá písmena, čísla a pomlčky.

4. Zadejte umístění, kde se úloha spouští. Toto umístění se může lišit od umístění, kde je služba nasazena.

5. Chcete-li určit úložiště zdrojových dat, klepněte na **tlačítko Zdroj.**

    ![Konfigurace úložiště zdrojových dat](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Vzhledem k tomu, že se jedná o novou službu Správce dat, nejsou nakonfigurována žádná úložiště dat. V **části Konfigurace zdroje dat**zadejte podrobnosti o zařízení řady StorSimple 8000 a data, která vás zajímají.

   Pokud chcete přidat Správce zařízení StorSimple jako úložiště dat, klikněte v rozevíracím souboru úložiště dat na **Přidat nové** a potom klikněte na Přidat **úložiště dat**.

    ![Přidání nového úložiště dat](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Jako typ úložiště dat zvolte **Správce řad StorSimple 8000.**
    
   2. Zadejte popisný název úložiště zdrojových dat.
    
   3. V rozevíracím seznamu zvolte předplatné přidružené ke službě Správce zařízení StorSimple.
    
   4. Zadejte název Správce zařízení StorSimple pro **prostředek**.

   5. Zadejte **šifrovací** klíč dat služby pro službu StorSimple Device Manager. 

      ![Konfigurace ukládání zdrojových dat 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Po dokončení klepněte na **tlačítko OK.** Tím se uloží úložiště dat. Znovu použít tento StorSimple Správce zařízení v jiných definicích úloh bez zadání těchto parametrů znovu. Trvá několik sekund po klepnutí na tlačítko **OK** pro nově vytvořené úložiště zdrojových dat se zobrazí v rozevíracím souboru.

7. V rozevíracím seznamu **úložiště dat**vyberte úložiště dat, které jste vytvořili. 

   1. Zadejte název zařízení řady StorSimple 8000, které obsahuje data zájmu.

   2. Zadejte název svazku s bydlištěm na zařízení StorSimple, který má vaše data zájmu.

   3. Do pododdílu **Filtr** zadejte kořenový adresář, který obsahuje vaše data, která jsou v zájmu _\MyRootDirectory\Data._ Písmena jednotek _například \C:\Data_ nejsou podporována. Zde můžete také přidat libovolné filtry souborů.

   4. Služba transformace dat funguje jenom na nejnovější snímek dat, která je posunuta do Azure.

   5. Klikněte na tlačítko **OK**.

      ![Konfigurace ukládání zdrojových dat 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Dále je třeba nakonfigurovat cílové úložiště dat. Zvolte účty úložiště, chcete-li umístit soubory do objektů BLOB v tomto účtu. V rozevíracím souboru vyberte **Přidat nové** a pak **Konfigurovat nastavení**.

9. Vyberte typ cílového úložiště, které chcete přidat, a další parametry přidružené k úložišti.

    Pokud vyberete cíl typu účtu úložiště, můžete zadat popisný název, předplatné (zvolit stejný jako u služby nebo jiné) a účet úložiště.
        ![Konfigurace ukládání cílových dat 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Při spuštění úlohy je vytvořena fronta úložiště. Tato fronta je naplňována zprávami o transformovaných objektech blob, jakmile jsou připravené. Název této fronty je stejný jako název definice úlohy.
    
10. Po přidání úložiště dat počkejte několik minut.
    
    1. Vyberte úložiště, které jste vytvořili jako cíl, z rozevíracího seznamu v **názvu cílového účtu**.

    2. Zvolte typ úložiště jako objekty BLOB nebo soubory. Zadejte název kontejneru úložiště, ve kterém se transformovaná data nacházejí. Klikněte na tlačítko **OK**.

        ![Konfigurace cílového účtu úložiště úložišť dat](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Můžete také zkontrolovat možnost před spuštěním úlohy předložit odhad doby trvání úlohy. Chcete-li vytvořit definici projektu, klepněte na tlačítko **OK.** Definice vaší práce je nyní dokončena. Tuto definici úlohy můžete použít vícekrát prostřednictvím ui s různými nastaveními běhu.

    ![Kompletní definice úlohy](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Nově vytvořená definice úlohy je přidána do seznamu definic zakázek pro tuto službu.

### <a name="run-the-job-definition"></a>Spuštění definice úlohy

Kdykoli potřebujete přesunout data ze StorSimple do účtu úložiště, který jste zadali v definici úlohy, je třeba je spustit. Za běhu mohou být některé parametry zadány odlišně. Kroky jsou následující:

1. Vyberte službu StorSimple Data Manager a přejděte na **definici úloh správy >**. Vyberte a klikněte na definici úlohy, kterou chcete spustit.
     
     ![Spustit úlohu 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klepněte na tlačítko **Spustit nyní**.
     
     ![Spustit úlohu 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Chcete-li změnit nastavení, která chcete pro spuštění úlohy změnit, klepněte na tlačítko **Spustit** nastavení. Klikněte na **OK** a potom kliknutím na **Spustit** spusťte svou úlohu.

    ![Spustit úlohu 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Chcete-li tuto úlohu sledovat, přejděte na **úlohy** ve správci dat StorSimple. Kromě monitorování v okně **Úlohy** můžete také naslouchat ve frontě úložiště, kde je zpráva přidána pokaždé, když je soubor přesunut ze StorSimple do účtu úložiště.

    ![Spustit úlohu 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Zobrazit protokoly po dokončení úlohy

Po dokončení úlohy můžete zobrazit stav úlohy. Stav **úlohy**může být úspěšný , **částečně úspěšný** a **neúspěšný**. Můžete zobrazit seznam souborů, které byly úspěšně zkopírovány, a souborů, které se nepodařilo zkopírovat. Tyto seznamy jsou k dispozici v kontejneru s názvem **"storsimple-data-manager-joblogs"** v rámci vašeho účtu cílovéúložiště. V rámci tohoto kontejneru můžete vyhledat složku se stejným názvem jako definice úlohy. V rámci tohoto, bude vytvořena složka pro každou úlohu spustit, které budou obsahovat vaše seznamy. Název této složky bude identifikátor GUID úlohy, který můžete získat ze stránky podrobností o úloze. Případně ve většině případů uvidíte odkaz pro protokoly kopírování v rámci samotné stránky úloh.
K dispozici jsou 2 sady souborů CSV, které uvidíte v této složce. Všechny soubory, které začínají **zkopírovanýmfilelistem...** budou obsahovat seznam úspěšně zkopírovaných souborů. Všechny soubory, které začínají **failedfilelist...** obsahují soubory, které nebylo možné zkopírovat, spolu s chybovou zprávou.


## <a name="next-steps"></a>Další kroky

[Pomocí sady .NET SDK spusťte úlohy Správce dat StorSimple](storsimple-data-manager-dotnet-jobs.md).
