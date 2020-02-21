---
title: Vytváření vlastních pravidel pro analytiky k detekci podezřelých hrozeb s využitím služby Azure Sentinel | Microsoft Docs
description: V tomto kurzu se naučíte vytvářet vlastní pravidla pro analytiky k detekci podezřelých hrozeb s využitím funkce Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: rkarlin
ms.openlocfilehash: c643c037506725b1a48588ca779d074b6aecf7c2
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77506080"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Kurz: vytvoření vlastních pravidel pro analytiky k detekci podezřelých hrozeb

Po [připojení zdrojů dat](quickstart-onboard.md) ke službě Azure Sentinel můžete vytvořit vlastní pravidla, která budou vyhledávat konkrétní kritéria napříč vaším prostředím, a generovat incidenty, když se kritéria shodují, abyste je mohli prozkoumat. Tento kurz vám pomůže vytvořit vlastní pravidla pro detekci hrozeb pomocí služby Azure Sentinel.

Tento kurz vám pomůže detekovat hrozby pomocí služby Azure Sentinel.
> [!div class="checklist"]
> * Vytvořit analytická pravidla
> * Automatizace odpovědí na hrozby

## <a name="create-custom-analytic-rules"></a>Vytváření vlastních pravidel pro analytiky

Můžete vytvořit vlastní analytická pravidla, která vám pomůžou vyhledat typy hrozeb a anomálií, které jsou podezřelé ve vašem prostředí. Pravidlo vám zajistí, že se vám pošle oznámení hned, abyste mohli určit jejich třídění, prozkoumat je a vyřešit hrozby.

1. V Azure Portal v části Azure Sentinel vyberte **Analytics**.

1. V horním řádku nabídek vyberte **+ vytvořit** a vyberte **pravidlo plánovaného dotazu**. Tím se otevře **Průvodce analytickým pravidlem**.

    ![Vytvořit plánovaný dotaz](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Na kartě **Obecné** zadejte jedinečný **název**a **Popis**. V poli **taktiku** si můžete vybrat z kategorií útoků, podle kterých se pravidlo klasifikuje. Nastavte **závažnost** výstrahy podle potřeby. Když vytvoříte pravidlo, jeho **stav** je ve výchozím nastavení **povolený** , což znamená, že se spustí hned po dokončení jeho vytvoření. Pokud nechcete, aby se spouštěla hned, vyberte **disabled (zakázáno**) a pravidlo se přidá na kartu **aktivní pravidla** a Vy ho můžete v případě potřeby povolit.

    ![Zahájení vytváření vlastního pravidla pro analytiky](media/tutorial-detect-threats-custom/general-tab.png)

1. Na kartě **nastavit logiku pravidla** můžete buď napsat dotaz přímo do pole **dotazu pravidla** , nebo vytvořit dotaz v Log Analytics a pak ho zkopírovat a vložit.
 
   ![Vytvořit dotaz ve službě Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Podívejte se na pravé místo v oblasti **Náhled výsledků** , kde se v Azure Sentinel zobrazuje počet výsledků (protokoluje události), který dotaz vygeneruje a při psaní a konfiguraci dotazu se průběžně mění. Graf zobrazuje počet výsledků v rámci definovaného časového období, které je určeno nastavením v sekci **plánování dotazů** .
    - Pokud vidíte, že dotaz by aktivoval příliš mnoho nebo příliš časté výstrahy, můžete nastavit směrný plán v části **prahová hodnota pro výstrahu** .

      Tady je ukázkový dotaz, který vás upozorní, když se v aktivitě Azure vytvoří neobvykléý počet prostředků.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > Délka dotazu by měla být mezi 1 a 10 000 znaky a nesmí obsahovat "Search \*" ani "Union \*".

    1. Pomocí oddílu **mapové entity** můžete propojit parametry z výsledků dotazu do entit rozpoznaných pomocí služby Azure Sentinel. Tyto entity tvoří základ pro další analýzu, včetně seskupení výstrah na incidenty na kartě **Nastavení incidentu** .
    1. V části **plánování dotazů** nastavte následující parametry:

       1. **Pomocí příkazu Spustit dotaz vždy** proveďte kontrolu nad tím, jak často se má dotaz spouštět – stejně jako každých 5 minut nebo jako nečasto jako jednou denně.

       1. Nastavte **hledaná data z poslední** pro určení časového období dat pokrytých dotazem – můžete například zadat dotaz na posledních 10 minut dat nebo posledních 6 hodin dat.

       > [!NOTE]
       > Tato dvě nastavení jsou nezávislá na sobě navzájem, až do bodu. Dotaz můžete spustit v krátkém intervalu pokrývající dobu delší, než je interval (v důsledku překrývajících se dotazů), ale nemůžete spustit dotaz v intervalu, který překračuje období pokrytí. v opačném případě budete mít v celkovém pokrytí dotazu mezery.

    1. Pomocí oddílu **prahová hodnota pro výstrahu** definujte směrný plán. Například nastavte **Generovat výstrahu, pokud je počet výsledků dotazu** **větší než** a zadejte číslo 1000, pokud chcete, aby pravidlo vygenerovalo výstrahu pouze v případě, že dotaz vygeneruje více než 1000 výsledků při každém spuštění. Toto pole je povinné, pokud nechcete nastavit směrný plán – to znamená, že pokud chcete, aby vaše výstraha zaregistrovala každou událost – zadejte 0 do pole číslo.

    1. V části **potlačení** můžete zapnout funkci **zastavit běžící dotaz po vygenerování výstrahy** nastavení **v** případě, že po obdržení výstrahy budete chtít pozastavit operaci tohoto pravidla v časovém intervalu, který překračuje interval dotazu. Pokud tuto funkci zapnete v, musíte nastavit **zastavit běžící dotaz pro** na dobu, po kterou by měl dotaz skončit, a to až 24 hodin.

1. Na kartě **Nastavení incidentu** si můžete vybrat, jestli a jakým způsobem funkce Sentinel Azure zapne výstrahy na nenapadnutelné incidenty. Pokud je tato karta jenom vlevo, Azure Sentinel vytvoří jeden samostatný incident od každého a každého upozornění. Můžete zvolit, aby se nevytvořily žádné incidenty, nebo pokud chcete seskupit několik výstrah do jednoho incidentu, a to změnou nastavení na této kartě.

    1. V části **Nastavení incidentu** můžete **vytvořit incidenty z výstrah aktivovaných tímto pravidlem analýzy** **, což znamená, že**Azure Sentinel vytvoří jeden samostatný incident od každého a všechny výstrahy aktivované pravidlem.<br></br>Pokud nechcete, aby toto pravidlo vedlo k vytvoření jakýchkoli incidentů (například pokud toto pravidlo slouží pouze ke shromažďování informací pro následnou analýzu), nastavte tuto hodnotu na **zakázáno**.

    1. Pokud chcete, aby se jeden incident vygeneroval ze skupiny podobných nebo opakovaných výstrah, v části **seskupování výstrah** nastavte **výstrahy související se skupinou, aktivované pomocí tohoto pravidla analýzy, do incidentů** , které se mají **Povolit**, a nastavte následující parametry.

    1. **Omezte skupinu na výstrahy vytvořené v rámci vybraného časového rámce**:<br></br> Určete časový rámec, ve kterém budou seskupeny podobné nebo opakované výstrahy. Všechny odpovídající výstrahy v tomto časovém rámci budou souhrnně generovat incident nebo sadu incidentů (v závislosti na nastavení seskupení níže). Výstrahy mimo tento časový rámec způsobí vygenerování samostatného incidentu nebo sady incidentů.

    2. **Výstrahy skupin aktivované pomocí tohoto pravidla analýzy do jednoho incidentu**: vyberte základnu, na které se budou seskupovat výstrahy:

        - **Seskupit výstrahy do jednoho incidentu, pokud se všechny entity shodují**: <br></br>Výstrahy jsou seskupené dohromady, pokud sdílí stejné hodnoty pro každou namapovanou entitu (definované na kartě nastavit logiku pravidla výše). Toto je doporučené nastavení.

        - **Seskupit všechny výstrahy aktivované tímto pravidlem do jednoho incidentu**: <br></br>Všechny výstrahy vygenerované tímto pravidlem jsou seskupené i v případě, že nesdílejí žádné identické hodnoty.

        - **Seskupit výstrahy do jednoho incidentu, pokud se vybrané entity shodují**: <br></br>Výstrahy jsou seskupené dohromady, pokud sdílí stejné hodnoty pro některé mapované entity (které můžete vybrat z rozevíracího seznamu). Toto nastavení můžete chtít použít například v případě, že chcete vytvořit samostatné incidenty založené na zdrojových nebo cílových IP adresách.

    3. **Znovu otevřít uzavřené odpovídající incidenty**: Pokud byl incident uzavřený (což znamená, že došlo k vyřešení základního problému) a následně se vygeneruje další výstraha, která by se do tohoto incidentu přidala, nastavte toto nastavení na **povoleno** , pokud chcete, aby byl uzavřený incident znovu otevřený, a nechejte jako **zakázaný** , pokud chcete, aby výstraha vytvořila nový incident.

1. Na kartě **automatizované odpovědi** vyberte libovolný playbooky, který chcete spustit automaticky, když se vygeneruje výstraha vlastním pravidlem. Další informace o vytváření a automatizaci playbooky najdete v tématu [reakce na hrozby](tutorial-respond-threats-playbook.md).

1. Vyberte **zkontrolovat a vytvořit** a zkontrolujte všechna nastavení nového pravidla výstrahy a potom vyberte **vytvořit a inicializujte pravidlo upozornění**.
  
1. Po vytvoření výstrahy se do tabulky v části **aktivní pravidla**přidá vlastní pravidlo. V tomto seznamu můžete každé pravidlo Povolit, zakázat nebo odstranit.

1. Pokud chcete zobrazit výsledky vytvořených pravidel výstrah, navštivte stránku **incidenty** , kde můžete určit jejich třídění, [prozkoumat incidenty](tutorial-investigate-cases.md)a napravit hrozby.


> [!NOTE]
> Výstrahy vygenerované v Azure Sentinel jsou k dispozici prostřednictvím [Microsoft Graph zabezpečení](https://aka.ms/securitygraphdocs). Další informace najdete v dokumentaci k [výstrahám zabezpečení Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak začít s detekcí hrozeb pomocí funkce Azure Sentinel.

Pokud se chcete dozvědět, jak automatizovat vaše odezvy na hrozby, [nastavte v Azure Sentinelu automatické odpovědi na hrozby](tutorial-respond-threats-playbook.md).

