---
title: Zpráva k vydání verze pro StorSimple 8000 Series Update 5
description: Popisuje nové funkce, problémy a alternativní řešení pro StorSimple 8000 Series Update 5.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021040"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Zpráva k vydání verze pro StorSimple 8000 Series Update 5

## <a name="overview"></a>Přehled

Následující poznámky k verzi popisují nové funkce a identifikují důležité otevřené problémy pro StorSimple 8000 Series Update 5. Obsahují také seznam StorSimple aktualizací softwaru, které jsou součástí této verze.

Aktualizaci 5 lze použít pro všechna zařízení StorSimple s aktualizací 0,1 až Update 4. Verze zařízení, která je přidružená k Update 5, je 6.3.9600.17845.

Před nasazením aktualizace v řešení StorSimple si projděte informace obsažené v poznámkách k verzi.

> [!IMPORTANT]
> * Aktualizace 5 je povinná aktualizace a je nutné ji nainstalovat hned. Další informace najdete v tématu Postup [použití aktualizace Update 5](storsimple-8000-install-update-5.md).
> * Aktualizace 5 má software zařízení, firmware disku, zabezpečení operačního systému a další aktualizace operačního systému. Instalace této aktualizace trvá přibližně 4 hodiny. Aktualizace firmwaru disku je nevratná aktualizace a vede k výpadkům vašeho zařízení. Doporučujeme, abyste použili aktualizaci 5, aby vaše zařízení zůstalo v aktuálním stavu.
> * Pro nové verze se aktualizace nemusí okamžitě zobrazovat, protože provádíme dvoufázové zavedení aktualizací. Počkejte několik dní a pak znovu vyhledejte aktualizace, protože tyto aktualizace budou brzy k dispozici.

## <a name="whats-new-in-update-5"></a>Co je nového v Update 5

V Update 5 byly provedeny následující hlavní vylepšení a opravy chyb.

* **Použití Azure Active Directory (AAD) k ověření ve službě StorSimple Device Manager Service** – od aktualizace 5 a vyšší, Azure Active Directory slouží k ověření pomocí služby StorSimple Device Manager. Starý ověřovací mechanismus bude od prosince 2017 zastaralý. Všichni uživatelé musí ve svých pravidlech firewallu zahrnovat nové adresy URL pro ověřování. Další informace najdete na adrese [URL pro ověřování uvedené v článku požadavky na síť pro vaše zařízení StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Pokud adresa URL pro ověření není v pravidlech brány firewall zahrnutá, zobrazí se uživatelům kritická výstraha, že jejich zařízení StorSimple nebylo možné ověřit pomocí služby. Pokud se uživatelům zobrazí tato výstraha, musí zahrnovat novou adresu URL pro ověření. Další informace najdete na webu výstrahy týkající se [StorSimple sítě](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nová verze StorSimple Snapshot Manager** – nová verze StorSimple Snapshot Manager je vydaná s aktualizací Update 5 a je kompatibilní se všemi StorSimple zařízeními, na kterých běží Update 4 nebo novější. Doporučujeme, abyste provedli aktualizaci na tuto verzi. Předchozí verze StorSimple Snapshot Manager se používá pro zařízení StorSimple se systémem Update 3 nebo starším. [Stáhněte si příslušnou verzi StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) a podívejte se na [Snapshot Manager nasazení StorSimple](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problémy opravené ve Update 5

Následující tabulka poskytuje souhrn problémů, které byly opraveny v Update 5.

| Ne | Doporučené | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Vzdálená komunikace Windows PowerShellu |V předchozí verzi se uživateli při pokusu o navázání vzdáleného připojení k StorSimple Cloud Appliance pomocí Windows PowerShellu zobrazila chyba. Tento problém byl způsobený kořenem a opravil v této verzi. |No |Yes |
| 2 |Šablony šířky pásma |V dřívějších verzích došlo k potížím se šablonami šířky pásma, jejichž výsledkem je menší šířka pásma, než bylo zařízení nakonfigurované. Tento problém je vyřešen v této verzi. |Yes |Yes |
| 3 |Převzetí služeb při selhání |Když v předchozí verzi došlo při převzetí služeb při selhání zařízení s velkým počtem svazků na jiné zařízení se systémem Update 4, proces při pokusu o použití záznamů řízení přístupu selže. Tento problém je opravený v této verzi. |Yes |Yes |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Známé problémy s aktualizací Update 5 z předchozích verzí

V Update 5 nejsou žádné nové známé problémy. Seznam problémů přenesených do aktualizace 5 z předchozích verzí najdete v [poznámkách k verzi Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Řadič SCSI a aktualizace firmwaru připojené k sériovému portu (SAS) ve Update 5

Tato verze obsahuje řadič SAS a aktualizace ovladačů LSI a firmware. Další informace o tom, jak tyto aktualizace nainstalovat, najdete v tématu [instalace aktualizace Update 5](storsimple-8000-install-update-5.md) na zařízení StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Aktualizace StorSimple Cloud Appliance v Update 5

Tuto aktualizaci nelze použít pro StorSimple Cloud Appliance (označuje se také jako virtuální zařízení). Nová cloudová zařízení je potřeba vytvořit pomocí Image Update 5. Informace o tom, jak vytvořit StorSimple Cloud Appliance, najdete v tématu [nasazení a správa StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Další krok

Přečtěte si, jak [nainstalovat Update 5](storsimple-8000-install-update-5.md) na zařízení StorSimple.

