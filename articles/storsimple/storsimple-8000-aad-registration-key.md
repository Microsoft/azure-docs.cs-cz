---
title: Ověřování Azure AD pro StorSimple 8000 v Správce zařízení
description: Vysvětluje, jak používat ověřování na základě AAD pro vaši službu, generovat nový registrační klíč a provádět ruční registraci zařízení.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b09d68e7859a787c05a2fc62294f081c4345ae08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99805036"
---
# <a name="use-azure-active-directory-ad-authentication-for-your-storsimple"></a>Použití ověřování pomocí Azure Active Directory (AD) pro StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled

Služba StorSimple Správce zařízení běží v Microsoft Azure a připojuje se k více zařízením StorSimple. Do data Služba StorSimple Správce zařízení používala službu Access Control (ACS) k ověření služby na zařízení StorSimple. Mechanismus ACS bude brzy zastaralý a nahrazený ověřováním Azure Active Directory (AAD). Další informace najdete v následujících oznámeních o vyřazení služby ACS a použití ověřování AAD.

- [Budoucnost služby Azure ACS je Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Nadcházející změny v Access Control Service Microsoftu](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Tento článek popisuje podrobné informace o ověřování AAD a přidruženém novém registračním klíči služby a úpravách pravidel brány firewall, které platí pro zařízení StorSimple. Informace obsažené v tomto článku se vztahují jenom na zařízení řady StorSimple 8000.

Ověřování AAD probíhá na zařízení řady StorSimple 8000 s aktualizací Update 5 nebo novější. V důsledku zavedení ověřování AAD dojde k změnám v:

- Vzory adres URL pro pravidla brány firewall
- Registrační klíč služby.

Tyto změny jsou podrobněji popsány v následujících částech.

## <a name="url-changes-for-aad-authentication"></a>Změny adresy URL pro ověřování AAD

Aby služba používala ověřování pomocí AAD, všichni uživatelé musí do pravidel brány firewall zahrnout nové adresy URL pro ověřování.

Pokud používáte řady StorSimple 8000, zajistěte, aby byla v pravidlech brány firewall zahrnutá následující adresa URL:

| Vzor adresy URL                         | Cloud | Součást/funkce         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Veřejný partnerský vztah Azure |Služba ověřování AAD      |
| `https://login.microsoftonline.us` | Vláda USA |Služba ověřování AAD      |

Úplný seznam vzorů adres URL pro zařízení StorSimple 8000 series najdete v části [vzory adres URL pro pravidla brány firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Pokud adresa URL pro ověření není zahrnutá v pravidlech brány firewall mimo datum vyřazení a na zařízení je spuštěná aktualizace 5, uživatelé uvidí upozornění na adresu URL. Uživatelé musí zadat adresu URL nového ověřování. Pokud je na zařízení spuštěná verze před aktualizací 5, uživatelé uvidí upozornění prezenčního signálu. V každém případě se zařízení StorSimple nemůže ověřit u služby a služba není schopná komunikovat se zařízením.

## <a name="device-version-and-authentication-changes"></a>Verze zařízení a změny ověřování

Pokud používáte zařízení řady StorSimple 8000, pomocí následující tabulky určete, jakou akci potřebujete provést na základě používané verze softwaru zařízení.

| Pokud je vaše zařízení spuštěné| Proveďte následující akci                                    |
|--------------------------|------------------------|
| Aktualizace 5 nebo novější a zařízení je offline. <br> Zobrazí se upozornění, že adresa URL není schválená.|1. Upravte pravidla brány firewall tak, aby zahrnovala adresu URL ověřování. Viz [adresy URL pro ověřování](#url-changes-for-aad-authentication).<br>2. [Získejte registrační klíč AAD ze služby](#aad-based-registration-keys).<br>3. [Připojte se k rozhraní Windows PowerShell zařízení řady StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. pomocí `Redo-DeviceRegistration` rutiny zaregistrujete zařízení přes Windows PowerShell. Zadejte klíč, který jste získali v předchozím kroku.|
| Aktualizujte 5 nebo novější a zařízení online.| Nevyžaduje se žádná akce.                                       |
| Aktualizace 4 nebo starší a zařízení je offline. |1. Upravte pravidla brány firewall tak, aby zahrnovala adresu URL ověřování.<br>2. [Stáhněte si Update 5 přes katalogový server](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [použijte aktualizaci Update 5 prostřednictvím metody hotfix](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Získejte registrační klíč AAD ze služby](#aad-based-registration-keys).<br>5. [Připojte se k rozhraní Windows PowerShell zařízení řady StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. pomocí `Redo-DeviceRegistration` rutiny zaregistrujete zařízení přes Windows PowerShell. Zadejte klíč, který jste získali v předchozím kroku.|
| Aktualizace 4 nebo starší a zařízení je online. |Upravte pravidla brány firewall tak, aby zahrnovala adresu URL ověřování.<br> Nainstalujte aktualizaci 5 prostřednictvím Azure Portal.              |
| Obnovení továrního nastavení na verzi před aktualizací Update 5.      |Portál zobrazuje registrační klíč založený na AAD, zatímco na zařízení běží starší software. V případě, že je na zařízení spuštěná aktualizace 4 nebo starší, postupujte podle kroků v předchozím scénáři.              |

## <a name="aad-based-registration-keys"></a>Registrační klíče založené na AAD

Počínaje aktualizací 5 pro zařízení řady StorSimple 8000 se použijí nové registrační klíče založené na AAD. Pomocí registračních klíčů zaregistrujete službu StorSimple Správce zařízení se zařízením.

Nové registrační klíče služby AAD nemůžete použít, pokud používáte zařízení řady StorSimple 8000 se systémem Update 4 nebo starším (zahrnuje aktuálně aktivované zařízení).
V tomto scénáři je nutné znovu vygenerovat registrační klíč služby. Po opětovném vygenerování klíče se nový klíč použije k registraci všech dalších zařízení. Starý klíč již není platný.

- Platnost nového registračního klíče AAD vyprší za 3 dny.
- Registrační klíče AAD fungují jenom se zařízeními řady StorSimple 8000 se systémem Update 5 nebo novějším.
- Registrační klíče AAD jsou delší než odpovídající registrační klíče služby ACS.

K vygenerování registračního klíče služby AAD proveďte následující kroky.

#### <a name="to-generate-the-aad-service-registration-key"></a>Vygenerování registračního klíče služby AAD

1. V **StorSimple Správce zařízení** přejít na **klíče** **pro &gt; správu** . K vyhledání _klíčů_ můžete použít také panel hledání.
    
2. Klikněte na **vygenerovat klíč**.

    ![Kliknout na znovu vygenerovat](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Zkopírujte nový klíč. Starší klíč už nebude fungovat.

    ![Potvrdit opětovné vygenerování](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Pokud vytváříte StorSimple Cloud Appliance služby zaregistrované na zařízení řady StorSimple 8000, nevytvářejte registrační klíč, zatímco probíhá vytváření. Počkejte, až se vytváření dokončí, a pak vygenerujte registrační klíč.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o tom, jak nasadit [zařízení řady StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md).
