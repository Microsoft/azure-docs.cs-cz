---
title: Vytváření vlastních pravidel analýzy pro detekci hrozeb pomocí služby Azure Sentinel | Microsoft Docs
description: V tomto kurzu se naučíte vytvářet vlastní pravidla analýzy pro detekci hrozeb zabezpečení pomocí funkce Sentinel Azure. Využijte seskupování událostí a seskupování výstrah a Naučte se automaticky zakázat.
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 55853cc6a3dc27df4c63e0a28ab079813040e45d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617175"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Kurz: vytvoření vlastních pravidel analýzy pro detekci hrozeb

Po [připojení zdrojů dat](quickstart-onboard.md)   ke službě Azure Sentinel můžete vytvořit vlastní pravidla, která budou vyhledávat konkrétní kritéria v rámci vašeho prostředí a generovat incidenty, když jsou kritéria shodná, abyste je mohli prozkoumat. Tento kurz vám pomůže vytvořit vlastní pravidla pro detekci hrozeb pomocí služby Azure Sentinel.

Tento kurz vám pomůže detekovat hrozby pomocí služby Azure Sentinel.
> [!div class="checklist"]
> * Vytvořit analytická pravidla
> * Automatizace odpovědí na hrozby

## <a name="create-custom-analytics-rules"></a>Vytváření vlastních pravidel analýzy

Můžete vytvořit vlastní analytická pravidla, která vám pomůžou vyhledat typy hrozeb a anomálií, které jsou podezřelé ve vašem prostředí. Pravidlo vám zajistí, že se vám pošle oznámení hned, abyste mohli určit jejich třídění, prozkoumat je a vyřešit hrozby.

1. V Azure Portal v části Azure Sentinel vyberte **Analytics**.

1. V horním řádku nabídek vyberte **+ vytvořit** a vyberte **pravidlo plánovaného dotazu**. Tím se otevře **Průvodce analytickým pravidlem**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query.png" alt-text="Vytvořit plánovaný dotaz":::

1. Na kartě **Obecné** zadejte jedinečný **název** a **Popis**. V poli **taktiku** si můžete vybrat z kategorií útoků, podle kterých se pravidlo klasifikuje. Nastavte **závažnost** výstrahy podle potřeby. Když vytvoříte pravidlo, jeho **stav** je ve výchozím nastavení **povolený** , což znamená, že se spustí hned po dokončení jeho vytvoření. Pokud nechcete, aby se spouštěla hned, vyberte **disabled (zakázáno**) a pravidlo se přidá na kartu **aktivní pravidla** a Vy ho můžete v případě potřeby povolit.

    ![Začněte vytvářet vlastní pravidlo analýzy.](media/tutorial-detect-threats-custom/general-tab.png)

1. Na kartě **nastavit logiku pravidla** můžete buď napsat dotaz přímo do pole **dotazu pravidla** , nebo vytvořit dotaz v Log Analytics a pak ho zkopírovat a vložit.
 
   ![Vytvořit dotaz ve službě Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Podívejte se na pravé místo v oblasti **Náhled výsledků** , kde se v Azure Sentinel zobrazuje počet výsledků (protokoluje události), který dotaz vygeneruje a při psaní a konfiguraci dotazu se průběžně mění. Graf zobrazuje počet výsledků v rámci definovaného časového období, které je určeno nastavením v sekci **plánování dotazů** .
    - Pokud vidíte, že dotaz by aktivoval příliš mnoho nebo příliš časté výstrahy, můžete nastavit směrný plán v části **prahová hodnota pro výstrahu** .

      Tady je ukázkový dotaz, který vás upozorní, když se v aktivitě Azure vytvoří neobvykléý počet prostředků.

      ```kusto
      AzureActivity
      | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
      | where ActivityStatus == "Succeeded"
      | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
      ```

        > [!NOTE]
        > Délka dotazu by měla být mezi 1 a 10 000 znaky a nesmí obsahovat "Search \* " ani "Union" \* .

    1. Pomocí oddílu **mapové entity** můžete propojit parametry z výsledků dotazu do entit rozpoznaných pomocí služby Azure Sentinel. Tyto entity tvoří základ pro další analýzu, včetně seskupení výstrah na incidenty na kartě **Nastavení incidentu** .
  
    1. V části **plánování dotazů** nastavte následující parametry:

       1. **Pomocí příkazu Spustit dotaz vždy** proveďte kontrolu nad tím, jak často se má dotaz spouštět – stejně jako každých 5 minut nebo jako nečasto jako jednou denně.

       1. Nastavte **hledaná data z poslední** pro určení časového období dat pokrytých dotazem – můžete například zadat dotaz na posledních 10 minut dat nebo posledních 6 hodin dat.

          > [!NOTE]
          > **Intervaly dotazů a lookback období**
          > - Tato dvě nastavení jsou nezávislá na sobě navzájem, až do bodu. Dotaz můžete spustit v krátkém intervalu pokrývající dobu delší, než je interval (v důsledku překrývajících se dotazů), ale nemůžete spustit dotaz v intervalu, který překračuje období pokrytí. v opačném případě budete mít v celkovém pokrytí dotazu mezery.
          >
          > **Zpoždění přijímání**
          > - Pro zajištění **latence** , ke které může dojít mezi generováním události ve zdroji a jejich ingestování do Azure Sentinel, a za účelem zajištění úplného pokrytí bez duplikace dat, služba Azure Sentinel spustí plánovaná analytická pravidla o **zpoždění po pěti minutách** od naplánovaného času.

    1. Pomocí oddílu **prahová hodnota pro výstrahu** definujte směrný plán. Například nastavte **vygenerovat výstrahu, pokud je počet výsledků dotazu** **větší než** a zadejte číslo 1000, pokud chcete, aby pravidlo vygenerovalo výstrahu pouze v případě, že dotaz vrátí více než 1000 výsledků při každém spuštění. Toto pole je povinné, takže pokud nechcete nastavovat směrný plán – to znamená, že pokud chcete, aby vaše výstraha zaregistrovala každou událost – zadejte 0 do pole číslo.
    
    1. V části **Seskupování událostí**vyberte jeden ze dvou způsobů, jak zpracovat seskupení **událostí** do **výstrah**: 

       - **Seskupí všechny události do jedné výstrahy** (výchozí nastavení). Pravidlo generuje jedinou výstrahu při každém spuštění, pokud dotaz vrátí více výsledků než zadaná **prahová hodnota pro výstrahu** výše. Výstraha obsahuje souhrn všech událostí vrácených ve výsledcích. 

       - **Aktivovat výstrahu pro každou událost**. Pravidlo generuje jedinečnou výstrahu pro každou událost vrácenou dotazem. To je užitečné, pokud chcete, aby se události zobrazovaly jednotlivě, nebo pokud je chcete seskupit podle určitých parametrů – podle uživatele, názvu hostitele nebo jiného objektu. Tyto parametry můžete definovat v dotazu.
    
       V současné době je počet výstrah, které může pravidlo generovat, omezené na 20. Pokud je v konkrétním pravidle nastaveno **seskupení událostí** , aby se **aktivovala výstraha pro každou událost**, a dotaz pravidla vrátí více než 20 událostí, každá z prvních 19 událostí vygeneruje jedinečnou výstrahu a dvacáté upozornění vytvoří souhrn celé sady vrácených událostí. Jinými slovy, dvacáté upozornění je to, co se vygenerovalo v rámci **skupiny všechny události na jednu možnost výstrahy** .

       > [!NOTE]
       > Jaký je rozdíl mezi **událostmi** a **výstrahami**?
       >
       > - **Událost** je popis jednoho výskytu. Například jedna položka v souboru protokolu by mohla počítat jako událost. V tomto kontextu událost odkazuje na jeden výsledek vrácený dotazem v pravidle analýzy.
       >
       > - **Výstraha** je kolekce událostí, které jsou společně důležité z hlediska zabezpečení. Výstraha může obsahovat jednu událost, pokud došlo k významným dopadům na zabezpečení – třeba při administrativním přihlášení z cizí země mimo kancelář.
       >
       > - Jak jsou **incidenty**? Interní logika služby Azure Sentinel vytváří **incidenty** z **výstrah** nebo skupin výstrah. Fronta incidentů je ústředním bodem pro třídění, vyšetřování a nápravu pracovníků analytiků.
       > 
       > Azure Sentinel ingestuje nezpracované události z některých zdrojů dat a již zpracovává výstrahy od ostatních. Je důležité si uvědomit, že tu z nich můžete kdykoli řešit.

       > [!IMPORTANT]
       > Seskupování událostí je aktuálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. V části **potlačení** můžete zapnout funkci **zastavit běžící dotaz po vygenerování výstrahy** nastavení **v** případě, že po obdržení výstrahy budete chtít pozastavit operaci tohoto pravidla v časovém intervalu, který překračuje interval dotazu. Pokud tuto funkci zapnete v, musíte nastavit **zastavit běžící dotaz pro** na dobu, po kterou by měl dotaz skončit, a to až 24 hodin.

1. Na kartě **Nastavení incidentu** si můžete vybrat, jestli a jakým způsobem funkce Sentinel Azure zapne výstrahy na nenapadnutelné incidenty. Pokud je tato karta jenom vlevo, Azure Sentinel vytvoří jeden samostatný incident od každého a každého upozornění. Můžete zvolit, aby se nevytvořily žádné incidenty, nebo pokud chcete seskupit několik výstrah do jednoho incidentu, a to změnou nastavení na této kartě.

   > [!IMPORTANT]
   > Karta Nastavení incidentu je aktuálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. V části **Nastavení incidentu** můžete **vytvořit incidenty z výstrah aktivovaných tímto pravidlem analýzy** **, což znamená, že**Azure Sentinel vytvoří jeden samostatný incident od každého a všechny výstrahy aktivované pravidlem.
       - Pokud nechcete, aby toto pravidlo vedlo k vytvoření jakýchkoli incidentů (například pokud toto pravidlo slouží pouze ke shromažďování informací pro následnou analýzu), nastavte tuto hodnotu na **zakázáno**.

    1. Pokud chcete, aby se jeden incident vygeneroval ze skupiny až 150 podobných nebo opakovaných výstrah (viz poznámku), nastavte v části **seskupování výstrah** **výstrahy související se skupinami, aktivované pomocí tohoto pravidla analýzy, na incidenty** , které chcete **Povolit**, a nastavte následující parametry.

    - **Omezte skupinu na výstrahy vytvořené ve vybraném časovém rámci**: určete časový rámec, ve kterém se budou seskupovat podobné nebo opakované výstrahy. Všechny odpovídající výstrahy v tomto časovém rámci budou souhrnně generovat incident nebo sadu incidentů (v závislosti na nastavení seskupení níže). Výstrahy mimo tento časový rámec způsobí vygenerování samostatného incidentu nebo sady incidentů.

    - **Výstrahy skupin aktivované pomocí tohoto pravidla analýzy do jednoho incidentu**: vyberte základnu, na které se budou seskupovat výstrahy:

        - **Seskupit výstrahy do jednoho incidentu, pokud se shodují všechny entity**: výstrahy se seskupují společně, pokud sdílejí stejné hodnoty pro každou namapovanou entitu (definované na kartě nastavit logiku pravidla výše). Toto je doporučené nastavení.

        - **Seskupit všechny výstrahy aktivované tímto pravidlem do jednoho incidentu**: všechny výstrahy vygenerované tímto pravidlem jsou seskupené i v případě, že nesdílejí žádné identické hodnoty.

        - **Seskupit výstrahy do jednoho incidentu, pokud se shodují vybrané entity**: výstrahy se seskupují, pokud sdílí stejné hodnoty pro některé mapované entity (které můžete vybrat z rozevíracího seznamu). Toto nastavení můžete chtít použít například v případě, že chcete vytvořit samostatné incidenty založené na zdrojových nebo cílových IP adresách.

    - **Znovu otevřít uzavřené odpovídající incidenty**: Pokud byl incident vyřešen a uzavřen a později dojde k vygenerování jiné výstrahy, která by měla patřit do tohoto incidentu, nastavte toto nastavení na **povoleno** , pokud chcete, aby byl uzavřený incident znovu otevřen, a nechejte jako **zakázaný** , pokud chcete, aby výstraha vytvořila nový incident.
    
        > [!NOTE]
        > Do jednoho incidentu lze seskupit až 150 upozornění. Pokud pravidlo, které je seskupuje do jednoho incidentu, generuje více než 150 výstrah, vytvoří se nový incident se stejnými podrobnostmi o incidentech jako původní a nadbytečné výstrahy budou seskupeny do nového incidentu.

1. Na kartě **automatizované odpovědi** vyberte libovolný playbooky, který chcete spustit automaticky, když se vygeneruje výstraha vlastním pravidlem. Další informace o vytváření a automatizaci playbooky najdete v tématu [reakce na hrozby](tutorial-respond-threats-playbook.md).

1. Vyberte **zkontrolovat a vytvořit** a zkontrolujte všechna nastavení nového pravidla výstrahy a potom vyberte **vytvořit a inicializujte pravidlo upozornění**.
  
1. Po vytvoření výstrahy se do tabulky v části **aktivní pravidla**přidá vlastní pravidlo. V tomto seznamu můžete každé pravidlo Povolit, zakázat nebo odstranit.

1. Pokud chcete zobrazit výsledky vytvořených pravidel výstrah, navštivte stránku **incidenty** , kde můžete určit jejich třídění, [prozkoumat incidenty](tutorial-investigate-cases.md)a napravit hrozby.


> [!NOTE]
> Výstrahy vygenerované v Azure Sentinel jsou k dispozici prostřednictvím [Microsoft Graph zabezpečení](https://aka.ms/securitygraphdocs). Další informace najdete v dokumentaci k [výstrahám zabezpečení Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Naplánované pravidlo se nepovedlo spustit, nebo se zobrazuje s automaticky ZAKÁZANým přidaným názvem.

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

Pokud se chcete dozvědět, jak automatizovat vaše odezvy na hrozby, [nastavte v Azure Sentinelu automatické odpovědi na hrozby](tutorial-respond-threats-playbook.md).

