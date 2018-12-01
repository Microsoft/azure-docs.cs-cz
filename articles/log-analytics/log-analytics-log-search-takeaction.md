---
title: Uživatelem iniciované akce Runbooku Azure Automation ve službě Log Analytics | Dokumentace Microsoftu
description: Tento článek popisuje, jak spouštět Automation runbook z Log Analytics search výsledek na vyžádání.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 737fea452e80782446fdd4a6252ab6af0caaa1dc
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723308"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Provést akci s Runbook Automation z výsledků na vyhledávacím protokolu Log Analytics

Ve výsledku hledání protokolů ve službě Azure Log Analytics, můžete teď vybrat **provést akci** spustit runbook služby Automation.  Sada runbook lze napravit problém nebo provést jiné akce, jak shromažďovat informace o odstraňování potíží, odeslat e-mailu nebo vytvořit žádost o službu. 

## <a name="components-and-features-used"></a>Použité komponenty a funkce
* [Účet Azure Automation](../automation/automation-quickstart-create-account.md)
* [Pracovní prostor služby Log Analytics](log-analytics-queries.md)

## <a name="to-initiate-runbook-from-log-search"></a>K zahájení sady runbook z prohledávání protokolů

Reagovat na události a zahájit z výsledků vyhledávání protokolu sady runbook, začněte vytvořením prohledávání protokolu a z výsledků můžete vyvolat sadu runbook na vyžádání.  Toho lze dosáhnout z protokolu vyhledávací funkce [webu Azure portal](log-analytics-queries.md).  V tomto příkladu budeme provádět prohledávání protokolů z webu Azure portal pomocí základní ukázku této funkce.

1. Na webu Azure Portal, klikněte na tlačítko **všechny služby** a vyberte **Log Analytics**.  
2. Vyberte pracovní prostor Log Analytics.
3. V pracovním prostoru vyberte **prohledávání protokolů**.  
4. Na stránce hledání v protokolu proveďte hledání v protokolu.  
5. Z výsledků prohledávání protokolu, klikněte na tlačítko se třemi tečkami nalevo od polí a v místní nabídce vyberte **provádět akce na**.<br><br> ![Vyberte akci přijmout z výsledku hledání](./media/log-analytics-log-search-takeaction/log-search-takeaction-menuoption.png) 
6. Vyberte **spuštění sady runbook** a vyberte spuštění sady runbook.  Můžete vybrat jakoukoli sadu runbook v účtu Automation, který je propojený s pracovním prostorem Log Analytics.  Je třeba počítat s následujícím:

    * Sady Runbook jsou uspořádané podle značky
    * Hodnoty vstupní parametr Runbooku můžete vybrat přímo z pole výsledku hledání.  Rozevíracím seznamu se zobrazí zobrazení všechna dostupná pole z výsledku lze vybírat.  
    * Můžete také spouštět sadu runbook [procesu hybrid runbook worker](../automation/automation-hybrid-runbook-worker.md) nainstalovaného v počítači, který má problém, pokud máte odpovídající skupiny Hybrid Runbook Worker, který obsahuje pouze tento počítač jako člena.  Pokud název skupiny Hybrid Worker odpovídá názvu počítače, který je v výsledek hledání v protokolu, je automaticky vybrán skupině.    

6. Po kliknutí na **spustit**, aby bylo možné zkontrolovat stav úlohy se otevře stránka úlohy runbooku.   

Pokud můžete vybrat sadu runbook, která byla nakonfigurovaná na stav [volat z upozornění Log Analytics](../automation/automation-create-alert-triggered-runbook.md), má vstupní parametr s názvem **WebhookData** , který je **objekt** typu.  Pokud vstupní parametr je povinný, je potřeba předat výsledky hledání do runbooku, proto ji převede řetězec ve formátu JSON na typ objektu, který umožňuje filtrovat konkrétní položky, které budete odkazovat v aktivity sady runbook.  To provedete tak, že vyberete **výsledek (objekt) vyhledávání** z rozevíracího seznamu.<br><br> ![Vyberte datový objekt Webhooku pro parametr postupu runbook](media/log-analytics-log-search-takeaction/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Další postup

* Zkontrolujte [protokolu v log Analytics search odkaz](log-analytics-queries.md) zobrazíte všechna pole hledání a omezující vlastnosti, které jsou k dispozici ve službě Log Analytics.
* Zjistěte, jak vyvolat Automation runbook automaticky, najdete v tématu [volání runbooku Azure Automation z upozornění Log Analytics](../automation/automation-create-alert-triggered-runbook.md).  
