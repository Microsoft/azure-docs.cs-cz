---
title: Nasazení služby Správce zařízení StorSimple | Dokumentace Microsoftu
description: Vysvětluje, jak vytvářet a odstraňovat služby Správce zařízení StorSimple na webu Azure Portal a popisuje, jak spravovat registrační klíč služby.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38688149"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Nasazení služby Správce zařízení StorSimple pro StorSimple Virtual Array
## <a name="overview"></a>Přehled

Služba Správce zařízení StorSimple běží v Microsoft Azure a připojí k více zařízení StorSimple. Po vytvoření služby ji můžete použít ke správě zařízení z portálu Microsoft Azure pro spouštění v prohlížeči. To vám umožňuje monitorovat všechna zařízení, které jsou připojené ke službě Správce zařízení StorSimple v jednom centrálním umístění, a tím minimalizovat správní režie.

Běžné úlohy související s služby Správce zařízení StorSimple jsou:

* Vytvoření služby
* Odstranit službu
* Získání registračního klíče služby
* Znovu vygenerovat registrační klíč služby

Tento kurz popisuje, jak k provádění všech předchozích úlohách. Informace obsažené v tomto článku se vztahuje pouze na virtuálních polí StorSimple. Další informace o StorSimple řady 8000, přejděte na [nasazení služby StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Vytvoření služby

Pokud chcete vytvořit službu, musíte mít:

* Předplatné se smlouvou Enterprise
* Aktivní účet úložiště Microsoft Azure
* Fakturační údaje, který se používá pro správu přístupu

Můžete také vytvořit účet úložiště při vytváření služby.

Jediné služby může spravovat více zařízení. Zařízení, ale nemůžou zahrnovat víc služeb. Velký podnik může mít víc instancí služby pro práci s různým předplatným, organizace nebo dokonce nasazení umístění.

> [!NOTE]
> Budete potřebovat samostatné instance služby Správce zařízení StorSimple ke správě zařízení řady StorSimple 8000 a virtuálních polí StorSimple.


Proveďte následující kroky k vytvoření služby.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Odstranit službu

Než odstraníte službu, ujistěte se, že žádná připojená zařízení budou používat. Pokud se služba používá, deaktivujte připojená zařízení. Operaci deaktivovat severu připojení mezi zařízením a službou, ale zachovat data zařízení v cloudu.

> [!IMPORTANT]
> Po odstranění služby je nevratná operace. Jakékoli zařízení, které se pomocí služby bude muset být výrobního předtím, než je možné s jinou službu. V tomto scénáři místní data na zařízení, a také konfigurace, budou ztracena.
 

Proveďte následující kroky pro odstranění služby.

#### <a name="to-delete-a-service"></a>Chcete-li odstranit službu

1. Přejděte na **všechny prostředky**. Hledání služby Správce zařízení StorSimple. Vyberte službu, kterou chcete odstranit.
   
    ![Vyberte službu odstranit](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Přejdete na řídicí panel služby, abyste zajistili zde nejsou žádné zařízení připojená ke službě. Pokud nejsou žádná zařízení registrovaná ve službě, zobrazí se také zprávě o tom. Klikněte na **Odstranit**.
   
    ![Odstranit službu](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano** v potvrzení oznámení. 
   
    ![Potvrzení odstranění služby](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Může trvat několik minut, než služba má být odstraněn. Po úspěšném odstranění služby, budete upozorněni.
   
    ![Odstraňuje se služba úspěšné](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Seznam služeb se aktualizují.

 ![Aktualizovaný seznam služeb](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Získání registračního klíče služby
Po úspěšném vytvoření služby je potřeba zařízení StorSimple zaregistrovat do služby. K registraci prvního zařízení StorSimple, potřebujete registrační klíč služby. K registraci dalších zařízení s existující službu StorSimple, budete potřebovat registrační klíč a šifrovací klíč dat služby (který je generován v prvním zařízení během registrace). Další informace o šifrovací klíč dat služby najdete v tématu [zabezpečení zařízení StorSimple](storsimple-security.md). Můžete získat registrační klíč díky přístupu **klíče** okno pro vaši službu.

Proveďte následující kroky, chcete-li získat registrační klíč služby.

#### <a name="to-get-the-service-registration-key"></a>Chcete-li získat registrační klíč služby
1. V **Správce zařízení StorSimple** okno, přejděte na **správu &gt;**  **klíče**.
   
   ![Okno Klíče](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. V **klíče** otevře se okno, registrační klíč služby. Zkopírujte registrační klíč pomocí ikony kopírování. 

Zachovat registrační klíč služby do bezpečného umístění. Potřebujete tento klíč, jakož i šifrovací klíč dat služby, k registraci dalších zařízení s touto službou. Po obdržení registračního klíče služby, je potřeba nakonfigurovat své zařízení pomocí Windows Powershellu pro StorSimple rozhraní.

## <a name="regenerate-the-service-registration-key"></a>Znovu vygenerovat registrační klíč služby
Je potřeba znovu vygenerovat registrační klíč služby, pokud je nutné provést obměna klíčů, nebo pokud došlo ke změně seznamu správců služeb. Pokud klíč znovu vygenerujete, nový klíč slouží pouze k registraci dalších zařízení. Zařízení, která již byla registrována nejsou ovlivněny tímto procesem.

Proveďte následující kroky se znovu vygenerovat registrační klíč služby.

#### <a name="to-regenerate-the-service-registration-key"></a>Znovu vygenerovat registrační klíč služby
1. V **Správce zařízení StorSimple** okno, přejděte na **správu &gt;**  **klíče**.
   
   ![Okno Klíče](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. V **klíče** okna, klikněte na tlačítko **znovu vygenerovat**.
   
   ![Klikněte na znovu vygenerovat klíč](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. V **znovu vygenerovat registrační klíč** okno, zkontrolujte tato akce vyžadována, je-li klíče budou znovu vygenerovány. Další zařízení, která jsou registrovaná ve službě použije nový registrační klíč. Klikněte na tlačítko **znovu vygenerovat** potvrďte. Po dokončení registrace, budete upozorněni.
   
   ![Zkontrolujte znovu vygenerovat klíč](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Zobrazí se nový registrační klíč služby.
   
    ![Zkontrolujte znovu vygenerovat klíč](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Klíč zkopírujte a uložte ho pro registraci všechna nová zařízení s touto službou.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [Začínáme](storsimple-virtual-array-deploy1-portal-prep.md) službou StorSimple Virtual Array.
* Zjistěte, jak [spravovat zařízení StorSimple](storsimple-ova-web-ui-admin.md).

