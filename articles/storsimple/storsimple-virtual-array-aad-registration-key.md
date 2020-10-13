---
title: Nové ověřování pro virtuální pole StorSimple
description: Přečtěte si o ověřování AAD, přidruženém novém registračním klíči služby a změnách pravidel brány firewall, které platí pro zařízení StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: d750b3a85741bb4761b640848f01e5917760b5aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91261086"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Použití nového ověřování pro StorSimple

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Služba StorSimple Device Manager běží v Microsoft Azure a připojuje se k několika virtuálním polím StorSimple. Do data Služba StorSimple Device Manager používala službu Access Control (ACS) k ověření služby na zařízení StorSimple. Mechanismus ACS bude brzy zastaralý a nahrazený ověřováním Azure Active Directory (AAD).

Informace obsažené v tomto článku se vztahují jenom na virtuální pole řady StorSimple 1200. Tento článek popisuje podrobné informace o ověřování AAD a přidruženém novém registračním klíči služby a úpravách pravidel brány firewall, které platí pro zařízení StorSimple.

Ověřování AAD probíhá ve virtuálních polích StorSimple (model 1200) s aktualizací Update 1 nebo novější.

V důsledku zavedení ověřování AAD dojde k změnám v:

- Vzory adres URL pro pravidla brány firewall
- Registrační klíč služby.

Tyto změny jsou podrobněji popsány v následujících částech.

## <a name="url-changes-for-aad-authentication"></a>Změny adresy URL pro ověřování AAD

Aby služba používala ověřování pomocí AAD, všichni uživatelé musí do pravidel brány firewall zahrnout nové adresy URL pro ověřování.

Pokud používáte virtuální pole StorSimple, ujistěte se, že jsou v pravidlech brány firewall zahrnutá následující adresa URL:

| Vzor adresy URL                         | Cloud | Součást/funkce         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Veřejný partnerský vztah Azure |Služba ověřování AAD      |
| `https://login.microsoftonline.us` | Vláda USA |Služba ověřování AAD      |

Úplný seznam vzorů adres URL pro virtuální pole StorSimple naleznete v části [vzory adres URL pro pravidla brány firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Pokud adresa URL pro ověření není zahrnuta v pravidlech brány firewall mimo datum vyřazení, uživatelé uvidí kritickou výstrahu, že jejich zařízení StorSimple nebylo možné ověřit pomocí služby. Služba nebude schopna komunikovat se zařízením. Pokud se uživatelům zobrazí tato výstraha, musí zahrnovat novou adresu URL pro ověření. Další informace o výstraze najdete v části [použití výstrah pro monitorování zařízení StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Verze zařízení a změny ověřování

Pokud používáte virtuální pole StorSimple, pomocí následující tabulky určete, jakou akci potřebujete provést na základě používané verze softwaru zařízení.

| Pokud je vaše zařízení spuštěné  | Proveďte následující akci                                    |
|----------------------------|--------------------------------------------------------------|
| Aktualizace 1,0 nebo novější a je offline. <br> Zobrazí se upozornění, že adresa URL není povolená.| 1. Upravte pravidla brány firewall tak, aby zahrnovala adresu URL ověřování. Viz [adresy URL pro ověřování](#url-changes-for-aad-authentication). <br> 2. [Získejte registrační klíč AAD ze služby](#aad-based-registration-keys). <br> 3. proveďte kroky 1-5 pro [připojení k rozhraní Windows PowerShell virtuálního pole](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. pomocí `Invoke-HcsReRegister` rutiny zaregistrujete zařízení přes Windows PowerShell. Zadejte klíč, který jste získali v předchozím kroku.|
| Aktualizace 1,0 nebo novější a zařízení je online.| Nevyžaduje se žádná akce.                                       |
| Aktualizace 0,6 nebo starší verze a zařízení je offline. | 1. [Stáhněte aktualizaci 1,0 prostřednictvím katalogu serveru](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [použijte aktualizaci 1,0 prostřednictvím místního webového uživatelského rozhraní](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Získejte registrační klíč AAD ze služby](#aad-based-registration-keys). <br>4. proveďte kroky 1-5 pro [připojení k rozhraní Windows PowerShell virtuálního pole](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. pomocí `Invoke-HcsReRegister` rutiny zaregistrujete zařízení přes Windows PowerShell. Zadejte klíč, který jste získali v předchozím kroku.|
| Aktualizace 0,6 nebo starší verze a zařízení je online | Upravte pravidla brány firewall tak, aby zahrnovala adresu URL ověřování.<br> Nainstalujte aktualizaci 1,0 prostřednictvím Azure Portal. |

## <a name="aad-based-registration-keys"></a>Registrační klíče založené na AAD

Počínaje aktualizací 1,0 pro virtuální pole StorSimple se použijí nové registrační klíče založené na AAD. Pomocí registračních klíčů zaregistrujete službu StorSimple Device Manager se zařízením.

Pokud používáte virtuální pole StorSimple se systémem Update 0,6 nebo starším, nemůžete použít nové registrační klíče služby AAD. Je nutné znovu vygenerovat registrační klíč služby. Po opětovném vygenerování klíče se nový klíč použije k registraci všech dalších zařízení. Starý klíč již není platný.

- Platnost nového registračního klíče AAD vyprší za 3 dny.
- Registrační klíče AAD fungují jenom s virtuálními poli řady StorSimple 1200, na kterých běží aktualizace 1 nebo novější. Registrační klíč AAD ze zařízení řady StorSimple 8000 nebude fungovat.
- Registrační klíče AAD jsou delší než odpovídající registrační klíče služby ACS.

K vygenerování registračního klíče služby AAD proveďte následující kroky.

#### <a name="to-generate-the-aad-service-registration-key"></a>Vygenerování registračního klíče služby AAD

1. V **StorSimple Device Manager**přejít na **klíče** **pro &gt; správu** .
    
    ![Přejít k klíčům](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klikněte na **vygenerovat klíč**.

    ![Kliknout na znovu vygenerovat](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Zkopírujte nový klíč. Starší klíč už nefunguje.

    ![Potvrdit opětovné vygenerování](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak nasadit [virtuální pole StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
