---
title: Změnit heslo správce zařízení StorSimple Virtual Array | Dokumenty společnosti Microsoft
description: Popisuje, jak změnit heslo správce zařízení pomocí webového uživatelského rozhraní Azure Portal nebo StorSimple Virtual Array.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580366"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Změna hesla správce zařízení StorSimple Virtual Array pomocí Správce zařízení StorSimple

## <a name="overview"></a>Přehled

Při použití rozhraní prostředí Windows PowerShell pro přístup k virtuálnímu poli StorSimple je nutné zadat heslo správce zařízení. Když je zařízení StorSimple poprvé zřízeno a spuštěno, výchozí heslo je *Password1*. Z důvodu zabezpečení dat vyprší platnost výchozího hesla při prvním přihlášení a je nutné toto heslo změnit.

Místní webové uživatelské rozhraní nebo portál Azure můžete také použít ke změně hesla správce zařízení kdykoli po nasazení zařízení ve vašem produkčním prostředí. Každý z těchto postupů je popsán v tomto článku.

 ![čepel zařízení](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Změna hesla pomocí portálu Azure

Pomocí následujících kroků můžete změnit heslo správce zařízení prostřednictvím portálu Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Změna hesla správce zařízení prostřednictvím webu Azure Portal

1. Na vstupní stránce služby vyberte službu, poklikejte na název služby a v části **Správa** klikněte na **Položku Zařízení**. Tím se otevře okno **Devices,** ve které jsou uvedena všechna vaše zařízení StorSimple Virtual Array.

2. V okně **Zařízení** poklikejte na zařízení, které vyžaduje změnu hesla.

3. V okně **Nastavení** zařízení klepněte na tlačítko **Zabezpečení**.

4. V okně **Nastavení zabezpečení** postupujte takto:
   
   1. Přejděte dolů do části **Heslo správce zařízení.** Zadejte heslo správce, které obsahuje 8 až 15 znaků.
   2. Potvrďte heslo.
   3. V horní části okna klikněte na **Uložit.**

Heslo správce zařízení je nyní aktualizováno. Toto upravené heslo můžete použít pro místní přístup k zařízení.

![Okno nastavení zabezpečení](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Změna hesla pomocí místního webového uživatelského uživatelského prostředí

Pomocí následujících kroků můžete změnit heslo správce zařízení prostřednictvím místního webového uživatelského rozhraní.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Změna hesla správce zařízení prostřednictvím místního webového uživatelského rozhraní

1. V místním webovém uživatelském rozhraní klikněte na Změna hesla **údržby** > **Password change** pro vaše zařízení.
   
    ![změna hesla1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Zadejte **aktuální heslo**.
3. Zadejte **nové heslo**. Heslo musí mít tloušat alespoň 8 znaků. Musí obsahovat 3 ze 4 z následujících: velká, malá, číselná a speciální znaky.
   
    Všimněte si, že vaše heslo nemůže být stejné jako posledních 24 hesel.
4. Znovu zadejte heslo, abyste ho potvrdili.
   
    ![změna hesla2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. V dolní části stránky klikněte na **Použít**. Nyní je použito nové heslo. Pokud změna hesla není úspěšná, zobrazí se následující chyba:
   
    ![chyba hesla](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Po úspěšné aktualizaci hesla budete upozorněni. Toto upravené heslo pak můžete použít pro místní přístup k zařízení.


## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [spravovat virtuální pole StorSimple](storsimple-ova-web-ui-admin.md).

