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
ms.openlocfilehash: 4285ee94d2f2a9b5fff9d1896cdf7013f88801ab
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979601"
---
#### <a name="to-create-a-new-service"></a>Vytvoření nové služby

1.  Pomocí přihlašovacích údajů účtu Microsoft se přihlaste k webu Azure portal na této adrese URL: <https://portal.azure.com/>. Pokud nasazení zařízení na portálu Government, přihlaste se na: <https://portal.azure.us/>

2.  Na webu Azure Portal, klikněte na tlačítko **+ vytvořit prostředek** &gt; **úložiště** &gt; **virtuálních řad StorSimple**.

    ![Vytvořit novou službu](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  V **Správce zařízení StorSimple** okno, které se otevře, proveďte následující:

    1.  Zadejte pro službu jedinečný **Název prostředku**. Název prostředku je popisný název, který slouží k identifikaci služby. Název může být tvořen 2 až 50 písmeny, číslicemi a spojovníky. Název musí začínat a končit písmenem nebo číslicí.

    2.  V rozevíracím seznamu **Předplatné** vyberte předplatné. Předplatné je propojeno s vaším fakturačním účtem. Toto pole není dostupné, pokud máte pouze jedno předplatné.

    3.  Pro **skupiny prostředků**, vyberte existující nebo vytvořte novou skupinu. Další informace najdete v tématu [Skupiny prostředků Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Zadejte **Umístění** služby. Zobrazit [oblastí Azure](https://azure.microsoft.com/regions/#services) Další informace o tom, které služby jsou dostupné v konkrétních oblastech. Obecně platí, zvolte **umístění** nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit. Zvažte i následující okolnosti:

        -   Pokud máte existující úlohy v Azure, které chcete také nasadit se zařízením StorSimple, doporučujeme použít stejné datové centrum.

        -   Správce zařízení StorSimple a Azure storage může být ve dvou různých umístěních. V takovém případě je nutné vytvořit Správce zařízení StorSimple a účet úložiště Azure odděleně. Chcete-li vytvořit účet úložiště Azure, přejděte do služby Azure Storage na webu Azure Portal a postupujte podle kroků popsaných v [vytvořit účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). Po vytvoření účtu jej přidejte do služby Správce zařízení StorSimple pomocí kroků popsaných v tématu [Konfigurace nového účtu úložiště pro službu](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Pokud nasazení virtuálního zařízení na portálu Government, služba Správce zařízení StorSimple je dostupná v nám Iowě a USA Virginie umístění.

    5.  Vyberte **vytvořit nový účet úložiště Azure** pro automatické vytvoření účtu úložiště ve službě. Zadejte **název účtu úložiště**. Pokud potřebujete mít data v jiném umístění, zaškrtnutí políčka zrušte.

    6.  Pokud chcete mít na řídicím panelu rychlý odkaz na tuto službu, zaškrtněte políčko **Připnout na řídicí panel**.

    7.  Kliknutím na **Vytvořit** vytvořte Správce zařízení StorSimple.

        ![Vytvořit novou službu](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Budete přesměrováni **služby** cílovou stránkou. Vytvoření služby trvá několik minut. Po úspěšném vytvoření služby budete odpovídajícím způsobem upozorněni a stav služby se změní na **Aktivní**.


