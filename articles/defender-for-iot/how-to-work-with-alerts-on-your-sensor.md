---
title: O výstrahách senzorů
description: Pracujte s výstrahami, které vám pomůžou zlepšit zabezpečení a provoz vaší sítě.
ms.date: 11/30/2020
ms.topic: how-to
ms.openlocfilehash: 178d3aedb44c29f53aab481894defeb4b9355d39
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781343"
---
# <a name="about-sensor-alerts"></a>O výstrahách senzorů

Výstrahy vám pomůžou zlepšit zabezpečení a provoz vaší sítě. Výstrahy obsahují informace o:

- Odchylky od autorizované síťové aktivity

- Protokoly a provozní anomálie

- Podezřelý provoz malwaru

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Zjistit kontrolu adresy.":::

Možnosti správy výstrah umožňují uživatelům:

- Dejte senzorům pokyn, aby zjistili, jaké aktivity se zjistila jako autorizovaný provoz.

- Potvrďte kontrolu výstrahy.

- Vydejte senzory k ztlumení událostí zjištěných pomocí identických zařízení a srovnatelných přenosů.

K dispozici jsou další nástroje, které vám pomůžou zdokonalit a urychlit šetření výstrah. Například:

  - Přidejte komentáře k pokynům pro kontrolory výstrah.

  - Vytvořte skupiny výstrah pro zobrazení na SOC řešeních. 

  - Vyhledat konkrétní výstrahy; Kontrola souvisejících souborů PCAP; Zobrazit zjištěná zařízení a další připojená zařízení v mapě zařízení nebo odesílat podrobnosti o výstrahách partnerským systémům.

  - Dopředné výstrahy dodavatelům partnerů: systémy SIEM, systémy MSSP a další.

## <a name="alerts-and-engines"></a>Výstrahy a moduly

Výstrahy se aktivují, když senzory zjišťují změny v síťovém provozu a chování, které vyžadují vaši pozornost. Tento článek popisuje druh výstrah, které každý modul spouští.

| Typ upozornění | Popis |
|-|-|
| Upozornění na porušení zásad | Aktivováno, když modul porušení zásad detekuje odchylku od dříve zjištěného provozu. Například: <br /> – Zjistilo se nové zařízení.  <br /> – V zařízení se zjistila nová konfigurace. <br /> – Zařízení, které není definované jako programové zařízení, provede změnu v programování. <br /> – Změnila se verze firmwaru. |
| Výstrahy porušení protokolu | Aktivováno, když modul porušení protokolu detekuje struktury paketů nebo hodnoty polí, které nevyhovují specifikaci protokolu. | 
| Provozní výstrahy | Aktivuje se, když provozní modul detekuje provozní incidenty sítě nebo zařízení, které nefunguje. Například síťové zařízení bylo zastaveno prostřednictvím příkazu zastavit PLC nebo rozhraní na senzoru zastavilo monitorování provozu. |
| Výstrahy malwaru | Aktivováno, když malware zjistí aktivitu škodlivou v síti. Například modul detekuje známý útok, jako je například Conficker. |
| Výstrahy anomálií | Aktivováno, když modul anomálií detekuje odchylku. Například zařízení provádí kontrolu sítě, ale není definováno jako skenovací zařízení. |

K dispozici jsou nástroje pro povolení a zakázání modulů senzorů. Výstrahy se nespouštějí z modulů, které jsou zakázané. Viz téma [řízení sledování provozu](how-to-control-what-traffic-is-monitored.md).

## <a name="alerts-and-sensor-reporting"></a>Výstrahy a vytváření sestav senzorů

Aktivity, které se projeví v upozorněních, se dají vypočítat při generování dolování dat, posouzení rizik a vektorových sestav útoků. Když tyto události spravujete, senzor tyto sestavy aktualizuje odpovídajícím způsobem.

Například:

  - Neoprávněné připojení mezi zařízením v definované podsíti a zařízeními umístěnými mimo podsíť (Public) se zobrazí v části dolování dat *Internet Internetová aktivita* a hodnocení rizik *Internetová připojení* . Po autorizaci těchto zařízení se tato zařízení počítají při generování těchto sestav.

  - V sestavách hodnocení rizik jsou hlášeny malware události zjištěné v síťových zařízeních. Pokud jsou výstrahy týkající se událostí malwaru *ztlumeny*, ovlivněná zařízení nebudou v sestavě posouzení rizik vypočítána.

## <a name="see-also"></a>Viz také

- [Výukové a inteligentní školicí režimy IT](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
- [Zobrazení informací uvedených v upozorněních](how-to-view-information-provided-in-alerts.md)
- [Správa události upozornění](how-to-manage-the-alert-event.md)
- [Urychlení pracovních postupů výstrah](how-to-accelerate-alert-incident-response.md)
