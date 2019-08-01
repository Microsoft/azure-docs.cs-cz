---
title: Správa potíží s ochranou Endpoint Protection pomocí Azure Security Center | Microsoft Docs
description: Naučte se spravovat problémy ochrany koncových bodů v Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: rkarlin
ms.openlocfilehash: 4274b3684a2e9e79385a561b1cad426b4ffd954e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609940"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Správa potíží s ochranou Endpoint Protection pomocí Azure Security Center
Azure Security Center monitoruje stav antimalwarové ochrany a oznamuje ho v okně problémy ochrany koncového bodu. Security Center zvýrazní problémy, jako jsou zjištěné hrozby a nedostatečná ochrana, což může způsobit, že vaše virtuální počítače a počítače budou zranitelné proti malwarovým hrozbám. Pomocí informací v části **problémy s ochranou koncového bodu Endpoint Protection**můžete identifikovat plán, který řeší všechny zjištěné problémy.

Security Center oznamuje následující problémy ochrany koncového bodu:

- Služba Endpoint Protection není nainstalovaná na virtuálních počítačích Azure – na těchto virtuálních počítačích Azure není nainstalované podporované antimalwarové řešení.
- Služba Endpoint Protection není nainstalovaná na počítačích mimo Azure – na těchto počítačích mimo Azure se nepodporuje podporovaný antimalwarový software.
- Stav služby Endpoint Protection:

  - Zastaralý podpis – antimalwarové řešení je nainstalované na těchto virtuálních počítačích a počítačích, ale řešení nemá nejnovější antimalwarové signatury.
  - Bez ochrany v reálném čase – na tyto virtuální počítače a počítače je nainstalované antimalwarové řešení, ale není nakonfigurované na ochranu v reálném čase.   Služba může být zakázaná nebo Security Center nemůže získat stav, protože řešení není podporované. Seznam podporovaných řešení najdete v tématu [integrace partnera](security-center-os-coverage.md#supported-endpoint-protection-solutions) .
  - Bez hlášení – antimalwarové řešení je nainstalované, ale neoznamuje data.
  - Neznámé – je nainstalované antimalwarové řešení, ale jeho stav je neznámý nebo oznamuje neznámou chybu.

    > [!NOTE]
    > Seznam řešení zabezpečení Endpoint Protection integrovaných s Security Center najdete v tématu věnovaném [integraci řešení zabezpečení](security-center-os-coverage.md#supported-endpoint-protection-solutions) .
    >
    >

## <a name="implement-the-recommendation"></a>Implementace doporučení
Problémy s ochranou koncového bodu se zobrazují jako doporučení v Security Center.  Pokud je vaše prostředí zranitelné proti malwarovým hrozbám, toto doporučení se zobrazí v části **doporučení** a v části **COMPUTE**. Pokud chcete zobrazit **řídicí panel problémy ochrany koncového bodu**, musíte postupovat podle pracovního postupu služby Compute.

V tomto příkladu použijeme **COMPUTE**.  Podíváme se na to, jak nainstalovat antimalware na virtuální počítače Azure a v počítačích mimo Azure.

## <a name="install-antimalware-on-azure-vms"></a>Instalace antimalwarového softwaru na virtuálních počítačích Azure

1. V hlavní nabídce Security Center nebo **přehledu**vyberte **COMPUTE & aplikace** .

   ![Výběr COMPUTE][1]

2. V části **COMPUTE**vyberte **problémy s ochranou koncového bodu**. Otevře se řídicí panel **problémů ochrany koncového bodu Endpoint Protection** .

   ![Vybrat problémy ochrany koncového bodu][2]

   Horní část řídicího panelu poskytuje:

   - Nainstalovaná poskytovatelé Endpoint Protection – vypíše různé poskytovatele identifikované Security Center.
   - Nainstalovaný stav služby Endpoint Protection – zobrazuje stav virtuálních počítačů a počítačů, ve kterých je nainstalované řešení ochrany koncových bodů. Graf znázorňuje počet virtuálních počítačů a počítačů, které jsou v pořádku, a číslo s nedostatečnou ochranou.
   - Zjištěn malware – zobrazuje počet virtuálních počítačů a počítačů, ve kterých Security Center oznamuje zjištěný malware.
   - Napadené počítače – zobrazuje počet virtuálních počítačů a počítačů, na kterých Security Center nahlásí útoky malwarem.

   V dolní části řídicího panelu je k dispozici seznam problémů ochrany koncových bodů, které obsahují následující informace:  

   - **Total** – počet virtuálních počítačů a počítačů ovlivněných problémem.
   - Pruh, který agreguje počet virtuálních počítačů a počítačů ovlivněných problémem. Barvy na panelu identifikují prioritu:

      - Červená – vysoká priorita a měla by se řešit hned
      - Oranžová – střední priorita a měla by se řešit co nejdříve

3. Vyberte možnost **Endpoint Protection není nainstalovaná na virtuálních počítačích Azure**.

   ![Vyberte možnost Endpoint Protection není nainstalovaná na virtuálních počítačích Azure.][3]

4. V části **Endpoint Protection není nainstalovaná na virtuálních počítačích Azure** seznam virtuálních počítačů Azure, u kterých není nainstalovaný antimalwarový software.  Můžete zvolit instalaci antimalwaru na všechny virtuální počítače v seznamu nebo vybrat jednotlivé virtuální počítače pro instalaci antimalwaru kliknutím na konkrétní virtuální počítač.
5. V části **Vybrat službu Endpoint Protection**vyberte řešení ochrany koncových bodů, které chcete použít. V tomto příkladu vyberte **Microsoft Antimalware**.
6. Zobrazí se další informace o řešení ochrany koncových bodů. Vyberte **Vytvořit**.

## <a name="install-antimalware-on-non-azure-computers"></a>Instalace antimalwaru v počítačích mimo Azure

1. Vraťte se na **problémy ochrany koncových bodů** a vyberte **Endpoint Protection není nainstalovaná na počítačích mimo Azure**.

   ![Vyberte možnost Endpoint Protection není nainstalovaná na počítačích mimo Azure.][4]

2. V části **Endpoint Protection není nainstalovaná v počítačích mimo Azure**. Vyberte pracovní prostor. Spustí se vyhledávací dotaz Azure Monitor protokoly filtrované do pracovního prostoru a zobrazí se seznam počítačů, ve kterých chybí antimalwarový software. Pro další informace vyberte počítač ze seznamu.

   ![Hledání protokolů Azure Monitor][5]

Otevře se další výsledek hledání s informacemi filtrovanými pouze pro tento počítač.

  ![Hledání protokolů Azure Monitor][6]

> [!NOTE]
> Doporučujeme zřídit službu Endpoint Protection pro všechny virtuální počítače a počítače, aby bylo možné identifikovat a odstraňovat viry, spyware a další škodlivý software.
>
>

## <a name="next-steps"></a>Další postup
Tento článek vám ukázal, jak implementovat Security Center doporučení "instalovat Endpoint Protection". Další informace o tom, jak povolit Microsoft Antimalware v Azure, najdete v tomto dokumentu:

* [Microsoft Antimalware pro Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md) – Přečtěte si, jak nasadit Microsoft Antimalware.

Další informace o Security Center najdete v následujících dokumentech:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Přečtěte si, jak nakonfigurovat zásady zabezpečení.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si blogové příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
