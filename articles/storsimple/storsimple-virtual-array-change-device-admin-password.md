---
title: Heslo správce zařízení změnu pole virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje, jak změnit heslo správce zařízení pomocí portálu Azure nebo pole virtuální zařízení StorSimple webového uživatelského rozhraní.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 260a23003d705e6598da8c51bb5a96f2539a0014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875568"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Změna hesla správce zařízení pole virtuální zařízení StorSimple pomocí Správce zařízení StorSimple

## <a name="overview"></a>Přehled

Pokud použijete rozhraní Windows PowerShell pro přístup k poli virtuální zařízení StorSimple, musíte k zadání hesla správce zařízení. Když zařízení StorSimple je nejdřív zřízený a spuštěna, je výchozí heslo *Heslo1*. Pro zabezpečení vašich dat výchozí heslo vyprší platnost při prvním přihlášení a je nutné toto heslo změnit.

Změna hesla správce zařízení kdykoli po nasazení zařízení v provozním prostředí můžete také použít místní webového uživatelského rozhraní nebo portálu Azure. Každá z těchto postupů je popsána v tomto článku.

 ![Okno zařízení](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Použijte portál Azure. Chcete-li změnit heslo

Proveďte následující kroky, chcete-li změnit heslo správce zařízení prostřednictvím portálu Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Chcete-li změnit heslo správce zařízení prostřednictvím portálu Azure

1. Na stránce cílové služby, vyberte svoji službu, dvakrát klikněte na název služby a pak v rámci **správy** klikněte na tlačítko **zařízení**. Tím se otevře **zařízení** okno, které obsahuje seznamy všech zařízení StorSimple virtuální pole.

2. V **zařízení** okno, dvakrát klikněte na zařízení, která vyžaduje změnu hesla.

3. V **nastavení** pro zařízení, klikněte na **zabezpečení**.

4. V **nastavení zabezpečení** okno, postupujte takto:
   
   1. Přejděte dolů k položce **heslo správce zařízení** části. Zadejte heslo správce, který obsahuje 8 až 15 znaků.
   2. Potvrzení hesla.
   3. Klikněte na tlačítko **Uložit** v horní části okna.

Heslo správce zařízení se nyní aktualizuje. Toto upravené heslo slouží k přístupu k zařízení místně.

![Okno nastavení zabezpečení](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Chcete-li změnit heslo použít místní webového uživatelského rozhraní

Proveďte následující kroky, chcete-li změnit heslo správce zařízení prostřednictvím místního webového uživatelského rozhraní.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Chcete-li změnit heslo správce zařízení prostřednictvím místního webového uživatelského rozhraní

1. V místní webového uživatelského rozhraní, klikněte na **údržby** > **Změna hesla** pro vaše zařízení.
   
    ![změnit Heslo1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Zadejte **aktuální heslo**.
3. Zadejte **nové heslo**. Heslo musí být dlouhé alespoň 8 znaků. Musí obsahovat 3 z následujících 4: velká písmena, malá písmena, číselné a speciální znaky.
   
    Všimněte si, že vaše heslo nemůže být stejný jako poslední 24 hesla.
4. Zadejte znovu heslo k potvrzení této akce.
   
    ![změnit Heslo2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. V dolní části stránky klikněte na tlačítko **použít**. Nové heslo se teď použijí. Pokud změna hesla se nepovedlo úspěšně dokončit, zobrazí se následující chyba:
   
    ![Chyba hesla](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Po úspěšné aktualizaci heslo, budete upozorněni. Pak můžete toto upravené heslo pro přístup k zařízení místně.


## <a name="next-steps"></a>Další kroky
Zjistěte, jak [spravovat vaše pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).

