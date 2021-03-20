---
title: Změnit heslo správce zařízení StorSimple Virtual Array | Microsoft Docs
description: Popisuje, jak pomocí webového uživatelského rozhraní Azure Portal nebo StorSimple změnit heslo správce zařízení.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8bc8846d546faec194617ccb753cdbd105e16bf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85513623"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Změňte heslo správce zařízení StorSimple Virtual Array pomocí StorSimple Správce zařízení

## <a name="overview"></a>Přehled

Když k přístupu k virtuálnímu poli StorSimple použijete rozhraní Windows PowerShell, budete muset zadat heslo správce zařízení. Po prvním zřízení a spuštění zařízení StorSimple je výchozí heslo *Heslo1*. Pro zabezpečení dat vyprší výchozí heslo při prvním přihlášení a je nutné změnit toto heslo.

Můžete také použít místní webové uživatelské rozhraní nebo Azure Portal a kdykoli po nasazení zařízení do provozního prostředí změnit heslo správce zařízení. Jednotlivé postupy jsou popsány v tomto článku.

 ![okno zařízení](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Změna hesla pomocí Azure Portal

Pomocí následujících kroků můžete změnit heslo správce zařízení prostřednictvím Azure Portal.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Změna hesla správce zařízení pomocí Azure Portal

1. Na úvodní stránce služby vyberte svou službu, dvakrát klikněte na název služby a potom v části **Správa** klikněte na **zařízení**. Otevře se okno **zařízení** , ve kterém jsou uvedena všechna zařízení Virtual Array v StorSimple.

2. V okně **zařízení** poklikejte na zařízení, které vyžaduje změnu hesla.

3. V okně **Nastavení** zařízení klikněte na **zabezpečení**.

4. V okně **nastavení zabezpečení** proveďte následující akce:
   
   1. Přejděte dolů do části **heslo správce zařízení** . Zadejte heslo správce, které obsahuje 8 až 15 znaků.
   2. Potvrďte heslo.
   3. V horní části okna klikněte na **Uložit** .

Heslo správce zařízení je teď aktualizované. Toto upravené heslo můžete použít k místnímu přístupu k zařízení.

![Okno nastavení zabezpečení](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Změna hesla pomocí místního webového uživatelského rozhraní

Pomocí následujících kroků můžete změnit heslo správce zařízení prostřednictvím místního webového uživatelského rozhraní.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Změna hesla správce zařízení prostřednictvím místního webového uživatelského rozhraní

1. V místním webovém uživatelském rozhraní klikněte na možnost  >  **změny hesla** údržby pro vaše zařízení.
   
    ![změnit Heslo1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Zadejte **aktuální heslo**.
3. Zadejte **nové heslo**. Heslo musí mít délku alespoň 8 znaků. Musí obsahovat 3 ze 4 z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky.
   
    Všimněte si, že heslo nemůže být stejné jako posledních 24 hesel.
4. Znovu zadejte heslo, abyste ho potvrdili.
   
    ![změnit password2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. V dolní části stránky klikněte na **použít**. Nové heslo se teď použije. Pokud se změna hesla nezdařila, zobrazí se následující chyba:
   
    ![Chyba hesla](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Po úspěšné aktualizaci hesla budete upozorněni. Pak můžete toto upravené heslo použít k místnímu přístupu k zařízení.


## <a name="next-steps"></a>Další kroky
Naučte se [spravovat virtuální pole StorSimple](storsimple-ova-web-ui-admin.md).

