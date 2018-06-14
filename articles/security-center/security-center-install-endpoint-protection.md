---
title: Správa služby endpoint protection problémy s Azure Security Center | Microsoft Docs
description: Zjistěte, jak spravovat problémy ochrany koncového bodu v Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2017
ms.author: terrylan
ms.openlocfilehash: abbcb0a8e0206d78ca94520dfa81ab92506c47af
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2017
ms.locfileid: "23936291"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Správa služby endpoint protection problémy s Azure Security Center
Azure Security Center sledovat stav ochrany proti malwaru a sestavách to v okně Endpoint protection problémy. Security Center označuje problémy, jako je například zjištěných hrozeb a nedostatečnou ochranou, které můžete k ohrožení své virtuální počítače (VM) a počítače antimalwarových hrozeb. Podle informací uvedených v části **potíže s ochranou koncového bodu**, můžete identifikovat plán veškeré zjištěné potíže vyřešit.

Security Center sestavy následující potíže s ochranou koncového bodu:

- Aplikace Endpoint protection není nainstalován na virtuálních počítačích Azure – podporované antimalwarové řešení není nainstalována na těchto virtuálních počítačích Azure.
- Aplikace Endpoint protection není nainstalován v počítačích bez Azure – podporované antimalwarových není nainstalován na tyto počítače mimo Azure.
- Stavu funkce Endpoint protection:

   - Zastaralý podpis – antimalwarové řešení je nainstalovaná na těchto virtuálních počítačů a počítačů, ale řešení nemá nejnovější antimalwarových signatur.
   - Žádná ochrana v reálném čase – antimalwarové řešení je nainstalovaná na těchto virtuálních počítačů a počítačů, ale není nakonfigurován pro ochranu v reálném čase.   Služba může být zakázán nebo Security Center může Nepodařilo se získat stav, protože řešení není podporována. V tématu [partner integrace](security-center-partner-integration.md) seznam podporovaných řešení.
   - Nevykazují – antimalwarové řešení je nainstalován, ale není data pro vytváření sestav.
   - Je nainstalována Neznámá – antimalwarové řešení, ale její stav je neznámý nebo generování sestav k neznámé chybě.

   > [!NOTE]
   > V tématu [integrovat řešení zabezpečení](security-center-partner-integration.md#integrated-azure-security-solutions) seznam zabezpečení řešení ochrany koncových bodů, který je integrovaný s Security Center.
   >
   >

## <a name="implement-the-recommendation"></a>Implementace doporučení
Problémy ochrany koncového bodu je zobrazen jako doporučení ve službě Security Center.  Pokud je prostředí bude zranitelný vůči antimalwarových hrozby, toto doporučení se zobrazí v části **doporučení** a v části **výpočetní**. Chcete-li zobrazit **problémy řídicího panelu ochrany koncového bodu**, potřebujete pracovní postup výpočetní.

V tomto příkladu použijeme **výpočetní**.  Podíváme se na postup instalace antimalwarových na virtuálních počítačích Azure a na počítače mimo Azure.

## <a name="install-antimalware-on-azure-vms"></a>Nainstalujte antimalwarových na virtuálních počítačích Azure

1. Vyberte **výpočetní** v hlavní nabídce Security Center nebo **přehled**.

   ![Vyberte výpočetní][1]

2. V části **výpočetní**, vyberte **potíže s ochranou koncového bodu**. **Potíže s ochranou koncového bodu** otevře řídicí panel.

   ![Vyberte potíže s ochranou koncového bodu][2]

   Horní části řídicí panel poskytuje:

   - Nainstalovat zprostředkovatele ochrany koncového bodu - seznamy různé zprostředkovatele identifikovaný Security Center.
   - Stav stavu nainstalované služby endpoint protection – zobrazuje stav virtuálních počítačů a počítačů, které mají nainstalovaný řešení ochrany koncových bodů. Graf zobrazuje počet virtuálních počítačů a počítačů, které jsou v pořádku a číslo s nedostatečnou ochranou.
   - Malware zjištěn – zobrazuje počet virtuálních počítačů a počítačů, kde je generování sestav Security Center zjistila malware.
   - Attacked počítače – zobrazuje počet virtuálních počítačů a počítačů, kde je Security Center hlášení útoky malwaru nalezeného.

   V dolní části řídicího panelu je seznam koncový bod problémy ochrany, které obsahuje následující informace:  

   - **Celkový počet** -počet virtuálních počítačů a počítačů dopad na problém.
   - A panelu agregování počet virtuálních počítačů a počítačů dopad na problém. Barvy v panelu určení priority:

      - Červená - s vysokou prioritou a mělo by se řešit okamžitě
      - Oranžová – Střední priorita, mělo by se řešit co nejdříve

3. Vyberte **Endpoint protection není nainstalován na virtuálních počítačích Azure**.

   ![Vyberte aplikace Endpoint protection není nainstalován na virtuálních počítačích Azure][3]

4. V části **Endpoint protection není nainstalován na virtuálních počítačích Azure** je seznam virtuálních počítačů Azure, které nemají nainstalovaný Antimalwarový program.  Můžete k instalaci antimalwarových na všech virtuálních počítačích v seznamu nebo vyberte jednotlivé virtuální počítače nainstalovat antimalwarových na kliknutím na konkrétní virtuální počítač.
5. V části **vyberte Endpoint protection**, vyberte řešení ochrany koncových bodů, které chcete použít. V tomto příkladu vyberte **Antimalware od Microsoftu**.
6. Zobrazí se další informace o řešení ochrany koncových bodů. Vyberte **Vytvořit**.

## <a name="install-antimalware-on-non-azure-computers"></a>Nainstalovat antimalwarových na počítače mimo Azure

1. Přejděte zpět na **potíže s ochranou koncového bodu** a vyberte **Endpoint protection není nainstalován v počítačích bez Azure**.

   ![Vyberte aplikace Endpoint protection není nainstalován v počítačích bez Azure][4]

2. V části **Endpoint protection není nainstalován v počítačích bez Azure**, vyberte pracovní prostor. Filtrované do pracovního prostoru analýzy protokolů vyhledávací dotaz se otevře a uvádí počítače s chybějícími proti malwaru. Vyberte počítač ze seznamu pro další informace.

   ![Hledání analýzy protokolů][5]

Další výsledek hledání otevře s informacemi, které jsou filtrovány pouze pro tento počítač.

  ![Hledání analýzy protokolů][6]

> [!NOTE]
> Doporučujeme vám, že aplikace endpoint protection zřídí pro všechny virtuální počítače a počítače mohly identifikovat a odstraňovat viry, spyware a další škodlivý software.
>
>

## <a name="next-steps"></a>Další kroky
Tento článek vám ukázal, jak provést doporučení Security Center "Nainstalovat Endpoint Protection." Další informace o povolení Antimalware od Microsoftu v Azure, najdete v následujících dokumentech:

* [Antimalware od Microsoftu pro cloudové služby a virtuální počítače](../security/azure-security-antimalware.md) – informace o nasazení Antimalware od Microsoftu.

Další informace o službě Security Center, najdete v následujících dokumentech:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – zjistěte, jak nakonfigurovat zásady zabezpečení.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
