---
title: Urychlení pracovních postupů výstrah
description: Vylepšete pracovní postupy výstrah a incidentů.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 14d7a0de1cd29b8c07f90c759a4d423d7186fdb9
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838644"
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

4. Vyberte **Add** (Přidat).

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
| Ověřování | Změna firmwaru | Prohledávání |
| Neoprávněné chování komunikace | Neplatné příkazy | Provoz senzorů |
| Anomálie šířky pásma | Přístup k internetu | Podezření na malware |
| Přetečení vyrovnávací paměti | Selhání operace | Podezření na škodlivou aktivitu |
| Selhání příkazu | Provozní problémy |  |
| Změny konfigurace | Plánování |  |

Skupiny výstrah jsou předdefinované. Pokud chcete získat podrobnosti o výstrahách spojených se skupinami výstrah a o vytváření vlastních skupin výstrah, obraťte se na [Podpora Microsoftu](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="customize-alert-rules"></a>Přizpůsobení pravidel výstrah

Můžete přidat vlastní pravidla upozornění na základě informací zjištěných jednotlivými senzory. Můžete například definovat pravidlo, které instruuje senzor, který aktivuje výstrahu na základě zdrojové IP adresy, cílové IP adresy nebo příkazu (v rámci protokolu). Když senzor detekuje provoz definovaný v pravidle, vygeneruje se výstraha nebo událost.

Zpráva výstrahy indikuje, že výstraha aktivovala uživatelsky definované pravidlo.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Snímek obrazovky, který zobrazuje přizpůsobené výstrahy.":::

Vytvoření vlastního pravidla výstrahy:

1. V postranní nabídce snímače vyberte **vlastní výstrahy** .
1. Vyberte znaménko plus ( **+** ) a vytvořte pravidlo.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="Snímek obrazovky, který zobrazuje pravidlo definované uživatelem.":::

1. Definujte název pravidla.
1. V podokně **kategorie** vyberte kategorii nebo protokol.
1. Definujte konkrétní zdrojovou a cílovou IP adresu nebo adresu MAC nebo vyberte libovolnou adresu.
1. Přidejte podmínku. Seznam podmínek a jejich vlastností je jedinečný pro každou kategorii. Pro každou výstrahu můžete vybrat více než jednu podmínku.
1. Určuje, zda pravidlo aktivuje **alarm** nebo **událost**.
1. Přiřaďte výstrahy úroveň závažnosti.
1. Určete, zda bude tato výstraha obsahovat soubor PCAP.
1. Vyberte **Uložit**.

Toto pravidlo se přidá do seznamu **vlastní pravidla výstrah** , kde můžete zkontrolovat parametry základního pravidla, čas posledního spuštění pravidla a další. Můžete také povolit a zakázat pravidlo ze seznamu.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Snímek obrazovky přizpůsobeného pravidla přidaného uživatele":::

### <a name="see-also"></a>Viz také

[Zobrazení informací uvedených v výstrahách](how-to-view-information-provided-in-alerts.md)

[Spravovat událost výstrahy](how-to-manage-the-alert-event.md)
