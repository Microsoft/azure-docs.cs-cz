---
title: Práce s upozorněními na místní konzole pro správu
description: Místní Konzola pro správu vám umožní získat přehled o nedávných hrozbách v síti a lépe pochopit, jak je uživatelé ze senzorů zpracovávají.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 604650f0cb08eac4a3ab1cfd3fdcbf2e7ff0d19e
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105032136"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Práce s upozorněními na místní konzole pro správu 

Z okna **výstrahy** v konzole pro správu můžete provést následující akce:

- Práce s filtry výstrah

- Práce s čítači výstrah

- Zobrazit informace o výstraze

- Správa událostí výstrah

- Vytvořit pravidla vyloučení výstrah

- Aktivovat pravidla vyloučení výstrah z externích systémů

- Urychlení pracovního postupu incidentu pomocí skupin výstrah

## <a name="view-alerts-in-the-on-premises-management-console"></a>Zobrazení výstrah v místní konzole pro správu

Místní Konzola pro správu agreguje výstrahy ze všech připojených senzorů. To poskytuje podnikovým zobrazením nedávných hrozeb ve vaší síti a pomůže vám lépe pochopit, jak je uživatelé ze senzorů zpracovávají.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Snímek obrazovky okna výstrahy.":::

### <a name="work-with-alert-filters"></a>Práce s filtry výstrah

V okně **výstrahy** se zobrazují výstrahy vygenerované senzory připojenými k místní konzole pro správu. Můžete zobrazit všechny výstrahy pro připojené senzory nebo prezentovat výstrahy odeslané z konkrétního:

- Web

- Zóna

- Zařízení

- Elektrické

Pokud chcete zobrazit všechny výstrahy, vyberte **Vymazat filtry** .

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Filtry vymažete tak, že vyberete tlačítko Vymazat filtry.":::

### <a name="work-with-alert-counters"></a>Práce s čítači výstrah

Čítače výstrah poskytují rozpis výstrah podle závažnosti a potvrzeného stavu.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="Čítač výstrah zobrazuje počet výstrah, které máte.":::

V čítači výstrah se zobrazí následující úrovně závažnosti:

- **Kritické**: označuje škodlivý útok, který by měl být zpracován okamžitě.

- **Hlavní**: označuje bezpečnostní hrozbu, která je důležitá pro řešení.

- **Vedlejší**: označuje určitou odchylku od chování standardních hodnot, která by mohla obsahovat bezpečnostní hrozbu.

- **Upozornění**: označuje určitou odchylku od chování standardních hodnot bez bezpečnostních hrozeb.

Úrovně závažnosti jsou předdefinovány.

Čítač můžete upravit tak, aby poskytoval čísla na základě potvrzených a nepotvrzených upozornění. V programu Defender pro senzory IoT se aktivovaly nepotvrzené výstrahy, ale ještě jste je nezkontrolovali pomocí operátorů na senzoru.

Pokud je vybrána možnost **Zobrazit potvrzené výstrahy** , zobrazí se v okně **výstrahy** všechny potvrzené výstrahy.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Zobrazit vaše potvrzené výstrahy.":::

### <a name="view-alert-information"></a>Zobrazit informace o výstraze

Výstraha zobrazí následující informace:

- Souhrn události výstrahy.

- Závažnost výstrahy.

- Odkaz na výstrahu ve snímači, který ji rozpoznal.

- UUID výstrahy. Identifikátor UUID se skládá z ID výstrahy, které je přidruženo k události výstrahy zjištěné na senzoru, oddělené pomlčkou a následovaný jedinečným IDENTIFIKAČNÍm číslem systému.

**Místní Konzola pro správu – UUID výstrah**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Zařízení je připojené, ale není autorizované.":::

**ID upozornění senzoru**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="ID upozornění snímače.":::

Když pracujete s identifikátory UUID, zajistíte, aby se každá výstraha zobrazená v místní konzole pro správu mohla prohledávat a identifikovat jedinečné číslo. To je nutné, protože výstrahy generované z různých senzorů mohou vytvořit stejné ID výstrahy.

> [!NOTE]
> Ve výchozím nastavení se identifikátory UUID zobrazují v následujících partnerských systémech, když jsou definována pravidla předávání: ArcSight, servery syslog, QRadar, Sentinel a NetWitness. Není nutné žádné nastavení.

Zobrazení informací o výstrahách:

- V seznamu výstrah vyberte výstrahu.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Snímek obrazovky s informacemi o výstraze":::

Zobrazení výstrahy ve snímači:

- Z výstrahy vyberte **otevřít senzor** .

Zobrazení zařízení v mapě zóny:

- Pokud chcete zobrazit mapu zařízení s fokusem na zařízení s výstrahou a všemi připojenými zařízeními, vyberte **Zobrazit zařízení**.

## <a name="manage-alert-events"></a>Správa událostí výstrah

K dispozici je několik možností pro správu událostí výstrah z místní konzoly pro správu.

- Zjistěte nebo potvrďte události výstrah. Chcete-li zjistit všechny události výstrah, které mohou být autorizovány, a potvrdit všechny události výstrah, které aktuálně nejsou potvrzeny, vyberte možnost **zjistit & potvrzení** .

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Vyberte Další informace & potvrzení, abyste se seznámili.":::

- Ztlumení a ztlumení událostí výstrah.

Další informace o studiu, potvrzování a ztlumení událostí výstrah najdete v článku o [správě událostí](how-to-manage-the-alert-event.md) pro senzory.

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
1. Vyberte Exportovat rozšířené výstrahy a exportujte informace o výstrahách v samostatných řádcích pro každou výstrahu, která pokrývá více zařízení. Když je vybraná možnost exportovat rozšířené výstrahy, vytvoří soubor. csv duplicitní řádek výstrahy s jedinečnými položkami v každém řádku. Použití této možnosti usnadňuje zkoumání exportovaných událostí výstrah.  

## <a name="create-alert-exclusion-rules"></a>Vytvořit pravidla vyloučení výstrah

Požádejte Defender, aby IoT ignoroval triggery výstrah na základě:

- Časová pásma a časová období

- Adresa zařízení (IP adresa, MAC, podsíť)

- Názvy výstrah

- Určitý senzor

Vytvořte pravidla vyloučení výstrah, pokud chcete, aby program Defender pro IoT ignoroval aktivitu, která aktivuje výstrahu.

Pokud například víte, že všechna zařízení, která monitoruje určitý senzor, procházejí postupy údržby po dobu dvou dnů, můžete definovat pravidlo vyloučení, které dá programu Defenderu pokyn, aby potlačil výstrahy zjištěné tímto senzorem během předdefinovaného období.

### <a name="alert-exclusion-logic"></a>Logika vyloučení výstrahy

Logika pravidla výstrahy je `AND` založena na. To znamená, že výstraha se aktivuje jenom v případě, že jsou splněné všechny podmínky pravidla.

Pokud není definována podmínka pravidla, bude podmínka obsahovat všechny možnosti. Pokud například v pravidle nezahrnete název snímače, použije se u všech senzorů.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Snímek obrazovky vytvoření zobrazení pravidla vyloučení":::

Souhrny pravidel se zobrazí v okně **pravidlo vyloučení** .

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Snímek obrazovky se souhrnným zobrazením pravidla vyloučení":::

Kromě práce s pravidly vyloučení můžete výstrahy potlačit jejich vypnutím.

### <a name="create-exclusion-rules"></a>Vytvořit pravidla vyloučení

Vytvoření pravidel vyloučení:

1. V levém podokně místní konzoly pro správu vyberte **vyloučení výstrahy**. Definujte nové pravidlo vyloučení výběrem ikony **Přidat** :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: v pravém horním rohu okna, které se otevře. Otevře se dialogové okno **vytvořit pravidlo vyloučení** .

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Vyplněním informací zde vytvořte vyloučení výstrahy.":::

2. Do pole **název** zadejte název pravidla. Název nesmí obsahovat uvozovky ( `"` ).

3. V části **podle časového pásma/období** zadejte časové období v rámci určitého časového pásma. Tuto funkci použijte, když se pravidlo vyloučení vytvoří pro konkrétní časové období v jednom časovém pásmu, ale v dalších časových pásmech byste měli implementovat současně. Například může být nutné použít pravidlo vyloučení mezi 8:00 a 10:00 ve třech různých časových pásmech. V takovém případě vytvořte tři samostatná pravidla vyloučení, která používají stejné časové období a příslušné časové pásmo.

4. Vyberte **Přidat**. Během období vyloučení se v připojených senzorech nevytvoří žádné výstrahy.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Snímek obrazovky zobrazení podle časového období":::

5. V části **podle adresy zařízení** definujte:

  - IP adresa zařízení, adresa MAC nebo adresa podsítě, kterou chcete vyloučit.

  - Směr přenosu vyloučených zařízení, zdroje a cíle.

6. Vyberte **Přidat**.

7. V části **podle názvu výstrahy** začněte psát název výstrahy. V rozevíracím seznamu vyberte název nebo názvy výstrah, které chcete vyloučit.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Snímek obrazovky zobrazení podle názvu výstrahy":::

8. Vyberte **Přidat**.

9. V části **podle názvu senzoru** začněte psát název snímače. V rozevíracím seznamu vyberte senzor nebo senzory, které chcete vyloučit.

10. Vyberte **Přidat**.

11. Vyberte **Uložit**. Nové pravidlo se zobrazí v seznamu pravidel.

Výstrahy můžete potlačit buď jejich vypnutím, nebo vytvořením pravidel pro vyloučení výstrah. Tato část popisuje možné případy použití obou funkcí.

- **Pravidlo vyloučení**. Zapsat pravidlo vyloučení v těchto případech:

  - Znáte čas, kdy chcete událost vyloučit z databáze. Například víte, že scénář zjištěný u určitého snímače bude aktivovat nedůležité výstrahy. Například budete provádět údržbu v rámci organizace PLCs v konkrétní lokalitě a chcete potlačit výstrahy týkající se PLCs pro tento web.

  - Má Defender pro IoT ignorovat události pro určitý časový rozsah (pro úlohy údržby systému).

  - Chcete ignorovat události v konkrétní podsíti.

  - Chcete řídit události výstrah vygenerované z několika senzorů s jedním pravidlem.

  - Nechcete sledovat vyloučení výstrah jako událost v protokolu událostí.

- **Ztlumení**. Ztlumení výstrahy v těchto případech:

  - Položky, které je třeba ztlumení, nejsou plánovány. Nevíte, které události nebudou důležité.

  - Tuto výstrahu chcete potlačit z okna **výstrahy** , ale přesto ji chcete sledovat v protokolu událostí.

  - Chcete ignorovat události na konkrétním kanálu.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Aktivovat pravidla vyloučení výstrah z externích systémů

Aktivovat pravidla vyloučení výstrah z externích systémů. Můžete například spravovat pravidla vyloučení ze systémů pro vytváření podnikových lístků nebo systémů, které spravují procesy údržby sítě.

Pro použití pravidla definujte senzory, moduly, čas spuštění a čas ukončení. Další informace najdete v tématu [Defender pro rozhraní API pro senzory rozhraní IoT a konzolu pro správu](references-work-with-defender-for-iot-apis.md).

Pravidla, která vytvoříte pomocí rozhraní API, se zobrazí v okně **pravidlo vyloučení** jako ro.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Snímek obrazovky s zobrazením pro úpravy pravidla vyloučení":::

## <a name="next-steps"></a>Další kroky

[Pracujte s výstrahami na senzoru](how-to-work-with-alerts-on-your-sensor.md).
Přečtěte si téma [Defender for IoT Engine – výstrahy](alert-engine-messages.md).