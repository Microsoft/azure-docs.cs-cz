---
title: Použít nové ověřování pro službu Správce zařízení StorSimple 8000 v Azure | Microsoft Docs
description: Vysvětluje, jak používat ověřování AAD na základě služby, vygenerujte nový registrační klíč a proveďte ruční registraci zařízení.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 37f44538d94ed78509bbcb09e726dc34a9e92e95
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28030944"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Nové ověřování používejte pro vaše zařízení StorSimple

## <a name="overview"></a>Přehled

Služba Správce zařízení StorSimple běží v Microsoft Azure a připojí k více zařízení StorSimple. Aktuální, má použít službu StorSimple Manager zařízení k ověřování pro zařízení StorSimple služby Řízení přístupu (ACS). Mechanismus ACS bude brzy zastaralé a nahrazuje ověřování Azure Active Directory (AAD). Další informace přejděte na následující hlášení pro vyřazení služby ACS a použití ověřování AAD.

- [Budoucí Azure ACS je Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Nadcházející změny službě Řízení přístupu Microsoft](https://azure.microsoft.com/en-in/blog/acs-access-control-service-namespace-creation-restriction/)

Tento článek popisuje podrobnosti o ověřování AAD a přidružené nový registrační klíč služby a úpravy, aby se pravidla brány firewall se pro zařízení StorSimple. Informace obsažené v tomto článku se vztahuje na řadu zařízení StorSimple 8000 jenom.

Dojde k ověření AAD v zařízení řady StorSimple 8000 verzi Update 5 nebo novější. Z důvodu Úvod k ověřování AAD změny jsou prováděny v:

- Adresa URL vzory pro pravidla brány firewall.
- Registrační klíč služby.

Tyto změny jsou podrobněji v následujících částech.

## <a name="url-changes-for-aad-authentication"></a>Změny adresy URL pro ověřování AAD

Aby se zajistilo, že služba používá ověřování založené na AAD, musíte zahrnout všechny uživatele na nové adresy URL ověřování jejich pravidla brány firewall.

Pokud pomocí řady StorSimple 8000, ověřte, že následující adresy URL je součástí pravidla brány firewall:

| Vzor adresy URL                         | Cloud | Komponenta nebo funkce         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Veřejný Azure |Služba ověřování v AAD      |
| `https://login.microsoftonline.us` | US Government |Služba ověřování v AAD      |

Pro úplný seznam URL vzory pro řadu zařízení StorSimple 8000, přejděte na [vzorů adresy URL pro pravidla brány firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Pokud adresa URL pro ověření není zahrnutý v pravidlech brány firewall nad rámec datum vyřazení a je na zařízení spuštěný aktualizací 5, se uživatelům zobrazuje upozornění na adresu URL. Uživatelé musí obsahovat nové adresy URL ověřování. Pokud zařízení používá verzi před aktualizací 5, se uživatelům zobrazuje výstraha prezenčního signálu. V každém případě zařízení StorSimple se nemohou ověřovat se služba a služba není schopen komunikovat se zařízením.

## <a name="device-version-and-authentication-changes"></a>Změny verze a ověřování zařízení

Pokud používáte zařízení řady StorSimple 8000, používejte k určení akce, které je třeba provést podle verze softwaru zařízení, kterou používáte v následující tabulce.

| Pokud vaše zařízení používá| Následující akce se provede                                    |
|--------------------------|------------------------|--------------------|--------------------------------------------------------------|
| Aktualizace 5 nebo novější a zařízení je offline. <br> Zobrazí výstrahu, že adresa URL není povolený.| Úprava pravidla brány firewall, která zahrnují adresy URL ověřování.<br> V tématu [ověřování adresy URL](#url-changes-for-aad-authentication). |
| Aktualizace 5 nebo novější a zařízení online.| Není vyžadována žádná akce.                                       |
| S aktualizací Update 4 nebo starší a zařízení je offline. | Úprava pravidla brány firewall, která zahrnují adresy URL ověřování.<br>[Stažení aktualizací 5 prostřednictvím serveru katalogu](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>[Použít aktualizace 5 prostřednictvím metody opravu hotfix](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix). <br> [Získání registračního klíče AAD ze služby](#aad-based-registration-keys). <br> [Připojit k rozhraní Windows PowerShell zařízení řady StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>Použití `Redo-DeviceRegistration` rutiny registrace zařízení pomocí prostředí Windows PowerShell. Zadejte klíč, který jste získali v předchozím kroku.|
| S aktualizací Update 4 nebo starší a zařízení je online. |Úprava pravidla brány firewall, která zahrnují adresy URL ověřování.<br> Nainstalujte aktualizace 5 prostřednictvím portálu Azure.              |
| Tovární nastavení na verzi před aktualizací 5.      |Portál zobrazuje založené na registrační klíč AAD, zatímco zařízení se systémem starším softwarem. Postupujte podle kroků v předchozím scénáři pro když je na zařízení spuštěný Update 4 nebo dřívější.              |

## <a name="aad-based-registration-keys"></a>Na základě AAD registrační kódy

Zahájení aktualizace 5 pro řadu zařízení StorSimple 8000 nové registrace na základě AAD, které se používají klíče. Registrace služby StorSimple Manager zařízení u zařízení použijete registrační kódy.

Nových klíčů registrace služby AAD nelze použít, pokud používáte zařízení řady StorSimple 8000 s aktualizací 4 nebo starší (včetně zařízení s starší aktivované nyní).
V tomto scénáři budete muset znovu vygenerovat registrační klíč služby. Po opětovném vygenerování klíče, nový klíč slouží k registraci dalších zařízení. Starý klíč již není platný.

- Nový registrační klíč AAD vyprší po 3 dny.
- Klíče registrace AAD pracovní pouze s řadu zařízení StorSimple 8000 s aktualizací 5 nebo novější.
- U klíčů registrace AAD se delší než odpovídající klíče registrace služby ACS.

Proveďte následující kroky ke generování registrační klíč služby AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>K vygenerování registračního klíče služby AAD

1. V **Manager zařízení StorSimple**, přejděte na **správy &gt;**  **klíče**. Panelu Hledat můžete také použít k vyhledání _klíče_.
    
2. Klikněte na tlačítko **vygenerovat klíč**.

    ![Klikněte na tlačítko znovu vygenerovat](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Zkopírujte nový klíč. Starší klíč přestane fungovat.

    ![Zkontrolujte znovu vygenerovat](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Pokud vytváříte o cloudu zařízení StorSimple ve službě zaregistrovat do vašeho zařízení řady StorSimple 8000, negenerují registrační klíč, když probíhá vytvoření. Počkejte, než pro vytvoření k dokončení a potom vygenerujte registrační klíč.

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak nasadit [zařízení řady StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md).

