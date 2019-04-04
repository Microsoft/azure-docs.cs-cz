---
title: Sestava ověření pro Azure Stack | Dokumentace Microsoftu
description: Zkontrolujte výsledky ověření pomocí sestavy Kontrola připravenosti Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 7a6d2b417d7c17ea343ce3019a4ba05cf87b2219
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58800980"
---
# <a name="azure-stack-validation-report"></a>Sestava ověření služby Azure Stack

Použití *Kontrola připravenosti Azure Stack* nástroj spustit ověření, které podporují nasazení a údržby prostředí Azure Stack. Nástroj zapisuje výsledky do sestavy soubor .json. Sestava zobrazí podrobné a souhrnné údaje o stavu všech požadovaných součástí pro nasazení služby Azure Stack. Sestava také informace o otočení tajné kódy pro existující nasazení služby Azure Stack.  

## <a name="where-to-find-the-report"></a>Kde najít sestavy

Když je nástroj spuštěn, zaznamená výsledky do **AzsReadinessCheckerReport.json**. Nástroj také vytvoří protokol s názvem **AzsReadinessChecker.log**. Umístění těchto souborů se zobrazí spolu s výsledky ověření v prostředí PowerShell:

![spustit ověření](./media/azure-stack-validation-report/validation.png)

Oba soubory zachovat výsledky kontrol další ověření při spuštění ve stejném počítači. Například nástroj můžete spustit ověřování certifikátů, spusťte znovu ověření identit Azure a pak potřetí k ověření registrace. Výsledky všech tří ověřování jsou k dispozici do výsledné sestavy .json.  

Ve výchozím nastavení, oba soubory jsou zapsány do **C:\Users\<uživatelské jméno > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Použití `-OutputPath <path>` parametr na konec příkazového řádku a zadejte umístění různých sestav.
- Použití `-CleanReport` parametr na konec příkazového řádku vymazat informace o předchozích spuštění nástroje z **AzsReadinessCheckerReport.json**.

## <a name="view-the-report"></a>Zobrazit zprávu

Chcete-li zobrazit sestavy v prostředí PowerShell, zadejte cestu k sestavě jako hodnotu `-ReportPath`. Tento příkaz zobrazí obsah sestavy a identifikuje ověření, která ještě nemají výsledky.

Například chcete-li zobrazit sestavu z příkazového řádku Powershellu, která je otevřená do umístění, kde se sestava nachází, spusťte následující příkaz:

```shell
Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json
```

Výstup se podobá následujícímu příkladu:

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>Zobrazit souhrnnou sestavu

Chcete-li zobrazit souhrn sestavy, můžete přidat `-summary` parametr na konec příkazu prostředí PowerShell. Příklad:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

Souhrn zobrazuje ověření, která nemají výsledky a indikuje úspěch nebo selhání pro ověření, která se dokončí. Výstup se podobá následujícímu příkladu:

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation found no errors or warnings.

############### Registration Validation Summary ###############

Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>Zobrazení filtrované sestavy

Chcete-li zobrazit sestavu s filtrem na jeden typ ověřování, použijte **- ReportSections** parametr s jedním z následujících hodnot:

- Certifikát
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- Úlohy
- Vše  

Například chcete-li zobrazit sestavu shrnutí pro certifikáty, použít pouze následující příkazový řádek Powershellu:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate – Summary
```
