---
title: Urychlení pracovních postupů výstrah
description: Vylepšete pracovní postupy výstrah a incidentů.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 64e81e246ec62c8995d0e31629b4f21a2c1096b0
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522542"
---
# <a name="accelerate-alert-workflows"></a>Urychlení pracovních postupů výstrah

Tento článek popisuje, jak zrychlit pracovní postupy výstrah pomocí komentářů výstrah, skupin výstrah a vlastních pravidel výstrah v Azure Defenderu pro IoT.  Tyto nástroje vám pomůžou:

- Analyzujte a spravujte velký objem událostí výstrah zjištěných ve vaší síti.

- Přesné určení a zpracování konkrétní síťové aktivity.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Urychlení pracovních postupů pomocí komentářů s výstrahami

Pracujte s komentáři k výstrahám a vylepšete komunikaci mezi jednotlivci a týmy během vyšetřování události výstrahy.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="Snímek obrazovky zobrazující škodlivou aktivitu":::

Použijte komentáře výstrahy ke zlepšení:

- **Kroky pracovního postupu**: Zajistěte kroky zmírnění výstrah.

- **Následná akce pracovního postupu**: upozorní na to, že byly provedeny kroky.

- **Pokyny k pracovnímu postupu**: Poskytněte doporučení, přehledy nebo upozornění týkající se události.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="Snímek obrazovky, který zobrazuje komentáře k výstrahám.":::

V každé výstraze se zobrazí seznam dostupných možností. Uživatelé můžou vybrat jednu nebo několik zpráv.

Přidání komentářů výstrah:

1. V postranní nabídce vyberte **nastavení systému**.

2. V okně **nastavení systému** vyberte možnost **Komentáře výstrahy**.

3. Do pole **přidat komentáře** zadejte text komentáře. Použijte až 50 znaků. Čárky nejsou přípustné.

4. Vyberte **Přidat**.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Urychlení pracovních postupů incidentů pomocí skupin výstrah

Skupiny výstrah umožňují SOC týmům zobrazovat a filtrovat výstrahy ve svých řešeních SIEM a pak je spravovat na základě podnikových zásad zabezpečení a obchodních priorit. Například výstrahy týkající se nových detekcí jsou uspořádány do skupiny zjišťování. Tato skupina obsahuje výstrahy, které se týkají detekce nových zařízení, nových sítí VLAN, nových uživatelských účtů, nových adres MAC a dalších.

Skupiny výstrah se používají při vytváření pravidel předávání pro následující partnerská řešení:

  - Servery syslog

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="Snímek obrazovky s vytvořením pravidla předávání":::

Příslušná skupina výstrah se zobrazí ve výstupních řešeních partnerů. 

### <a name="requirements"></a>Požadavky

Tato skupina výstrah se zobrazí v podporovaných partnerských řešeních s následujícími předponami:

- **Cat** pro QRadar, ArcSight, syslog CEF, syslog LEEF

- **Skupina výstrah** pro textové zprávy syslog

- **alert_group** pro objekty syslog

Tato pole by se měla nakonfigurovat v partnerském řešení, aby se zobrazil název skupiny výstrah. Pokud ke skupině výstrah není přidružená žádná výstraha, zobrazí se pole v partnerském řešení **na**.

### <a name="default-alert-groups"></a>Výchozí skupiny výstrah

Následující skupiny výstrah jsou automaticky definovány:
|  |  |  |
|--|--|--|
| Neobvyklé chování komunikace | Vlastní výstrahy | Vzdálený přístup |
| Neobvyklé chování komunikace HTTP | Zjišťování | Příkazy pro restartování a zastavení |
| Authentication | Změna firmwaru | Prohledávání |
| Neoprávněné chování komunikace | Neplatné příkazy | Provoz senzorů |
| Anomálie šířky pásma | Přístup k internetu | Podezření na malware |
| Přetečení vyrovnávací paměti | Selhání operace | Podezření na škodlivou aktivitu |
| Selhání příkazu | Provozní problémy |  |
| Změny konfigurace | Plánování |  |

Skupiny výstrah jsou předdefinované. Pokud chcete získat podrobnosti o výstrahách spojených se skupinami výstrah a o vytváření vlastních skupin výstrah, obraťte se na [Podpora Microsoftu](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="customize-alert-rules"></a>Přizpůsobení pravidel výstrah

Použijte vlastní pravidla upozornění k přesnější přesnému určení aktivity, které vás zajímají. 

Můžete přidat vlastní pravidla upozornění na základě:

- Kategorie, například protokol, port nebo soubor.
- Zdrojové a cílové adresy
- Podmínka na základě zvolené kategorie, například funkce přidružené k protokolu, názvu souboru, portu nebo přenosového čísla.
- Podmínka založená na referenci data a času, například v případě zjištění určitého dne nebo určité části dne.

Pokud senzor detekuje aktivitu popsanou v pravidle, bude odeslána výstraha.
informace, které jednotlivé senzory zjišťují. Můžete například definovat pravidlo, které instruuje senzor, který aktivuje výstrahu na základě zdrojové IP adresy, cílové IP adresy nebo příkazu (v rámci protokolu). Když senzor detekuje provoz definovaný v pravidle, vygeneruje se výstraha nebo událost.

Pomocí akcí pravidla výstrah můžete také dát programu Defender pro IoT:

- Povolí uživatelům přístup k souboru PCAP z výstrahy.
- Přiřaďte Závažnost výstrahy.
- Vygenerujte událost namísto výstrahy. Zjištěné informace se zobrazí v časové ose události.

:::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="Snímek obrazovky, který zobrazuje pravidlo definované uživatelem.":::

Zpráva výstrahy indikuje, že výstraha aktivovala uživatelsky definované pravidlo.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Snímek obrazovky, který zobrazuje přizpůsobené výstrahy.":::

Vytvoření vlastního pravidla výstrahy:

1. V postranní nabídce snímače vyberte **vlastní výstrahy** .
1. Vyberte znaménko plus ( **+** ) a vytvořte pravidlo.
1. Definujte název pravidla.
1. V podokně **kategorie** vyberte kategorii nebo protokol.
1. Definujte konkrétní zdrojovou a cílovou IP adresu nebo adresu MAC nebo vyberte libovolnou adresu.
1. Definujte jednu nebo několik podmínek pravidla. Lze vytvořit dvě kategorie podmínek:
    - Podmínky na základě jedinečných hodnot přidružených ke zvolené kategorii. Vyberte Přidat a definujte hodnoty.
    - Podmínky založené na době, kdy byla aktivita zjištěna. V části detekce vyberte časové období a den, ve kterém se musí objevit zjišťování, aby se výstraha mohla odeslat. Výstrahu můžete odeslat, pokud je aktivita zjištěna kdykoli, během nebo po pracovní době. Pomocí možnosti definovat pracovní dobu můžete pro vaši organizaci dát programu Defender pro pracovní dobu IoT.
1. Definovat akce pravidla: 
    - Určuje, zda pravidlo aktivuje **alarm** nebo **událost**.
    - Přiřaďte výstrahy úroveň závažnosti.
    - Určete, zda bude tato výstraha obsahovat soubor PCAP.
1. Vyberte **Uložit**.

Toto pravidlo se přidá do seznamu **vlastní pravidla výstrah** , kde můžete zkontrolovat parametry základního pravidla, čas posledního spuštění pravidla a další. Můžete také povolit a zakázat pravidlo ze seznamu.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Snímek obrazovky přizpůsobeného pravidla přidaného uživatele":::

## <a name="next-steps"></a>Další kroky

[Zobrazení informací uvedených v upozorněních](how-to-view-information-provided-in-alerts.md)

[Správa události upozornění](how-to-manage-the-alert-event.md)
