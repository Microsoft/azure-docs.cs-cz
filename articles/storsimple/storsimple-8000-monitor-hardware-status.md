---
title: Hardwarové komponenty a stav StorSimple 8000 series | Microsoft Docs
description: Naučte se monitorovat hardwarové součásti zařízení StorSimple prostřednictvím služby StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 6b750659bf22e856ee8ad7368e3fea64dd7245df
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960355"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Monitorování hardwarových komponent a stavů pomocí služby StorSimple Device Manager

## <a name="overview"></a>Přehled
Tento článek popisuje různé fyzické a logické komponenty v místním zařízení řady StorSimple 8000. Vysvětluje také, jak monitorovat stav součásti zařízení pomocí okna **stav a stav hardwaru** ve službě StorSimple Device Manager.

Okno **stav a stav hardwaru** zobrazuje stav hardwaru všech komponent zařízení StorSimple.

V seznamu součástí pro 8100 existují tři oddíly, které popisují:

* **Sdílené součásti** – tyto ovladače nejsou součástí řadičů, jako jsou například diskové jednotky, skříně, součásti napájení a chladicího modulu (PCM) a teplota PCM, napětí na řádku a proudové senzory.
* **Komponenty řadiče 0** – součásti, které jsou umístěné na řadiči 0, jako je například kontrolér, rozšíření SAS a konektor, senzory teploty řadiče a různá síťová rozhraní.
* **Komponenty řadiče 1** – součásti, které představují řadič 1, jsou podobné těm, které jsou popsány pro řadič 0.

Zařízení 8600 má další součásti, které odpovídají skříni EBOD (Extended of disks). V seznamu součástí je pět oddílů. Z těchto důvodů existují tři části, které obsahují součásti v primární skříni a jsou shodné s těmi, které jsou popsány pro 8100. Existují dva další oddíly pro EBOD skříň, které popisují:

* **Komponenty eBOD Controller 0** – součásti, které se nacházejí v eBOD skříni 0, jako je například kontroler eBOD, rozšíření SAS a konektor a teplotní senzory.
* **Komponenty eBOD Controller 1** – součásti, které tvoří eBOD skříň 1, jsou podobné těm, které jsou popsané pro eBOD skříň 0.
* **Sdílené součásti eBOD skříni** – součásti přítomné ve skříni eBOD a PCM, které nejsou součástí řadiče eBOD.

> [!NOTE]
> **Stav hardwaru není k dispozici pro StorSimple Cloud Appliance (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitorování stavu hardwaru
Chcete-li zobrazit stav hardwaru součásti zařízení, proveďte následující kroky:

1. Přejděte na **zařízení** a vyberte konkrétní zařízení StorSimple. Přejít na **Monitor > stav hardwaru**.

    ![Snímek obrazovky zobrazující okno nastavení služby Device Manager V části monitorování se vybere stav hardwaru.](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Vyhledejte část **hardwarové součásti** a vyberte z dostupných součástí. Stačí kliknout na popisek součásti a seznam se rozbalí a zobrazí se stav různých součástí zařízení. Prohlédněte si [podrobný seznam komponent pro primární skříň](#component-list-for-primary-enclosure-of-storsimple-device) a [seznam podrobných součástí pro eBOD skříň](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![Snímek obrazovky s jednou součástí vybranou v části hardwarové součásti. Sousední okno zobrazuje název a stav částí této součásti.](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Pomocí následujícího schématu barevného kódování můžete interpretovat stav součásti:
   
   * **Zelená check** – označuje součást v pořádku s stavem **OK** .
   * **Žlutá** – označuje sníženou součást ve stavu **varování** .
   * **Red vykřičník** – označuje neúspěšnou součást, která má stav **selhání** .
   * **Bílá s černým textem** – označuje komponentu, která není k dispozici.
   
   Na následujícím snímku obrazovky vidíte zařízení, které obsahuje komponenty v **pořádku**, **Upozornění** a stav **selhání** .
       
   ![Snímek obrazovky se třemi součástmi v části hardwarové součásti: jeden ve stavu v pořádku, jeden ve stavu selhání a jeden ve stavu varování.](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Rozbalením **seznamu sdílených komponent** zjistíme, že paměť NVRAM a cluster jsou degradovány.

   ![Snímek obrazovky znázorňující vybranou položku Sdílené součásti V přilehlých oknech je N v R a M a cluster ve stavu upozornění.](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Rozbalením seznamu **součástí kontroleru 1** zjistíme, že se uzel clusteru nezdařil.  

   ![Snímek obrazovky znázorňující vybranou položku součásti kontroleru 1 V přilehlých oknech je uzel clusteru ve stavu selhání.](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Pokud narazíte na součást, která není v **dobrém** stavu, kontaktujte podpora Microsoftu. Pokud jsou v zařízení povolené výstrahy, obdržíte e-mailové upozornění. Pokud potřebujete vyměnit neúspěšnou hardwarovou součást, přečtěte si téma [Výměna StorSimple hardwarové součásti](./storsimple-8000-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Seznam součástí pro primární skříň zařízení StorSimple
Následující tabulka popisuje fyzické a logické komponenty obsažené v primární skříni (v 8100 a 8600) místního zařízení StorSimple.

| Součást | Modul | Typ | Umístění | Přimístit jednotky (FRU) pole? | Popis |
| --- | --- | --- | --- | --- | --- |
| Jednotka ve slotu [0-11] |Diskové jednotky |Fyzické |Shared |Yes |Jeden řádek se zobrazí pro každou jednotku SSD nebo hard jednotky v primární skříni. |
| Senzor okolní teploty |Skříně |Fyzické |Shared |Ne |Měří teplotu v rámci skříně. |
| Senzor teploty střední roviny |Skříně |Fyzické |Shared |Ne |Měří teplotu střední roviny. |
| Zvukový poplach |Skříně |Fyzické |Shared |Ne |Určuje, jestli je funkční podsystém zvukového alarmu v rámci skříně. |
| Skříně |Skříně |Fyzické |Shared |Yes |Označuje přítomnost skříně. |
| Nastavení skříně |Skříně |Fyzické |Shared |Ne |Odkazuje na přední panel skříně. |
| Senzory napětí čáry |PCM |Fyzické |Shared |Ne |Množství senzorů napětí v řadě je zobrazených stavů, což označuje, zda je měřené napětí v toleranci. |
| Čára aktuální senzory |PCM |Fyzické |Shared |Ne |Množství čáry aktuální senzory mají zobrazený stav, který označuje, zda je měřený aktuální počet v toleranci. |
| Senzory teploty v PCM |PCM |Fyzické |Shared |Ne |Množství senzorů teploty, jako jsou vstupní a aktivní senzory, má zobrazený stav, který označuje, jestli je měřená teplota v toleranci. |
| Zdroj napájení [0-1] |PCM |Fyzické |Shared |Yes |Jeden řádek se zobrazí pro každou spotřebu napájení v obou PCMs, která se nachází na zadní straně zařízení. |
| Chlazení [0-1] |PCM |Fyzické |Shared |Yes |Pro každý ze čtyř chladicích ventilátorů umístěných v obou PCMs se zobrazí jeden řádek. |
| Baterie [0-1] |PCM |Fyzické |Shared |Yes |Jeden řádek je zobrazen pro každý z modulů záložní baterie, které jsou umístěny v PCM. |
| Metis |– |Logické |Shared |– |Zobrazuje stav baterie: to, jestli potřebují zpoplatnění, a blíží se k ukončení životního cyklu. |
| Cluster |– |Logické |Shared |– |Zobrazuje stav clusteru, který se vytvoří mezi dvěma integrovanými moduly řadiče. |
| Uzel clusteru |– |Logické |Shared |– |Určuje stav kontroleru v rámci clusteru. |
| Kvorum clusteru |– |Logické | |– |Určuje přítomnost většiny členství na disku ve fondu úložiště HDD. |
| Datový prostor HDD |– |Logické |Shared |– |Prostor úložiště, který se používá pro data ve fondu úložiště jednotky pevného disku (HDD). |
| Místo pro správu HDD |– |Logické |Shared |– |Rezervované místo ve fondu úložiště HDD pro úlohy správy. |
| Místo kvora HDD |– |Logické |Shared |– |Rezervované místo ve fondu úložiště HDD pro kvorum clusteru. |
| Místo pro nahrazení HDD |– |Logické |Shared |– |Místo vyhrazené fondem úložiště HDD pro nahrazení řadičem. |
| Datový prostor SSD |– |Logické |Shared |– |Prostor úložiště, který se používá pro data ve fondu úložiště SSD (Solid State Drive). |
| PAMĚŤ SSD NVRAM |– |Logické |Shared |– |Prostor úložiště ve fondu úložiště SSD, který je vyhrazený pro logiku paměti NVRAM. |
| Fond úložiště HDD |– |Logické |Shared |– |Zobrazuje stav logického fondu úložiště, který je vytvořený ze zařízení HDD. |
| Fond úložiště SSD |– |Logické |Shared |– |Zobrazuje stav logického fondu úložiště, který je vytvořený ze zařízení SSD. |
| Kontroler [0-1] [stav] |I/O |Fyzické |Kontrolér |Yes |Zobrazí stav kontroleru a zda je v rámci skříně v aktivním nebo pohotovostním režimu. |
| Senzory teploty v kontroleru |I/O |Fyzické |Kontrolér |Ne |Množství senzorů teploty, jako je vstupně-výstupní modul, teplota procesoru, DIMM a senzory PCIe, mají zobrazený stav, který označuje, jestli se zjištěná teplota nachází v toleranci. |
| Rozšíření SAS |I/O |Fyzické |Kontrolér |Ne |Označuje stav rozšíření SAS (Serial Attached SCSI), které se používá k připojení integrovaného úložiště k řadiči. |
| Konektor SAS [0-1] |I/O |Fyzické |Kontrolér |Ne |Označuje stav každého konektoru SAS, který se používá k připojení integrovaného úložiště k rozšíření SAS. |
| Propojení střední roviny SBB |I/O |Fyzické |Kontrolér |Ne |Určuje stav konektoru střední roviny, který se používá pro připojení každého kontroleru k střední rovině. |
| Jádro procesoru |I/O |Fyzické |Kontrolér |Ne |Určuje stav jader procesoru v rámci každého kontroleru. |
| Power elektroniky skříně |I/O |Fyzické |Kontrolér |Ne |Určuje stav systému napájení, který skříň používá. |
| Diagnostika elektroniky skříně |I/O |Fyzické |Kontrolér |Ne |Určuje stav subsystémů diagnostiky, které poskytuje kontroler. |
| Řadič pro správu základní desky (BMC) |I/O |Fyzické |Kontrolér |Ne |Určuje stav řadiče pro správu základní desky (BMC), což je specializovaný procesor služeb, který monitoruje hardwarové zařízení prostřednictvím senzorů a komunikuje se správcem systému prostřednictvím nezávislého připojení. |
| Ethernet |I/O |Fyzické |Kontrolér |Ne |Označuje stav každého síťového rozhraní, tedy porty pro správu a data, které jsou k dispozici na řadiči. |
| NVRAM disku |I/O |Fyzické |Kontrolér |Ne |Označuje stav paměti NVRAM, nestálou paměť náhodného přístupu zálohovanou baterií, která slouží k uchování informací důležitých pro aplikaci v případě výpadku napájení. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Seznam součástí pro EBOD skříň zařízení StorSimple
Následující tabulka popisuje fyzické a logické komponenty obsažené v EBOD skříni (k dispozici pouze v modelu 8600) místního zařízení StorSimple.

| Součást | Modul | Typ | Umístění | JEDNOTKA? | Popis |
| --- | --- | --- | --- | --- | --- |
| Jednotka ve slotu [0-11] |Diskové jednotky |Fyzické |Shared |Yes |Jeden řádek se zobrazí pro každou jednotku HDD na začátku EBOD skříně. |
| Senzor okolní teploty |Skříně |Fyzické |Shared |Ne |Měří teplotu v rámci skříně. |
| Senzor teploty střední roviny |Skříně |Fyzické |Shared |Ne |Měří teplotu střední roviny. |
| Zvukový poplach |Skříně |Fyzické |Shared |Ne |Určuje, jestli je funkční podsystém zvukového alarmu v rámci skříně. |
| Skříně |Skříně |Fyzické |Shared |Yes |Označuje přítomnost skříně. |
| Nastavení skříně |Skříně |Fyzické |Shared |Ne |Odkazuje na operace nebo na přední panel skříně. |
| Senzory napětí čáry |PCM |Fyzické |Shared |Ne |Množství senzorů napětí v řadě je zobrazených stavů, což označuje, zda je měřené napětí v toleranci. |
| Čára aktuální senzory |PCM |Fyzické |Shared |Ne |Množství čáry aktuální senzory mají zobrazený stav, který označuje, zda je měřený aktuální počet v toleranci. |
| Senzory teploty v PCM |PCM |Fyzické |Shared |Ne |Množství senzorů teploty, jako jsou vstupní a aktivní senzory, má zobrazený stav, který označuje, zda je měřená teplota v toleranci. |
| Zdroj napájení [0-1] |PCM |Fyzické |Shared |Yes |Jeden řádek se zobrazí pro každou spotřebu napájení v obou PCMs, která se nachází na zadní straně zařízení. |
| Chlazení [0-1] |PCM |Fyzické |Shared |Yes |Pro každý ze čtyř chladicích ventilátorů umístěných v obou PCMs se zobrazí jeden řádek. |
| Místní úložiště [HDD] |– |Logické |Shared |– |Zobrazuje stav logického fondu úložiště, který je vytvořený ze zařízení HDD. |
| Kontroler [0-1] [stav] |I/O |Fyzické |Kontrolér |Yes |Zobrazuje stav řadičů v modulu EBOD. |
| Senzory teploty v EBOD |I/O |Fyzické |Kontrolér |Ne |Množství senzorů teploty od každého kontroleru se zobrazuje jejich stav, což označuje, jestli se zjištěná teplota nachází v toleranci. |
| Rozšíření SAS |I/O |Fyzické |Kontrolér |Ne |Určuje stav rozšíření SAS, které se používá k připojení integrovaného úložiště k řadiči. |
| Konektor SAS [0-2] |I/O |Fyzické |Kontrolér |Ne |Označuje stav každého konektoru SAS, který se používá k připojení integrovaného úložiště k rozšíření SAS. |
| Propojení střední roviny SBB |I/O |Fyzické |Kontrolér |Ne |Určuje stav konektoru střední roviny, který se používá pro připojení každého kontroleru k střední rovině. |
| Power elektroniky skříně |I/O |Fyzické |Kontrolér |Ne |Určuje stav systému napájení, který skříň používá. |
| Diagnostika elektroniky skříně |I/O |Fyzické |Kontrolér |Ne |Určuje stav subsystémů diagnostiky, které poskytuje kontroler. |
| Připojení k řadiči zařízení |I/O |Fyzické |Kontrolér |Ne |Určuje stav připojení mezi modulem EBOD I/O a řadičem zařízení. |

## <a name="next-steps"></a>Další kroky
* Pokud chcete ke správě svého zařízení použít službu StorSimple Device Manager, použijte ke [správě zařízení StorSimple službu StorSimple Device Manager](storsimple-8000-manager-service-administration.md).
* Pokud potřebujete řešit potíže s komponentou zařízení, která má snížený nebo neúspěšný stav, přečtěte si [indikátory monitorování StorSimple](storsimple-monitoring-indicators.md).
* Chcete-li nahradit neúspěšnou hardwarovou součást, přečtěte si téma [Výměna hardwarové součásti StorSimple](./storsimple-8000-hardware-component-replacement.md).
* Pokud budete i nadále mít problémy se zařízením, obraťte se na [Podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).