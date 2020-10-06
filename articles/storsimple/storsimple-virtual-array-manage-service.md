---
title: Nasadit službu StorSimple Device Manager | Microsoft Docs
description: Vysvětluje, jak vytvořit a odstranit službu StorSimple Device Manager v Azure Portal a popisuje, jak spravovat registrační klíč služby.
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
ms.openlocfilehash: 29ea9300b2f86f442d95c96d4a5df3364628d866
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744130"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Nasazení služby StorSimple Device Manager pro virtuální pole StorSimple

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Služba StorSimple Device Manager běží v Microsoft Azure a připojuje se k více zařízením StorSimple. Po vytvoření služby ji můžete použít ke správě zařízení z portál Microsoft Azure spuštěné v prohlížeči. To vám umožní monitorovat všechna zařízení, která jsou připojená ke službě StorSimple Device Manager, z jediného centrálního umístění, což minimalizuje administrativní zatížení.

Běžné úlohy související se službou StorSimple Device Manager jsou:

* Vytvoření služby
* Odstranění služby
* Získání registračního klíče služby
* Znovu vygenerovat registrační klíč služby

V tomto kurzu se dozvíte, jak provést všechny předchozí úlohy. Informace obsažené v tomto článku se vztahují pouze na virtuální pole StorSimple. Další informace o řadě StorSimple 8000 najdete v [nasazení StorSimple Manager služby](storsimple-manage-service.md).

## <a name="create-a-service"></a>Vytvoření služby

K vytvoření služby potřebujete:

* Předplatné s smlouva Enterprise
* Účet úložiště Active Microsoft Azure
* Fakturační informace, které se používají pro správu přístupu

Při vytváření služby můžete také zvolit, že se má účet úložiště vygenerovat.

Jedna služba může spravovat více zařízení. Zařízení ale nemůže zahrnovat víc služeb. Velký podnik může mít více instancí služby pro práci s různými předplatnými, organizacemi nebo i umístěními nasazení.

> [!NOTE]
> Abyste mohli spravovat zařízení řady StorSimple 8000 a virtuální pole StorSimple, potřebujete samostatné instance služby StorSimple Device Manager.


Chcete-li vytvořit službu, proveďte následující kroky.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Odstranění služby

Před odstraněním služby se ujistěte, že ji nepoužívají žádná připojená zařízení. Pokud se služba používá, deaktivujte připojená zařízení. Operace deaktivace naruší spojení mezi zařízením a službou, ale zachová data zařízení v cloudu.

> [!IMPORTANT]
> Po odstranění služby nelze operaci vrátit zpět. Každé zařízení, které službu používá, bude nutné obnovit tovární nastavení, aby bylo možné ho použít s jinou službou. V tomto scénáři se ztratí místní data na zařízení i konfigurace.
 

Chcete-li odstranit službu, proveďte následující kroky.

#### <a name="to-delete-a-service"></a>Odstranění služby

1. Přejděte na **Všechny prostředky**. Vyhledejte službu StorSimple Device Manager. Vyberte službu, kterou chcete odstranit.
   
    ![Vyberte službu, kterou chcete odstranit.](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Pokud se chcete ujistit, že nejsou k této službě připojená žádná zařízení, přečtěte si řídicí panel služby. Pokud v této službě nejsou registrována žádná zařízení, zobrazí se také zpráva banner. Klikněte na **Odstranit**.
   
    ![Odstranit službu](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Po zobrazení výzvy k potvrzení klikněte v potvrzovacím oznámení na **Ano** . 
   
    ![Potvrdit odstranění služby](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Odstranění služby může trvat několik minut. Po úspěšném odstranění služby se zobrazí oznámení.
   
    ![Úspěšné odstranění služby](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Seznam služeb se aktualizuje.

 ![Aktualizovaný seznam služeb](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Získání registračního klíče služby
Po úspěšném vytvoření služby budete muset zaregistrovat zařízení StorSimple ve službě. K registraci prvního zařízení StorSimple budete potřebovat registrační klíč služby. K registraci dalších zařízení pomocí existující služby StorSimple budete potřebovat registrační klíč a šifrovací klíč dat služby (který se při registraci generuje na prvním zařízení). Další informace o šifrovacím klíči dat služby najdete v tématu [zabezpečení StorSimple](storsimple-security.md). Registrační klíč můžete získat tak, že získáte přístup k oknu **klíče** pro vaši službu.

K získání registračního klíče služby proveďte následující kroky.

#### <a name="to-get-the-service-registration-key"></a>Získání registračního klíče služby
1. V okně **StorSimple Device Manager** otevřete okno **klíče**pro **správu &gt; ** .
   
   ![Okno Klíče](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. V okně **klíče** se zobrazí registrační klíč služby. Zkopírujte registrační klíč pomocí ikony kopírování. 

Zachovejte registrační klíč služby v bezpečném umístění. K registraci dalších zařízení pomocí této služby budete potřebovat tento klíč a také šifrovací klíč dat služby. Po získání registračního klíče služby budete muset nakonfigurovat své zařízení prostřednictvím rozhraní Windows PowerShell pro StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Znovu vygenerovat registrační klíč služby
Registrační klíč služby bude nutné znovu vygenerovat, pokud je nutné provést střídání klíčů nebo pokud se změnil seznam správců služeb. Když znovu vygenerujete klíč, použije se nový klíč jenom k registraci následných zařízení. Zařízení, která jsou už zaregistrovaná, neovlivní tento proces.

K opětovnému vygenerování registračního klíče služby proveďte následující kroky.

#### <a name="to-regenerate-the-service-registration-key"></a>Opětovné vygenerování registračního klíče služby
1. V okně **StorSimple Device Manager** otevřete okno **klíče**pro **správu &gt; ** .
   
   ![Okno klíče](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. V okně **klíče** klikněte na **znovu vygenerovat**.
   
   ![Kliknout na znovu vygenerovat](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. V okně **znovu vygenerovat registrační klíč služby** si Projděte akci potřebnou při opětovném vygenerování klíčů. Všechna následující zařízení, která jsou zaregistrovaná v této službě, budou používat nový registrační klíč. Potvrďte kliknutím na **znovu vygenerovat** . Po dokončení registrace budete upozorněni.
   
   ![Potvrdit opětovné vygenerování klíče](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Zobrazí se nový registrační klíč služby.
   
    ![Další potvrdit opětovné vygenerování klíče](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Zkopírujte tento klíč a uložte ho pro registraci všech nových zařízení s touto službou.

## <a name="next-steps"></a>Další kroky
* Naučte se [, jak začít s StorSimple](storsimple-virtual-array-deploy1-portal-prep.md) virtuálním polem.
* Naučte se [spravovat zařízení StorSimple](storsimple-ova-web-ui-admin.md).

