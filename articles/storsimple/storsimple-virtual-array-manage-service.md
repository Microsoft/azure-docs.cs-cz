---
title: Nasazení služby Správce zařízení StorSimple | Microsoft Docs
description: Vysvětluje, jak vytvářet a odstraňovat služby StorSimple Manager zařízení na portálu Azure a popisuje, jak spravovat registrační klíč služby.
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
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 1881a0625b107ae1a90e5b772f5296a4d728973d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876100"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Nasazení služby StorSimple Manager zařízení StorSimple virtuální pole
## <a name="overview"></a>Přehled

Služba Správce zařízení StorSimple běží v Microsoft Azure a připojí k více zařízení StorSimple. Po vytvoření služby, můžete ke správě zařízení z portálu Microsoft Azure spuštěnému v prohlížeči. To vám umožňuje monitorovat všechna zařízení, které jsou připojené ke službě StorSimple Manager zařízení z jedné centrální umístění, a současně minimalizujete její související administrativní zátěže.

Běžné úlohy související s služby StorSimple Manager zařízení jsou:

* Vytvoření služby
* Odstranění služby
* Získání registračního klíče služby
* Znovu vygenerovat registrační klíč služby

Tento kurz popisuje, jak provést jednotlivé předchozí úlohy. Informace obsažené v tomto článku se vztahuje pouze na pole virtuální zařízení StorSimple. Další informace o řady StorSimple 8000, přejděte na [nasazení služby StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Vytvoření služby

Pokud chcete vytvořit službu, musíte mít:

* Předplatné s smlouvu Enterprise Agreement
* Aktivní účet úložiště Microsoft Azure
* Fakturační informace, které se používá pro správu přístupu

Můžete také vygenerovat účet úložiště při vytváření služby.

Jeden služby můžete spravovat více zařízení. Zařízení, ale nemůžou zahrnovat víc služeb. Velký podnik může mít víc instancí služby pro práci s různých předplatných, organizace nebo i umístění nasazení.

> [!NOTE]
> Je třeba samostatné instance služby StorSimple Manager zařízení ke správě zařízení řady StorSimple 8000 a pole virtuální zařízení StorSimple.


Proveďte následující kroky k vytvoření služby.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Odstranění služby

Před odstraněním služby, ujistěte se, že žádné připojená zařízení ji používají. Pokud služba se používá, deaktivujte připojená zařízení. Operaci deaktivovat severu připojení mezi zařízením a služby, ale zachovat data zařízení v cloudu.

> [!IMPORTANT]
> Po odstranění služby nelze vrátit operaci zpět. Jakékoli zařízení, které se pomocí služby bude muset být tovární nastavení, než se dá použít s jinou službu. V tomto scénáři místní data na zařízení, jakož i konfigurace, budou ztracena.
 

Proveďte následující kroky pro odstranění služby.

#### <a name="to-delete-a-service"></a>Chcete-li odstranit služby

1. Přejděte na **všechny prostředky**. Vyhledávání služby StorSimple Manager zařízení. Vyberte službu, kterou chcete odstranit.
   
    ![Vyberte službu odstranit](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Přejděte na řídicí panel služby zajistit nejsou žádná zařízení připojené ke službě. Pokud nejsou žádná zařízení registrovaná ve službě, zobrazí se také zpráva hlavičky v tom smyslu. Klikněte na **Odstranit**.
   
    ![Odstranění služby](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano** v potvrzení oznámení. 
   
    ![Potvrdit odstranění služby](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. To může trvat několik minut, než službu, kterou chcete odstranit. Po odstranění služby je úspěšně, budete upozorněni.
   
    ![Odstranění úspěšné služby](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Seznam služeb se aktualizují.

 ![Aktualizovaný seznam služeb](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Získání registračního klíče služby
Po úspěšném vytvoření služby musíte registrace zařízení StorSimple pomocí služby. K registraci svého prvního zařízení StorSimple, budete potřebovat registrační klíč služby. K registraci dalších zařízení s existující službu StorSimple, budete potřebovat registrační klíč a služby datového šifrovacího klíče (což je vygenerovaný na první zařízení během registrace). Další informace o šifrovacího klíče dat služby najdete v tématu [zabezpečení zařízení StorSimple](storsimple-security.md). Abyste mohli získat registrační klíč přístup k **klíče** okno pro vaši službu.

Proveďte následující kroky k získání registračního klíče služby.

#### <a name="to-get-the-service-registration-key"></a>Chcete-li získat registrační klíč služby
1. V **Manager zařízení StorSimple** okno, přejděte na **správy &gt;**  **klíče**.
   
   ![Okno Klíče](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. V **klíče** otevře se okno, registrační klíč služby. Zkopírujte registrační klíč pomocí ikonu kopírování. 

Zachovat registrační klíč služby do bezpečného umístění. Budete potřebovat tento klíč, jakož i služby datový šifrovací klíč k registraci dalších zařízení s touto službou. Po získání registračního klíče služby, musíte nakonfigurovat zařízení pomocí Windows Powershellu pro StorSimple rozhraní.

## <a name="regenerate-the-service-registration-key"></a>Znovu vygenerovat registrační klíč služby
Musíte se znova vygenerovat registrační klíč služby, pokud je nutné provést střídání klíče, nebo pokud došlo ke změně seznamu správců služeb. Pokud jste znovu vygenerovat klíč, nový klíč slouží pouze k registraci dalších zařízení. Zařízení, která již byla zaregistrována nejsou ovlivněny tímto procesem.

Proveďte následující kroky k opětovnému vytvoření registrační klíč služby.

#### <a name="to-regenerate-the-service-registration-key"></a>Chcete-li znovu vygenerovat registrační klíč služby
1. V **Manager zařízení StorSimple** okno, přejděte na **správy &gt;**  **klíče**.
   
   ![Okno Klíče](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. V **klíče** okně klikněte na tlačítko **znovu vygenerovat**.
   
   ![Klikněte na tlačítko znovu vygenerovat](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. V **registrační klíč znovu generovat služby** okno, zkontrolujte akce vyžadována, je-li se znovu vygenerovat klíče. Další zařízení, které jsou registrované s touto službou použije nový registrační klíč. Klikněte na tlačítko **znovu vygenerovat** k potvrzení. Po dokončení registrace, budete upozorněni.
   
   ![Zkontrolujte znovu generovat klíče](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Zobrazí se nový registrační klíč služby.
   
    ![Zkontrolujte znovu generovat klíče](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Zkopírujte tento klíč a uložit ho pro registraci nové zařízení s touto službou.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [Začínáme](storsimple-virtual-array-deploy1-portal-prep.md) s polem virtuální zařízení StorSimple.
* Zjistěte, jak [spravovat zařízení StorSimple](storsimple-ova-web-ui-admin.md).

