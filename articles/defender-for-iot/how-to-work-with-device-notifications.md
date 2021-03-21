---
title: Práce s oznámeními zařízení
description: Oznámení obsahují informace o aktivitě sítě, které by mohly vyžadovat vaši pozornost, spolu s doporučeními pro zpracování této aktivity.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: cac8d609272be1d9f34b7e0d6404e0a0ea524df7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523562"
---
# <a name="work-with-device-notifications"></a>Práce s oznámeními zařízení

Oznámení obsahují informace o aktivitě sítě, které by mohly vyžadovat vaši pozornost, spolu s doporučeními pro zpracování této aktivity. Můžete například obdržet oznámení o:

- Neaktivní zařízení. Pokud už zařízení není součástí vaší sítě, můžete ho odebrat. Pokud je zařízení neaktivní, například kvůli omylu odpojenému od sítě, připojte zařízení znovu a oznámení zavřete.

- Na zařízení, které je aktuálně určené pouze adresou MAC, bylo zjištěno IP adresa. Odpověď tím, že autorizujete IP adresu pro zařízení.

Reakce na oznámení vylepšuje informace poskytované v mapě zařízení, inventáři zařízení a dotazech a sestavách dolování dat. Poskytuje také přehled o legitimních změnách sítě a potenciálních síťových konfiguracích.

Přístup k oznámením:

- Vyberte **nastavení systému** a pak vyberte **vylepšení dat**.

## <a name="notifications-vs-alerts"></a>Oznámení vs. výstrahy

Kromě přijímání oznámení o síťové aktivitě může docházet k *upozorněním*. Oznámení obsahují informace o změnách sítě nebo nevyřešených vlastnostech zařízení, které nepředstavují hrozbu. Výstrahy obsahují informace o odchylce sítě a změnách, které by mohly představovat hrozbu pro síť.

Postup zobrazení oznámení:

1. V levém podokně nabídky konzoly vyberte **Mapa zařízení** .

2. Vyberte ikonu **oznámení** . Červené číslo nad ikonou označuje počet oznámení.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Ikona oznámení":::

   Okno **oznámení** zobrazuje všechna oznámení, která senzor zjistil.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Oznámení.":::

## <a name="filter-the-notifications-window"></a>Filtrování okna oznámení

Pomocí vyhledávacích filtrů můžete zobrazit oznámení, která vás zajímají.

| Filtrovat podle | Description |
|--|--|
| Filtrovat podle typu | Zobrazit oznámení, která pokrývají určitou oblast zájmu. Můžete například zobrazit pouze oznámení o neaktivních zařízeních. |
| Filtrovat podle rozsahu kalendářních dat | Zobrazí oznámení, která pokrývají konkrétní časový rozsah. Můžete například zobrazit oznámení odesílaná pouze za poslední týden. |
| Vyhledat konkrétní informace | Vyhledat konkrétní oznámení |

## <a name="notification-events-and-responses"></a>Události a odpovědi na oznámení

Následující tabulka popisuje typy oznámení, které můžete obdržet, spolu s možnostmi jejich zpracování. Informace o zařízení můžete aktualizovat pomocí Doporučené hodnoty nebo oznámení zavřít. Po zavření oznámení se informace o zařízení neaktualizují o Doporučené informace. Pokud se znovu zjistí provoz, oznámení se pošle znovu.

| Typy událostí oznámení | Description | Odpovědi |
|--|--|--|
| Zjistila se nová IP adresa. | K zařízení je přidružená nová IP adresa. Je možné zjistit pět scénářů: <br /><br /> K zařízení se přidružil další IP adresa. Toto zařízení je také přidruženo k existující adrese MAC.<br /><br /> Zjistila se nová IP adresa pro zařízení, které používá existující adresu MAC. V současné době zařízení nekomunikuje pomocí IP adresy.<br /> <br /> Zjistila se nová IP adresa pro zařízení, které používá název pro rozhraní NetBIOS. <br /><br /> Jako rozhraní pro správu zařízení přidruženého k adrese MAC byla zjištěna IP adresa. <br /><br /> Zjistila se nová IP adresa pro zařízení, které používá virtuální IP adresu. | **Nastavení další IP adresy na zařízení** (sloučení zařízení) <br /> <br />**Nahradit existující IP adresu** <br /> <br /> **Zrušit**<br /> Odeberte oznámení. |
| Neaktivní zařízení | V zařízení se nezjistil provoz po dobu delší než 60 dní. | **Odstranit** <br /> Pokud toto zařízení není součástí vaší sítě, odeberte ho. <br /><br />**Zrušit** <br /> Pokud je zařízení součástí vaší sítě, odeberte oznámení. Pokud je zařízení neaktivní (například kvůli omylu odpojenému odpojení od sítě), zavřete oznámení a znovu připojte zařízení. |
| Nová zařízení | Podsíť zahrnuje zařízení, které není definované v podsíti ICS. <br /><br /> Každá podsíť, která obsahuje alespoň jeden zařízení, může být definovaná jako podsíť ICS. To pomáhá rozlišovat mezi zařízením OT a IT na mapě. | **Nastavit jako podsíť ICS** <br /> <br /> **Zrušit** <br />Odeberte oznámení, pokud zařízení není součástí podsítě. |
| Nejsou nakonfigurované žádné podsítě. | V síti nejsou aktuálně nakonfigurované žádné podsítě. <br /><br /> Nakonfigurujte podsítě pro lepší reprezentaci v mapě a možnost rozlišit mezi zařízeními a IT. | **Otevřete konfiguraci podsítí** a nakonfigurujte podsítě. <br /><br />**Zrušit** <br /> Odeberte oznámení. |
| Změny operačního systému | K zařízení byl přidružen jeden nebo více nových operačních systémů. | Vyberte název nového operačního systému, který chcete přidružit k zařízení.<br /><br /> **Zrušit** <br /> Odeberte oznámení. |
| Nové podsítě | Nové podsítě byly zjištěny. | **Learn**<br />Přidejte podsíť automaticky.<br />**Otevřít konfiguraci podsítě**<br />Přidejte všechny informace o chybějící podsíti.<br />**Zrušit**<br />Odeberte oznámení. |
| Změny typu zařízení | K zařízení byl přidružen nový typ zařízení. | **Nastavit jako {...}**<br />Přidružit nový typ k zařízení<br />**Zrušit**<br />Odeberte oznámení. |

## <a name="respond-to-many-notifications-simultaneously"></a>Reakce na mnoho oznámení současně

Možná budete muset zvládnout několik oznámení současně. Například:

- Pokud došlo k upgradu operačního systému na velkou sadu síťových serverů, můžete dát senzorovi pokyn, aby se dozvěděl nové verze serveru pro všechny upgradované servery. 

- Pokud byla skupina zařízení v určitém řádku postaralá a už není aktivní, můžete dát senzorovi, aby tato zařízení odebral z konzoly.

Můžete dát snímači pokyn, aby na více zařízeních použili nově zjištěné informace, nebo ho ignorovat.   

Zobrazení oznámení a zpracování oznámení:

1. Použijte možnost **filtrovat podle typu, rozsah dat** nebo možnost **Vybrat vše** . V případě potřeby zrušte výběr oznámení.

2. Dejte senzorovi pokyn, aby na vybraná zařízení použili nově zjištěné informace výběrem možnosti **informace**. Nebo Dejte senzoru pokyn, aby ignoroval nově zjištěné informace výběrem možnosti **Zavřít**. Zobrazí se počet oznámení, která lze současně zjistit a zavřít, spolu s počtem oznámení, která je nutné zpracovat jednotlivě.

**Nové IP adresy** a žádné nakonfigurované **podsítě** nemůžou být zpracovávány současně. Vyžadují ruční potvrzení.

## <a name="improve-device-os-classification-data-enhancement"></a>Vylepšení klasifikace operačního systému zařízení: vylepšení dat 

Senzor nepřetržitě automatické zjišťování nových zařízení. Také automatické zjišťování změn u dříve zjištěných zařízení, včetně typů operačních systémů.

Za určitých okolností se konflikty můžou zjistit ve zjištěných operačních systémech. K tomu může dojít, protože máte verzi operačního systému, která odkazuje na stolní nebo serverové systémy. Pokud k tomu dojde, obdržíte oznámení s nepovinnými klasifikacemi OS.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Vylepšete data.":::

Prozkoumejte doporučení, aby bylo možné rozšířit klasifikaci OS. Tyto informace se zobrazí v inventáři zařízení, sestavách dolování dat a dalších zobrazeních. Může také zlepšit přesnost výstrah, hrozeb a analýzy rizik.

Když přijmete doporučení, informace o typu operačního systému se ve snímači aktualizují.

## <a name="see-also"></a>Viz také

[Zobrazení upozornění](how-to-view-alerts.md)
