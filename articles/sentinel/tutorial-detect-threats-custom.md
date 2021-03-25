---
title: Vytváření vlastních pravidel analýzy pro detekci hrozeb pomocí služby Azure Sentinel | Microsoft Docs
description: V tomto kurzu se naučíte vytvářet vlastní pravidla analýzy pro detekci hrozeb zabezpečení pomocí funkce Sentinel Azure. Využijte seskupování událostí, seskupování výstrah a rozšíření výstrah a pochopení možnosti automaticky zakázáno.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 70b56e70ec0e6f511142c48cc89720c054807a5c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105042794"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Kurz: vytvoření vlastních pravidel analýzy pro detekci hrozeb

Teď, když jste se [připojili ke zdrojům dat](quickstart-onboard.md) ke službě Azure Sentinel, můžete vytvořit vlastní analytická pravidla, která vám pomůžou odhalit hrozby a neobvyklé chování, která se nacházejí ve vašem prostředí. Tato pravidla hledají konkrétní události nebo sady událostí v celém prostředí, upozorňují na to, kdy jsou dosaženy určité prahové hodnoty událostí nebo podmínky, generují incidenty pro SOC k třídění a vyšetřování a reagují na hrozby pomocí automatizovaných sledovacích a nápravných procesů. 

Tento kurz vám pomůže vytvořit vlastní pravidla pro detekci hrozeb pomocí služby Azure Sentinel.

Po dokončení tohoto kurzu budete moci provést následující akce:
> [!div class="checklist"]
> * Vytvořit analytická pravidla
> * Definujte, jak se zpracují události a výstrahy.
> * Definování způsobu generování výstrah a incidentů
> * Výběr automatických odpovědí na hrozby pro vaše pravidla

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Vytvoření vlastního pravidla analýzy s plánovaným dotazem

1. V navigační nabídce Azure Sentinel vyberte **Analytics**.

1. Na panelu akcí v horní části vyberte **+ vytvořit** a vyberte **pravidlo plánovaného dotazu**. Tím se otevře **Průvodce analytickým pravidlem**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Vytvořit plánovaný dotaz" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Průvodce pravidly analýzy – karta Obecné

- Zadejte jedinečný **název** a **Popis**. 

- V poli **taktiku** si můžete vybrat z kategorií útoků, podle kterých se pravidlo klasifikuje. Ty jsou založené na taktiku rozhraní [Mitre ATT&CK](https://attack.mitre.org/) .

- Nastavte **závažnost** výstrahy podle potřeby. 

- Když vytvoříte pravidlo, jeho **stav** je ve výchozím nastavení **povolený** , což znamená, že se spustí hned po dokončení jeho vytvoření. Pokud nechcete, aby se spouštěla hned, vyberte **disabled (zakázáno**) a pravidlo se přidá na kartu **aktivní pravidla** a Vy ho můžete v případě potřeby povolit.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Začněte vytvářet vlastní pravidlo analýzy.":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Definování logiky dotazu pravidla a konfigurace nastavení

Na kartě **nastavit logiku pravidla** můžete buď napsat dotaz přímo do pole **dotazu pravidla** , nebo vytvořit dotaz v Log Analytics a potom ho zkopírovat a vložit sem.

- Dotazy se zapisují v jazyce KQL (Kusto Query Language). Přečtěte si další informace o [konceptech](/azure/data-explorer/kusto/concepts/) a [dotazech](/azure/data-explorer/kusto/query/)KQL a podívejte se na toto praktické [stručné referenční příručky](/azure/data-explorer/kql-quick-reference).

- Příklad zobrazený v tomto snímku obrazovky se dotazuje tabulky *SecurityEvent* , aby zobrazoval typ [neúspěšných událostí přihlášení do systému Windows](/windows/security/threat-protection/auditing/event-4625).

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Konfigurace logiky a nastavení pravidla dotazu" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Tady je další ukázkový dotaz, který vás upozorní, když se v [aktivitě Azure](../azure-monitor/essentials/activity-log.md)vytvoří neobvykléý počet prostředků.

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Osvědčené postupy pro dotaz na pravidlo
    > - Délka dotazu by měla být mezi 1 a 10 000 znaky a nesmí obsahovat znak " `search *` " nebo " `union *` ".
    >
    > - Použití funkcí ADX k vytvoření dotazů Azure Průzkumník dat v okně dotazu Log Analytics **není podporováno**.
    >
    > - Pokud použijete **`bag_unpack`** funkci v dotazu, pokud sloupce provedete jako pole pomocí " `project field1` " a sloupec neexistuje, dotaz se nezdaří. Chcete-li s tímto chováním chránit, je nutné tento sloupec promítnout následujícím způsobem:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Obohacení výstrahy

> [!IMPORTANT]
> Funkce pro obohacení výstrah jsou momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.
    
- Použijte konfigurační oddíl **mapování entit** k mapování parametrů z výsledků dotazu do entit rozpoznaných službou Azure Sentinel. Entity rozšiřují výstup pravidel (výstrahy a incidenty) základními informacemi, které slouží jako stavební kameny všech vyšetřovacích procesů a nápravných akcí, které následují. Jsou to také kritéria, podle kterých můžete výstrahy seskupovat do incidentů na kartě **Nastavení incidentu** .

    Přečtěte si další informace o [entitách v Sentinel Azure](entities-in-azure-sentinel.md).

    V tématu [mapování datových polí na entity v Azure Sentinel](map-data-fields-to-entities.md) najdete kompletní pokyny k mapování entit spolu s důležitými informacemi o [zpětné kompatibilitě](map-data-fields-to-entities.md#notes-on-the-new-version).

- Konfigurační oddíl **vlastní podrobnosti** slouží k extrakci položek dat událostí z dotazu a jejich navýšení v výstrahách vytvořených tímto pravidlem, což vám poskytne okamžitý přehled o obsahu událostí v upozorněních a incidentech.

    Přečtěte si další informace o vlastních podrobnostech zpřístupnění v upozorněních a Projděte si [kompletní pokyny](surface-custom-details-in-alerts.md).

### <a name="query-scheduling-and-alert-threshold"></a>Plánování dotazů a prahová hodnota pro výstrahu

- V části **plánování dotazů** nastavte následující parametry:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Nastavení plánu dotazů a seskupování událostí" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Nastavením **příkazu Spustit dotaz** můžete určit, jak často se má dotaz spouštět – stejně jako každých 5 minut nebo jako nečastý každých 14 dní.

    - Nastavte **hledaná data z poslední** pro určení časového období dat pokrytých dotazem – můžete například zadat dotaz na posledních 10 minut dat nebo posledních 6 hodin dat. Maximální počet je 14 dní.

        > [!NOTE]
        > **Intervaly dotazů a lookback období**
        >
        >  Tato dvě nastavení jsou nezávislá na sobě navzájem, až do bodu. Dotaz můžete spustit v krátkém intervalu pokrývající dobu delší, než je interval (v důsledku překrývajících se dotazů), ale nemůžete spustit dotaz v intervalu, který překračuje období pokrytí. v opačném případě budete mít v celkovém pokrytí dotazu mezery.
        >
        > **Zpoždění přijímání**
        >
        > Pro zajištění **latence** , ke které může dojít mezi generováním události ve zdroji a jejich ingestování do Azure Sentinel, a za účelem zajištění úplného pokrytí bez duplikace dat, služba Azure Sentinel spustí plánovaná analytická pravidla o **zpoždění po pěti minutách** od naplánovaného času.
        >
        > Podrobné technické vysvětlení, proč je tato prodleva nezbytná a jak tento problém vyřešit, najdete v blogovém příspěvku Ron Marsiano na předmětu a "[zpracování prodlevy při přijímání příjmu v pravidlech výstrah Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)".

- Pomocí oddílu **prahová hodnota pro výstrahu** Definujte úroveň citlivosti pravidla. Například nastavte **vygenerovat výstrahu, pokud je počet výsledků dotazu** **větší než** a zadejte číslo 1000, pokud chcete, aby pravidlo vygenerovalo výstrahu pouze v případě, že dotaz vrátí více než 1000 výsledků při každém spuštění. Toto pole je povinné, takže pokud nechcete nastavit prahovou hodnotu – to znamená, že pokud chcete, aby vaše výstraha zaregistrovala každou událost – zadejte 0 do pole číslo.
    
### <a name="results-simulation"></a>Simulace výsledků

V oblasti **simulace výsledků** na pravé straně průvodce vyberte **test s aktuálními daty** a Azure Sentinel zobrazí graf výsledků (události protokolu). dotaz by se vygeneroval v posledních 50 časech podle aktuálně definovaného plánu. Pokud upravíte dotaz, vyberte znovu **test s aktuálními daty** , aby se graf aktualizoval. Graf zobrazuje počet výsledků v rámci definovaného časového období, které je určeno nastavením v sekci **plánování dotazů** .
  
Tady je příklad, jak může být simulace výsledků vypadat jako u dotazu na snímku obrazovky výše. Levá strana je výchozí zobrazení a na pravé straně se zobrazuje, když najedete myší na bod v čase v grafu.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Snímky pro simulaci výsledků":::

Pokud vidíte, že dotaz by aktivoval příliš mnoho nebo příliš časté výstrahy, můžete experimentovat s nastavením v [částech](#query-scheduling-and-alert-threshold) **plánování dotazů** a **prahové hodnoty výstrahy** a znovu vybrat **test s aktuálními daty** .

### <a name="event-grouping-and-rule-suppression"></a>Potlačení skupin událostí a pravidel

> [!IMPORTANT]
> Seskupování událostí je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.
    
- V části **Seskupování událostí** vyberte jeden ze dvou způsobů, jak zpracovat seskupení **událostí** do **výstrah**: 

    - **Seskupí všechny události do jedné výstrahy** (výchozí nastavení). Pravidlo generuje jedinou výstrahu při každém spuštění, pokud dotaz vrátí více výsledků než zadaná **prahová hodnota pro výstrahu** výše. Výstraha obsahuje souhrn všech událostí vrácených ve výsledcích. 

    - **Aktivovat upozornění pro každou událost:** Pravidlo generuje jedinečnou výstrahu pro každou událost vrácenou dotazem. To je užitečné, pokud chcete, aby se události zobrazovaly jednotlivě, nebo pokud je chcete seskupit podle určitých parametrů – podle uživatele, názvu hostitele nebo jiného objektu. Tyto parametry můžete definovat v dotazu.
    
        V současné době je počet upozornění, která může pravidlo vygenerovat, omezený na 20. Pokud je v konkrétním pravidle nastaveno **seskupení událostí** , aby se **aktivovala výstraha pro každou událost**, a dotaz pravidla vrátí více než 20 událostí, každá z prvních 19 událostí vygeneruje jedinečnou výstrahu a 20 výstrah bude shrnout celou sadu vrácených událostí. Jinými slovy, 20 výstrah, co by bylo vygenerováno v rámci **skupiny všechny události do jedné možnosti výstrahy** .

    > [!NOTE]
    > Jaký je rozdíl mezi **událostmi** a **výstrahami**?
    >
    > - **Událost** je popis jednoho výskytu akce. Například jedna položka v souboru protokolu by mohla počítat jako událost. V tomto kontextu událost odkazuje na jeden výsledek vrácený dotazem v pravidle analýzy.
    >
    > - **Výstraha** je kolekce událostí, které jsou společně důležité z hlediska zabezpečení. Výstraha může obsahovat jednu událost, pokud došlo k významným dopadům na zabezpečení – třeba při administrativním přihlášení z cizí země mimo kancelář.
    >
    > - Jak jsou **incidenty**? Interní logika služby Azure Sentinel vytváří **incidenty** z **výstrah** nebo skupin výstrah. Fronta incidentů je ústředním bodem SOC analytiků práce, vyšetřování a nápravy.
    > 
    > Azure Sentinel ingestuje nezpracované události z některých zdrojů dat a již zpracovává výstrahy od ostatních. Je důležité si uvědomit, že tu z nich můžete kdykoli řešit.

- V části **potlačení** můžete zapnout funkci **zastavit běžící dotaz po vygenerování výstrahy** nastavení **v** případě, že po obdržení výstrahy budete chtít pozastavit operaci tohoto pravidla v časovém intervalu, který překračuje interval dotazu. Pokud tuto funkci zapnete v, musíte nastavit **zastavit běžící dotaz pro** na dobu, po kterou by měl dotaz skončit, a to až 24 hodin.

## <a name="configure-the-incident-creation-settings"></a>Konfigurovat nastavení vytváření incidentů

Na kartě **Nastavení incidentu** si můžete vybrat, jestli a jakým způsobem funkce Sentinel Azure zapne výstrahy na nenapadnutelné incidenty. Pokud je tato karta jenom vlevo, Azure Sentinel vytvoří jeden samostatný incident od každého a každého upozornění. Můžete zvolit, aby se nevytvořily žádné incidenty, nebo pokud chcete seskupit několik výstrah do jednoho incidentu, a to změnou nastavení na této kartě.

> [!IMPORTANT]
> Karta Nastavení incidentu je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Definování nastavení pro vytváření incidentů a seskupování výstrah":::

### <a name="incident-settings"></a>Nastavení incidentu

V části **Nastavení incidentu** můžete **vytvořit incidenty z výstrah aktivovaných tímto pravidlem analýzy** **, což znamená, že** Azure Sentinel vytvoří jeden samostatný incident od každého a všechny výstrahy aktivované pravidlem.
    
- Pokud nechcete, aby toto pravidlo vedlo k vytvoření jakýchkoli incidentů (například pokud toto pravidlo slouží pouze ke shromažďování informací pro následnou analýzu), nastavte tuto hodnotu na **zakázáno**.

- Pokud chcete vytvořit jeden incident ze skupiny výstrah místo jedné pro každou jednotlivou výstrahu, přečtěte si další část.

### <a name="alert-grouping"></a>Seskupení výstrah

Pokud chcete, aby se jeden incident vygeneroval ze skupiny až 150 podobných nebo opakovaných výstrah (viz poznámku), nastavte v části **seskupování výstrah** **výstrahy související se skupinami, aktivované pomocí tohoto pravidla analýzy, na incidenty** , které chcete **Povolit**, a nastavte následující parametry.

- **Omezte skupinu na výstrahy vytvořené ve vybraném časovém rámci**: určete časový rámec, ve kterém se budou seskupovat podobné nebo opakované výstrahy. Všechny odpovídající výstrahy v tomto časovém rámci budou souhrnně generovat incident nebo sadu incidentů (v závislosti na nastavení seskupení níže). Výstrahy mimo tento časový rámec způsobí vygenerování samostatného incidentu nebo sady incidentů.

- **Výstrahy skupin aktivované pomocí tohoto pravidla analýzy do jednoho incidentu**: vyberte základnu, na které se budou seskupovat výstrahy:

    - **Seskupit výstrahy do jednoho incidentu, pokud se shodují všechny entity**: výstrahy se seskupují společně, pokud sdílejí stejné hodnoty pro každou namapovanou entitu (definované na kartě nastavit logiku pravidla výše). Toto je doporučené nastavení.

    - **Seskupit všechny výstrahy aktivované tímto pravidlem do jednoho incidentu**: všechny výstrahy vygenerované tímto pravidlem jsou seskupené i v případě, že nesdílejí žádné identické hodnoty.

    - **Seskupit výstrahy do jednoho incidentu, pokud se shodují vybrané entity**: výstrahy se seskupují, pokud sdílí stejné hodnoty pro některé mapované entity (které můžete vybrat z rozevíracího seznamu). Toto nastavení můžete chtít použít například v případě, že chcete vytvořit samostatné incidenty založené na zdrojových nebo cílových IP adresách.

- **Znovu otevřít uzavřené odpovídající incidenty**: Pokud byl incident vyřešen a uzavřen a později dojde k vygenerování jiné výstrahy, která by měla patřit do tohoto incidentu, nastavte toto nastavení na **povoleno** , pokud chcete, aby byl uzavřený incident znovu otevřen, a nechejte jako **zakázaný** , pokud chcete, aby výstraha vytvořila nový incident.
    
    > [!NOTE]
    > Do jednoho incidentu lze seskupit **až 150 upozornění** . Pokud pravidlo, které je seskupuje do jednoho incidentu, generuje více než 150 výstrah, vytvoří se nový incident se stejnými podrobnostmi o incidentech jako původní a nadbytečné výstrahy budou seskupeny do nového incidentu.

## <a name="set-automated-responses-and-create-the-rule"></a>Nastavení automatizovaných odpovědí a vytvoření pravidla

1. Na kartě **automatizované odpovědi** vyberte libovolný playbooky, který chcete spustit automaticky, když se vygeneruje výstraha vlastním pravidlem. Další informace o vytváření a automatizaci playbooky najdete v tématu [reakce na hrozby](tutorial-respond-threats-playbook.md).

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Definování nastavení automatizované odezvy":::

1. Vyberte **zkontrolovat a vytvořit** a zkontrolujte všechna nastavení nového pravidla výstrahy. Po zobrazení zprávy "ověření proběhlo" vyberte **vytvořit** k inicializaci pravidla upozornění.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Zkontrolujte všechna nastavení a vytvořte pravidlo.":::

## <a name="view-the-rule-and-its-output"></a>Zobrazit pravidlo a jeho výstup
  
- Nově vytvořené vlastní pravidlo (typu "naplánované") můžete najít v tabulce na kartě **aktivní pravidla** na hlavní obrazovce **Analytics** . V tomto seznamu můžete každé pravidlo Povolit, zakázat nebo odstranit.

- Pokud chcete zobrazit výsledky vytvořených pravidel výstrah, navštivte stránku **incidenty** , kde můžete určit jejich třídění, [prozkoumat incidenty](tutorial-investigate-cases.md)a napravit hrozby.

> [!NOTE]
> Výstrahy vygenerované v Azure Sentinel jsou k dispozici prostřednictvím [Microsoft Graph zabezpečení](/graph/security-concept-overview). Další informace najdete v dokumentaci k [výstrahám zabezpečení Microsoft Graph](/graph/api/resources/security-api-overview).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="issue-no-events-appear-in-query-results"></a>Problém: ve výsledcích dotazu se neobjeví žádné události.

Pokud je **seskupení událostí** nastaveno tak, aby **pro každou událost aktivovalo výstrahu**, pak v některých scénářích při zobrazení výsledků dotazu v pozdějším čase (například při překlopení zpět na výstrahy z incidentu) je možné, že se nezobrazí žádné výsledky dotazu. Důvodem je to, že připojení události k výstraze je provedeno pomocí hashování informací o konkrétní události a zahrnutím hodnoty hash do dotazu. Pokud se výsledky dotazu od vygenerování výstrahy změnily, hodnota hash už nebude platná a nezobrazí se žádné výsledky. 

Chcete-li zobrazit události, ručně odeberte řádek s hodnotou hash z dotazu pravidla a spusťte dotaz.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Problém: naplánované pravidlo se nepovedlo spustit, nebo se zobrazuje s AUTOMATICKÝm ZAKÁZÁNím přidaným do názvu.

Jedná se o vzácnou chybu, kterou se nepodařilo spustit naplánované pravidlo dotazu, ale může k tomu dojít. Azure Sentinel klasifikuje chyby vpřed jako přechodné nebo trvalé, a to na základě konkrétního typu selhání a okolností, které to vedlo.

#### <a name="transient-failure"></a>Přechodná chyba

K přechodnému selhání dojde z důvodu nedokončené situace a brzy se vrátí do normálního okamžiku, kdy se spuštění pravidla nezdaří. Zde jsou některé příklady selhání, které se v Azure Sentinel klasifikují jako přechodné:

- Spuštění dotazu pravidla trvá příliš dlouho a vypršel časový limit.
- Problémy s připojením mezi zdroji dat a Log Analytics nebo mezi Log Analytics a Sentinel Azure
- Jakákoli jiná nová a neznámá chyba je považována za přechodný.

V případě přechodného selhání se Azure Sentinel stále pokouší znovu spustit pravidlo po předdefinovaných a stále rostoucích intervalech až do bodu. Pak se pravidlo spustí znovu jenom v dalším naplánovaném čase. Pravidlo nebude nikdy automaticky zakázáno z důvodu přechodného selhání.

#### <a name="permanent-failure---rule-auto-disabled"></a>Trvalá chyba – pravidlo je automaticky zakázané.

K trvalému selhání dochází z důvodu změny podmínek, které umožňují spuštění pravidla, což bez lidského zásahu se nevrátí do původního stavu. Následuje několik příkladů selhání klasifikovaných jako trvalá:

- Cílový pracovní prostor (na kterém je zpracováván dotaz pravidla) byl odstraněn.
- Cílová tabulka (na které běží dotaz na pravidlo) byla odstraněna.
- Z cílového pracovního prostoru se odebrala Azure Sentinel.
- Funkce používaná dotazem pravidla již není platná. byl buď změněn, nebo odebrán.
- Oprávnění k jednomu z datových zdrojů dotazu pravidla se změnila.
- Jeden z datových zdrojů dotazu pravidla byl odstraněn nebo odpojen.

**V případě předem vymezeného počtu po sobě jdoucích trvalých selhání stejného typu a stejného pravidla** Při pokusu o spuštění pravidla se Azure Sentinel ukončí a provede také následující kroky:

- Zakáže pravidlo.
- Přidá slova **"auto Disabled"** na začátek názvu pravidla.
- Přidá důvod selhání (a zakáže) k popisu pravidla.

Můžete snadno určit přítomnost všech automaticky zakázaných pravidel, protože seznam pravidel seřadíte podle názvu. Automaticky zakázaná pravidla budou v horní části seznamu nebo v ní.

SOC manažeři si nezapomeňte pravidelně kontrolovat seznam pravidel pro přítomnost automaticky zakázaných pravidel.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak začít s detekcí hrozeb pomocí funkce Azure Sentinel.

- Naučte se [prozkoumat incidenty v Azure Sentinel](tutorial-investigate-cases.md).
- Seznamte [se s entitami v Azure Sentinel](entities-in-azure-sentinel.md).
- Přečtěte si, jak [nastavit automatické reakce na hrozby ve službě Azure Sentinel](tutorial-respond-threats-playbook.md).