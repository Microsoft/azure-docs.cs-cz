---
title: Ověřování Azure AD pro StorSimple 8000 ve Správci zařízení
description: Vysvětluje, jak používat ověřování na základě AAD pro vaši službu, generovat nový registrační klíč a provádět ruční registraci zařízení.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b16132c24d35ee2c9902fa2b21c44416d8376b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470900"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Použití nového ověřování pro váš StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled

Služba StorSimple Device Manager běží v Microsoft Azure a připojuje se k více zařízením StorSimple. K dnešnímu dni služba StorSimple Device Manager použila službu ACS (Access Control) k ověření služby zařízení StorSimple. Mechanismus Služby ACS bude brzy zastaralá a nahrazen azure active directory (AAD) ověřování. Další informace naleznete v následujících oznámeních o vyřazení a použití ověřování AAD.

- [Budoucnost azure acs je Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Nadcházející změny služby Microsoft Access Control Service](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Tento článek popisuje podrobnosti ověřování AAD a přidružené nové služby registrační klíč a změny pravidel brány firewall, jak se vztahuje na zařízení StorSimple. Informace obsažené v tomto článku platí pouze pro zařízení řady StorSimple 8000.

K ověřování AAD dochází v zařízení řady StorSimple 8000 se spuštěnou aktualizací 5 nebo novější. V důsledku zavedení ověřování AAD dochází ke změnám v:

- Vzory adres URL pro pravidla brány firewall.
- Registrační klíč služby.

Tyto změny jsou podrobně popsány v následujících částech.

## <a name="url-changes-for-aad-authentication"></a>Změny adresy URL pro ověřování AAD

Chcete-li zajistit, aby služba používá ověřování založené na službě AAD, musí všichni uživatelé zahrnout nové adresy URL ověřování do svých pravidel brány firewall.

Pokud používáte řadu StorSimple 8000, ujistěte se, že v pravidlech brány firewall je zahrnuta následující adresa URL:

| Vzor adresy URL                         | Cloud | Součást/funkce         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Veřejný partnerský vztah Azure |Služba ověřování AAD      |
| `https://login.microsoftonline.us` | Vláda USA |Služba ověřování AAD      |

Úplný seznam vzorů adres URL pro zařízení řady StorSimple 8000 naleznete v [části Vzory adres URL pro pravidla brány firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Pokud adresa URL ověřování není zahrnuta v pravidlech brány firewall po datu vyřazení a v zařízení je spuštěna aktualizace 5, zobrazí se uživatelům výstraha adresy URL. Uživatelé musí zahrnout novou adresu URL ověřování. Pokud zařízení používá verzi před aktualizací 5, zobrazí se uživatelům upozornění na prezenční signál. V každém případě zařízení StorSimple nelze ověřit se službou a služba není schopna komunikovat se zařízením.

## <a name="device-version-and-authentication-changes"></a>Změny verze zařízení a ověření

Pokud používáte zařízení řady StorSimple 8000, použijte následující tabulku k určení, jakou akci je třeba provést na základě verze softwaru zařízení, kterou používáte.

| Pokud je zařízení spuštěno| Provedení následující akce                                    |
|--------------------------|------------------------|
| Aktualizujte 5 nebo novější a zařízení je offline. <br> Zobrazí se výstraha, že adresa URL není na seznamu povolených.|1. Upravte pravidla brány firewall tak, aby zahrnovala ověřovací adresu URL. Viz [adresy URL ověřování](#url-changes-for-aad-authentication).<br>2. [Získejte registrační klíč AAD ze služby](#aad-based-registration-keys).<br>3. [Připojte se k rozhraní prostředí Windows PowerShell zařízení řady StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. `Redo-DeviceRegistration` Pomocí rutiny zaregistrujte zařízení prostřednictvím prostředí Windows PowerShell. Zadej klíč, který jste dostali v předchozím kroku.|
| Aktualizace 5 nebo novější a zařízení online.| Nevyžaduje se žádná akce.                                       |
| Aktualizujte 4 nebo starší a zařízení je offline. |1. Upravte pravidla brány firewall tak, aby zahrnovala ověřovací adresu URL.<br>2. [Stáhněte si aktualizaci 5 prostřednictvím katalogového serveru](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Použijte aktualizaci 5 metodou opravy hotfix](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Získejte registrační klíč AAD ze služby](#aad-based-registration-keys).<br>5. [Připojte se k rozhraní prostředí Windows PowerShell zařízení řady StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. `Redo-DeviceRegistration` Pomocí rutiny zaregistrujte zařízení prostřednictvím prostředí Windows PowerShell. Zadej klíč, který jste dostali v předchozím kroku.|
| Aktualizace 4 nebo starší a zařízení je online. |Upravte pravidla brány firewall tak, aby zahrnovala ověřovací adresu URL.<br> Nainstalujte aktualizaci 5 přes portál Azure.              |
| Tovární nastavení na verzi před aktualizací 5.      |Portál zobrazuje registrační klíč založený na AAD, zatímco zařízení používá starší software. Postupujte podle kroků v předchozím scénáři, kdy zařízení používá aktualizaci 4 nebo starší.              |

## <a name="aad-based-registration-keys"></a>Registrační klíče založené na aad

Začátek aktualizace 5 pro zařízení řady StorSimple 8000 se používají nové registrační klíče založené na AAD. Pomocí registračních klíčů zaregistrujete službu Správce zařízení StorSimple se zařízením.

Nové registrační klíče služby AAD nelze použít, pokud používáte zařízení řady StorSimple 8000 se spuštěnou aktualizací 4 nebo starší (zahrnuje nyní aktivované starší zařízení).
V tomto scénáři je třeba znovu vygenerovat klíč registrace služby. Po obnovení klíče se nový klíč použije pro registraci všech následujících zařízení. Starý klíč již není platný.

- Platnost nového registračního klíče AAD vyprší po 3 dnech.
- Registrační klíče AAD fungují pouze se zařízeními řady StorSimple 8000 se spuštěnou aktualizací 5 nebo novější.
- Registrační klíče AAD jsou delší než odpovídající registrační klíče ACS.

Proveďte následující kroky ke generování registračního klíče služby AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Generování registračního klíče služby AAD

1. Ve **Správci zařízení StorSimple**přejděte na **klíči pro &gt; správu** . **Keys** Pomocí vyhledávacího panelu můžete také vyhledat _klávesy_.
    
2. Klepněte na **tlačítko Generovat klíč**.

    ![Klikněte na regenerovat](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Zkopírujte nový klíč. Starší klíč již nebude fungovat.

    ![Potvrdit regenerovat](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Pokud vytváříte StorSimple Cloud Appliance na službu registrovanou na zařízení řady StorSimple 8000, negenerujte registrační klíč, zatímco probíhá vytváření. Počkejte na dokončení vytvoření a vygenerujte registrační klíč.

## <a name="next-steps"></a>Další kroky

* Další informace o nasazení [zařízení řady StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md).

