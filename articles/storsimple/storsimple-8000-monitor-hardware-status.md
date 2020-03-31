---
title: StorSimple 8000 série hardwarových komponent a stav | Dokumenty společnosti Microsoft
description: Zjistěte, jak sledovat hardwarové součásti zařízení StorSimple prostřednictvím služby StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: a987239669e7437a179f5f24034f4dbe45535663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254739"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Použití služby StorSimple Device Manager ke sledování hardwarových součástí a stavu

## <a name="overview"></a>Přehled
Tento článek popisuje různé fyzické a logické součásti v místním zařízení řady StorSimple 8000. Vysvětluje také, jak sledovat stav součásti zařízení pomocí okna **Stav a stav hardwaru** ve službě StorSimple Device Manager.

Okno **Stav a stav hardwaru** zobrazuje stav hardwaru všech součástí zařízení StorSimple.

V seznamu komponent pro 8100 jsou tři části, které popisují:

* **Sdílené komponenty** – nejedná se o součást řadičů, jako jsou diskové jednotky, skříň, komponenty power and cooling module (PCM) a snímače teploty PCM, napětí a linky.
* **Controller 0 Komponenty** – součásti, které jsou umístěny na řadič0, jako je například řadič, SAS expandér a konektor, snímače teploty řadiče a různá síťová rozhraní.
* **Controller 1 Součásti** – součásti, které tvoří řadič 1, podobné těm, které jsou podrobně popsány pro řadič 0.

Zařízení 8600 má další součásti, které odpovídají skříni Extended Bunch of Disks (EBOD). V seznamu komponent je pět sekcí. Z nich existují tři části, které obsahují součásti v primární skříni a jsou shodné s těmi popsanými pro 8100. Existují dvě další části pro skříň EBOD, které popisují:

* **Komponenty EBOD Controller 0** – součásti, které jsou umístěny ve skříni EBOD 0, jako je například řadič EBOD, expandér SAS a konektor a teplotní senzory řadiče.
* **EBOD Controller 1 Komponenty** – komponenty, které tvoří skříň EBOD 1, podobné těm, které jsou podrobně popsány pro skříň EBOD 0.
* **Sdílené součásti skříně EBOD** – součásti přítomné ve skříni EBOD a PCM, které nejsou součástí řadiče EBOD.

> [!NOTE]
> **Stav hardwaru není k dispozici pro zařízení StorSimple Cloud Appliance (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Sledování stavu hardwaru
Chcete-li zobrazit stav hardwaru součásti zařízení, proveďte následující kroky:

1. Přejděte na **Zařízení**, vyberte konkrétní zařízení StorSimple. Přejděte na **informace o stavu > hardwaru**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Vyhledejte část **Hardwarové součásti** a vyberte si z dostupných součástí. Jednoduše kliknutím na popisek komponenty rozbalte seznam a zobrazte stav různých součástí zařízení. Podívejte se na [podrobný seznam součástí pro primární skříň](#component-list-for-primary-enclosure-of-storsimple-device) a podrobný seznam součástí pro [přílohu EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. K interpretaci stavu komponenty použijte následující schéma barevného kódování:
   
   * **Zelená kontrola** – označuje součást v pořádku se stavem **OK.**
   * **Žlutá** – označuje degradovosoučást ve stavu **Upozornění.**
   * **Červený vykřičník** – označuje selhání součásti, která má stav **Selhání.**
   * **Bílá s černým textem** – Označuje komponentu, která není přítomna.
   
   Následující snímek obrazovky ukazuje zařízení, které má součásti ve stavu **OK**, **Upozornění**a **Selhání.**
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Rozbalením **seznamu sdílených součástí**zjistíme, že paměť NVRAM a cluster jsou degradovány.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Rozbalením seznamu **součástí řadiče 1** vidíme, že uzel clusteru selhal.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Pokud narazíte na součást, která není ve stavu **V pořádku,** obraťte se na podporu společnosti Microsoft. Pokud jsou v zařízení povolena upozornění, obdržíte e-mailové upozornění. Pokud potřebujete vyměnit neúspěšnou hardwarovou součást, přečtěte si informace o [nahrazení hardwarové součásti StorSimple](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Seznam komponent pro primární kryt zařízení StorSimple
Následující tabulka popisuje fyzické a logické součásti obsažené v primární skříni (v 8100 a 8600) místního zařízení StorSimple.

| Komponenta | Modul | Typ | Umístění | Vyměnitelná jednotka pole (FRU)? | Popis |
| --- | --- | --- | --- | --- | --- |
| Pohon ve slotu [0-11] |Disky |Fyzické |Sdílená |Ano |Pro každou ssd diodu nebo pevné disky v primární skříni je zobrazen jeden řádek. |
| Čidlo okolní teploty |Kabiny |Fyzické |Sdílená |Ne |Měří teplotu v podvozku. |
| Teplotní čidlo střední roviny |Kabiny |Fyzické |Sdílená |Ne |Měří teplotu střední roviny. |
| Akustický alarm |Kabiny |Fyzické |Sdílená |Ne |Označuje, zda je subsystém zvukového poplachu v podvozku funkční. |
| Kabiny |Kabiny |Fyzické |Sdílená |Ano |Označuje přítomnost podvozku. |
| Nastavení skříně |Kabiny |Fyzické |Sdílená |Ne |Odkazuje na přední panel podvozku. |
| Linky napěťové senzory |PCM |Fyzické |Sdílená |Ne |Četné snímače síťového napětí mají zobrazen svůj stav, což znamená, zda je měřené napětí v toleranci. |
| Snímače proudu linky |PCM |Fyzické |Sdílená |Ne |Četné snímače linkového proudu mají zobrazen svůj stav, což znamená, zda je měřený proud v toleranci. |
| Teplotní senzory v PCM |PCM |Fyzické |Sdílená |Ne |Mnoho teplotních senzorů, jako jsou senzory inlet a hotspot, má svůj stav, což ukazuje, zda je naměřená teplota v toleranci. |
| Napájení [0-1] |PCM |Fyzické |Sdílená |Ano |Pro každý napájecí zdroj ve dvou PCM umístěných na zadní straně zařízení je uveden jeden řádek. |
| Chlazení [0-1] |PCM |Fyzické |Sdílená |Ano |Pro každý ze čtyř chladicích ventilátorů s bydlištěm ve dvou PCM je uveden jeden řádek. |
| Baterie [0-1] |PCM |Fyzické |Sdílená |Ano |Pro každý z modulů záložní baterie, které jsou usazeny v PCM, je uveden jeden řádek. |
| Metis |Není dostupné. |Logické |Sdílená |Není dostupné. |Zobrazuje stav baterií: zda potřebují nabíjet a blíží se ke konci životnosti. |
| Cluster |Není dostupné. |Logické |Sdílená |Není dostupné. |Zobrazuje stav clusteru vytvořeného mezi dvěma integrovanými moduly řadiče. |
| Uzel clusteru |Není dostupné. |Logické |Sdílená |Není dostupné. |Označuje stav řadiče jako součást clusteru. |
| Kvorum clusteru |Není dostupné. |Logické | |Není dostupné. |Označuje přítomnost většinového členství na disku ve fondu úložišť pevných disků. |
| Datový prostor hdd |Není dostupné. |Logické |Sdílená |Není dostupné. |Úložný prostor, který se používá pro data ve fondu úložiště pevného disku (HDD). |
| Prostor pro správu hdd |Není dostupné. |Logické |Sdílená |Není dostupné. |Místo vyhrazené ve fondu úložiště pevného disku pro úlohy správy. |
| Prostor kvora HDD |Není dostupné. |Logické |Sdílená |Není dostupné. |Místo vyhrazené ve fondu úložiště PEVNÉHO disku pro kvorum clusteru. |
| Náhradní místo hdd |Není dostupné. |Logické |Sdílená |Není dostupné. |Místo vyhrazené ve fondu úložiště PEVNÉHO disku pro výměnu řadiče. |
| Datový prostor SSD |Není dostupné. |Logické |Sdílená |Není dostupné. |Úložný prostor používaný pro data ve fondu úložiště jednotky SSD (SSD). |
| SSD NVRAM prostor |Není dostupné. |Logické |Sdílená |Není dostupné. |Úložný prostor ve fondu úložiště SSD, který je vyhrazen pro logiku NVRAM. |
| Fond úložiště HDD |Není dostupné. |Logické |Sdílená |Není dostupné. |Zobrazuje stav fondu logického úložiště vytvořeného z pevných disků zařízení. |
| Fond úložiště SSD |Není dostupné. |Logické |Sdílená |Není dostupné. |Zobrazuje stav fondu logického úložiště vytvořeného ze ssd disků SSD zařízení. |
| Řadič [0-1] [stav] |I/O |Fyzické |Řadič |Ano |Zobrazuje stav ovladače a zda je v aktivním nebo pohotovostním režimu v rámci šasi. |
| Teplotní senzory v regulátoru |I/O |Fyzické |Řadič |Ne |Četné teplotní senzory, jako je I / O modul, teplota procesoru, DIMM a PCIe senzory mají svůj stav, což znamená, zda je zjištěná teplota v toleranci. |
| Expandér SAS |I/O |Fyzické |Řadič |Ne |Označuje stav sériového připojeného expandéru SCSI (SAS), který se používá k připojení integrovaného úložiště k řadiči. |
| Konektor SAS [0-1] |I/O |Fyzické |Řadič |Ne |Označuje stav každého konektoru SAS, který se používá k připojení integrovaného úložiště k expandéru SAS. |
| Propojení SBB uprostřed roviny |I/O |Fyzické |Řadič |Ne |Označuje stav konektoru střední roviny, který se používá k připojení každého řadiče k střední rovině. |
| Jádro procesoru |I/O |Fyzické |Řadič |Ne |Označuje stav procesorových jader v rámci každého řadiče. |
| Výkon elektroniky skříně |I/O |Fyzické |Řadič |Ne |Označuje stav napájecího systému používaného skříní. |
| Diagnostika elektroniky skříně |I/O |Fyzické |Řadič |Ne |Označuje stav podsystémů diagnostiky poskytovaných řadičem. |
| Řadič pro správu základní desky (BMC) |I/O |Fyzické |Řadič |Ne |Označuje stav řadiče správy základní desky (BMC), což je specializovaný servisní procesor, který monitoruje hardwarové zařízení prostřednictvím senzorů a komunikuje se správcem systému prostřednictvím nezávislého připojení. |
| Síť Ethernet |I/O |Fyzické |Řadič |Ne |Označuje stav každého ze síťových rozhraní, to znamená, že správa a datové porty poskytované na řadiči. |
| Nvram |I/O |Fyzické |Řadič |Ne |Označuje stav paměti NVRAM, nestálé paměti ram zálohy zálohované baterií, která slouží k uchování informací důležitých pro aplikaci v případě výpadku napájení. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Seznam komponent pro skříň EBOD zařízení StorSimple
Následující tabulka popisuje fyzické a logické součásti obsažené ve skříni EBOD (k dispozici pouze v modelu 8600) místního zařízení StorSimple.

| Komponenta | Modul | Typ | Umístění | Fru? | Popis |
| --- | --- | --- | --- | --- | --- |
| Pohon ve slotu [0-11] |Disky |Fyzické |Sdílená |Ano |Pro každou z pevných disků v přední části skříně EBOD je uvedena jedna linka. |
| Čidlo okolní teploty |Kabiny |Fyzické |Sdílená |Ne |Měří teplotu v podvozku. |
| Teplotní čidlo střední roviny |Kabiny |Fyzické |Sdílená |Ne |Měří teplotu střední roviny. |
| Akustický alarm |Kabiny |Fyzické |Sdílená |Ne |Označuje, zda je subsystém zvukového poplachu v podvozku funkční. |
| Kabiny |Kabiny |Fyzické |Sdílená |Ano |Označuje přítomnost podvozku. |
| Nastavení skříně |Kabiny |Fyzické |Sdílená |Ne |Označuje OPS nebo přední panel podvozku. |
| Linky napěťové senzory |PCM |Fyzické |Sdílená |Ne |Četné snímače síťového napětí mají zobrazen svůj stav, což znamená, zda je měřené napětí v toleranci. |
| Snímače proudu linky |PCM |Fyzické |Sdílená |Ne |Četné snímače linkového proudu mají zobrazen svůj stav, což znamená, zda je měřený proud v toleranci. |
| Teplotní senzory v PCM |PCM |Fyzické |Sdílená |Ne |Mnoho teplotních senzorů, jako jsou senzory inlet a hotspot, má svůj stav, což ukazuje, zda je naměřená teplota v toleranci. |
| Napájení [0-1] |PCM |Fyzické |Sdílená |Ano |Pro každý napájecí zdroj ve dvou PCM umístěných na zadní straně zařízení je uveden jeden řádek. |
| Chlazení [0-1] |PCM |Fyzické |Sdílená |Ano |Pro každý ze čtyř chladicích ventilátorů s bydlištěm ve dvou PCM je uveden jeden řádek. |
| Místní úložiště [HDD] |Není dostupné. |Logické |Sdílená |Není dostupné. |Zobrazuje stav fondu logického úložiště vytvořeného z pevných disků zařízení. |
| Řadič [0-1] [stav] |I/O |Fyzické |Řadič |Ano |Zobrazuje stav řadičů v modulu EBOD. |
| Teplotní senzory v EBOD |I/O |Fyzické |Řadič |Ne |Četné teplotní senzory z každého regulátoru mají svůj stav zobrazen, což znamená, zda je zjištěná teplota v toleranci. |
| Expandér SAS |I/O |Fyzické |Řadič |Ne |Označuje stav expandéru SAS, který se používá k připojení integrovaného úložiště k řadiči. |
| Konektor SAS [0-2] |I/O |Fyzické |Řadič |Ne |Označuje stav každého konektoru SAS, který se používá k připojení integrovaného úložiště k expandéru SAS. |
| Propojení SBB uprostřed roviny |I/O |Fyzické |Řadič |Ne |Označuje stav konektoru střední roviny, který se používá k připojení každého řadiče k střední rovině. |
| Výkon elektroniky skříně |I/O |Fyzické |Řadič |Ne |Označuje stav napájecího systému používaného skříní. |
| Diagnostika elektroniky skříně |I/O |Fyzické |Řadič |Ne |Označuje stav podsystémů diagnostiky poskytovaných řadičem. |
| Připojení k řadiči zařízení |I/O |Fyzické |Řadič |Ne |Označuje stav připojení mezi modulem EBOD I/O a řadičem zařízení. |

## <a name="next-steps"></a>Další kroky
* Chcete-li ke správě zařízení použít službu StorSimple Device Manager, [přejděte ke správě zařízení StorSimple pomocí služby StorSimple Device Manager](storsimple-8000-manager-service-administration.md).
* Pokud potřebujete řešit potíže s komponentou zařízení, která má stav se sníženým nebo neúspěšným stavem, přečtěte si [informace o indikátorech monitorování StorSimple](storsimple-monitoring-indicators.md).
* Chcete-li nahradit neúspěšnou hardwarovou součást, přečtěte si informace [o nahrazení hardwarové součásti StorSimple](storsimple-hardware-component-replacement.md).
* Pokud se problémy se zařízením přetrvávají, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).

