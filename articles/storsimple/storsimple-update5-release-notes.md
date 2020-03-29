---
title: StorSimple 8000 Series Update 5 poznámky k verzi
description: Popisuje nové funkce, problémy a řešení pro aktualizaci StorSimple 8000 Series Update 5.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275172"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 Series Update 5 poznámky k verzi

## <a name="overview"></a>Přehled

Následující poznámky k verzi popisují nové funkce a identifikují kritické otevřené problémy pro aktualizaci Řady StorSimple 8000 5. Obsahují také seznam aktualizací softwaru StorSimple zahrnutých v této verzi.

Aktualizaci 5 lze použít na libovolné zařízení StorSimple se spuštěnou aktualizací 0.1 až aktualizací 4. Verze zařízení přidružená k aktualizaci 5 je 6.3.9600.17845.

Před nasazením aktualizace v řešení StorSimple zkontrolujte informace obsažené v poznámkách k verzi.

> [!IMPORTANT]
> * Aktualizace 5 je povinná aktualizace a musí být nainstalována okamžitě. Další informace naleznete v tématu [Jak použít aktualizaci 5](storsimple-8000-install-update-5.md).
> * Aktualizace 5 obsahuje software zařízení, firmware disku, zabezpečení operačního systému a další aktualizace operačního systému. Instalace této aktualizace trvá přibližně 4 hodiny. Aktualizace firmwaru disku je rušivá aktualizace a má za následek prostoje vašeho zařízení. Doporučujeme použít aktualizaci 5, aby bylo vaše zařízení aktuální.
> * U nových verzí se aktualizace nemusí zobrazit okamžitě, protože provádíme postupné zavádění aktualizací. Počkejte několik dní a poté znovu vyhledání aktualizací, protože tyto aktualizace budou brzy k dispozici.

## <a name="whats-new-in-update-5"></a>Co je nového v aktualizaci 5

V aktualizaci 5 byla provedena následující klíčová vylepšení a opravy chyb.

* **Použití služby Azure Active Directory (AAD) k ověření pomocí služby StorSimple Device Manager** – od aktualizace 5 se služba Azure Active Directory používá k ověření pomocí služby StorSimple Device Manager. Starý mechanismus ověřování bude zastaralé do prosince 2017. Všichni uživatelé musí do svých pravidel brány firewall zahrnout nové adresy URL ověřování. Další informace naleznete v [adresách URL ověřování uvedených v požadavcích na síť pro zařízení StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Pokud adresa URL ověřování není zahrnuta v pravidlech brány firewall, zobrazí se uživatelům kritická výstraha, že jejich zařízení StorSimple nebylo možné ověřit pomocí služby. Pokud se uživatelům zobrazí tato výstraha, musí zahrnout novou adresu URL ověřování. Další informace naleznete v [oznámeních o sítích StorSimple](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nová verze Správce snímků StorSimple** – nová verze Správce snímků StorSimple je vydána s aktualizací 5 a je kompatibilní se všemi zařízeními StorSimple, která používají aktualizaci 4 nebo novější. Doporučujeme aktualizovat na tuto verzi. Předchozí verze Správce snímků StorSimple se používá pro zařízení StorSimple, na kterých je spuštěna aktualizace 3 nebo starší. [Stáhněte si příslušnou verzi Správce snímků StorSimple](https://www.microsoft.com/en-us/download/details.aspx?id=44220) a podívejte se na [nasazení Správce snímků StorSimple](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problémy opravené v aktualizaci 5

Následující tabulka obsahuje souhrn problémů, které byly opraveny v aktualizaci 5.

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Vzdálené hlasování prostředí Windows PowerShell |V předchozí verzi by uživatel obdrží chybu při pokusu o navázání vzdáleného připojení k zařízení StorSimple Cloud Appliance prostřednictvím prostředí Windows PowerShell. Tento problém byl root-caused a opravena v této verzi. |Ne |Ano |
| 2 |Šablony šířky pásma |V dřívější verzi došlo k problému se šablonami šířky pásma, který vedl k nižší šířce pásma, než pro kterou bylo zařízení nakonfigurováno. Tento problém je vyřešen v této verzi. |Ano |Ano |
| 3 |Převzetí služeb při selhání |V předchozí verzi, když zařízení s velkým počtem svazků došlo k převzetí služeb při selhání na jiné zařízení se spuštěnou aktualizací 4, proces by se nezdaří při pokusu o použití záznamů řízení přístupu. Tento problém je vyřešen v této verzi. |Ano |Ano |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Známé problémy v aktualizaci 5 z předchozích verzí

V aktualizaci 5 nejsou žádné nové známé problémy. Seznam problémů přenesených do aktualizace 5 z předchozích verzí naleznete v [poznámkách k verzi aktualizace 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Sériově připojený řadič SCSI (SAS) a aktualizace firmwaru v aktualizaci 5

Tato verze obsahuje řadič SAS a aktualizace ovladačů a firmwaru LSI. Další informace o instalaci těchto aktualizací naleznete v [tématu instalace aktualizace 5](storsimple-8000-install-update-5.md) na zařízení StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Aktualizace storsimple cloudových spotřebičů v aktualizaci 5

Tuto aktualizaci nelze použít na StorSimple Cloud Appliance (označované také jako virtuální zařízení). Pomocí obrázku aktualizace 5 je třeba vytvořit nová cloudová zařízení. Informace o tom, jak vytvořit StorSimple Cloud Appliance, přejděte na [nasazení a správu StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Další krok

Přečtěte si, jak [nainstalovat aktualizaci 5](storsimple-8000-install-update-5.md) do zařízení StorSimple.

