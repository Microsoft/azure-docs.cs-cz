---
title: Správa událostí výstrah
description: Spravujte události výstrah zjištěné ve vaší síti.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 6a1a4d13ee9f9032c739773b678600d52dec82b8
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523919"
---
# <a name="manage-alert-events"></a>Správa událostí výstrah

Pro správu událostí výstrah jsou k dispozici následující možnosti:

 | Akce | Popis |
 |--|--|
 | **Learn** | Autorizovat zjištěnou událost. Další informace najdete v tématu [o událostech učení a neučení](#about-learning-and-unlearning-events). |
 | **Potvrdit** | Skrýt výstrahu jednou pro zjištěnou událost. Po opětovném zjištění události bude výstraha znovu aktivována. Další informace najdete v tématu [o potvrzování a nepotvrzujících událostech](#about-acknowledging-and-unacknowledging-events). |
 | **Vypnutí** | Nepřetržitě ignorujte aktivitu se stejnými zařízeními a srovnatelnými přenosy. Další informace najdete v tématu [popisujícím ztlumení a odztlumení událostí](#about-muting-and-unmuting-events). |
 
Můžete také exportovat informace o výstrahách.
## <a name="about-learning-and-unlearning-events"></a>Události učení a neučení

Události indikující odchylky zjištěné sítě mohou odrážet platné změny sítě. Příklady mohou zahrnovat nová oprávněná zařízení, která se připojila k síti nebo k autorizované aktualizaci firmwaru.

Když vyberete **informace**, senzor považuje provoz, konfigurace nebo činnost za platné. To znamená, že výstrahy už nebudou pro danou událost aktivované. Také to znamená, že událost nebude vypočtena, pokud senzor generuje hodnocení rizik, vektor útoku a další sestavy.

Například obdržíte výstrahu, která indikuje aktivitu kontroly adresy v zařízení, které dříve nedefinoval síťový skener. Pokud bylo toto zařízení pro účely kontroly přidáno do sítě, můžete dát senzorovi pokyn, aby zařízení zjistil jako skenovací zařízení.

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="Adresa zjištěná v okně vyhledávání.":::

Zjištěné události se dají zjistit. Když senzor zjistí události, znovu aktivuje výstrahy související s touto událostí.

## <a name="about-acknowledging-and-unacknowledging-events"></a>O potvrzování a nepotvrzujících událostech

V některých situacích možná nebudete chtít, aby se senzor dozvěděl o zjištěné události, nebo aby tato možnost nebyla k dispozici. Místo toho může incident vyžadovat zmírnění. Příklad:

- **Zmírnění síťové konfigurace nebo zařízení**: zobrazí se výstraha oznamující, že v síti bylo zjištěno nové zařízení. Při šetření zjistíte, že zařízení je neautorizované síťové zařízení. Incident můžete zpracovat odpojením zařízení od sítě.
- **Aktualizace konfigurace senzoru**: zobrazí se výstraha oznamující, že server inicializoval nadměrné množství vzdálených připojení. Tato výstraha se aktivovala, protože prahové hodnoty anomálií snímače byly definované tak, aby během jedné minuty aktivovaly výstrahy nad určitým počtem relací. Incident můžete zpracovat tak, že aktualizujete prahové hodnoty.

Po provedení zmírnění nebo šetření můžete dát senzorovi pokyn, aby výstrahu skryl, a to výběrem možnosti **Potvrdit**. Pokud je událost znovu rozpoznána, bude výstraha znovu aktivována.

Postup vymazání výstrahy:

  - Vyberte **Potvrdit**.

Chcete-li znovu zobrazit výstrahu:

  - Přejděte na výstrahu a vyberte **Nepotvrdit**.

Pokud je potřeba další šetření, Nepotvrzujte výstrahy.

## <a name="about-muting-and-unmuting-events"></a>O ztlumení a odztlumení událostí

Za určitých okolností můžete chtít, aby váš senzor mohl ignorovat konkrétní scénář ve vaší síti. Příklad:

  - Modul **anomálií** aktivuje výstrahu ve špičkě šířky pásma mezi dvěma zařízeními, ale špička je pro tato zařízení platná.

  - Modul **porušení protokolu** aktivuje výstrahu u odchylky protokolu zjištěné mezi dvěma zařízeními, ale odchylka je platná mezi zařízeními.

V těchto situacích není učení k dispozici. Když se naučíte postupovat a chcete upozornění potlačit a odebrat toto zařízení při výpočtu rizik a vektorů útoku, můžete místo toho ztlumení události upozornění.

> [!NOTE] 
> Nemůžete ztlumit události, ve kterých je internetové zařízení definované jako zdroj nebo cíl.

### <a name="what-alert-activity-is-muted"></a>Jaká aktivita výstrah je ztlumená?

Neztlumený scénář zahrnuje síťová zařízení a provoz zjištěný pro událost. Název výstrahy popisuje neztlumený provoz.

Zařízení nebo zařízení, která jsou ztlumená, se zobrazí jako obrázek v upozornění. Pokud se zobrazí dvě zařízení, konkrétní výstrahy s výstrahou mezi nimi budou ztlumené.

**Příklad 1**

Pokud je událost ztlumená, je ignorována vždy, když primární (zdroj) pošle sekundární (cílový) neplatný kód funkce definovaný dodavatelem.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="Bylo přijato sekundární zařízení.":::

**Příklad 2**

Pokud je událost ztlumená, ignoruje se vždy, když zdroj pošle hlavičku HTTP s neplatným obsahem, bez ohledu na cíl.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Snímek obrazovky s neplatným obsahem záhlaví HTTP":::

**Po ztlumení události:**

- Výstraha bude k dispozici v zobrazení **potvrzené** výstrahy, dokud nebude ztlumeně.

- Akce ztlumení se zobrazí v **časové ose události**.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Byla zjištěna a ztlumená událost.":::

- Senzor přepočítá zařízení při generování posouzení rizik, vektoru útoku a dalších sestav. Pokud jste například vypustili výstrahu, která zjistila škodlivý provoz na zařízení, nebude toto zařízení v sestavě posouzení rizik vypočítáno.

**Vypnutí a ztlumení výstrahy:**

- Vyberte ikonu **ztlumení** výstrahy.

**Zobrazení ztlumených výstrah:**

1. Vyberte formulář s možností **potvrzení** na hlavní obrazovce **výstrahy** .

2. Pokud chcete zjistit, jestli je ztlumený, najeďte myší na výstrahu.  

## <a name="export-alert-information"></a>Exportovat informace o výstrahách

Exportujte informace o výstraze do souboru. csv. Můžete exportovat informace o všech zjištěných výstrahách nebo informace o exportu na základě filtrovaného zobrazení. Exportují se následující informace:

- Zdrojová adresa
- Cílová adresa
- Název výstrahy
- Závažnost výstrahy
- Zpráva výstrahy
- Další informace
- Potvrzený stav
- Dostupnost PCAP

Export:

1. V postranní nabídce vyberte výstrahy.
1. Vyberte Exportovat.
1. Vyberte Exportovat rozšířené výstrahy a exportujte informace o výstrahách v samostatných řádcích pro každou výstrahu, která pokrývá více zařízení. Když je vybraná možnost exportovat rozšířené výstrahy, vytvoří soubor. CSV v každém řádku duplicitní řádek události výstrahy s jedinečnými položkami. Použití této možnosti usnadňuje zkoumání exportovaných událostí výstrah.

## <a name="next-steps"></a>Další kroky

[Určení, který provoz se monitoruje](how-to-control-what-traffic-is-monitored.md)
