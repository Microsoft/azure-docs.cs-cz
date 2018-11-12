---
title: Opravit konfigurace zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení služby Azure Security Center, "Opravit konfigurace zabezpečení."
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d4523a9e2d5a5466c1617930d14a8a2d25a0e16f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238773"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Opravit konfigurace zabezpečení v Azure Security Center
Azure Security Center analyzuje denně operační systém (OS) virtuálních počítačů (VM) a počítačů pro konfiguraci, které by mohly znamenat virtuálních počítačů a počítačů, které jsou zranitelnější vůči útokům. Security Center doporučuje, abyste vyřešili ohrožení zabezpečení, když vaše konfigurace operačního systému se neshoduje s pravidla konfigurace doporučené zabezpečení a doporučuje změny konfigurace, které tyto nedostatky zabezpečení řeší.

Další informace o konkrétní konfigurace se monitorují, najdete v článku [seznam doporučenými konfiguračními pravidly](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Zjistěte, jak přizpůsobit hodnocení konfigurace zabezpečení, najdete v článku [přizpůsobení operačního systému konfigurace zabezpečení v Azure Security Center (Preview)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implementace doporučení
"Opravit konfigurace zabezpečení" se zobrazí jako doporučení ve službě Security Center. Doporučení se zobrazí v části **doporučení** > **výpočty a aplikace**.

Tento příklad se vztahuje "Opravit konfigurace zabezpečení" doporučení v části **výpočty a aplikace**.
1. Ve službě Security Center, v levém podokně vyberte **výpočty a aplikace**.  
  **Výpočty a aplikace** otevře se okno.

   ![Náprava konfigurací zabezpečení][1]

2. Vyberte **náprava konfigurací zabezpečení**.  
  **Konfigurace zabezpečení** otevře se okno.

   ![V okně "Konfigurace zabezpečení"][2]

  Horní části řídicího panelu zobrazí:

  - **Neúspěšná pravidla podle závažnosti**: Celkový počet pravidel, konfigurace operačního systému se nezdařilo napříč virtuálních počítačů a počítačů rozdělené podle závažnosti.
  - **Neúspěšná pravidla podle typu**: Celkový počet pravidel, konfigurace operačního systému se nezdařilo napříč virtuálních počítačů a počítačů rozdělené podle typu.
  - **Neúspěšná pravidla Windows**: Celkový počet pravidel, která selhala v konfiguraci operačního systému Windows.
  - **Neúspěšná pravidla Linux**: Celkový počet pravidel, která selhala v konfiguraci operačního systému Linux.

  Dolní části řídicího panelu zobrazuje seznam všech pravidel, která selhala pro vaše virtuální počítače a počítače a závažnost chybějící aktualizace. Seznam obsahuje následující prvky:

  - **CCEID**: The CCE jedinečný identifikátor pro pravidlo. Security Center používá Common Configuration Enumeration (CCE) pro přiřazení jedinečných identifikátorů pro konfiguraci pravidla.
  - **Název**: název pravidla se nezdařilo.
  - **Typ pravidla**: *klíč registru*, *zásady zabezpečení*, *zásady auditu*, nebo *IIS* typ pravidla.
  - **Ne. virtuálních počítačů a počítačů**: Celkový počet virtuálních počítačů a počítačů, pro které platí pravidlo, které selhalo.
  - **Závažnost pravidla**: hodnota The CCE *kritický*, *důležité*, nebo *upozornění*.
  - **Stav**: aktuální stav doporučení:

    - **Otevřené**: Doporučení dosud nebylo řešeno.
    - **V průběhu**: doporučení se aktuálně zavádí prostředky a nevyžaduje žádné akce se.
    - **Vyřešené**: použil doporučení. Pokud je problém vyřešen, položka je vyšedlá.

3. Chcete-li zobrazit podrobnosti o pravidlo, které selhalo, vyberte ho v seznamu.

   ![Podrobné zobrazení pravidla konfigurace se nezdařilo][3]

   V podrobném přehledu se zobrazí následující informace:

   - **Název**: název pravidla.
   - **CCIED**: The CCE jedinečný identifikátor pro pravidlo.
   - **Verze operačního systému**: verze operačního systému virtuálního počítače nebo počítače.
   - **Závažnost pravidla**: hodnota The CCE *kritický*, *důležité*, nebo *upozornění*.
   - **Úplný popis**: popis pravidla.
   - **Ohrožení zabezpečení**: vysvětlení ohrožení zabezpečení nebo riziko, pokud nebude toto pravidlo použito.
   - **Potenciální dopad**: obchodní dopad, když se pravidlo použije.
   - **Protiopatřením**: nápravných kroků.
   - **Očekávaná hodnota**: hodnotu, která očekává se, když Security Center analyzuje konfigurace operačního systému virtuálního počítače pro pravidlo.
   - **Skutečná hodnota**: hodnotu, která se vrátí po analýzu konfigurace operačního systému virtuálního počítače pro pravidlo.
   - **Pravidlo operace**: operace pravidla, která se používá služba Security Center při analýze konfigurace operačního systému virtuálního počítače pro pravidlo.

4. V horní části okna podrobné zobrazení, vyberte **hledání**.  
  Vyhledávání se otevře seznam pracovních prostorů, které mají virtuální počítače a počítače s konfigurací neshoda vybrané zabezpečení. Výběr pracovního prostoru se zobrazí, jenom Pokud vybrané pravidlo se vztahuje na několika virtuálních počítačů, které jsou připojeny k různé pracovní prostory.

   ![Uvedené pracovní prostory][4]

5. Vyberte pracovní prostor.  
  Vyfiltrováno do pracovního prostoru s neshoda konfigurace zabezpečení se otevře vyhledávací dotaz Log Analytics.

   ![Pracovní prostor s ohrožení zabezpečení operačního systému][5]

6. V seznamu vyberte počítač.  
  Nový výsledek vyhledávání se otevře s informacemi filtrovat pouze pro daný počítač.

   ![Podrobné informace o vybraném počítači][6]

## <a name="next-steps"></a>Další postup
Tento článek vám ukázali, jak implementovat doporučení služby Security Center "Opravit konfigurace zabezpečení." Zjistěte, jak přizpůsobit hodnocení konfigurace zabezpečení, najdete v článku [přizpůsobení operačního systému konfigurace zabezpečení v Azure Security Center (Preview)](security-center-customize-os-security-config.md).

Jaké konkrétní konfigurace se monitorují najdete v tématu [seznam doporučenými konfiguračními pravidly](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Security Center používá Common Configuration Enumeration (CCE) pro přiřazení jedinečných identifikátorů pro konfiguraci pravidla. Další informace najdete v části [CCE](https://nvd.nist.gov/cce/index.cfm) lokality.

Další informace o službě Security Center, najdete v následujících zdrojích:

* Seznam podporovaných virtuálních počítačů s Linuxem a Windows, naleznete v tématu [podporovaných platforem ve službě Azure Security Center](security-center-os-coverage.md).
* Další informace o konfiguraci zásad zabezpečení pro vaše předplatná Azure a skupiny prostředků, najdete v článku [nastavení zásad zabezpečení ve službě Azure Security Center](security-center-policies.md).
* Informace o tom, jak vám doporučení pomáhají chránit prostředky Azure, najdete v článku [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).
* Zjistěte, jak můžete monitorovat stav svých prostředků Azure, najdete v článku [sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md).
* Zjistěte, jak spravovat a reagovat na výstrahy zabezpečení, najdete v článku [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md).
* Zjistěte, jak sledovat stav vašich partnerských řešení, najdete v článku [monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md).
* Odpovědi na nejčastější dotazy k používání této služby najdete v části [Azure Security Center – nejčastější dotazy](security-center-faq.md).
* Blog příspěvky o zabezpečení Azure a dodržování předpisů, najdete v části [blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
