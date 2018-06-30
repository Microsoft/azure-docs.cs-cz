---
title: Nové ověřování pro pole virtuální zařízení StorSimple | Microsoft Docs
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
ms.date: 06/28/2018
ms.author: alkohli
ms.openlocfilehash: e33a3f843017ec24f3a79701fac9a62e15b4f9ba
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109184"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Nové ověřování používejte pro vaše zařízení StorSimple

## <a name="overview"></a>Přehled

Service Manager zařízení StorSimple běží v Microsoft Azure a připojí k několik polí virtuální zařízení StorSimple. Aktuální, má použít službu StorSimple Manager zařízení k ověřování pro zařízení StorSimple služby Řízení přístupu (ACS). Mechanismus ACS bude brzy zastaralé a nahrazuje ověřování Azure Active Directory (AAD).

Informace obsažené v tomto článku se vztahuje na obou StorSimple 1200 řady virtuální pole pouze. Tento článek popisuje podrobnosti o ověřování AAD a přidružené nový registrační klíč služby a úpravy, aby se pravidla brány firewall se pro zařízení StorSimple.

V polích virtuální zařízení StorSimple (model 1200) dojde k ověření AAD verzi Update 1 nebo novější.

Z důvodu Úvod k ověřování AAD změny jsou prováděny v:

- Adresa URL vzory pro pravidla brány firewall.
- Registrační klíč služby.

Tyto změny jsou podrobněji v následujících částech.

## <a name="url-changes-for-aad-authentication"></a>Změny adresy URL pro ověřování AAD

Aby se zajistilo, že služba používá ověřování založené na AAD, musíte zahrnout všechny uživatele na nové adresy URL ověřování jejich pravidla brány firewall.

Pokud používáte pole virtuální zařízení StorSimple, zkontrolujte, zda je v pravidlech brány firewall patří následující adresu URL:

| Vzor adresy URL                         | Cloud | Komponenta nebo funkce         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Veřejný Azure |Služba ověřování v AAD      |
| `https://login.microsoftonline.us` | US Government |Služba ověřování v AAD      |

Pro úplný seznam URL vzory pro pole virtuální zařízení StorSimple, přejděte na [vzorů adresy URL pro pravidla brány firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Pokud adresa URL pro ověření není zahrnut do pravidel brány firewall nad rámec datum vyřazení, se uživatelům zobrazuje kritickou výstrahu, že jejich zařízení StorSimple nelze ověřit ve službě. Služba nebude moci komunikovat s zařízení. Pokud uživatelé zobrazit tuto výstrahu, musí obsahovat nové adresy URL ověřování. Další informace o výstraze, přejděte na [výstrahy můžete použít ke sledování zařízení StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Změny verze a ověřování zařízení

Pokud pomocí škály virtuálního zařízení StorSimple, použijte k určení akce, které je třeba provést podle verze softwaru zařízení, kterou používáte v následující tabulce.

| Pokud vaše zařízení používá  | Následující akce se provede                                    |
|----------------------------|--------------------------------------------------------------|
| Aktualizace 1.0 nebo novější a je v režimu offline. <br> Zobrazí výstrahu, že adresa URL není seznam povolených adres.| 1. Úprava pravidla brány firewall, která zahrnují adresy URL ověřování. V tématu [ověřování adresy URL](#url-changes-for-aad-authentication). <br> 2. [Získání registračního klíče AAD ze služby](#aad-based-registration-keys). <br> 3. Pomocí kroků 1 až 5 [připojit k rozhraní Windows PowerShell virtuální pole](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Použití `Invoke-HcsReRegister` rutiny registrace zařízení pomocí prostředí Windows PowerShell. Zadejte klíč, který jste získali v předchozím kroku.|
| Aktualizace 1.0 nebo novější a zařízení je online.| Není vyžadována žádná akce.                                       |
| Aktualizace 0,6 nebo starší a zařízení je offline. | 1. [Stáhnout aktualizace 1.0 prostřednictvím serveru katalogu](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Použít aktualizace 1.0 prostřednictvím místního webového uživatelského rozhraní](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Získání registračního klíče AAD ze služby](#aad-based-registration-keys). <br>4. Pomocí kroků 1 až 5 [připojit k rozhraní Windows PowerShell virtuální pole](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Použití `Invoke-HcsReRegister` rutiny registrace zařízení pomocí prostředí Windows PowerShell. Zadejte klíč, který jste získali v předchozím kroku.|
| Aktualizace 0,6 nebo starší a zařízení je online | Úprava pravidla brány firewall, která zahrnují adresy URL ověřování.<br> Instalace aktualizace 1.0 prostřednictvím portálu Azure. |

## <a name="aad-based-registration-keys"></a>Na základě AAD registrační kódy

Pro pole virtuální zařízení StorSimple, nové registrace na základě AAD, které se používají klíče od Update 1.0. Registrace služby StorSimple Manager zařízení u zařízení použijete registrační kódy.

Pokud používáte StorSimple virtuální pole 0,6 nebo starší verzi Update nelze použít nových klíčů registrace služby AAD. Budete muset znovu vygenerovat registrační klíč služby. Po opětovném vygenerování klíče, nový klíč slouží k registraci dalších zařízení. Starý klíč již není platný.

- Nový registrační klíč AAD vyprší po 3 dny.
- Pracovní klíče registrace AAD pouze s řady StorSimple 1200 virtuální maticových spuštěné Update 1 nebo novější. Registrační klíč AAD ze zařízení řady StorSimple 8000 nebude fungovat.
- U klíčů registrace AAD se delší než odpovídající klíče registrace služby ACS.

Proveďte následující kroky ke generování registrační klíč služby AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>K vygenerování registračního klíče služby AAD

1. V **Manager zařízení StorSimple**, přejděte na **správy &gt;**  **klíče**.
    
    ![Přejděte na klíče](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klikněte na tlačítko **vygenerovat klíč**.

    ![Klikněte na tlačítko znovu vygenerovat](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Zkopírujte nový klíč. Starší klíč již nefunguje.

    ![Zkontrolujte znovu vygenerovat](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak nasadit [pole virtuální zařízení StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
