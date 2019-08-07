---
title: Prozkoumat výstrahy pomocí Azure Sentinel Preview | Microsoft Docs
description: V tomto kurzu se dozvíte, jak prozkoumat výstrahy pomocí funkce Sentinel Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: 2cc33a9ac55ae9e906d88b72476d4b5ee244d2c8
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780430"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Kurz: Zjišťování hrozeb pomocí Azure Sentinel Preview

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Po [připojení zdrojů dat](quickstart-onboard.md) ke službě Azure Sentinel chcete být upozorněni, když dojde k nějaké podezřelé situaci. Abyste to mohli udělat, Azure Sentinel vám umožní vytvářet Pokročilá pravidla výstrah, která generují incidenty, které můžete přiřadit a použít k hloubku šetření anomálií a hrozeb ve vašem prostředí. 

Tento kurz vám pomůže detekovat hrozby pomocí služby Azure Sentinel.
> [!div class="checklist"]
> * Vytvořit pravidla detekce
> * Automatizace odpovědí na hrozby

## <a name="create-detection-rules"></a>Vytvořit pravidla detekce

Chcete-li prozkoumat incidenty, musíte nejprve vytvořit pravidla detekce. 

> [!NOTE]
> Výstrahy vygenerované v Azure Sentinel jsou k dispozici prostřednictvím [Microsoft Graph zabezpečení](https://aka.ms/securitygraphdocs). Další podrobnosti a partnery pro integraci najdete v [dokumentaci k výstrahám zabezpečení Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs) .

Pravidla detekce jsou založená na typech hrozeb a anomálií, které by ve vašem prostředí mohly být podezřelé, o kterých se chystáte hned, a zajišťuje, aby byly Surface, prověřeny a opraveny. 

1. V Azure Portal v části Azure Sentinel vyberte **Analytics**.

   ![Analýzy](./media/tutorial-detect-threats/alert-rules.png)

2. V horním řádku nabídek klikněte na **+ Přidat**.  

   ![Vytvořit pravidlo upozornění](./media/tutorial-detect-threats/create-alert-rule.png)

3. V části **vytvořit pravidlo výstrahy**zadejte popisný název a podle potřeby nastavte **závažnost** . 

4. Vytvořte dotaz v Log Analytics a pak jej vložte do pole **nastavit pravidlo upozornění** . Tady je ukázkový dotaz, který vás upozorní, když se v aktivitě Azure vytvoří neobvykléý počet prostředků.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > Délka dotazu by měla být mezi 1 a 10000 znaky a nesmí obsahovat "Search *" a "Union *".


5. V části **mapování entit** použijte pole v části **typ entity** k mapování sloupců v dotazu na pole entit rozpoznaná službou Azure Sentinel. Pro každé pole namapujte příslušný sloupec v dotazu, který jste vytvořili v Log Analytics, do pole příslušná entita. V rámci **vlastnosti**vyberte příslušný název sloupce. Každá entita obsahuje několik polí, například SID, GUID atd. Entitu můžete namapovat na základě kteréhokoli z polí, nikoli jenom u entity nejvyšší úrovně.

6. V **aktivační události výstrahy**definujte podmínky triggeru výstrahy. To definuje podmínky, které aktivují výstrahu. 

7. Nastavte **frekvenci** , jak často se spouští dotaz – stejně jako každých 5 minut, nebo jako jednou denně. 

8. Nastavte **období** , které řídí časový interval, ve kterém se dotaz spouští, a dá se spustit každou hodinu během 60 minut dat.

9. Můžete také nastavit potlačení. Potlačení je užitečné, pokud chcete zastavit spouštění duplicitních výstrah u stejného incidentu. Tímto způsobem můžete zastavit spouštění výstrah v průběhu konkrétního období. To vám může přispět k tomu, abyste se vyhnuli duplicitním výstrahám u stejného incidentu a po určitou dobu mohli potlačit po sobě jdoucí výstrahy. Pokud je například **frekvence** **Plánování výstrah** nastavena na 60 minut a **období plánování výstrahy** je nastaveno na dvě hodiny a výsledky dotazu překročí stanovenou prahovou hodnotu, aktivuje výstrahu dvakrát, při prvním zjištění. za posledních 60 minut a znovu, až bude během prvních 60 minut od 2 hodin vzorkování dat. Doporučujeme, aby v případě, že se aktivuje výstraha, potlačení měla být určena pro dobu určenou v období výstrahy. V našem příkladu můžete chtít nastavit potlačení na 60 minut, aby se výstrahy aktivovaly jenom pro události, ke kterým došlo během poslední hodiny.

8. Po vložení dotazu do pole **nastavit pravidlo upozornění** můžete okamžitě zobrazit simulaci výstrahy v rámci **simulace Logic Alert** , abyste mohli získat informace o tom, kolik dat se bude v určitém časovém intervalu generovat pro výstrahu. vytvořili jste. To bude záviset na tom, co jste nastavili pro **frekvenci** a **prahovou hodnotu**. Pokud zjistíte, že se v průměru bude vaše výstraha spouštět příliš často, budete chtít nastavit počet výsledků vyšší, aby bylo nad rámec průměrného směrného plánu.

9. Kliknutím na **vytvořit** inicializujte pravidlo upozornění. Po vytvoření výstrahy se vytvoří incident, který výstrahu obsahuje. Definovaná pravidla zjišťování můžete zobrazit jako řádky na kartě **Security Analytics** . Můžete také zobrazit počet shod pro každé pravidlo – aktivované výstrahy. V tomto seznamu můžete každé pravidlo Povolit, zakázat nebo odstranit. Můžete také vybrat tři tečky (...) na konci řádku pro každou výstrahu, chcete-li upravit, zakázat, klonovat, zobrazit shody nebo odstranit pravidlo. Stránka **Analytics** je galerie všech aktivních pravidel výstrah, včetně šablon, které povolíte, a pravidel upozornění, která vytvoříte na základě šablon.

1. Výsledky pravidel výstrah lze zobrazit na stránce incidenty, kde můžete určit jejich třídění, [prozkoumat incidenty](tutorial-investigate-cases.md)a napravit hrozby.



## <a name="automate-threat-responses"></a>Automatizace odpovědí na hrozby

SIEM/SOC týmy můžou být pravidelně inundated s výstrahami zabezpečení. Objem vygenerovaných výstrah je tak velký, takže správci zabezpečení jsou k dispozici zahlcení. To je velmi často v situacích, kdy se nedá prozkoumat mnoho výstrah, což je ponecháno v organizaci zranitelné vůči útokům, které jsou nepatrné. 

Mnoho z těchto výstrah, pokud není většina, je v souladu s opakovanými vzorci, které lze řešit pomocí konkrétních a definovaných opravných akcí. Sentinel Azure už umožňuje definovat svou nápravu v playbooky. V rámci definice PlayBook je také možné nastavit automatizaci v reálném čase, která vám umožní plně automatizovat definovanou odpověď na konkrétní výstrahy zabezpečení. Díky automatizaci v reálném čase můžou odpovědní týmy významně snižovat své úlohy tím, že plně automatizují rutinní reakce na opakované typy výstrah, což vám umožní soustředit se na jedinečné výstrahy, analyzovat vzory, lovecké hrozby a další informace.

Automatizace odpovědí:

1. Vyberte výstrahu, pro kterou chcete odpověď automatizovat.
1. V navigační nabídce pracovního prostoru Azure Sentinel vyberte **Analytics**.
1. Vyberte výstrahu, kterou chcete automatizovat. 
1. Na stránce **Upravit pravidlo výstrahy** v části **automatizace v reálném čase**vyberte **aktivovaný PlayBook** , který chcete spustit, když se toto pravidlo výstrahy shoduje.
1. Vyberte **Uložit**.

   ![automatizace v reálném čase](./media/tutorial-detect-threats/rt-configuration.png)


Kromě toho můžete ručně opravit upozornění spuštěním PlayBook z výstrahy, a to tak, že kliknete na **Zobrazit playbooky** a pak vyberete PlayBook, které se má spustit. Informace o tom, jak vytvořit novou PlayBook nebo upravit existující, najdete v tématu [Working with playbooky in Azure Sentinel](tutorial-respond-threats-playbook.md).



## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak začít s detekcí hrozeb pomocí funkce Azure Sentinel. 

Informace o tom, jak automatizovat odpovědi na hrozby, najdete v tématu [reakce na hrozby pomocí automatizovaného playbooky](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reakce](tutorial-respond-threats-playbook.md) na hrozby pro automatizaci reakcí na hrozby.

