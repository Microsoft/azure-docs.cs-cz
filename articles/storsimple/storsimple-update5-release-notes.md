---
title: Poznámky k verzi zařízení StorSimple 8000 řady aktualizací 5 | Microsoft Docs
description: Popisuje nové funkce, problémy a řešení pro zařízení StorSimple 8000 řady aktualizací 5.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 672757e82bcf645b705f46a9975e09c9dc5eef92
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2017
ms.locfileid: "24518915"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Poznámky k verzi zařízení StorSimple 8000 řady aktualizací 5

## <a name="overview"></a>Přehled

Následující poznámky k verzi popisují nové funkce a identifikovat kritická otevřené problémy pro StorSimple 8000 řady aktualizací 5. Také obsahují seznam aktualizací softwaru zařízení StorSimple, zahrnuté v této verzi.

Aktualizace 5 můžete použít pro všechna zařízení StorSimple Update 0.1 spuštění prostřednictvím aktualizace 4. Zařízení verzi spojenou s aktualizací 5 je 6.3.9600.17845.

Zkontrolujte informace obsažené v poznámkách k verzi před nasazením aktualizace v řešení StorSimple.

> [!IMPORTANT]
> * Aktualizace 5 je povinná aktualizace a musí být nainstalován okamžitě. Další informace najdete v tématu Jak [použít aktualizaci 5](storsimple-8000-install-update-5.md).
> * Aktualizace 5 má zařízení softwaru, firmwaru disku, zabezpečení operačního systému a další aktualizace operačního systému. Jak dlouho trvá přibližně 4 hodiny nainstalovat tuto aktualizaci. Aktualizace firmwaru disku je rušivý aktualizace a výsledkem výpadku pro vaše zařízení. Doporučujeme, abyste použili aktualizaci 5 k zachování aktualizovaného stavu zařízení.
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože provedeme postupné zavádění aktualizací. Počkejte několik dní, a pak vyhledejte aktualizace znovu jako tyto aktualizace brzy bude dostupná.

## <a name="whats-new-in-update-5"></a>Co je nového v aktualizací 5

V aktualizaci 5 byly provedeny následující klíčových vylepšení a opravy chyb.

* **Použití nástroje Azure Active Directory (AAD) k ověření pomocí služby StorSimple Manager zařízení** – od aktualizace 5 a vyšší, Azure Active Directory se používá k ověření u služby StorSimple Manager zařízení. Původní mechanismus ověřování přestanou prosinec 2017. Všichni uživatelé musí obsahovat nové adresy URL ověřování v jejich pravidla brány firewall. Další informace, přejděte na [ověřování adresy URL uvedené v síťové požadavky pro zařízení StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Pokud adresa URL pro ověření není zahrnut do pravidel brány firewall, uživatelé uvidí kritickou výstrahu, že jejich zařízení StorSimple nelze ověřit ve službě. Pokud uživatelé zobrazit tuto výstrahu, musí obsahovat nové adresy URL ověřování. Další informace, přejděte na [StorSimple sítě výstrahy](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nová verze služby StorSimple Snapshot Manager** -nová verze služby StorSimple Snapshot Manager vydání s aktualizací 5 a je kompatibilní s všechna zařízení StorSimple, která běží aktualizací 4 nebo novější. Doporučujeme aktualizovat na tuto verzi. Předchozí verze služby StorSimple Snapshot Manager se používá pro zařízení StorSimple, které běží Update 3 nebo dřívější. [Stáhněte si příslušnou verzi služby StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) a odkazovat na [nasazení zařízení StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Chyby v aktualizaci 5

Následující tabulka obsahuje souhrn problémy, které jsme vyřešili v aktualizací 5.

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Vzdálená komunikace prostředí Windows PowerShell |V předchozí verzi uživatel by k chybě při pokusu vytvořit vzdálené připojení k zařízení StorSimple cloudu pomocí prostředí Windows PowerShell. Tento problém byl způsobeno kořenové a pevné v této verzi. |Ne |Ano |
| 2 |Šablony šířky pásma |Ve starší verzi byl problém se šablonami šířky pásma, jejichž výsledkem menší šířkou pásma, než co zařízení bylo konfigurováno pro. Tento problém se vyřeší v této verzi. |Ano |Ano |
| 3 |Převzetí služeb při selhání |V předchozí verzi když zařízení s vysoký počet svazků byla při selhání na jiné zařízení spuštěný aktualizace 4, proces skončí s chybou při pokusu o použití záznamy řízení přístupu. Tento problém vyřešen v této verzi. |Ano |Ano |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Známé problémy v aktualizaci 5 z předchozích verzí

V aktualizaci 5 nejsou žádné nové známé problémy. Pro seznam problémů, které přenášejí do aktualizací 5 z předchozích verzí, přejděte na [poznámky k verzi Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Serial-attached SCSI (SAS) řadiče a aktualizace firmwaru v aktualizací 5

Tato verze obsahuje řadič SAS a LSI ovladače a firmware aktualizace. Další informace o tom, jak tyto aktualizace nainstalujete najdete v tématu [instalaci aktualizací 5](storsimple-8000-install-update-5.md) zařízení StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Aktualizace zařízení StorSimple cloudu v aktualizací 5

Tuto aktualizaci nelze nainstalovat do cloudu zařízení StorSimple (také označované jako virtuální zařízení). Nové zařízení cloudu musí vytvořit pomocí bitové kopie aktualizací 5. Informace o tom, jak vytvořit o cloudu zařízení StorSimple, přejděte na [nasadit a spravovat zařízení s StorSimple cloudu](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Další krok

Zjistěte, jak [instalaci aktualizací 5](storsimple-8000-install-update-5.md) zařízení StorSimple.

