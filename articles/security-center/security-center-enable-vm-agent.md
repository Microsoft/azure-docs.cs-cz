---
title: Povolit agenta virtuálního počítače v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení služby Azure Security Center **povolit agenta virtuálního počítače**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7a059cd5c22306c42adc4c7c8fcc4e52c3ebcd00
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313567"
---
# <a name="enable-vm-agent-in-azure-security-center"></a>Povolit agenta virtuálního počítače v Azure Security Center
Agent virtuálního počítače musí být nainstalovaný na virtuálních počítačích (VM) za účelem [Povolit shromažďování dat](security-center-enable-data-collection.md).  Azure Security Center vám umožní zobrazit které virtuální počítače vyžadují agenta virtuálního počítače a doporučí, abyste povolili agenta virtuálního počítače na těchto virtuálních počítačích.

Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. V článku [Agenti a rozšíření virtuálních počítačů – Část 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) najdete informace o tom, jak agenta virtuálního počítače nainstalovat.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení. Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **okno doporučení**vyberte **povolit agenta virtuálního počítače**.
   ![Povolení agenta virtuálního počítače][1]
2. Otevře se okno **virtuálního počítače agenta je chybí nebo neodpovídá**. Toto okno obsahuje seznam virtuálních počítačů, které vyžadují agenta virtuálního počítače. Postupujte podle pokynů v okně instalace agenta virtuálního počítače.
   ![Chybí Agent virtuálního počítače][2]

## <a name="see-also"></a>Další informace najdete v tématech
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-azure-policy.md) – Zjistěte, jak se konfigurují zásady zabezpečení pro vaše předplatné Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
