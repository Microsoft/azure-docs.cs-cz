---
title: Přehled Azure Automation DSC
description: Přehled o Azure Automation Desired State Configuration (DSC), podmínky a známé problémy
keywords: PowerShell dsc, konfigurace požadovaného stavu prostředí powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f6d49ffa59ed53c0a1966a4132fd5fe1689a13ce
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247345"
---
# <a name="azure-automation-dsc-overview"></a>Přehled Azure Automation DSC

Azure Automation DSC je služba Azure, která umožňuje zápis, spravovat a kompilovat PowerShell Desired State Configuration (DSC) [konfigurace](https://msdn.microsoft.com/powershell/dsc/configurations), importovat [prostředky DSC](https://msdn.microsoft.com/powershell/dsc/resources)a přiřadit konfigurace na cílových uzlů, vše v cloudu.

## <a name="why-use-azure-automation-dsc"></a>Proč používat Azure Automation DSC

Azure Automation DSC poskytuje několik výhod oproti použití DSC mimo Azure.

### <a name="built-in-pull-server"></a>Server integrované o přijetí změn

Azure Automation poskytuje server DSC o přijetí změn, podobně jako [služba DSC funkce Windows](/powershell/dsc/pullserver) tak, aby cílové uzly automaticky zobrazí konfigurace, v souladu s požadovaného stavu a zpětně hlásit dodržování předpisů.
Server integrované o přijetí změn ve službě Azure Automation eliminuje potřebu nastavili a spravovali vlastním serverem vyžádané replikace.
Azure Automation můžete cílit na virtuálních nebo fyzických Windows nebo Linuxem počítačů, v cloudu nebo místně.

### <a name="management-of-all-your-dsc-artifacts"></a>Správa všechny artefakty DSC

Azure Automation DSC přináší stejnou úroveň správy do [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) jako nabízí Azure Automation pro skriptování PowerShell.

Na webu Azure Portal nebo prostředí PowerShell můžete spravovat všechny vaše DSC konfigurace, prostředků a cílové uzly.

![Snímek obrazovky okna Azure Automation.](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importovat data pro generování sestav do Log Analytics

Uzly, které se spravují pomocí Azure Automation DSC odeslat podrobná data sestav stavu serveru integrované o přijetí změn.
Azure Automation DSC k odesílání dat do pracovního prostoru Log Analytics můžete nakonfigurovat.
Zjistěte, jak odesílat data o stavu DSC do pracovního prostoru Log Analytics, najdete v článku [vpřed Azure Automation DSC data pro generování sestav do služby Log Analytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Úvodní video

Raději se díváte, než čtete? Podíváme se na následující video z května 2015, kdy bylo poprvé oznámena Azure Automation DSC.

>[!NOTE]
>Koncepty a životního cyklu, které jsou popsané v tomto videu jsou sice správné, Azure Automation DSC provedení určité mnohem od tohoto videa.
>Je teď obecně dostupná, má mnohem rozsáhlejší uživatelské rozhraní na webu Azure Portal a podporuje řadu dalších funkcí.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Další postup

* Další informace jak připojit uzly pro správu s Azure Automation DSC, najdete v článku [připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md)
* Chcete-li začít používat Azure Automation DSC, přečtěte si téma [Začínáme s Azure Automation DSC](automation-dsc-getting-started.md)
* Další informace o kompilaci konfigurace DSC, takže můžete je přiřadit k cílové uzly, naleznete v tématu [kompilace konfigurací v Azure Automation DSC](automation-dsc-compile.md)
* Reference k rutinám Powershellu pro Azure Automation DSC, naleznete v tématu [rutiny Azure Automation DSC](/powershell/module/azurerm.automation/#automation)
* Informace o cenách najdete v tématu [ceny Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
* Příklad použití Azure Automation DSC v kanálu průběžného nasazování najdete v tématu [průběžné nasazování do IaaS virtuálních počítačů pomocí Azure Automation DSC a Chocolatey](automation-dsc-cd-chocolatey.md)
