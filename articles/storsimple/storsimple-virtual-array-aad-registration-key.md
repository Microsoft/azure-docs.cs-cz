---
title: Nové ověřování pro virtuální pole StorSimple | Dokumentace Microsoftu
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
ms.date: 06/28/2018
ms.author: alkohli
ms.openlocfilehash: 080f49ca1078858462264f229e9acfee6fad17d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61387614"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Použití nové ověřování pro StorSimple

## <a name="overview"></a>Přehled

Služba Správce zařízení StorSimple běží v Microsoft Azure a připojí k více virtuálních polí StorSimple. Do dnešního dne služby Správce zařízení StorSimple používá služby Access Control service (ACS) k ověření služby k zařízení StorSimple. Mechanismus služby ACS se brzy přestanou používat a nahrazují ověřování Azure Active Directory (AAD).

Informace obsažené v tomto článku se vztahuje na oba StorSimple 1200 Series virtuální pole určená pouze. Tento článek popisuje podrobnosti o ověřování AAD a přidružené nový registrační klíč služby a úpravy pravidel brány firewall se pro zařízení StorSimple.

Ověřování AAD probíhá virtuálních polí StorSimple (model 1200) s verzí Update 1 nebo novější.

Kvůli implementaci ověřování AAD změnách v:

- Vzory adres URL pro pravidla brány firewall.
- Registrační klíč služby.

Tyto změny jsou podrobně popsány v následující části.

## <a name="url-changes-for-aad-authentication"></a>Změny adresy URL pro ověřování AAD

Aby bylo zajištěno, že služba používá ověřování pomocí AAD, musí obsahovat všechny uživatele nové ověřovací adresy URL v jeho pravidlech brány firewall.

Pokud používáte StorSimple Virtual Array, ujistěte se, že následující adresa URL se vloží v pravidlech brány firewall:

| Vzor adresy URL                         | Cloud | Komponenta nebo funkce         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |Služba ověřování AAD      |
| `https://login.microsoftonline.us` | US Government |Služba ověřování AAD      |

Pro úplný seznam URL vzory pro virtuální pole StorSimple, přejděte na [vzory adres URL pro pravidla brány firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Pokud ověřovací adresa URL není zahrnut v pravidlech brány firewall po datu vyřazení, uvidí kritickou výstrahu, která se jejich zařízení StorSimple nemohlo ověřit ve službě. Služba nebude moci komunikovat se zařízením. Pokud se uživatelům zobrazuje toto upozornění, musí obsahovat nové adresy URL ověřování. Další informace o upozornění, přejděte na [výstrahy můžete použít ke sledování zařízení StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Změny v ověřování a verze zařízení

Pokud pomocí StorSimple Virtual Array, použijte k určení akce, které musíte provést na základě zařízení software verze, které jsou spuštěné v následující tabulce.

| Pokud vaše zařízení se systémem  | Proveďte následující akci                                    |
|----------------------------|--------------------------------------------------------------|
| S aktualizací Update 1.0 nebo novější a je v režimu offline. <br> Zobrazí upozornění, že adresa URL není na seznamu povolených.| 1. Upravte pravidla brány firewall zahrnout ověřovací adresa URL. Zobrazit [ověřovací adresy URL](#url-changes-for-aad-authentication). <br> 2. [Získání registračního klíče AAD ze služby](#aad-based-registration-keys). <br> 3. Pomocí kroků 1 až 5 [připojit k prostředí Windows PowerShell rozhraní virtuálního pole](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Použití `Invoke-HcsReRegister` rutiny k registraci zařízení pomocí Windows Powershellu. Zadejte klíč, který jste získali v předchozím kroku.|
| Update 1.0 nebo novější a zařízení je online.| Nevyžaduje se žádná akce.                                       |
| Aktualizace 0,6 nebo starší a zařízení je offline. | 1. [Stáhnout Update 1.0 prostřednictvím serveru katalogu](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Použít Update 1.0 prostřednictvím místního webového uživatelského rozhraní](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Získání registračního klíče AAD ze služby](#aad-based-registration-keys). <br>4. Pomocí kroků 1 až 5 [připojit k prostředí Windows PowerShell rozhraní virtuálního pole](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Použití `Invoke-HcsReRegister` rutiny k registraci zařízení pomocí Windows Powershellu. Zadejte klíč, který jste získali v předchozím kroku.|
| Aktualizace 0,6 nebo starší a zařízení je online | Upravte pravidla brány firewall zahrnout ověřovací adresa URL.<br> Instalace Update 1.0 na webu Azure portal. |

## <a name="aad-based-registration-keys"></a>Na základě AAD registrační kódy

Začátek Update 1.0 pro virtuální pole StorSimple, na základě AAD registrace nové klíče se používají. Pomocí kláves registrace k registraci služby Správce zařízení StorSimple se zařízením.

Nové klíče registrace služby AAD nelze použít, pokud používáte virtuálních polí StorSimple s aktualizací Update 0,6 nebo starší. Je potřeba znovu vygenerovat registrační klíč služby. Jakmile vygenerujete klíč, nový klíč se používá k registraci dalších zařízení. Starý klíč již není platný.

- Nový registrační klíč AAD platnost vyprší za 3 dny.
- AAD registrační klíče fungují jenom s StorSimple 1200 series virtuální pole spuštěné Update 1 nebo novější. Registrační klíč AAD ze zařízení řady StorSimple 8000 nebude fungovat.
- Registrační kódy AAD je delší než odpovídající registraci klíče služby ACS.

Proveďte následující kroky a vygenerujte registrační klíč AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>K vygenerování registračního klíče služby AAD

1. V **Správce zařízení StorSimple**, přejděte na stránku **správu &gt;**  **klíče**.
    
    ![Přejděte ke klíčům](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klikněte na tlačítko **vygenerovat klíč**.

    ![Klikněte na znovu vygenerovat klíč](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Zkopírujte nový klíč. Starší klíč přestane fungovat.

    ![Potvrdit obnovení](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak nasadit [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
