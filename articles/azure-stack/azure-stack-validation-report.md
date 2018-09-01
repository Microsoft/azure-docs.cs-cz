---
title: Sestava ověření pro Azure Stack | Dokumentace Microsoftu
description: Zkontrolujte výsledky ověření pomocí sestavy Kontrola připravenosti Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 06b5660a9428e98d2e99b5d447a05700968ec884
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2018
ms.locfileid: "43381909"
---
# <a name="azure-stack-validation-report"></a>Sestava ověření služby Azure Stack
Použijte nástroj prerequisite Checker připravenosti Azure Stack spustit ověření, které podporují nasazení a údržby prostředí Azure Stack. Nástroj zapisuje výsledky do sestavy soubor .json. Sestava zobrazí podrobné a souhrnné údaje o stavu všech požadovaných součástí pro nasazení služby Azure Stack. Sestava také zobrazí informace o otočení tajné kódy pro existující nasazení Azure Stack.  

 ## <a name="where-to-find-the-report"></a>Kde najít sestavy
Když je nástroj spuštěn, zaznamená výsledky do **AzsReadinessCheckerReport.json**. Nástroj také vytvoří protokol s názvem **AzsReadinessChecker.log**. Umístění těchto souborů se zobrazí s výsledky ověření v prostředí PowerShell.

![spustit ověření](./media/azure-stack-validation-report/validation.png)

Oba soubory zachovat výsledky kontrol další ověření při spuštění ve stejném počítači.  Například nástroj můžete spustit ověřování certifikátů, spusťte znovu ověření identit Azure a pak potřetí k ověření registrace. Výsledky všech tří ověřování jsou k dispozici do výsledné sestavy .json.  

Ve výchozím nastavení, oba soubory jsou zapsány do *C:\Users\<uživatelské jméno > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Použití **- OutputPath** ***&lt;cesta&gt;*** parametr na konci spuštění příkazového řádku a zadejte umístění různých sestav.   
- Použití **- CleanReport** parametr na konci příkazu run chcete vymazat informace z *AzsReadinessCheckerReport.json*. informace o předchozích spuštění tohoto nástroje.

## <a name="view-the-report"></a>Zobrazit zprávu
Chcete-li zobrazit sestavy v prostředí PowerShell, zadejte cestu k sestavě jako hodnotu **– možnost ReportPath**. Tento příkaz zobrazí obsah sestavy a identifikuje ověření, která ještě nemají výsledky.

Například chcete-li zobrazit sestavu z příkazového řádku Powershellu, která je otevřená do umístění, kde se sestava nachází, spusťte tento příkaz: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

Výstup vypadá podobně jako na následujícím obrázku:

![Zobrazit sestavu](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Zobrazit souhrnnou sestavu
Chcete-li zobrazit souhrn sestavy, můžete přidat **-Summary** přepnout na konec příkazového řádku Powershellu. Příklad: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

Souhrn zobrazuje ověření, která nemají výsledky a indikuje úspěch nebo selhání pro ověření, která se dokončí. Výstup vypadá podobně jako na následujícím obrázku:

![Souhrnná sestava](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Zobrazení filtrované sestavy
Chcete-li zobrazit sestavu s filtrem na jeden typ ověřování, použijte **- ReportSections** parametr s jedním z následujících hodnot:
- Certifikát
- AzureRegistration
- AzureIdentity
- Počet úloh   
- Všechna  

Například chcete-li zobrazit sestavu shrnutí pro certifikáty, použít pouze následující příkazový řádek Powershellu: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Další informace najdete v tématech
[Reference k rutinám Start AzsReadinessChecker](azure-stack-azsreadiness-cmdlet.md)
