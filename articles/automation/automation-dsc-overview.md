---
title: Přehled služby Azure Automation DSC
description: Přehled o DSC Azure Automation požadovaného stavu konfigurace (), podmínky a známé problémy
keywords: prostředí PowerShell dsc, konfigurace požadovaného stavu prostředí powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 83d5b89422a0181c06dbfe3b2bd8975ef7214b9d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="azure-automation-dsc-overview"></a>Přehled služby Azure Automation DSC

Azure Automation DSC je služba Azure, který umožňuje zapisovat a spravovat zkompilovat PowerShell požadovaného stavu konfigurace (DSC) [konfigurace](https://msdn.microsoft.com/powershell/dsc/configurations), import [prostředky DSC](https://msdn.microsoft.com/powershell/dsc/resources)a přiřadit konfigurace pro cílové uzly, všechny v cloudu.

## <a name="why-use-azure-automation-dsc"></a>Proč používat Azure Automation DSC.

Azure Automation DSC poskytuje několik výhod oproti použití DSC mimo Azure.

### <a name="built-in-pull-server"></a>Předdefinované načítacího serveru

Poskytuje služby Azure Automation [server DSC za](https://msdn.microsoft.com/powershell/dsc/pullserver) tak, aby cílové uzly automaticky zobrazí konfigurace, podřídit se požadované stavu a zpětně hlásit dodržování předpisů.
Předdefinované vyžádání serveru ve službě Azure Automation se eliminuje potřeba nastavit a spravovat vlastní server vyžádané replikace.
Služby Azure Automation, můžete vybrat virtuální nebo fyzická systému Windows nebo Linux počítače, v cloudu nebo místně.

### <a name="management-of-all-your-dsc-artifacts"></a>Správa všech artefaktů DSC

Azure Automation DSC přináší stejné vrstva správy [konfigurace požadovaného stavu prostředí PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) jako Azure Automation nabízí skriptů prostředí PowerShell.

Z portálu Azure nebo z prostředí PowerShell můžete spravovat všechny vaše DSC konfigurace, prostředky a cílové uzly.

![Snímek obrazovky okna Azure Automation.](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importovat data pro generování sestav do analýzy protokolů

Uzly, které budou spravovat pomocí Azure Automation DSC poslat podrobné údaje o chybách stav předdefinované načítacího serveru.
Můžete nakonfigurovat odesílat tato data do pracovního prostoru analýzy protokolů Azure Automation DSC.
Zjistěte, jak odesílat data o stavu DSC do pracovního prostoru analýzy protokolů, najdete v tématu [dál Azure Automation DSC data pro vytváření sestav k analýze protokolů](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Úvodní video

Raději se díváte, než čtete? Podíváme se na následující videa z květen 2015, když se poprvé oznámeno Azure Automation DSC.

>[!NOTE]
>Koncepty a životního cyklu, které jsou popsané v tomto videu jsou sice správné, Azure Automation DSC má posunula hodně dopředu a vzhledem k tomu, že záznamu tohoto videa.
>Nyní je obecně k dispozici, má mnohem rozsáhlejší uživatelské rozhraní na portálu Azure a podporuje mnoho další funkce.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Další postup

* Další informace jak pro zařadit uzly pro správu ve službě Azure Automation DSC, najdete v části [registrace počítačů pro správu Azure Automation DSC.](automation-dsc-onboarding.md)
* Chcete-li začít používat Azure Automation DSC, přečtěte si téma [Začínáme s Azure Automation DSC.](automation-dsc-getting-started.md)
* Další informace o kompilaci konfigurace DSC, takže můžete je přiřadit k cílové uzly najdete v tématu [kompilování konfigurace v Azure Automation DSC.](automation-dsc-compile.md)
* Referenční informace o rutinách prostředí PowerShell pro Azure Automation DSC, najdete v části [rutiny Azure Automation DSC.](/powershell/module/azurerm.automation/#automation)
* Informace o cenách naleznete v tématu [ceny Azure Automation DSC.](https://azure.microsoft.com/pricing/details/automation/)
* Příklad použití Azure Automation DSC v kanálu průběžné nasazování, najdete v sekci [průběžné nasazování a Chocolatey IaaS virtuálních počítačů pomocí Azure Automation DSC](automation-dsc-cd-chocolatey.md)
