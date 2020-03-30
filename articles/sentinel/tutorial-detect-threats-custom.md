---
title: Vytvořte vlastní analytická pravidla pro detekci podezřelých hrozeb pomocí Azure Sentinelu| Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak vytvořit vlastní analytická pravidla pro detekci podezřelých hrozeb pomocí Azure Sentinelu.
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
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585103"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Kurz: Vytvoření vlastních analytických pravidel pro detekci podezřelých hrozeb

Po [připojení zdrojů](quickstart-onboard.md) dat k Azure Sentinelu můžete vytvořit vlastní pravidla, která můžete vyhledávat konkrétní kritéria v celém prostředí a generovat incidenty, když jsou kritéria spárována, abyste je mohli prozkoumat. Tento kurz vám pomůže vytvořit vlastní pravidla pro detekci hrozeb s Azure Sentinel.

Tento kurz vám pomůže zjistit hrozby pomocí Azure Sentinelu.
> [!div class="checklist"]
> * Vytvoření analytických pravidel
> * Automatizace reakcí na hrozby

## <a name="create-custom-analytic-rules"></a>Vytvoření vlastních analytických pravidel

Můžete vytvořit vlastní analytická pravidla, která vám pomohou vyhledat typy hrozeb a anomálií, které jsou podezřelé ve vašem prostředí. Pravidlo zajišťuje, že budete okamžitě upozorněni, abyste mohli tísni, vyšetřovat a napravovat hrozby.

1. Na webu Azure Portal v části Azure Sentinel vyberte **Analytics**.

1. V horním řádku nabídek vyberte **+Vytvořit** a vyberte **pravidlo naplánovaného dotazu**. Otevře se **Průvodce pravidlem Analytics**.

    ![Vytvořit naplánovaný dotaz](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Na kartě **Obecné** zadejte jedinečný **název**a **popis**. V poli **Taktika** si můžete vybrat z kategorií útoků, podle kterých chcete pravidlo klasifikovat. Podle potřeby nastavte **závažnost výstrahy.** Když vytvoříte pravidlo, jeho **stav** je ve výchozím nastavení **povolen,** což znamená, že bude spuštěno ihned po dokončení jeho vytváření. Pokud nechcete, aby se spouštěla okamžitě, vyberte **Zakázáno**a pravidlo bude přidáno na kartu **Aktivní pravidla** a můžete ho povolit, když ho budete potřebovat.

    ![Zahájení vytváření vlastního analytického pravidla](media/tutorial-detect-threats-custom/general-tab.png)

1. Na kartě **Nastavit logiku pravidla** můžete buď napsat dotaz přímo do pole **dotazu pravidla,** nebo vytvořit dotaz v Log Analytics a pak jej tam zkopírovat a vložit.
 
   ![Vytvoření dotazu v Azure Sentinelu](media/tutorial-detect-threats-custom/settings-tab.png)

   - Podívejte se na oblast **náhledu výsledků** vpravo, kde Azure Sentinel zobrazuje počet výsledků (událostí protokolu), které dotaz vygeneruje, a při psaní a konfiguraci dotazu se mění průběžně. Graf zobrazuje počet výsledků za definované časové období, které je určeno nastavením v části **Plánování dotazů.**
    - Pokud zjistíte, že by dotaz aktivoval příliš mnoho nebo příliš časté výstrahy, můžete nastavit směrný plán v části **Prahová hodnota výstrahy.**

      Tady je ukázkový dotaz, který by vás upozornil, když se v Azure Activity vytvoří neobvyklý počet prostředků.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > Délka dotazu by měla být mezi 1 a 10 \*000 \*znaky a nesmí obsahovat "search " nebo "union ".

    1. Část **Entity mapy** slouží k propojení parametrů z výsledků dotazu s entitami uznávanými azure sentinelem. Tyto entity tvoří základ pro další analýzu, včetně seskupení výstrah do incidentů na kartě **Nastavení incidentů.**
    1. V části **Plánování dotazů** nastavte následující parametry:

       1. Nastavte **spustit dotaz každý** řídit, jak často je dotaz spuštěn – tak často, jak každých 5 minut nebo jako zřídka jako jednou denně.

       1. Nastavte **vyhledávací data z poslední** ho určit časové období dat, na které se dotaz vztahuje – například může dotaz za posledních 10 minut dat nebo za posledních 6 hodin dat.

       > [!NOTE]
       > Tato dvě nastavení jsou na sobě nezávislá, až do bodu. Dotaz lze spustit v krátkém intervalu pokrývající časové období delší než interval (ve skutečnosti s překrývajícími se dotazy), ale dotaz nelze spustit v intervalu, který přesahuje období disponibility, jinak budete mít mezery v celkovém pokrytí dotazu.

    1. K definování směrného plánu použijte oddíl **Prahová hodnota výstrahy.** Například nastavte **generovat výstrahu, když je počet výsledků dotazu** **větší než,** a zadejte číslo 1000, pokud chcete, aby pravidlo vygenerovalo výstrahu pouze v případě, že dotaz při každém spuštění generuje více než 1000 výsledků. Vzhledem k tomu, že se jedná o povinné pole, pokud nechcete nastavit směrný plán – to znamená, že pokud chcete, aby výstraha zaregistrovala každou událost – zadejte do číselného pole hodnotu 0.

    1. V části **Potlačení** můžete zapnout **zastavit spuštěný dotaz po vygenerování výstrahy Zapnuto,** pokud po zobrazení výstrahy chcete pozastavit činnost tohoto pravidla po dobu přesahující interval dotazu. **On** Pokud tuto hodnotu zapnete, je nutné nastavit **zastavit spouštění dotazu na** dobu, po kterou by měl dotaz přestat běžet, až 24 hodin.

1. Na kartě **Nastavení incidentů** můžete zvolit, jestli a jak Azure Sentinel změní výstrahy na události, které lze použít k akci. Pokud tato karta zůstane sama, Azure Sentinel vytvoří jeden, samostatný incident z každé výstrahy. Změnou nastavení na této kartě můžete zvolit, že nevytvoříte žádné incidenty, nebo seskupíte několik výstrah do jednoho incidentu.

    1. V části **Nastavení incidentů** je možnost **Vytvořit incidenty z výstrah aktivovaných tímto pravidlem analýzy** ve výchozím nastavení nastavena na **povoleno**, což znamená, že Azure Sentinel vytvoří jeden samostatný incident z každé výstrahy aktivované pravidlem.<br></br>Pokud nechcete, aby toto pravidlo vedlo k vytvoření jakýchkoli incidentů (například pokud toto pravidlo pouze shromažďuje informace pro následnou analýzu), nastavte toto **na Zakázáno**.

    1. Pokud v části **Seskupování výstrah** chcete vygenerovat jeden incident ze skupiny podobných nebo opakovaných výstrah, nastavte **výstrahy související se skupinou, aktivované tímto pravidlem analýzy, na incidenty** na **Povoleno**a nastavte následující parametry.

    1. **Omezte skupinu na výstrahy vytvořené ve vybraném časovém rámci**:<br></br> Určete časový rámec, ve kterém budou podobné nebo opakované výstrahy seskupeny. Všechny odpovídající výstrahy v tomto časovém rámci společně vygenerují incident nebo sadu incidentů (v závislosti na nastavení seskupení níže). Výstrahy mimo tento časový rámec vygenerují samostatný incident nebo sadu incidentů.

    2. **Skupinové výstrahy spuštěné tímto pravidlem analýzy do jednoho incidentu podle**: Zvolte základ, na kterém budou výstrahy seskupeny:

        - **Seskupte výstrahy do jednoho incidentu, pokud se všechny entity shodují**: <br></br>Výstrahy jsou seskupeny, pokud sdílejí stejné hodnoty pro každou z mapovaných entit (definované na kartě Nastavit logiku pravidla výše). Toto je doporučené nastavení.

        - **Seskupte všechny výstrahy aktivované tímto pravidlem do jednoho incidentu**: <br></br>Všechny výstrahy generované tímto pravidlem jsou seskupeny i v případě, že nesdílejí žádné identické hodnoty.

        - **Seskupte výstrahy do jednoho incidentu, pokud se vybrané entity shodují**: <br></br>Výstrahy jsou seskupeny, pokud sdílejí identické hodnoty pro některé mapované entity (které můžete vybrat z rozevíracího seznamu). Toto nastavení můžete použít například v případě, že chcete vytvořit samostatné incidenty založené na zdrojové nebo cílové IP adresě.

    3. **Znovu otevřít uzavřené odpovídající incidenty**: Pokud byl incident uzavřen (což znamená, že základní problém byl vyřešen) a následně je generována další výstraha, která by byla seskupena do tohoto incidentu, nastavte toto nastavení na **Povoleno,** pokud chcete, aby byl uzavřený incident znovu otevřen, a ponechte jako **Zakázáno,** pokud chcete, aby výstraha vytvořila nový incident.

1. Na kartě **Automatické odpovědi** vyberte všechny playbooky, které chcete spustit automaticky, když je výstraha generována vlastním pravidlem. Další informace o vytváření a automatizaci playbooků naleznete v [tématu Reakce na hrozby](tutorial-respond-threats-playbook.md).

1. Vyberte **Zkontrolovat a vytvořte,** chcete-li zkontrolovat všechna nastavení nového pravidla výstrahy, a pak vyberte **Vytvořit, chcete-li pravidlo výstrahy inicializovat**.
  
1. Po vytvoření výstrahy je do tabulky v části **Aktivní pravidla**přidáno vlastní pravidlo . Z tohoto seznamu můžete povolit, zakázat nebo odstranit každé pravidlo.

1. Chcete-li zobrazit výsledky vytvářených pravidel výstrah, přejděte na stránku **Incidenty,** kde můžete tísnit, [vyšetřovat incidenty](tutorial-investigate-cases.md)a napravovat hrozby.


> [!NOTE]
> Výstrahy generované v Azure Sentinelu jsou dostupné prostřednictvím [microsoft graph security](https://aka.ms/securitygraphdocs). Další informace naleznete v [dokumentaci k výstrahám zabezpečení aplikace Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak začít rozpoznávat hrozby pomocí Azure Sentinelu.

Chcete-li se dozvědět, jak automatizovat odpovědi na hrozby, [nastavte automatické reakce na hrozby v Azure Sentinelu](tutorial-respond-threats-playbook.md).

