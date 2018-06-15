---
title: Sestava ověření pro Azure zásobníku | Microsoft Docs
description: Pomocí sestavy kontrolu připravenosti zásobník Azure a zkontrolovat výsledky ověření.
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
ms.openlocfilehash: a0ca0ae3ed615f6bc2774364f7a443023b911b5d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937815"
---
# <a name="azure-stack-validation-report"></a>Sestava ověření Azure zásobníku
Nástroj pro kontrolu připravenosti zásobník Azure spustí ověření, které podporují nasazení a obsluhy prostředí Azure zásobníku. Tento nástroj zapisuje do souboru sestavy .json výsledky ověření. Sestava zobrazí podrobné a souhrnná data týkající se stavu požadavky na nasazení Azure zásobníku a na otočení tajné klíče pro existující nasazení zásobník Azure.  

 ## <a name="where-to-find-the-report"></a>Kde najít sestavy
Když je nástroj spuštěn, zaprotokoluje výsledky **AzsReadinessCheckerReport.json**. Nástroj vytvoří také protokolu s názvem **AzsReadinessChecker.log**. Umístění těchto souborů se zobrazí ve výsledcích ověření v prostředí PowerShell.

![Ověření spuštění](./media/azure-stack-validation-report/validation.png)

Oba soubory zachovat výsledky kontrol následné ověřování při spouštění ve stejném počítači.  Například nástroj lze spustit a ověřit certifikáty, spusťte znovu k ověření Azure identity a pak třetí čas k ověření registrace. Jsou k dispozici v sestavě výsledné .json výsledky všechny tři neúspěšná.  

Ve výchozím nastavení, oba soubory jsou zapsány do *C:\Users\<uživatelské jméno > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Použití **- OutputPath** ***&lt;cesta&gt;*** parametr na konci spuštění příkazového řádku a zadejte umístění různé sestavy.   
- Použití **- CleanReport** parametr na konci spusťte příkaz chcete vymazat informace z *AzsReadinessCheckerReport.json*. o předchozích spuštění nástroje.

## <a name="view-the-report"></a>Zobrazit zprávu
Chcete-li zobrazit sestavu v prostředí PowerShell, zadejte jako hodnotu pro cestu k sestavě **- možnost ReportPath**. Tento příkaz zobrazí obsah sestavy a také ověření, které ještě nemají výsledky.

Například pokud chcete zobrazit sestavu z řádku prostředí PowerShell, která je otevřená a umístění, kde se sestava nachází, spusťte tento příkaz: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

Výstup se podobá na následujícím obrázku:

![Zobrazit sestavu](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Zobrazit souhrnnou sestavu
Chcete-li zobrazit souhrn sestavy, můžete přidat **-shrnutí** přepnout na konec příkazového řádku prostředí PowerShell. Příklad: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

Souhrn zobrazí ověření, které nemají výsledky a označuje úspěch nebo selhání pro ověření, které jsou dokončeny. Výstup se podobá na následujícím obrázku:

![Souhrn sestavy](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Zobrazení filtrované sestavy
Chcete-li zobrazit sestavu filtrováno jeden typ ověřování, použijte **- ReportSections** parametr a určete jednu z následujících hodnot, které odpovídají typu ověřování, které chcete zobrazit:
- Certifikát
- AzureRegistration
- AzureIdentity
- Úlohy   
- Vše  

Chcete-li zobrazit sestavu je například souhrn pro certifikáty pouze, použijte následující příkaz prostředí PowerShell: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Další informace najdete v tématech
[Rutiny Start-AzsReadinessChecker – reference](azure-stack-azsreadiness-cmdlet.md)
