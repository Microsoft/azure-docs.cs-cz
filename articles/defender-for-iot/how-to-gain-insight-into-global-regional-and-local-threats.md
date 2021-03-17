---
title: Získejte přehled o globálních, regionálních i místních hrozbách
description: Získejte přehled o globálních, regionálních a místních hrozbách pomocí mapy webu v místní konzole pro správu.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: cde55f3c2a875c593c07ea05427f8075d8599196
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839372"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Získejte přehled o globálních, regionálních i místních hrozbách

Mapa webu v místní konzole pro správu vám pomůže dosáhnout plného pokrytí zabezpečení dělením sítě do geografických a logických segmentů, které odpovídají vaší obchodní topologii:

- **Úroveň zeměpisného zařízení**: lokalita odráží počet zařízení seskupených podle geografického umístění uvedeného na mapě. Ve výchozím nastavení vám Azure Defender pro IoT poskytuje celosvětovou mapu. Mapu aktualizujete tak, aby odrážela vaši organizační nebo obchodní strukturu. Použijte například mapu, která odráží weby v určité zemi, městě nebo průmyslovém areálu. Když se barva lokality změní na mapě, poskytne týmu SOC informace o kritickém stavu systému v zařízení.

  Mapa je interaktivní a umožňuje otevírání jednotlivých lokalit a informování o těchto lokalitách.

- **Globální logická vrstva**: obchodní jednotka představuje způsob, jak rozdělit vaši firmu do logických segmentů podle konkrétních odvětví. V takovém případě se vaše obchodní topologie projeví na mapě.

  Například globální společnost, která obsahuje skleněné továrny, plastové továrny a továrny automobilů, se dají spravovat jako tři různé obchodní jednotky. Fyzický web umístěný v Toronto zahrnuje tři různé skleněné výrobní linky, plastovou výrobní linku a výrobní linku modulu nákladní automobil. Tento web proto obsahuje zástupce všech tří obchodních jednotek.

- **Úroveň geografické oblasti**: Vytvořte oblasti pro rozdělení globální organizace do geografických oblastí. Například společnost, kterou jsme popsali, může používat oblasti Severní Amerika, západní Evropa a východní Evropa. Severní Amerika má továrny ze všech tří obchodních jednotek. Západní Evropa má automobilové továrny a skleněné továrny a východní Evropa obsahuje jenom továrny na plasty.

- **Místní úroveň logické segment**: zóna je logický segment v rámci lokality, která definuje, například funkční oblast nebo výrobní linku. Práce se zónami umožňuje vynucování zásad zabezpečení, které jsou relevantní pro definici zóny. Například lokalita, která obsahuje pět výrobních linek, se dá rozdělit do pěti zón.

- **Úroveň místního zobrazení**: místní zobrazení instalace s jedním senzorem poskytuje přehled o stavu provozu a zabezpečení připojených zařízení.

## <a name="work-with-site-map-views"></a>Práce s zobrazeními map webu

Místní Konzola pro správu poskytuje celkový přehled průmyslové sítě v mapě související s kontextem. Obecné zobrazení mapy prezentuje globální mapu vaší organizace s geografickým umístěním jednotlivých lokalit.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Snímek obrazovky s zobrazením podnikového mapování":::

### <a name="color-coded-map-views"></a>Barevně kódované zobrazení map

**Zelená**: počet událostí zabezpečení je pod prahovou hodnotou, kterou pro váš systém definoval Defender pro IoT. Není nutné provádět žádnou akci.

**Žlutá**: počet událostí zabezpečení je roven prahové hodnotě, kterou pro váš systém definoval Defender pro IoT. Zvažte zkoumání událostí.  

**Red**: počet událostí zabezpečení je nad prahovou hodnotou, kterou pro váš systém definoval Defender pro IoT. Proveďte okamžitou akci.

### <a name="risk-level-map-views"></a>Zobrazení mapy na úrovni rizika

**Posouzení rizik**: zobrazení posouzení rizik zobrazuje informace o rizicích webu. Informace o riziku pomáhají určit prioritu zmírnění a vytvořit mapu cest pro plánování vylepšení zabezpečení.

**Reakce na incidenty**: Získejte centralizované zobrazení všech nepotvrzených upozornění na jednotlivých lokalitách v celém podniku. Můžete přejít k podrobnostem a spravovat výstrahy zjištěné v konkrétní lokalitě.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Snímek obrazovky se zobrazením v podnikové mapě s odpovědí na incidenty":::

**Škodlivá aktivita**: Pokud byl zjištěn malware, zobrazí se stránka červeně. To znamená, že byste měli provést okamžitou akci.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Snímek obrazovky s zobrazením firemních map se škodlivými aktivitami":::

**Provozní výstrahy**: Toto zobrazení mapy pro systémy s nabízí lepší informace o tom, které systém může vyskytnout provozní incidenty, jako je například zastavení PLC, nahrávání firmwaru a nahrávání programu.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Snímek obrazovky se zobrazením podnikového mapování s provozními výstrahami.":::

Chcete-li zvolit zobrazení mapy:

1. Z mapy vyberte **výchozí zobrazení** .
2. Vyberte zobrazení.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Snímek obrazovky výchozího zobrazení mapy webu":::

## <a name="update-the-site-map-image"></a>Aktualizovat obrázek mapy webu

Defender pro IoT poskytuje výchozí celosvětovou mapu. Můžete ji změnit tak, aby odrážela vaši organizaci: mapu země nebo mapu města, například. 

Chcete-li nahradit mapu:

1. V levém podokně vyberte **nastavení systému**.

2. Vyberte možnost **změnit mapu webu** a nahrajte grafický soubor, aby se výchozí mapa nahradila.

## <a name="next-step"></a>Další krok

[Zobrazení upozornění](how-to-view-alerts.md)
