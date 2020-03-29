---
title: Nové ověřování pro virtuální pole StorSimple
description: Vysvětluje, jak používat ověřování na základě AAD pro vaši službu, generovat nový registrační klíč a provádět ruční registraci zařízení.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 89f367e866c1a794f4359c76b8b8a8a9cfefd50d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273805"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Použití nového ověřování pro váš StorSimple

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Služba StorSimple Device Manager běží v Microsoft Azure a připojuje se k několika virtuálním polím StorSimple. K dnešnímu dni služba StorSimple Device Manager použila službu ACS (Access Control) k ověření služby zařízení StorSimple. Mechanismus Služby ACS bude brzy zastaralá a nahrazen azure active directory (AAD) ověřování.

Informace obsažené v tomto článku platí pouze pro virtuální pole řady StorSimple 1200. Tento článek popisuje podrobnosti ověřování AAD a přidružené nové služby registrační klíč a změny pravidel brány firewall, jak se vztahuje na zařízení StorSimple.

K ověřování AAD dochází ve virtuálních polích StorSimple (model 1200) se spuštěnou aktualizací 1 nebo novější.

V důsledku zavedení ověřování AAD dochází ke změnám v:

- Vzory adres URL pro pravidla brány firewall.
- Registrační klíč služby.

Tyto změny jsou podrobně popsány v následujících částech.

## <a name="url-changes-for-aad-authentication"></a>Změny adresy URL pro ověřování AAD

Chcete-li zajistit, aby služba používá ověřování založené na službě AAD, musí všichni uživatelé zahrnout nové adresy URL ověřování do svých pravidel brány firewall.

Pokud používáte storsimple virtual array, ujistěte se, že následující adresa URL je zahrnuta v pravidlech brány firewall:

| Vzor adresy URL                         | Cloud | Součást/funkce         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Veřejný partnerský vztah Azure |Služba ověřování AAD      |
| `https://login.microsoftonline.us` | Vláda USA |Služba ověřování AAD      |

Úplný seznam vzorů adres URL pro virtuální pole StorSimple naleznete v [části Vzory adres URL pro pravidla brány firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Pokud adresa URL ověřování není zahrnuta v pravidlech brány firewall po datu vyřazení, uživatelé uvidí kritickou výstrahu, že jejich zařízení StorSimple nelze ověřit pomocí služby. Služba nebude moci se zařízením komunikovat. Pokud se uživatelům zobrazí tato výstraha, musí zahrnout novou adresu URL ověřování. Další informace o výstraze naleznete v části [Použití výstrah ke sledování zařízení StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Změny verze zařízení a ověření

Pokud používáte virtuální pole StorSimple, použijte následující tabulku k určení, jakou akci je třeba provést na základě verze softwaru zařízení, kterou používáte.

| Pokud je zařízení spuštěno  | Provedení následující akce                                    |
|----------------------------|--------------------------------------------------------------|
| Aktualizace 1.0 nebo novější a je offline. <br> Zobrazí se výstraha, že adresa URL není na seznamu povolených.| 1. Upravte pravidla brány firewall tak, aby zahrnovala ověřovací adresu URL. Viz [adresy URL ověřování](#url-changes-for-aad-authentication). <br> 2. [Získejte registrační klíč AAD ze služby](#aad-based-registration-keys). <br> 3. Proveďte kroky 1-5 pro [připojení k rozhraní prostředí Windows PowerShell virtuálního pole](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. `Invoke-HcsReRegister` Pomocí rutiny zaregistrujte zařízení prostřednictvím prostředí Windows PowerShell. Zadej klíč, který jste dostali v předchozím kroku.|
| Aktualizace 1.0 nebo novější a zařízení je online.| Nevyžaduje se žádná akce.                                       |
| Aktualizujte 0.6 nebo starší a zařízení je offline. | 1. [Stáhněte si aktualizaci 1.0 prostřednictvím katalogového serveru](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Použijte aktualizaci 1.0 prostřednictvím místního webového uživatelského uživatelského prostředí](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Získejte registrační klíč AAD ze služby](#aad-based-registration-keys). <br>4. Proveďte kroky 1-5 pro [připojení k rozhraní prostředí Windows PowerShell virtuálního pole](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. `Invoke-HcsReRegister` Pomocí rutiny zaregistrujte zařízení prostřednictvím prostředí Windows PowerShell. Zadej klíč, který jste dostali v předchozím kroku.|
| Aktualizace 0.6 nebo starší a zařízení je online | Upravte pravidla brány firewall tak, aby zahrnovala ověřovací adresu URL.<br> Nainstalujte aktualizaci 1.0 přes portál Azure. |

## <a name="aad-based-registration-keys"></a>Registrační klíče založené na aad

Začátek aktualizace 1.0 pro StorSimple Virtual Arrays, nové registrační klíče založené na AAD se používají. Pomocí registračních klíčů zaregistrujete službu Správce zařízení StorSimple se zařízením.

Nové registrační klíče služby AAD nelze použít, pokud používáte virtuální pole StorSimple se spuštěnou aktualizací 0.6 nebo starší. Je třeba znovu vygenerovat registrační klíč služby. Po obnovení klíče se nový klíč použije pro registraci všech následujících zařízení. Starý klíč již není platný.

- Platnost nového registračního klíče AAD vyprší po 3 dnech.
- Registrační klíče AAD fungují pouze s virtuálními poli řady StorSimple 1200, ve kterých je spuštěna aktualizace 1 nebo novější. Registrační klíč AAD ze zařízení řady StorSimple 8000 nebude fungovat.
- Registrační klíče AAD jsou delší než odpovídající registrační klíče ACS.

Proveďte následující kroky ke generování registračního klíče služby AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Generování registračního klíče služby AAD

1. Ve **Správci zařízení StorSimple**přejděte na **klíči pro &gt; správu** . **Keys**
    
    ![Přejít na klávesy](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klepněte na **tlačítko Generovat klíč**.

    ![Klikněte na regenerovat](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Zkopírujte nový klíč. Starší klíč již nefunguje.

    ![Potvrdit regenerovat](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Další kroky

* Další informace o nasazení [virtuálního pole StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
