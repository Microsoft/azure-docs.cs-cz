---
title: Microsoft Azure StorSimple Data Manager UI | Dokumentace Microsoftu
description: Popisuje, jak pomocí služby StorSimple Data Manager uživatelského rozhraní
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: e773986d11a11c6cc84df367aa48ac1b01a9183e
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157118"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Správa služby StorSimple Data Manageru na webu Azure portal

Tento článek vysvětluje, jak transformovat data uložená na zařízeních StorSimple 8000 series pomocí uživatelského rozhraní Správce dat StorSimple. Transformovaná data mohou být spotřebovány pak dalšími službami Azure, jako jsou Azure Media Services, Azure HDInsight, Azure Machine Learning a Azure Search.


## <a name="use-storsimple-data-transformation"></a>Pomocí transformace dat StorSimple

Správce dat StorSimple je prostředků, ve kterých je vytvořena instance transformace. Transformace dat service umožňuje transformovat data z formátu StorSimple na nativním formátu do objektů BLOB nebo soubory Azure. Transformace dat StorSimple nativním formátu, budete muset zadat podrobnosti o zařízení řady StorSimple 8000 a datech, které vás zajímají, který chcete transformovat.

### <a name="create-a-storsimple-data-manager-service"></a>Vytvoření služby StorSimple Data Manageru

Proveďte následující kroky k vytvoření služby StorSimple Data Manageru.

1. Pomocí přihlašovacích údajů účtu Microsoft se přihlaste na webu [Azure Portal](https://portal.azure.com/).

2. Klikněte na tlačítko **+ vytvořit prostředek** a vyhledávání za StorSimple Data Manager.

    ![Vytvoření služby StorSimple Data Manager 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Klikněte na tlačítko Správce dat StorSimple a potom klikněte na tlačítko **vytvořit**.
    
    ![Vytvoření služby StorSimple Data Manager 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Pro novou službu zadejte následující informace:

    1. Zadejte jedinečnou **název služby** pro správce dat StorSimple. Jedná se o popisný název, který můžete použít k identifikaci služby. Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. Název musí začínat a končit písmenem nebo číslicí.

    2. Zvolte **předplatné** z rozevíracího seznamu. Předplatné je propojeno s vaším fakturačním účtem. Toto pole je automaticky naplněná (a nikoli volitelnou) Pokud máte jenom jedno předplatné.

    3. Vyberte existující skupinu prostředků nebo vytvořte novou skupinu. Další informace najdete v tématu [Skupiny prostředků Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4. Zadejte **umístění** pro vaši službu, která jsou uloženy vaše účty úložiště a služby Správce dat StorSimple. Služby Správce zařízení StorSimple, service Data Manager a přidružený účet úložiště by měl být v podporovaných oblastech.
    
    5. Pokud chcete získat odkaz na tuto službu na řídicí panel, vyberte **připnout na řídicí panel**.
    
    6. Klikněte na možnost **Vytvořit**.

    ![Vytvoření služby StorSimple Data Manager 3](./media/storsimple-data-manager-ui/create-service-4.png)

Vytvoření služby trvá několik minut. Po úspěšném vytvoření služby a zobrazí se nové služby se zobrazí oznámení.

### <a name="create-a-data-transformation-job-definition"></a>Vytvoření definice úlohy transformace dat

V rámci služby StorSimple Data Manageru je potřeba vytvořit definice úlohy transformace data. Definice úlohy Určuje informace, které vás zajímají Přesun do účtu úložiště v nativním formátu dat StorSimple. Jakmile vytvoříte definici úlohy, poté můžete spouštět tuto úlohu znovu pomocí nastavení různých běhového prostředí.

Proveďte následující kroky a vytvoříte definici úlohy.

1. Přejděte na službu, kterou jste vytvořili. Přejděte na **správy > úlohy definice**.

2. Klikněte na tlačítko **+ úlohy definice**.

    ![Klikněte na + definice úlohy](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Zadejte název vaší definice úlohy. Název může být v rozmezí 3 až 63 znaků. Název může obsahovat velká a malá písmena, číslice a pomlčky.

4. Zadejte umístění, kde běží vaše úlohy. Toto umístění může být jiné než umístění, ve kterém je nasazená služba.

5. Klikněte na tlačítko **zdroj** k určení zdroje dat úložiště.

    ![Konfigurace úložiště dat zdrojového kódu](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Protože to je nová služba Data Manager, žádná data úložiště jsou nakonfigurované. V **nastavit zdroj dat**, zadejte podrobnosti o zařízení řady StorSimple 8000 a dat, které vás zajímají.

   Chcete-li přidat správce zařízení StorSimple jako úložiště dat, klikněte na tlačítko **přidat nový** v rozevíracím seznamu úložiště dat a pak klikněte na tlačítko **úložiště dat přidat**.

    ![Přidat nové úložiště dat](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
    1. Zvolte **StorSimple řady 8000 správce** jako datový typ úložiště.
    
    2. Zadejte popisný název pro zdroj dat úložiště.
    
    3. Z rozevíracího seznamu vyberte předplatné přidružené k vaší službě Správce zařízení StorSimple.
    
    4. Zadejte název sady aplikaci Správce zařízení StorSimple pro **prostředků**.

    5. Zadejte **Service data encryption** klíče pro službu Správce zařízení StorSimple. 

    ![Konfigurace zdroje dat, úložiště 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

    Klikněte na tlačítko **OK** až budete hotovi. Toto uloží datového úložiště. Opětovné použití tohoto správce zařízení StorSimple v jiné definice úloh bez nutnosti zadávat znovu tyto parametry. Trvá několik sekund, po kliknutí na **OK** pro nově vytvořený zdrojové úložiště dat se zobrazí v rozevírací nabídce.

7. Z rozevíracího seznamu pro **úložiště dat**, vyberte úložiště dat, který jste vytvořili. 

    1. Zadejte název zařízení řady StorSimple 8000, obsahující data, které vás zajímají.

    2. Zadejte název svazku na zařízení StorSimple, které má vaše data, které vás zajímají.

    3. V **filtr** podsekce, zadejte kořenový adresář, který obsahuje vaše data zájem _\MyRootDirectory\Data_ formátu. Například písmena jednotky _\C:\Data_ nejsou podporovány. Můžete také přidat nějaké souboru filtry.

    4. Služba transformace dat funguje na data, která se nasdílí Azure pomocí snímků. Při spuštění této úlohy můžete vytvořit zálohu pokaždé, když se tato úloha je spuštěna (pro práci na nejnovější data) nebo použijte existující poslední zálohy v cloudu (Pokud pracujete na některých Archivovaná data).

    5. Klikněte na **OK**.

    ![Konfigurace zdroje dat, úložiště 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. V dalším kroku cílového úložiště dat je potřeba nakonfigurovat. Vyberte účty úložiště pro soubory umístit do objektů BLOB v příslušném účtu. V rozevíracím seznamu vyberte **přidat nový** a potom **nastavení**.

9. Vyberte typ cílového úložiště, které chcete přidat a jiné parametry přidružené úložiště.

    Pokud vyberete cílový typ účtu úložiště, můžete zadat popisný název předplatného (zvolte stejné jako u služby nebo jiné) a účet úložiště.
        ![Konfigurace cílového úložiště dat 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Fronty úložiště se vytvoří při spuštění úlohy. Tato fronta je naplňována zprávami o transformovaných objektech blob, jakmile jsou připravené. Název této fronty je stejný jako název definice úlohy.
    
10. Poté, co přidáte úložiště dat, počkejte několik minut.
    
    1. Vybrat jako cíl z rozevíracího seznamu v úložišti, které jste vytvořili **název cílového účtu**.

    2. Zvolte typ úložiště jako objekty BLOB nebo soubory. Zadejte název kontejneru úložiště, ve které se nachází Transformovaná data. Klikněte na **OK**.

        ![Konfigurace cílového účtu úložiště úložiště dat](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Můžete také zkontrolovat možnost zobrazit odhad doby trvání úloh před spuštěním úlohy. Klikněte na tlačítko **OK** k vytvoření definice úlohy. Vaší definice úlohy je nyní dokončena. Pomocí nastavení různých běhových můžete tuto definici úlohy více než jednou prostřednictvím uživatelského rozhraní.

    ![Definice dokončení úlohy](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Definice nově vytvořeného projektu se přidá do seznamu definice úloh pro tuto službu.

### <a name="run-the-job-definition"></a>Spustit definice úloh

Pokaždé, když potřebujete přesunout data z StorSimple k účtu úložiště, kterou jste zadali v definici úlohy, musíte ho spustit. Za běhu můžete některé parametry zadávají jinak. Kroky jsou následující:

1. Vyberte svoji službu StorSimple Data Manager a přejděte na **správy > úlohy definice**. Vyberte a klikněte na definici úlohy, které chcete spustit.
     
     ![Spustit úlohu spustit 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klikněte na tlačítko **spustit nyní**.
     
     ![Spustit úlohu spustit 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Klikněte na tlačítko **parametry běhu** upravit všechna nastavení, která můžete chtít změnit pro tuto úlohu spustit. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **spustit** ke spuštění vaší úlohy.

    ![Spustit úlohu spustit 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Pokud chcete tuto úlohu monitorovat, přejděte na **úlohy** do Správce dat StorSimple. Kromě sledování **úlohy** okna, můžete také poslouchat na frontu úložiště, kam se zpráva přidá pokaždé, když se soubor přesune ze StorSimple k účtu úložiště.

    ![Spustit úlohu spustit 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>Další postup

[Použití sady .NET SDK ke spuštění úlohy správce dat StorSimple](storsimple-data-manager-dotnet-jobs.md).