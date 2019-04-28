---
title: Změna hesla správce zařízení StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje, jak můžete změnit heslo správce zařízení na portálu Azure nebo StorSimple Virtual Array webového uživatelského rozhraní.
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
ms.openlocfilehash: 5308badf439254062a8aefca1840eb21bc234ace
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580366"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Změna hesla správce zařízení StorSimple Virtual Array pomocí Správce zařízení StorSimple

## <a name="overview"></a>Přehled

Při použití rozhraní Windows PowerShell pro StorSimple Virtual Array přístup, musíte k zadání hesla správce zařízení. Když zařízení StorSimple je nejprve zřízený a spuštěna, je výchozí heslo *Heslo1*. Pro zabezpečení vašich dat vyprší platnost hesla výchozí při prvním přihlášení a je nutné toto heslo změnit.

Chcete-li změnit heslo správce zařízení kdykoli po nasazení zařízení v provozním prostředí můžete také použít místního webového uživatelského rozhraní nebo na webu Azure portal. Každá z těchto postupů je popsána v tomto článku.

 ![Okno zařízení](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Změna hesla pomocí webu Azure portal

Proveďte následující kroky ke změně hesla správce zařízení na webu Azure portal.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Chcete-li změnit heslo správce zařízení prostřednictvím webu Azure portal

1. Na úvodní stránce služby vyberte svoji službu, klikněte dvakrát na název služby a pak v rámci **správu** klikněte na tlačítko **zařízení**. Tím se otevře **zařízení** okno, které se zobrazí seznam všech zařízení StorSimple Virtual Array.

2. V **zařízení** okna, dvakrát klikněte na zařízení, která vyžaduje změnu hesla.

3. V **nastavení** okna pro vaše zařízení, klikněte na tlačítko **zabezpečení**.

4. V **nastavení zabezpečení** okno, postupujte takto:
   
   1. Přejděte dolů k položce **heslo správce zařízení** oddílu. Zadejte heslo správce, který obsahuje z 8 až 15 znaků.
   2. Potvrďte heslo.
   3. V horní části okna klikněte na **Uložit**.

Heslo správce zařízení je teď aktualizovaný. Můžete použít toto změněné heslo pro přístup k zařízení místně.

![Okno nastavení zabezpečení](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Pomocí místního webového uživatelského rozhraní můžete změnit heslo

Proveďte následující kroky ke změně hesla správce zařízení prostřednictvím místního webového uživatelského rozhraní.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Chcete-li změnit heslo správce zařízení prostřednictvím místního webového uživatelského rozhraní

1. V místním webovém uživatelském rozhraní, klikněte na tlačítko **údržby** > **změnu hesla** pro vaše zařízení.
   
    ![změnit Heslo1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Zadejte **aktuální heslo**.
3. Zadejte **nové heslo**. Heslo musí být aspoň 8 znaků dlouhá. Musí obsahovat 3 ze 4 z následujících možností: velká písmena, malá písmena, číselné a speciální znaky.
   
    Všimněte si, že vaše heslo nemůže být stejný jako poslední 24 hesla.
4. Znovu zadejte heslo, abyste ho potvrdili.
   
    ![změnit Heslo2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. V dolní části stránky klikněte na tlačítko **použít**. Nové heslo se teď použijí. Změna hesla není úspěšné, zobrazí následující chyba:
   
    ![Chyba hesla](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Po úspěšné aktualizaci hesla, budete upozorněni. Pak můžete toto změněné heslo pro přístup k zařízení místně.


## <a name="next-steps"></a>Další postup
Zjistěte, jak [spravovat StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

