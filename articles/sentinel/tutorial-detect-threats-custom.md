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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 9c4ba09c7e3eca4482ed56b0b337124aeec5b838
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928258"
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

1. V horním řádku nabídek vyberte **+ vytvořit** a vyberte **pravidlo plánovaného dotazu**. Otevře se **Průvodce vytvořením vlastního pravidla**.

    ![Vytvořit plánovaný dotaz](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Na kartě **Obecné** zadejte popisný název a popis. Nastavte **Závažnost výstrahy** podle potřeby. Když vytvoříte pravidlo, můžete ho povolit, což způsobí, že se spustí hned po dokončení jeho vytvoření. Pokud ho vytvoříte jako zakázaný, pravidlo se přidá na kartu **aktivních pravidel** a Vy ho můžete v případě potřeby povolit.

    ![Zahájení vytváření vlastního pravidla pro analytiky](media/tutorial-detect-threats-custom/general-tab.png)

1. Na kartě **Nastavení** můžete buď přímo napsat dotaz, nebo vytvořit dotaz v Log Analytics a pak ho vložit do pole **vyhledávací dotaz** . Když změníte a nakonfigurujete svůj dotaz, Azure Sentinel simuluje výsledky dotazu v okně **Náhled výsledků** na pravé straně. To vám umožní získat přehled o tom, kolik dat se vygenerovalo v konkrétním časovém intervalu pro vytvářenou výstrahu. Tato částka závisí na tom, co jste nastavili pro **příkaz Spustit dotaz každé** a **data vyhledávání z posledního**. Pokud vidíte, že výstraha v průměru vyvolala příliš často upozornění, můžete nastavit počet výsledků, který je vyšší, aby byl nad rámec průměrného směrného plánu.

   ![Vytvořit dotaz ve službě Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   Tady je ukázkový dotaz, který vás upozorní, když se v aktivitě Azure vytvoří neobvykléý počet prostředků.

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > Délka dotazu by měla být mezi 1 a 10 000 znaky a nesmí obsahovat "Search \*" ani "Union \*".

    1. V části **plánování dotazů**nastavte následující parametry:

        1.  **Pomocí příkazu Spustit dotaz** můžete nastavit **frekvenci** , jak často se dotaz spouští – stejně jako každých 5 minut nebo jako zřídka jako jednou denně.

        1.  Nastavte **data vyhledávání z poslední** na kontrolu časového okna, ve kterém se dotaz spustí, například může běžet každou hodinu během 1 až 60 minut dat.

        1. Můžete nastavit možnost Azure Sentinel pro **zastavení spuštění dotazu po vygenerování výstrahy** , pokud chcete, aby se výstraha zobrazila pouze jednou, když k ní dojde. Musíte nastavit okno, během kterého by se měl dotaz zastavit, a to až 24 hodin.

    1. V **aktivační události výstrahy**definujte podmínky triggeru výstrahy. V části **mapování entit**můžete namapovat sloupce v dotazu na pole entit rozpoznaná službou Azure Sentinel. Pro každé pole namapujte příslušný sloupec v dotazu, který jste vytvořili v Log Analytics, do pole příslušná entita. Každá entita obsahuje několik polí, například SID a GUID. Entitu můžete mapovat podle libovolných polí, nikoli jenom z entity nejvyšší úrovně.

1.  Na kartě **automatizace odpovědí** vyberte libovolný playbooky, který chcete spustit automaticky, když se vygeneruje výstraha vlastním pravidlem. Další informace o vytváření a automatizaci playbooky najdete v tématu [reakce na hrozby](tutorial-respond-threats-playbook.md).

    ![Automatizace reakce na hrozby v Azure Sentinel](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. Vyberte **zkontrolovat** a zkontrolujte všechna nastavení nového pravidla výstrahy a potom vyberte **vytvořit a inicializujte pravidlo upozornění**.

   ![Kontrola vlastního dotazu](media/tutorial-detect-threats-custom/review-tab.png)

1.  Po vytvoření výstrahy se do tabulky v části **aktivní pravidla**přidá vlastní pravidlo. V tomto seznamu můžete každé pravidlo Povolit, zakázat nebo odstranit.

1.  Pokud chcete zobrazit výsledky vytvořených pravidel výstrah, navštivte stránku **incidenty** , kde můžete určit jejich třídění, [prozkoumat incidenty](tutorial-investigate-cases.md)a napravit hrozby.


> [!NOTE]
> Výstrahy vygenerované v Azure Sentinel jsou k dispozici prostřednictvím [Microsoft Graph zabezpečení](https://aka.ms/securitygraphdocs). Další informace najdete v dokumentaci k [výstrahám zabezpečení Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak začít s detekcí hrozeb pomocí funkce Azure Sentinel.

Pokud se chcete dozvědět, jak automatizovat vaše odezvy na hrozby, [nastavte v Azure Sentinelu automatické odpovědi na hrozby](tutorial-respond-threats-playbook.md).

