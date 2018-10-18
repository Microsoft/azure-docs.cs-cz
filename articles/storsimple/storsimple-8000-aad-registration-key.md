---
title: Použít nové ověřování pro službu Správce zařízení StorSimple 8000 v Azure | Dokumentace Microsoftu
description: Vysvětluje, jak používat ověřování na základě AAD pro vaši službu, generovat nový registrační klíč a provádět ruční registraci zařízení.
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
ms.openlocfilehash: b1ea195ab0b06c4ca0fab37fe7e5701229b34938
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387034"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Použití nové ověřování pro StorSimple

## <a name="overview"></a>Přehled

Služba Správce zařízení StorSimple běží v Microsoft Azure a připojí k více zařízení StorSimple. Do dnešního dne služby Správce zařízení StorSimple používá služby Access Control service (ACS) k ověření služby k zařízení StorSimple. Mechanismus služby ACS se brzy přestanou používat a nahrazují ověřování Azure Active Directory (AAD). Další informace přejděte na následující oznámení o vyřazení ACS a použití ověřování AAD.

- [Budoucnost Azure ACS je Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Připravované změny Microsoft Access Control Service](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Tento článek popisuje podrobnosti o ověřování AAD a přidružené nový registrační klíč služby a úpravy pravidel brány firewall se pro zařízení StorSimple. Informace obsažené v tomto článku se vztahuje na zařízeních StorSimple řady 8000 pouze.

Vyvolá se v zařízení StorSimple řady 8000 s aktualizací Update 5 nebo novější ověřování AAD. Kvůli implementaci ověřování AAD změnách v:

- Vzory adres URL pro pravidla brány firewall.
- Registrační klíč služby.

Tyto změny jsou podrobně popsány v následující části.

## <a name="url-changes-for-aad-authentication"></a>Změny adresy URL pro ověřování AAD

Aby bylo zajištěno, že služba používá ověřování pomocí AAD, musí obsahovat všechny uživatele nové ověřovací adresy URL v jeho pravidlech brány firewall.

Pokud používáte StorSimple řady 8000, ujistěte se, že následující adresa URL se vloží v pravidlech brány firewall:

| Vzor adresy URL                         | Cloud | Komponenta nebo funkce         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Veřejné Azure |Služba ověřování AAD      |
| `https://login.microsoftonline.us` | US Government |Služba ověřování AAD      |

Pro úplný seznam URL vzory pro řadu zařízení StorSimple 8000, přejděte na [vzory adres URL pro pravidla brány firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Pokud adresa URL pro ověřování nejsou zahrnuté v pravidlech brány firewall po datu vyřazení a zařízení se systémem Update 5, uživatelům se oznámení na adresu URL. Uživatelé třeba zahrnout nové adresy URL ověřování. Pokud zařízení používá verzi před Update 5, uživatelům zobrazí výstraha prezenčního signálu. V obou případech zařízení StorSimple se nemohou ověřovat se služba a služba není schopen komunikovat se zařízením.

## <a name="device-version-and-authentication-changes"></a>Změny v ověřování a verze zařízení

Pokud používáte zařízení řady StorSimple 8000, použijte k určení akce, které musíte provést na základě zařízení software verze, které jsou spuštěné v následující tabulce.

| Pokud vaše zařízení se systémem| Proveďte následující akci                                    |
|--------------------------|------------------------|--------------------|--------------------------------------------------------------|
| S aktualizací Update 5 nebo novější a zařízení je offline. <br> Zobrazí upozornění, že adresa URL není na seznamu povolených.|1. Upravte pravidla brány firewall zahrnout ověřovací adresa URL. Zobrazit [ověřovací adresy URL](#url-changes-for-aad-authentication).<br>2. [Získání registračního klíče AAD ze služby](#aad-based-registration-keys).<br>3. [Připojení k rozhraní prostředí Windows PowerShell zařízení StorSimple řady 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Použití `Redo-DeviceRegistration` rutiny k registraci zařízení pomocí Windows Powershellu. Zadejte klíč, který jste získali v předchozím kroku.|
| S aktualizací Update 5 nebo novější a je zařízení online.| Nemusíte nic dělat.                                       |
| S aktualizací Update 4 nebo dřívější a zařízení je offline. |1. Upravte pravidla brány firewall zahrnout ověřovací adresa URL.<br>2. [Stáhnout Update 5 prostřednictvím serveru katalogu](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Použít aktualizace 5 prostřednictvím metody opravy hotfix](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Získání registračního klíče AAD ze služby](#aad-based-registration-keys).<br>5. [Připojení k rozhraní prostředí Windows PowerShell zařízení StorSimple řady 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Použití `Redo-DeviceRegistration` rutiny k registraci zařízení pomocí Windows Powershellu. Zadejte klíč, který jste získali v předchozím kroku.|
| S aktualizací Update 4 nebo dřívější a zařízení je online. |Upravte pravidla brány firewall zahrnout ověřovací adresa URL.<br> Instalace aktualizace 5 prostřednictvím webu Azure portal.              |
| Obnovení továrního nastavení verze než Update 5.      |Na portálu se zobrazí na základě registrační klíč AAD starší softwaru spuštěného zařízení. Postupujte podle kroků v předchozím scénáři pro když je na zařízení spuštěný Update 4 nebo dřívější.              |

## <a name="aad-based-registration-keys"></a>Na základě AAD registrační kódy

Začátek Update 5 pro StorSimple 8000 series zařízení, na základě AAD registrace nové klíče se používají. Pomocí kláves registrace k registraci služby Správce zařízení StorSimple se zařízením.

Nové klíče registrace služby AAD nelze použít, pokud používáte zařízení řady StorSimple 8000 s aktualizací Update 4 nebo dřívější (včetně starší zařízení aktivuje nyní).
V tomto scénáři budete muset znovu vygenerovat registrační klíč služby. Jakmile vygenerujete klíč, nový klíč se používá k registraci dalších zařízení. Starý klíč již není platný.

- Nový registrační klíč AAD platnost vyprší za 3 dny.
- Klíče registrace AAD fungují jenom s zařízeních StorSimple řady 8000 s aktualizací 5 nebo novější.
- Registrační kódy AAD je delší než odpovídající registraci klíče služby ACS.

Proveďte následující kroky a vygenerujte registrační klíč AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>K vygenerování registračního klíče služby AAD

1. V **Správce zařízení StorSimple**, přejděte na stránku **správu &gt;**  **klíče**. Na panelu hledání můžete použít také pro hledání _klíče_.
    
2. Klikněte na tlačítko **vygenerovat klíč**.

    ![Klikněte na znovu vygenerovat klíč](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Zkopírujte nový klíč. Starší klíč přestane fungovat.

    ![Potvrdit obnovení](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Při vytváření cloudového zařízení StorSimple ve službě zaregistrované zařízení řady StorSimple 8000, negenerují registrační klíč, když probíhá vytváření. Počkejte vytvoření dokončí, a pak vygenerovat registrační klíč.

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak nasadit [zařízení StorSimple řady 8000](storsimple-8000-deployment-walkthrough-u2.md).

