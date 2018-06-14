---
title: Uživatelské rozhraní aplikace Microsoft Azure StorSimple Manager dat | Microsoft Docs
description: Popisuje způsob použití služby StorSimple Data Manager uživatelského rozhraní
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
ms.openlocfilehash: d704cf8e6840c6a7b0a637c404d421f9f1497c46
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862253"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Spravovat službu StorSimple Manager dat na portálu Azure

Tento článek vysvětluje, jak můžete pomocí uživatelského rozhraní StorSimple Manager dat pro transformaci dat, které se nacházejí na řadu zařízení StorSimple 8000. Transformovaná data mohou být spotřebovávána pak jinými službami Azure, například Azure Media Services, Azure HDInsight, Azure Machine Learning a Azure Search.


## <a name="use-storsimple-data-transformation"></a>Transformace dat StorSimple použít

Data Manager zařízení StorSimple je prostředek v rámci data, která je vytvořena instance transformace. Transformace dat služby vám umožňuje transformovat data z formátu StorSimple nativní formát objektů BLOB nebo Azure Files. Pro transformaci dat nativní formát StorSimple, budete muset zadat podrobnosti o zařízení řady StorSimple 8000 a data zájmu, které chcete transformace.

### <a name="create-a-storsimple-data-manager-service"></a>Vytvoření služby StorSimple Manager dat

Proveďte následující kroky k vytvoření služby StorSimple Data Manager.

1. Pomocí přihlašovacích údajů účtu Microsoft se přihlaste na webu [Azure Portal](https://portal.azure.com/).

2. Klikněte na tlačítko **+ vytvořit prostředek** a vyhledávání pro StorSimple Data Manager.

    ![Vytvoření služby StorSimple Data Manager 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Klikněte na tlačítko StorSimple Manager dat a pak klikněte na **vytvořit**.
    
    ![Vytvoření služby StorSimple Data Manager 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Pro novou službu zadejte následující informace:

    1. Zadejte jedinečný **název služby** vaše Data Manager zařízení StorSimple. Jedná se o popisný název, který můžete použít k identifikaci služby. Název může mít délku 3 až 24 znaků, které mohou být písmena, číslice a pomlčky. Název musí začínat a končit písmenem nebo číslicí.

    2. Vyberte **předplatné** z rozevíracího seznamu. Předplatné je propojeno s vaším fakturačním účtem. Toto pole je automaticky vyplněno (a ne volitelný) Pokud máte pouze jedno předplatné.

    3. Vyberte existující skupinu prostředků nebo vytvořte novou skupinu. Další informace najdete v tématu [Skupiny prostředků Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4. Zadejte **umístění** zaštiťující účty úložiště a služby StorSimple Manager dat služby. Služby StorSimple Manager zařízení, služba Data Manager a přidruženého účtu úložiště by měl být v podporovaných oblastí.
    
    5. Chcete-li získat odkaz na tuto službu na řídicím panelu, vyberte **připnout na řídicí panel**.
    
    6. Klikněte na možnost **Vytvořit**.

    ![Vytvoření služby StorSimple Manager dat 3](./media/storsimple-data-manager-ui/create-service-4.png)

Vytvoření služby trvá několik minut. Uvidíte oznámení po úspěšném vytvoření služby a zobrazí se nové služby.

### <a name="create-a-data-transformation-job-definition"></a>Vytvoření definice úlohy transformace dat

V rámci služby StorSimple Manager dat budete muset vytvořit definici úlohy transformace data. Definice úlohy Určuje podrobnosti o StorSimple data, která vás zajímá Přesun do účtu úložiště v nativním formátu. Po vytvoření definice úlohy, potom můžete spustit tuto úlohu znovu s jinou runtime nastavení.

Proveďte následující kroky k vytvoření definice úlohy.

1. Přejděte ke službě, kterou jste vytvořili. Přejděte na **správy > úlohy definice**.

2. Klikněte na tlačítko **+ úlohy definice**.

    ![Klikněte na tlačítko + definice úlohy](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Zadejte název vaší definice úlohy. Název může být v rozmezí 3 až 63 znaků. Název může obsahovat velká a malá písmena, číslice a pomlčky.

4. Zadejte umístění, kde běží vaše úlohy. Toto umístění může být jiná než umístění, kde se služba nasadit.

5. Klikněte na tlačítko **zdroj** k určení zdrojové datové úložiště.

    ![Konfigurace zdroje dat úložiště](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Vzhledem k tomu, že toto je nová služba Data Manager, jsou nakonfigurovány žádné datové úložiště. V **zdroj dat konfigurace**, zadejte podrobnosti o zařízení řady StorSimple 8000 a požadovaná data.

   Chcete-li přidat vaše zařízení StorSimple Manager jako úložiště dat, klikněte na tlačítko **přidat nový** rozevírací úložiště dat a pak klikněte na **úložiště dat přidat**.

    ![Přidat nové úložiště dat](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
    1. Zvolte **řady StorSimple 8000 Manager** jako datový typ úložiště.
    
    2. Zadejte popisný název úložiště dat zdrojů.
    
    3. Z rozevíracího seznamu vyberte předplatné spojené s služby StorSimple Manager zařízení.
    
    4. Zadejte název služby Správce zařízení StorSimple pro **prostředků**.

    5. Zadejte **šifrování dat služby** klíče pro službu Správce zařízení StorSimple. 

    ![Konfigurace zdroje dat úložiště 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

    Klikněte na tlačítko **OK** po dokončení. To umožňuje ušetřit úložiště vaše data. Znovu použít tento správce zařízení StorSimple v jiné definice úlohy bez opětovného zadávání tyto parametry. Jak dlouho trvá několik sekund, po kliknutí na tlačítko **OK** pro nově vytvořené zdrojové úložiště dat než se zobrazí v rozevírací nabídce.

7. Z rozevíracího seznamu pro **úložiště dat**, vyberte úložiště dat, který jste vytvořili. 

    1. Zadejte název pro zařízení řady StorSimple 8000 obsahující data, která vás zajímají.

    2. Zadejte název svazku umístěný na zařízení StorSimple, která obsahuje vaše data, která vás zajímají.

    3. V **filtru** pododdílu, zadejte kořenový adresář, který obsahuje vaše data zájem o _\MyRootDirectory\Data_ formátu. Písmena jednotky, například _\C:\Data_ nejsou podporovány. Můžete také přidat všechny souboru filtry.

    4. Služba transformaci dat funguje na data, která vložena do Azure pomocí snímků. Při spuštění této úlohy můžete proveďte zálohu při každém spuštění této úlohy (pro práci na nejnovější data) nebo použijte existující poslední zálohy v cloudu (Pokud pracujete na některé Archivovaná data).

    5. Klikněte na **OK**.

    ![Konfigurace zdroje dat úložiště 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. V dalším kroku cílového úložiště dat je potřeba nakonfigurovat. Vyberte účty úložiště pro soubory umístit do objektů BLOB v daném účtu. V rozevírací nabídce, a vyberte **přidat nový** a potom **nakonfigurovat nastavení**.

9. Vyberte typ cílového úložiště, které chcete přidat a ostatní parametry související s úložištěm.

    Pokud vyberete cíl typ účtu úložiště, můžete zadat popisný název, předplatné (vyberte stejný jako služby nebo jiných) a účet úložiště.
        ![Konfigurace úložiště dat cíl 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Fronty úložiště se vytvoří při spuštění úlohy. Tato fronta je naplňována zprávami o transformovaných objektech blob, jakmile jsou připravené. Název této fronty je stejný jako název definice úlohy.
    
10. Po přidání úložiště dat, počkejte několik minut.
    
    1. Vyberte jako cíl z rozevíracího seznamu v úložišti, který jste vytvořili **název cílového účtu**.

    2. Vyberte typ úložiště jako objekty BLOB nebo soubory. Zadejte název kontejneru storage, kde jsou uložena Transformovaná data. Klikněte na **OK**.

        ![Konfigurace cílového účtu úložiště úložišti dat](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Můžete také zkontrolovat možnost k dispozici odhad doby trvání úlohy před spuštěním úlohy. Klikněte na tlačítko **OK** k vytvoření definice úlohy. Vaše definice úlohy je nyní dokončen. Můžete použít tuto definici úlohy několikrát prostřednictvím uživatelského rozhraní pomocí nastavení různých běhového prostředí.

    ![Definice dokončení úlohy](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Definice nově vytvořený úlohy se přidá do seznamu definice úlohy pro tuto službu.

### <a name="run-the-job-definition"></a>Spustit definici úlohy

Kdykoli budete potřebovat pro přesun dat z StorSimple k účtu úložiště, který jste zadali v definici úlohy, budete muset spustit. V době běhu některé parametry lze jinak. Kroky jsou následující:

1. Vybrat služby StorSimple Manager dat, přejděte na **správy > úlohy definice**. Vyberte a klikněte na danou definici úlohy, který chcete spustit.
     
     ![Spustit úlohu spustit 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klikněte na tlačítko **spustit nyní**.
     
     ![Spustit úlohu spustit 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Klikněte na tlačítko **spustit nastavení** změnit nastavení, které můžete chtít změnit pro tuto úlohu spustit. Klikněte na tlačítko **OK** a pak klikněte na **spustit** spustit úlohu.

    ![Spustit úlohu spustit 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Ke sledování této úlohy, přejděte na **úlohy** vaše Data Manager zařízení StorSimple. Kromě monitorování v **úlohy** okno, můžete také poslouchat na fronty úložiště, kde se zpráva přidá pokaždé, když se soubor přesune ze zařízení StorSimple účet úložiště.

    ![Spustit úlohu spustit 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>Další kroky

[Spuštění úlohy StorSimple Manager dat pomocí .NET SDK](storsimple-data-manager-dotnet-jobs.md).