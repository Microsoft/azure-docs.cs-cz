---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 4ba5c8b69776b39d8a6640744b0c24600f3a0d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175194"
---
#### <a name="to-create-a-new-service"></a>Vytvoření nové služby

1.  Pomocí přihlašovacích údajů k účtu Microsoft se přihlaste na portál Azure na této adrese URL: <https://portal.azure.com/>. Pokud zařízení nasazujete na portálu pro státní správu, přihlaste se na adrese:<https://portal.azure.us/>

2.  Na webu Azure Portal klikněte na **+ Create a resource** &gt; **Storage** &gt; **StorSimple Virtual Series**.

    ![Vytvořit novou službu](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  V okně **Správce zařízení StorSimple,** které se otevře, postupujte takto:

    1.  Zadejte pro službu jedinečný **Název prostředku**. Název prostředku je popisný název, který lze použít k identifikaci služby. Název může být tvořen 2 až 50 písmeny, číslicemi a spojovníky. Název musí začínat a končit písmenem nebo číslicí.

    2.  V rozevíracím seznamu **Předplatné** vyberte předplatné. Předplatné je propojeno s vaším fakturačním účtem. Toto pole není dostupné, pokud máte pouze jedno předplatné.

    3.  Ve **skupině Prostředků**vyberte existující nebo vytvořte novou skupinu. Další informace najdete v tématu [Skupiny prostředků Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Zadejte **Umístění** služby. Další informace o tom, které služby jsou dostupné ve které oblasti, najdete v tématu [Oblasti Azure.](https://azure.microsoft.com/regions/#services) Obecně zvolte **umístění,** které je nejblíže zeměpisné oblasti, do které chcete zařízení nasadit. Zvažte i následující okolnosti:

        -   Pokud máte existující úlohy v Azure, které máte také v úmyslu nasadit s vaším zařízením StorSimple, doporučujeme použít toto datové centrum.

        -   Správce zařízení StorSimple a úložiště Azure můžou být ve dvou samostatných umístěních. V takovém případě je nutné vytvořit Správce zařízení StorSimple a účet úložiště Azure odděleně. Pokud chcete vytvořit účet úložiště Azure, přejděte na Azure Storage na webu Azure Portal a postupujte podle kroků popsaných v [části Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). Po vytvoření účtu jej přidejte do služby Správce zařízení StorSimple pomocí kroků popsaných v tématu [Konfigurace nového účtu úložiště pro službu](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Pokud nasazujete virtuální zařízení na portálu pro vládní služby, služba StorSimple Device Manager je k dispozici v lokalitách V Usa iowa a USA ve Virginii.

    5.  Vyberte **Vytvořit nový účet úložiště Azure,** chcete-li automaticky vytvořit účet úložiště se službou. Zadejte **název účtu úložiště**. Pokud potřebujete mít data v jiném umístění, zaškrtnutí políčka zrušte.

    6.  Pokud chcete mít na řídicím panelu rychlý odkaz na tuto službu, zaškrtněte políčko **Připnout na řídicí panel**.

    7.  Kliknutím na **Vytvořit** vytvořte Správce zařízení StorSimple.

        ![Vytvořit novou službu](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Budete přesměrováni na vstupní stránku **služby.** Vytvoření služby trvá několik minut. Po úspěšném vytvoření služby budete odpovídajícím způsobem upozorněni a stav služby se změní na **Aktivní**.


