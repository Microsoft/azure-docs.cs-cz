---
title: Přehled Azure Automation stavu konfigurace
description: Přehled o Azure Automation stavu Configuration (DSC), podmínky a známé problémy
keywords: PowerShell dsc, konfigurace požadovaného stavu prostředí powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ef55e6ca6fc913710bae68a7423369b33f26c009
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45628991"
---
# <a name="azure-automation-state-configuration-overview"></a>Přehled Azure Automation stavu konfigurace

Konfigurace stavu Azure Automation je služba Azure, která umožňuje zápis, spravovat a kompilovat PowerShell Desired State Configuration (DSC) [konfigurace](/powershell/dsc/configurations), importovat [prostředky DSC](/powershell/dsc/resources), a přiřadíte konfigurace k cílové uzly, vše v cloudu.

## <a name="why-use-azure-automation-state-configuration"></a>Proč používat Azure Automation stavu konfigurace

Konfigurace stavu Azure Automation poskytuje několik výhod oproti použití DSC mimo Azure.

### <a name="built-in-pull-server"></a>Server integrované o přijetí změn

Konfigurace stavu Azure Automation poskytuje server DSC o přijetí změn, podobně jako [služba DSC funkce Windows](/powershell/dsc/pullserver) tak, aby cílové uzly automaticky zobrazí konfigurace, v souladu s požadovaného stavu a zpětně hlásit jejich dodržování předpisů. Server integrované o přijetí změn ve službě Azure Automation eliminuje potřebu nastavili a spravovali vlastním serverem vyžádané replikace. Azure Automation můžete cílit na virtuálních nebo fyzických Windows nebo Linuxem počítačů, v cloudu nebo místně.

### <a name="management-of-all-your-dsc-artifacts"></a>Správa všechny artefakty DSC

Konfigurace stavu automatizace Azure přináší stejnou úroveň správy do [PowerShell Desired State Configuration](/powershell/dsc/overview) jako nabízí Azure Automation pro skriptování PowerShell.

Na webu Azure Portal nebo prostředí PowerShell můžete spravovat všechny vaše DSC konfigurace, prostředků a cílové uzly.

![Snímek obrazovky okna Azure Automation.](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importovat data pro generování sestav do Log Analytics

Uzly, které se spravují pomocí Azure Automation State Configuration odesílání podrobná data sestav stavu na server integrované o přijetí změn. Konfigurace stavu Azure Automation k odesílání dat do pracovního prostoru Log Analytics můžete nakonfigurovat. Zjistěte, jak odesílat data o stavu konfigurace stavu do pracovního prostoru Log Analytics, najdete v článku [vpřed Azure stav konfigurace Automation data pro generování sestav k protokolu nalytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Úvodní video

Raději se díváte, než čtete? Podíváme se na následující video z května 2015, kdy bylo poprvé oznámena konfigurace stavu služby Azure Automation.

> [!NOTE]
> Koncepty a životního cyklu, které jsou popsané v tomto videu jsou sice správné, konfigurace služby Azure Automation stavu provedení určité mnohem od tohoto videa. Je teď obecně dostupná, má mnohem rozsáhlejší uživatelské rozhraní na webu Azure Portal a podporuje řadu dalších funkcí.

[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Další postup

- Abyste mohli začít, najdete v článku [Začínáme s Azure Automation stavu konfigurace](automation-dsc-getting-started.md)
- Další informace jak připojit uzlů najdete v článku [připojování počítačů pro správu podle konfigurace stavu služby Azure Automation](automation-dsc-onboarding.md)
- Další informace o kompilaci konfigurace DSC, takže můžete je přiřadit k cílové uzly, naleznete v tématu [kompilace konfigurací v konfiguraci stavu služby Azure Automation](automation-dsc-compile.md)
- Reference k rutinám Powershellu najdete v části [rutiny Azure Automation stavu konfigurace](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v tématu [ceny konfigurace stavu služby Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití Azure Automation stav konfigurace v kanálu průběžného nasazování najdete v tématu [nepřetržité nasazení pomocí Azure Automation konfigurace stavu a Chocolatey](automation-dsc-cd-chocolatey.md)