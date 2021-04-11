---
title: Zpráva k vydání verze pro StorSimple 8000 Series Update 5,1
description: Popisuje nové funkce, problémy a alternativní řešení pro StorSimple 8000 Series Update 5,1.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: cdb971851ba678ce18f5a1c7954e5620740f3a4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657565"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>Zpráva k vydání verze pro StorSimple 8000 Series Update 5,1

## <a name="overview"></a>Přehled

Následující poznámky k verzi popisují nové funkce a identifikují důležité otevřené problémy pro StorSimple 8000 Series Update 5,1. Obsahují také seznam StorSimple aktualizací softwaru, které jsou součástí této verze.

Aktualizace 5,1 se dá použít pro všechna zařízení StorSimple s aktualizací Update 5. Pokud používáte verzi nižší než 5, nejprve použijte Update 5 a pak použijte 5,1. Verze zařízení, která je přidružená k aktualizaci 5,1, je 6.3.9600.17885.

Před nasazením aktualizace v řešení StorSimple si projděte informace obsažené v poznámkách k verzi.

> [!IMPORTANT]
>
> * Aktualizace 5,1 je povinná aktualizace a je nutné ji nainstalovat hned. Další informace najdete v tématu Jak [použít aktualizaci 5,1](storsimple-8000-install-update-51.md).
> * Aktualizace 5,1 obsahuje pouze aktualizace zabezpečení. Instalace této aktualizace trvá přibližně 30 minut. Důrazně doporučujeme, abyste použili aktualizaci 5,1, abyste zajistili fungování vašeho zařízení.
> * Pro nové verze se aktualizace nemusí okamžitě zobrazovat, protože provádíme dvoufázové zavedení aktualizací. Počkejte několik dní a pak znovu vyhledejte aktualizace, protože tyto aktualizace budou brzy k dispozici.

## <a name="whats-new-in-update-51"></a>Co je nového v aktualizaci 5,1

V aktualizaci 5,1 byly provedeny následující hlavní vylepšení a opravy chyb:

* **TLS 1,2** – Tato aktualizace StorSimple vynutila TLS 1,2 na všech klientech. Tato aktualizace je povinná pro všechna zařízení StorSimple řady 8000.

   Pokud se zobrazí následující upozornění, musíte software v zařízení aktualizovat, než budete pokračovat:

   Minimálně u jednoho zařízení StorSimple je spuštěná starší verze softwaru. Nejnovější dostupná aktualizace pro TLS 1,2 je povinná aktualizace a měla by být na těchto zařízeních nainstalovaná hned. Pro veškerou komunikaci Azure Portal a bez této aktualizace se používá TLS 1,2, zařízení nebude moct komunikovat se službou StorSimple.

## <a name="known-issues-in-update-51-from-previous-releases"></a>Známé problémy v aktualizaci 5,1 z předchozích verzí

V aktualizaci 5,1 nejsou žádné nové známé problémy. Seznam problémů přenesených do aktualizace 5,1 z předchozích verzí najdete v [poznámkách k verzi Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>Aktualizace StorSimple Cloud Appliance v aktualizaci 5,1

Tuto aktualizaci nelze použít pro StorSimple Cloud Appliance (označuje se také jako virtuální zařízení). Nová cloudová zařízení je potřeba vytvořit pomocí image aktualizace 5,1. Informace o tom, jak vytvořit StorSimple Cloud Appliance, najdete v tématu [nasazení a správa StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Další krok

Přečtěte si, jak [nainstalovat Update 5,1](storsimple-8000-install-update-51.md) na zařízení StorSimple.
