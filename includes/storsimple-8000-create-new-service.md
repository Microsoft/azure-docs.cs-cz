---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d47cf21e25c89c20a8baa31a80b867b74ada93df
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360669"
---
#### <a name="to-create-a-new-service"></a>Vytvoření nové služby

1. Pomocí přihlašovacích údajů účtu Microsoft se přihlaste na webu [Azure Portal](https://portal.azure.com/).

2. Na webu Azure Portal klikněte na **Vytvořit prostředek** a na Marketplace potom klikněte na **Zobrazit vše**.

    ![Vytvoření Správce zařízení StorSimple](./media/storsimple-8000-create-new-service/createssdevman1.png)

    Vyhledejte _Fyzické zařízení StorSimple_. Vyberte a klikněte na **Řada fyzických zařízení StorSimple** a pak klikněte na **Vytvořit**. Případně klikněte na Azure Portal **+** a potom v části **úložiště** klikněte na **řada fyzických zařízení StorSimple**.

    ![Vytvoření StorSimple Device Manager 2](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. V okně **Správce zařízení StorSimple** proveďte následující kroky:

   1. Zadejte pro službu jedinečný **Název prostředku**. Tento název je popisný název, který můžete použít k identifikaci služby. Název může být tvořen 2 až 50 písmeny, číslicemi a spojovníky. Název musí začínat a končit písmenem nebo číslicí.

   2. V rozevíracím seznamu **Předplatné** vyberte předplatné. Předplatné je propojeno s vaším fakturačním účtem. Toto pole není dostupné, pokud máte pouze jedno předplatné.

   3. V části **Skupina prostředků** vyberte možnost **Použít existující** nebo **Vytvořit novou** skupinu. Další informace najdete v tématu [Skupiny prostředků Azure](/azure/azure-resource-manager/management/manage-resource-groups-portal).

   4. Zadejte **Umístění** služby. Obvykle platí, že byste měli zvolit umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit. Měli byste zvážit i následující aspekty:

      * Pokud máte v Azure existující úlohy, které chcete také nasadit se zařízením StorSimple, měli byste použít stejné datové centrum.
      * Služba Správce zařízení StorSimple a úložiště Azure můžou být ve dvou různých umístěních. V takovém případě je nutné vytvořit Správce zařízení StorSimple a účet úložiště Azure odděleně. Pokud chcete vytvořit účet úložiště Azure, přejděte na webu Azure Portal do služby Azure Storage a postupujte podle kroků popsaných v tématu [Vytvoření účtu služby Azure Storage](../articles/storage/common/storage-account-create.md). Po vytvoření účtu jej přidejte do služby Správce zařízení StorSimple pomocí kroků popsaných v tématu [Konfigurace nového účtu úložiště pro službu](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service).

   5. Výběrem možnosti **Vytvořit nový účet úložiště** můžete vytvořit účet úložiště automaticky se službou. Zadejte název tohoto účtu úložiště. Pokud potřebujete mít data v jiném umístění, zaškrtnutí políčka zrušte.

   6. Pokud chcete mít na řídicím panelu rychlý odkaz na tuto službu, zaškrtněte políčko **Připnout na řídicí panel**.

   7. Kliknutím na **Vytvořit** vytvořte Správce zařízení StorSimple.

       ![Vytvoření StorSimple Device Manager 3](./media/storsimple-8000-create-new-service/createssdevman2.png)

Vytvoření služby trvá několik minut. Po úspěšném vytvoření služby se zobrazí oznámení a otevře se okno nové služby.

![Vytvoření StorSimple Device Manager 4](./media/storsimple-8000-create-new-service/createssdevman5.png)
