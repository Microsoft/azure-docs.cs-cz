---
title: StorSimple 8000 series hardwarové součásti a stav | Dokumentace Microsoftu
description: Další informace o monitorování hardwarové součásti zařízení StorSimple ve službě Správce zařízení StorSimple.
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
ms.openlocfilehash: 58007eea9ce25423bc3a9c2847de42db04be43eb
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42059082"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Použití služby Správce zařízení StorSimple pro monitorování hardwarových součástí a stav

## <a name="overview"></a>Přehled
Tento článek popisuje různé fyzické a logické součásti v vaše místní zařízení StorSimple řady 8000. Také vysvětluje, jak sledovat stav součásti zařízení pomocí **zdravotní stav a hardware** okno ve službě Správce zařízení StorSimple.

**Zdravotní stav a hardware** okno zobrazuje stav hardwaru všechny součásti zařízení StorSimple.

V seznamu komponent pro 8100 existují tři části, které popisují:

* **Sdílené komponenty** – ty nejsou součástí do řadičů, jako jsou diskové jednotky, skříň, napájení a chlazení modulu (PCM) součásti a teploty v PCM, napětí řádek a řádek aktuální senzory.
* **Komponenty řadiče 0** – komponenty, které se nacházejí na řadiči 0, například řadič, rozšíření SAS a konektor, řadič teploty senzorů a různých síťových rozhraní.
* **Komponenty řadiče 1** – komponenty, které tvoří podobné těm, které jsou podrobně popsané pro řadič 0 řadič 1.

Zařízení s 8600 má další součásti, které odpovídají skříň rozšířené Bunch disky (EBOD). V seznamu součástí jsou pět částí. Z nich jsou tři oddíly, které obsahují komponenty ve primárního skříni a jsou stejné jako ty, které jsou popsány pro 8100. Existují dva další oddíly pro EBOD skříň, které popisují:

* **Komponenty řadiče EBOD 0** – komponenty, které se nacházejí na skříň EBOD 0, jako je například kontroleru EBOD teploty senzorů SAS rozšíření a konektoru a kontroleru.
* **Komponenty EBOD řadiče 1** – komponent tvořících skříň EBOD 1, podobně jako tyto podrobné pro skříň EBOD 0.
* **Komponenty sdílené skříně EBOD** – prezentovat komponenty EBOD skříň a PCM, které nejsou součástí kontroleru EBOD.

> [!NOTE]
> **Stav hardwaru není k dispozici pro cloudové zařízení StorSimple (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitorování stavu hardwaru
Proveďte následující kroky a zobrazení stavu hardwaru zařízení součásti:

1. Přejděte do **zařízení**, vyberte konkrétní zařízení StorSimple. Přejděte na **sledování > Stav hardwaru**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Vyhledejte **hardwarové součásti** části a pak vyberte z dostupných komponentách. Jednoduše klikněte na popisek součásti v seznamu rozbalit a zobrazit stav různých součástí zařízení. Najdete v článku [seznamu podrobné komponent pro primární skříň](#component-list-for-primary-enclosure-of-storsimple-device) a [seznamu podrobné komponenty EBOD skříň](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Použijte následující schéma kódování interpretovat stav součásti:
   
   * **Zelené zaškrtnutí** – označuje součást v dobrém stavu s **OK** stav.
   * **Žlutý** – označuje degradovaný součástí **upozornění** stavu.
   * **Červený vykřičník** – Denotes selhání součásti, která má **selhání** stav.
   * **Bílé pomocí černého textového** – označuje komponenty, která není k dispozici.
   
   Na následujícím snímku obrazovky zobrazuje zařízení, která obsahuje součásti v **OK**, **upozornění**, a **selhání** stavu.
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Rozšíření **sdílené komponenty seznamu**, vidíme, že jsou degradovány souboru NVRAM a clusteru.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Rozšíření **komponenty řadiče 1** seznamu, vidíme, že na uzlu clusteru se nezdařilo.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Pokud narazíte na součást, která se nepoužívá **pořádku** stavu, obraťte se na Microsoft Support. Pokud výstrahy jsou povolené na vašem zařízení, dostanete e-mailové upozornění. Pokud je třeba nahradit komponentu selhání hardwaru, přečtěte si téma [StorSimple hardwarové komponenty nahrazení](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Seznam komponent pro primární skříň zařízení StorSimple
Následující tabulka uvádí fyzické a logické součásti, které jsou obsažené ve primárního skříň (k dispozici v nástroji 8100 nebo 8600) vašeho místního zařízení StorSimple.

| Komponenta | Modul | Typ | Umístění | Pole vyměnitelná jednotka (FRU)? | Popis |
| --- | --- | --- | --- | --- | --- |
| Jednotka ve slotu [0-11] |Diskové jednotky |Fyzické |Shared |Ano |Pro každou SSD nebo HDD disky ve primárního skříni se zobrazí jeden řádek. |
| Snímač okolní teploty |Skříň |Fyzické |Shared |Ne |Měří teploty v rámci skříni. |
| Senzor teploty uprostřed rovina |Skříň |Fyzické |Shared |Ne |Měří teploty rovině uprostřed. |
| Akustický signál |Skříň |Fyzické |Shared |Ne |Označuje, zda je podsystém akustický signál ve skříni funkční. |
| Skříň |Skříň |Fyzické |Shared |Ano |Indikuje přítomnost skříni. |
| Nastavení skříně |Skříň |Fyzické |Shared |Ne |Odkazuje na přední panel skříni. |
| Řádek napěťovými senzory |PCM |Fyzické |Shared |Ne |Mnoho řádku napěťovými senzory mít zobrazený, jejich stav označující, zda je měřené napětí v rámci proti chybám. |
| Aktuální řádek senzorů |PCM |Fyzické |Shared |Ne |Řada snímačů aktuální řádek mají jejich stav zobrazuje, který určuje, zda je aktuální měřené v toleranci. |
| Senzorů teploty v PCM |PCM |Fyzické |Shared |Ne |Řadu senzorů teploty, jako je například vstupu a senzory Hotspot mají jejich stav zobrazen, určující, zda je teplota měřená v rámci proti chybám. |
| Zdroj napájení [0-1] |PCM |Fyzické |Shared |Ano |Pro každou napájení ve dvou PCMs nachází zadní zařízení se zobrazí jeden řádek. |
| Chlazení [0-1] |PCM |Fyzické |Shared |Ano |Pro každý ze čtyř chladicí ventilátory, které se nacházejí ve dvou PCMs se zobrazí jeden řádek. |
| Baterie [0-1] |PCM |Fyzické |Shared |Ano |Pro každý záložní baterie modulů, které jsou v PCM sedí se zobrazí jeden řádek. |
| Metis |neuvedeno |Logické |Shared |neuvedeno |Zobrazí stav baterie: Určuje, zda potřebujete účtování a se začali přibližovat ukončenou životností. |
| Cluster |neuvedeno |Logické |Shared |neuvedeno |Zobrazí stav clusteru, který je vytvořen mezi dva moduly integrované kontroleru. |
| Uzel clusteru |neuvedeno |Logické |Shared |neuvedeno |Označuje stav řadiče jako součást clusteru. |
| Kvorum clusteru |neuvedeno |Logické | |neuvedeno |Indikuje přítomnost členství většinou disku ve fondu úložiště pevný disk. |
| Datový prostor pevného disku |neuvedeno |Logické |Shared |neuvedeno |Prostor úložiště, který se používá pro data do fondu úložiště pevném disku (HDD). |
| Prostor správy pevného disku |neuvedeno |Logické |Shared |neuvedeno |Místa vyhrazeného v fond úložiště pevného disku pro úlohy správy. |
| Místo kvora pevného disku |neuvedeno |Logické |Shared |neuvedeno |Místa vyhrazeného v fond úložiště pevného disku pro kvorum clusteru. |
| Místo nahrazení pevného disku |neuvedeno |Logické |Shared |neuvedeno |Místa vyhrazeného v fond úložiště pevného disku pro nahrazení řadiče. |
| Datový prostor disku SSD |neuvedeno |Logické |Shared |neuvedeno |Prostor úložiště využitý k datům ve fondu úložiště SSD (SOLID-State drive). |
| Prostor NVRAM disku SSD |neuvedeno |Logické |Shared |neuvedeno |Prostor úložiště ve fondu úložiště SSD, který je vyhrazen pro NVRAM logiku. |
| Fond úložiště pevného disku |neuvedeno |Logické |Shared |neuvedeno |Zobrazí stav logického úložiště fondu, který je vytvořen ze zařízení pevných disků. |
| Fond úložiště disku SSD |neuvedeno |Logické |Shared |neuvedeno |Zobrazí stav logického úložiště fondu, který je vytvořen ze zařízení SSD. |
| Řadič [0-1] [stav] |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ano |Zobrazuje stav řadiče, a zda je v režimu aktivní nebo pohotovostní ve skříni. |
| Teploty senzorů v kontroleru |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Mnoho senzorů teploty, jako je například modul vstupně-výstupních operací, teploty procesoru, DIMM a PCIe senzorů máte zobrazený, jejich stav označující, zda došlo k teplotu v rámci proti chybám. |
| Rozšíření SAS |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav sériového portu připojený rozšíření SCSI (SAS), který se používá pro připojení integrované úložiště ke kontroleru. |
| Konektor SAS [0-1] |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav každého konektoru SAS, který se používá pro připojení integrované úložiště k rozšířením SAS. |
| Propojení sběrnice SBB uprostřed roviny propojení |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav uprostřed roviny konektor, který slouží k připojení každého řadiče se uprostřed rovinou. |
| Jádro procesoru |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav jader procesoru v rámci každého řadiče. |
| Napájení elektroniky skříně |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav napájení systému používá přílohu. |
| Diagnostika elektroniky skříně |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav subsystémů diagnostiky poskytované kontroleru. |
| Řadič pro správu základní desky (BMC) |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav řadič pro správu (BMC), což je specializovaná služba procesor, který monitoruje hardwarové zařízení prostřednictvím snímačů a komunikuje se na správce systému prostřednictvím připojení k nezávislé. |
| Ethernet |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav jednotlivých síťových rozhraní, to znamená, správu a porty data k dispozici na kontroleru. |
| NVRAM |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav NVRAM paměť s náhodným přístupem stálé zajištěná baterie, který slouží k uchování aplikace důležitých informací v případě výpadku napájení. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Seznam komponent pro EBOD skříň zařízení StorSimple
Následující tabulka uvádí fyzické a logické součásti, které jsou obsaženy ve skříni EBOD (k dispozici pouze v modelu 8600) z vašeho místního zařízení StorSimple.

| Komponenta | Modul | Typ | Umístění | FRU? | Popis |
| --- | --- | --- | --- | --- | --- |
| Jednotka ve slotu [0-11] |Diskové jednotky |Fyzické |Shared |Ano |Pro každou HDD disků ve skříni EBOD se zobrazí jeden řádek. |
| Snímač okolní teploty |Skříň |Fyzické |Shared |Ne |Měří teploty v rámci skříni. |
| Senzor teploty uprostřed rovina |Skříň |Fyzické |Shared |Ne |Měří teploty rovině uprostřed. |
| Akustický signál |Skříň |Fyzické |Shared |Ne |Označuje, zda je podsystém akustický signál ve skříni funkční. |
| Skříň |Skříň |Fyzické |Shared |Ano |Indikuje přítomnost skříni. |
| Nastavení skříně |Skříň |Fyzické |Shared |Ne |Odkazuje na OPS nebo předního panelu skříni. |
| Řádek napěťovými senzory |PCM |Fyzické |Shared |Ne |Mnoho řádku napěťovými senzory mít zobrazený, jejich stav označující, zda je měřené napětí v rámci proti chybám. |
| Aktuální řádek senzorů |PCM |Fyzické |Shared |Ne |Řada snímačů aktuální řádek mají jejich stav zobrazuje, který určuje, zda je aktuální měřené v toleranci. |
| Senzorů teploty v PCM |PCM |Fyzické |Shared |Ne |Řadu senzorů teploty, jako je například vstupu a senzory aktivního bodu máte zobrazený, jejich stav označující, zda je teplota měřená v rámci proti chybám. |
| Zdroj napájení [0-1] |PCM |Fyzické |Shared |Ano |Pro každou napájení ve dvou PCMs nachází zadní zařízení se zobrazí jeden řádek. |
| Chlazení [0-1] |PCM |Fyzické |Shared |Ano |Pro každý ze čtyř chladicí ventilátory, které se nacházejí ve dvou PCMs se zobrazí jeden řádek. |
| Místní úložiště [HDD] |neuvedeno |Logické |Shared |neuvedeno |Zobrazí stav logického úložiště fondu, který je vytvořen ze zařízení pevných disků. |
| Řadič [0-1] [stav] |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ano |Zobrazuje stav řadiče EBOD modulu. |
| Teploty senzorů v EBOD |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Mnoho senzorů teploty z každého řadiče máte zobrazený, jejich stav označující, zda došlo k teplotu v rámci proti chybám. |
| Rozšíření SAS |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav rozšíření SAS, který se používá pro připojení integrované úložiště ke kontroleru. |
| Konektor SAS [0-2] |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav každého konektoru SAS, který se používá pro připojení integrované úložiště k rozšířením SAS. |
| Propojení sběrnice SBB uprostřed roviny propojení |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav uprostřed roviny konektor, který slouží k připojení každého řadiče se uprostřed rovinou. |
| Napájení elektroniky skříně |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav napájení systému používá přílohu. |
| Diagnostika elektroniky skříně |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav subsystémů diagnostiky poskytované kontroleru. |
| Připojení ke kontroleru zařízení |VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |Fyzické |Kontroler |Ne |Označuje stav připojení mezi řadiči zařízení a modul EBOD vstupně-výstupních operací. |

## <a name="next-steps"></a>Další postup
* Pokud chcete použít ke správě zařízení ve službě Správce zařízení StorSimple, přejděte na [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).
* Pokud potřebujete řešit komponenty zařízení, která je ve stavu degradované nebo ve stavu selhání, podívejte se na [StorSimple monitorování indikátory](storsimple-monitoring-indicators.md).
* Nahradit neúspěšné hardwarová komponenta, naleznete v tématu [StorSimple hardwarové komponenty nahrazení](storsimple-hardware-component-replacement.md).
* Pokud budete nadále dochází k problémům zařízení [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md).

