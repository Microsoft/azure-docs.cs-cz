---
title: Povolit agenta virtuálního počítače v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak implementovat Azure Security Center doporučení **Povolit agenta virtuálního počítače**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 12934ad53050d16b89dd5b4175ca19a24d1ec4d9
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "60916265"
---
# <a name="enable-vm-agent-in-azure-security-center"></a>Povolit agenta virtuálního počítače v Azure Security Center
Aby [bylo možné shromažďování dat Povolit](security-center-enable-data-collection.md), musí být na virtuálních počítačích nainstalován agent virtuálního počítače.  Azure Security Center vám umožní zjistit, které virtuální počítače vyžadují agenta virtuálního počítače. doporučujeme na těchto virtuálních počítačích povolit agenta virtuálního počítače.

Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. V článku [Agenti a rozšíření virtuálních počítačů – Část 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) najdete informace o tom, jak agenta virtuálního počítače nainstalovat.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení. Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V okně **doporučení**vyberte **Povolit agenta virtuálního počítače**.
   ![Povolení agenta virtuálního počítače][1]
2. Tím se otevře okno **Agent virtuálního počítače, které chybí nebo**neodpovídá. Toto okno obsahuje seznam virtuálních počítačů, které vyžadují agenta virtuálního počítače. Podle pokynů v okně nainstalujte agenta virtuálního počítače.
   ![Agent virtuálního počítače chybí.][2]

## <a name="see-also"></a>Viz také:
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak se konfigurují zásady zabezpečení pro vaše předplatné Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
