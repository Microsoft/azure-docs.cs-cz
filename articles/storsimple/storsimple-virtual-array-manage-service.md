---
title: Nasazení služby Správce zařízení StorSimple | Dokumenty společnosti Microsoft
description: Vysvětluje, jak vytvořit a odstranit službu StorSimple Device Manager na webu Azure Portal a popisuje, jak spravovat registrační klíč služby.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 2ff987448e3900c2a533789b2f38fd3ced71ce72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267453"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Nasazení služby StorSimple Device Manager pro virtuální pole StorSimple

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Služba StorSimple Device Manager běží v Microsoft Azure a připojuje se k více zařízením StorSimple. Po vytvoření služby ji můžete použít ke správě zařízení z portálu Microsoft Azure spuštěného v prohlížeči. To umožňuje sledovat všechna zařízení, která jsou připojena ke službě StorSimple Device Manager z jednoho centrálního umístění, čímž se minimalizuje administrativní zátěž.

Běžné úkoly související se službou StorSimple Device Manager jsou:

* Vytvoření služby
* Odstranění služby
* Získání registračního klíče služby
* Obnovení registračního klíče služby

Tento kurz popisuje, jak provádět každý z předchozích úkolů. Informace obsažené v tomto článku platí pouze pro virtuální pole StorSimple. Další informace o řadě StorSimple 8000 naleznete v [nasazení služby StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Vytvoření služby

Chcete-li vytvořit službu, musíte mít:

* Předplatné se smlouvou Enterprise
* Aktivní účet úložiště Microsoft Azure
* Fakturační údaje, které se používají pro správu přístupu

Můžete také vygenerovat účet úložiště při vytváření služby.

Jedna služba může spravovat více zařízení. Zařízení však nemůže span více služeb. Velký podnik může mít více instancí služeb pro práci s různými předplatnými, organizacemi nebo dokonce umístěními nasazení.

> [!NOTE]
> Ke správě zařízení řady StorSimple 8000 a virtuálních polí StorSimple potřebujete samostatné instance služby StorSimple Device Manager.


Chcete-li vytvořit službu, proveďte následující kroky.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Odstranění služby

Před odstraněním služby zkontrolujte, zda ji nepoužívají žádná připojená zařízení. Pokud je služba používána, deaktivujte připojená zařízení. Operace deaktivace přeruší spojení mezi zařízením a službou, ale zachová data zařízení v cloudu.

> [!IMPORTANT]
> Po odstranění služby nelze operaci vrátit zpět. Jakékoli zařízení, které službu používalo, bude muset být před použitím jiné služby nutné obnovit tovární nastavení. V tomto scénáři dojde ke ztrátě místních dat v zařízení, stejně jako konfigurace.
 

Chcete-li odstranit službu, proveďte následující kroky.

#### <a name="to-delete-a-service"></a>Odstranění služby

1. Přejděte na **Všechny prostředky**. Vyhledejte službu Správce zařízení StorSimple. Vyberte službu, kterou chcete odstranit.
   
    ![Vyberte službu, která má být odstraněna.](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Přejděte na řídicí panel služby a ujistěte se, že ke službě nejsou připojena žádná zařízení. Pokud nejsou k této službě registrována žádná zařízení, zobrazí se v tomto smyslu také bannerová zpráva. Klepněte na **tlačítko Odstranit**.
   
    ![Odstranit službu](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Po zobrazení výzvy k potvrzení klikněte v potvrzovacím oznámení na **tlačítko Ano.** 
   
    ![Potvrdit odstranění služby](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Může trvat několik minut, než bude služba odstraněna. Po úspěšném odstranění služby budete upozorněni.
   
    ![Úspěšné odstranění služby](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Seznam služeb bude aktualizován.

 ![Aktualizovaný seznam služeb](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Získání registračního klíče služby
Po úspěšném vytvoření služby budete muset zaregistrovat zařízení StorSimple se službou. Chcete-li zaregistrovat své první zařízení StorSimple, budete potřebovat registrační klíč služby. Chcete-li zaregistrovat další zařízení s existující službou StorSimple, budete potřebovat registrační klíč i šifrovací klíč dat služby (který je generován na prvním zařízení během registrace). Další informace o šifrovacím klíči dat služby naleznete v [tématu StorSimple security](storsimple-security.md). Registrační klíč můžete získat přístupem k blade **keys** pro vaši službu.

Chcete-li získat registrační klíč služby, proveďte následující kroky.

#### <a name="to-get-the-service-registration-key"></a>Získání registračního klíče služby
1. V okně **Správce zařízení StorSimple** přejděte na ** &gt; klíči pro správu** . **Keys**
   
   ![Okno Klíče](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. V okně **Klíče** se zobrazí registrační klíč služby. Zkopírujte registrační klíč pomocí ikony kopírování. 

Uchovávejte registrační klíč služby na bezpečném místě. Tento klíč a šifrovací klíč dat služby budete potřebovat k registraci dalších zařízení s touto službou. Po získání registračního klíče služby budete muset nakonfigurovat zařízení prostřednictvím rozhraní Windows PowerShell for StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Obnovení registračního klíče služby
Pokud je nutné provést střídání klíčů nebo pokud se změnil seznam správců služeb, budete muset znovu vygenerovat registrační klíč služby. Při regeneraci klíče se nový klíč používá pouze pro registraci následných zařízení. Zařízení, která již byla zaregistrována, nejsou tímto procesem ovlivněna.

Chcete-li znovu vygenerovat registrační klíč služby, proveďte následující kroky.

#### <a name="to-regenerate-the-service-registration-key"></a>Obnovení registračního klíče služby
1. V okně **Správce zařízení StorSimple** přejděte na ** &gt; klíči pro správu** . **Keys**
   
   ![Okno Klíče](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. V okně **Klíče** klepněte na tlačítko **Obnovit**.
   
   ![Klikněte na regenerovat](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. V okně **obnovit službu registrace klíče** zkontrolujte akci požadovanou při opětovném vygenerování klíčů. Všechna následující zařízení, která jsou registrována v této službě, budou používat nový registrační klíč. Potvrďte klepnutím na **tlačítko Obnovit.** Po dokončení registrace budete upozorněni.
   
   ![Potvrdit znovu vygenerovat klíč](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Zobrazí se nový registrační klíč služby.
   
    ![Potvrdit znovu vygenerovat klíč](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Zkopírujte tento klíč a uložte jej pro registraci nových zařízení s touto službou.

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [začít](storsimple-virtual-array-deploy1-portal-prep.md) s virtuálním polem StorSimple.
* Přečtěte si, jak [spravovat zařízení StorSimple](storsimple-ova-web-ui-admin.md).

