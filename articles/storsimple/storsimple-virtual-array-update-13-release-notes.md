---
title: Zpráva k vydání verze Microsoft Azure StorSimple Virtual Array Update 1,3 | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole Azure StorSimple s aktualizací 1,3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 4e50f866c0bd44ca54c2f4df78b9507aa7077169
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452556"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>Zpráva k vydání verze pro StorSimple Virtual Array Update 1,3

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple aktualizace virtuálních polí.

Poznámky k verzi se průběžně aktualizují. Při zjištění zásadních problémů, které vyžadují řešení, sem přidáme dostupné informace. Před nasazením StorSimple virtuálního pole pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 1,3 odpovídá 10.0.10319.0 verze softwaru.

> [!IMPORTANT]
> - Aktualizace 1,3 je kritická aktualizace. Důrazně doporučujeme, abyste ji nainstalovali co nejrychleji.
> - Aktualizaci 1,3 můžete nainstalovat jenom na zařízení s aktualizací Update 1,2.
> - Aktualizace jsou rušivé a restartují vaše zařízení. Pokud probíhá vstupně-výstupní operace, dojde k výpadku zařízení. Podrobné pokyny k balíčkům, které se používají k instalaci této aktualizace, najdete v článku [Stažení aktualizace 1,3](#download-update-13).

## <a name="whats-new-in-update-13"></a>Co je nového v aktualizaci 1,3

Tato aktualizace obsahuje následující vylepšení: KB4540725

- TLS (Transport Layer Security) 1,2 je povinná aktualizace a je nutné ji nainstalovat. Z této verze dostávají protokol TLS 1,2 standardní protokol pro veškerou komunikaci Azure Portal.
- Opravy chyb uvolňování paměti zlepšují výkon cyklu uvolňování paměti, když jsou zařízení a účet úložiště ve dvou vzdálených oblastech.
- Opravte chyby zálohování z důvodu vypršení časových limitů objektu BLOB.
- Aktualizované opravy zabezpečení operačního systému/. NET Framework:
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86): březen 2020 cestou nadřazené (aktualizace servisního zásobníku)
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01): července 2020 – souhrn
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00): červenec 2020 .NET Framework aktualizace

## <a name="download-update-13"></a>Stažení aktualizace 1,3

Pokud chcete tuto aktualizaci stáhnout, navštivte server [katalogu Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) a Stáhněte si balíček KB4575898. Tento balíček obsahuje následující balíčky:

- **KB4540725**, která obsahuje kumulativní aktualizace Windows pro 2012 R2 až do března 2020. Další informace o tom, co je zahrnuté v této kumulativní aktualizaci, najdete v [měsíci kumulativních zabezpečení v březnu](https://support.microsoft.com/help/4540725).
- **KB4565541**, která obsahuje kumulativní aktualizace Windows pro 2012 R2 až do července 2020. Další informace o tom, co je zahrnuté v této kumulativní aktualizaci, najdete v [měsíci kumulativního zabezpečení v únoru](https://support.microsoft.com/help/4565541).
- **KB4565622**, která obsahuje aktualizace rozhraní Cumulative.NET, až do července 2020. Další informace o tom, co je zahrnuté v této kumulativní aktualizaci, najdete v [měsíci kumulativního zabezpečení v únoru](https://support.microsoft.com/help/4565622).
- **KB3011067**, která obsahuje aktualizace softwaru zařízení.

Stáhněte si KB4575898 a postupujte podle těchto pokynů, abyste [aktualizaci použili prostřednictvím místního webového uživatelského rozhraní](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Známé problémy v aktualizaci 1,3
V aktualizaci 1,3 se neuvedly žádné nové problémy. Všechny problémy zaznamenané vydáním se přenesou z předchozích verzí. Pokud chcete zobrazit souhrn známých problémů zahrnutých z předchozích verzí, přejděte na [známé problémy v aktualizaci 1,2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12).

## <a name="next-steps"></a>Další kroky
Stáhněte si KB4575898 a [použijte aktualizaci prostřednictvím místního webového uživatelského rozhraní](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Reference
Hledáte starší poznámku k verzi? Přejít na:

- [Zpráva k vydání verze pro StorSimple Virtual Array Update 1,2](./storsimple-virtual-array-update-12-release-notes.md)
- [Zpráva k vydání verze pro StorSimple Virtual Array Update 1,0](./storsimple-virtual-array-update-1-release-notes.md)
- [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,6](./storsimple-virtual-array-update-06-release-notes.md)
- [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,5](./storsimple-virtual-array-update-05-release-notes.md)
- [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,4](./storsimple-virtual-array-update-04-release-notes.md)
- [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,3](./storsimple-ova-update-03-release-notes.md)
- [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,1 a 0,2](./storsimple-ova-update-01-release-notes.md)
- [Poznámky k verzi pro StorSimple Virtual Array General Availability](/azure/storsimple/storsimple-ova-pp-release-notes)