---
title: Opravit konfigurace zabezpečení v Azure Security Center | Microsoft Docs
description: Tento dokument ukazuje, jak implementovat Azure Security Center doporučení, "Konfigurace zabezpečení napravit."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 477973298d8cc9d99da78e36274933e0bb737c4f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2018
ms.locfileid: "28103927"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Opravit konfigurace zabezpečení v Azure Security Center
Azure Security Center analyzuje denně operační systém (OS) virtuálních počítačů (VM) a počítačů pro konfiguraci, které by mohly znamenat virtuálních počítačů a počítačů zranitelnější vůči útokům. Security Center doporučuje, když vaše konfigurace operačního systému neodpovídá pravidla konfigurace doporučené zabezpečení a doporučí změny konfigurace, které tyto nedostatky řeší chyby zabezpečení.

Další informace o konkrétní konfigurace, které jsou monitorovány, najdete v článku [seznam doporučenou konfiguraci pravidel](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Naučte se přizpůsobovat vyhodnocování konfigurace zabezpečení, najdete v tématu [přizpůsobení operačního systému konfigurace zabezpečení v Azure Security Center (Preview)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implementace doporučení
"Opravit konfigurace zabezpečení" je zobrazen jako doporučení ve službě Security Center. Doporučení se zobrazí v části **doporučení** > **výpočetní**.

Tento příklad obsahuje doporučení "Opravit konfigurace zabezpečení" v části **výpočetní**.
1. V Centru zabezpečení, v levém podokně vyberte **výpočetní**.  
  **Výpočetní** otevře se okno.

   ![Náprava konfigurací zabezpečení][1]

2. Vyberte **napravit konfigurace zabezpečení**.  
  **Konfigurace zabezpečení** otevře se okno.

   ![Okno "Konfigurace zabezpečení"][2]

  V horní části řídicího panelu zobrazí:

  - **Pravidla se nezdařila v důsledku závažnost**: Celkový počet pravidel, že konfigurace operačního systému se nezdařilo napříč virtuálních počítačů a počítačů, které jsou rozdělená podle závažnosti.
  - **Se nezdařilo pravidla podle typu**: Celkový počet pravidel, že konfigurace operačního systému se nezdařilo napříč virtuálních počítačů a počítačů, které jsou rozdělená podle typu.
  - **Pravidla Windows se nezdařilo**: Celkový počet pravidel podle konfigurace vašeho operačního systému Windows se nezdařilo.
  - **Se nezdařilo Linux pravidla**: Celkový počet pravidel se nezdařila v důsledku vaše konfigurace operačního systému Linux.

  V dolní části řídicího panelu obsahuje seznam všech neúspěšných pravidel pro virtuální počítače a počítače a závažnost chybějící aktualizace. Seznam obsahuje následující prvky:

  - **CCEID**: The CCE jedinečný identifikátor pro pravidlo. Security Center používá společné konfigurace – výčet (CCE) k přiřazení jedinečné identifikátory konfigurační pravidla.
  - **Název**: název pravidla se nezdařila.
  - **Typ pravidla**: *klíč registru*, *zásady zabezpečení*, nebo *zásady auditu* typ pravidla.
  - **Ne. virtuální počítače a počítače**: Celkový počet virtuálních počítačů a počítačů, které se nezdařilo pravidlo vztahuje.
  - **Pravidlo závažnost**: hodnota CCE *kritický*, *důležité*, nebo *upozornění*.
  - **Stav**: aktuální stav doporučení:

    - **Otevřené**: Doporučení dosud nebylo řešeno.
    - **V průběhu**: doporučení se aktuálně na prostředky a není třeba žádné akce.
    - **Vyřešit**: byl použit doporučení. Pokud je problém vyřešen, položka je neaktivní.

3. Chcete-li zobrazit podrobnosti o selhání pravidla, vyberte ho v seznamu.

   ![Podrobné zobrazení pravidla konfigurace se nezdařila][3]

   V podrobném zobrazení zobrazí následující informace:

   - **Název**: název pravidla.
   - **CCIED**: The CCE jedinečný identifikátor pro pravidlo.
   - **Verze operačního systému**: verze operačního systému virtuálního počítače nebo počítače.
   - **Pravidlo závažnost**: hodnota CCE *kritický*, *důležité*, nebo *upozornění*.
   - **Úplný popis**: popis pravidla.
   - **Ohrožení zabezpečení**: vysvětlení ohrožení zabezpečení nebo riziko, pokud není použita pravidlo.
   - **Potenciální dopad**: obchodní dopad při použití pravidla.
   - **Protiopatřením**: nápravy kroky.
   - **Očekávaná hodnota**: hodnota, která očekává se, když Security Center analyzuje konfiguraci operačního systému virtuálního počítače podle pravidla.
   - **Skutečná hodnota**: hodnota, která je vrácena po analýzu konfiguraci operačního systému virtuálního počítače podle pravidla.
   - **Pravidlo operaci**: operace pravidla, která se použije pomocí služby Security Center při analýze konfigurace operačního systému virtuálního počítače podle pravidla.

4. V horní části okna podrobné zobrazení, vyberte **vyhledávání**.  
  Hledání otevře se seznam pracovní prostory, které mají virtuální počítače a počítače s neshoda konfigurace vybrané zabezpečení. Výběr pracovního prostoru se zobrazí, jenom Pokud vybrané pravidlo platí pro víc virtuálních počítačů, které jsou připojené k jiné pracovní prostory.

   ![Uvedené pracovních prostorů][4]

5. Vyberte pracovní prostor.  
  Vyhledávací dotaz analýzy protokolů otevře filtrované do pracovního prostoru s neshoda konfigurace zabezpečení.

   ![Pracovní prostor se ohrožení zabezpečení operačního systému][5]

6. V seznamu vyberte počítač.  
  Nové výsledek hledání otevře s informacemi, které jsou filtrovány pouze pro tento počítač.

   ![Podrobné informace o vybraném počítači][6]

## <a name="next-steps"></a>Další postup
Tento článek vám ukázal, jak provést doporučení Security Center "Konfigurace zabezpečení napravit." Naučte se přizpůsobovat vyhodnocování konfigurace zabezpečení, najdete v tématu [přizpůsobení operačního systému konfigurace zabezpečení v Azure Security Center (Preview)](security-center-customize-os-security-config.md).

Konkrétní konfigurace, které jsou monitorovány najdete v tématu [seznam doporučenou konfiguraci pravidel](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Security Center používá společné konfigurace – výčet (CCE) k přiřazení jedinečné identifikátory konfigurační pravidla. Další informace najdete na [CCE](https://nvd.nist.gov/cce/index.cfm) lokality.

Další informace o službě Security Center, najdete v následujících zdrojích informací:

* Seznam podporovaných Windows a virtuální počítače s Linuxem najdete v tématu [podporovaných platforem v Azure Security Center](security-center-os-coverage.md). 
* Zjistěte, jak nakonfigurovat zásady zabezpečení pro skupiny prostředků a předplatná Azure, najdete v tématu [nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md). 
* Další informace, jak vám doporučení pomáhají chránit prostředky v Azure najdete v tématu [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md). 
* Naučte se monitorovat stav svých prostředků Azure, najdete v tématu [sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md). 
* Další informace o správě a reakce na výstrahy zabezpečení naleznete v tématu [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md).
* Zjistěte, jak sledovat stav vašich partnerských řešení, najdete v tématu [sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md).
* Odpovědi na nejčastější dotazy o použití této služby, naleznete v části [Azure Security Center – nejčastější dotazy](security-center-faq.md).
* Příspěvky o zabezpečení Azure a dodržování předpisů, najdete v části [blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
