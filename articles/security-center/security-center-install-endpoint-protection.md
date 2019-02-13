---
title: Spravovat problémy s endpoint protection pomocí Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak spravovat problémy s endpoint protection ve službě Azure Security Center.
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
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1ce1faa13c541939335d188866e15c683b6ffc28
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110264"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Spravovat problémy s endpoint protection pomocí Azure Security Center
Azure Security Center monitoruje stav ochrany proti malwaru a oznámí to pod oknem problémy ochrany koncového bodu. Security Center zvýrazňuje problémy, jako je například zjištěných hrozeb a nedostatečná ochrana, které můžou ohrožovat zabezpečení vašeho počítače a virtuální počítače (VM) na ně antimalwaru. Podle informací uvedených v části **problémy s Endpoint protection**, můžete určit plán veškeré zjištěné potíže vyřešit.

Security Center sestavy následující problémy s endpoint protection:

- Na virtuálních počítačích Azure – podporované antimalwarové řešení není nainstalovaná služba Endpoint protection není nainstalována na těchto virtuálních počítačích Azure.
- Řešení Endpoint protection není nainstalovaná na počítačích mimo Azure – podporované antimalwarové není nainstalována na tyto počítače mimo Azure.
- Stavu funkce Endpoint protection:

   - Zastaralý podpis – antimalwarové řešení je nainstalován na těchto virtuálních počítačů a počítačů, ale řešení nemá nejnovější antimalwarových signatur.
   - Žádná ochrana v reálném čase – antimalwarové řešení je nainstalován na těchto virtuálních počítačů a počítačů, ale není nakonfigurován pro ochranu v reálném čase.   Služba může být zakázán nebo může být Security Center se nepodařilo získat stav, protože řešení není podporován. Zobrazit [integrace partnerských](security-center-partner-integration.md) seznam podporovaných řešení.
   - Negenerují se sestavy – nainstalována antimalwarové řešení, ale negenerují se sestavy data.
   - Neznámé – antimalwarové řešení je nainstalovaná, ale jeho stav je neznámý nebo vytváření sestav k neznámé chybě.

   > [!NOTE]
   > Zobrazit [integrace řešení zabezpečení](security-center-partner-integration.md#integrated-azure-security-solutions) seznam zabezpečení řešení ochrany koncových bodů, který je integrovaná se službou Security Center.
   >
   >

## <a name="implement-the-recommendation"></a>Implementace doporučení
Zobrazí se problémy s Endpoint protection jako doporučení ve službě Security Center.  Pokud vaše prostředí je zranitelný vůči hrozbám antimalwaru, toto doporučení se zobrazí v části **doporučení** a v části **Compute**. Zobrazíte **vydá řídicí panel Endpoint protection**, budete muset postupovat podle výpočetní pracovního postupu.

V tomto příkladu budeme používat **Compute**.  Podíváme se na tom, jak nainstalovat antimalwarové na virtuálních počítačích Azure a na počítače mimo Azure.

## <a name="install-antimalware-on-azure-vms"></a>Nainstalujte antimalwarové na virtuálních počítačích Azure

1. Vyberte **Compute** hlavní nabídce služby Security Center nebo **přehled**.

   ![Zvolte Compute][1]

2. V části **Compute**vyberte **problémy s Endpoint protection**. **Problémy s Endpoint protection** otevře se řídicí panel.

   ![Vyberte problémy s Endpoint protection][2]

   Horní části řídicího panelu poskytuje:

   - Nainstalovat poskytovatelé endpoint protection – seznam různých zprostředkovatelů identifikované pomocí služby Security Center.
   - Stav stavu nainstalované endpoint protection – ukáže stav virtuálních počítačů a počítačů, které mají nainstalovaný žádné řešení ochrany koncových bodů. Tento graf zobrazuje počet virtuálních počítačů a počítačů, které jsou v pořádku a číslo s nedostatečnou ochranou.
   - Malware zjištěn – zobrazuje počet virtuálních počítačů a počítačů, ve kterém se hlásí, Security Center zjistila malware.
   - Napadené počítače – zobrazuje počet virtuálních počítačů a počítačů, ve kterém je Security Center reporting útoky pomocí služeb.

   V dolní části řídicího panelu je seznam koncových bodů potíže s ochranou, které zahrnují následující informace:  

   - **Celkový počet** – počet virtuálních počítačů a počítačů vliv problém.
   - Objekt panelu agregaci počet virtuálních počítačů a počítačů vliv problém. Barvy v panelu určit priority:

      - Červená – vysoká priorita, mělo by se řešit okamžitě
      - Oranžovou – Střední priorita, mělo by se řešit co nejdříve

3. Vyberte **na virtuálních počítačích Azure není nainstalovaná služba Endpoint protection**.

   ![Vybrat Endpoint protection není nainstalovaná na virtuálních počítačích Azure][3]

4. V části **na virtuálních počítačích Azure není nainstalovaná služba Endpoint protection** je seznam virtuálních počítačů Azure, která nemají nainstalovaný Antimalwarový program.  Můžete k instalaci antimalwarového na všechny virtuální počítače v seznamu nebo vyberte jednotlivé virtuální počítače k instalaci antimalwarového na kliknutím na konkrétní virtuální počítač.
5. V části **vyberte Endpoint protection**, vyberte řešení ochrany koncových bodů, které chcete použít. V tomto příkladu vyberte **Microsoft Antimalware**.
6. Zobrazí se další informace o řešení ochrany koncových bodů. Vyberte **Vytvořit**.

## <a name="install-antimalware-on-non-azure-computers"></a>Antimalware nainstalovat na počítače mimo Azure

1. Přejděte zpět na **problémy s Endpoint protection** a vyberte **Endpoint protection není nainstalovaná na počítačích mimo Azure**.

   ![Vybrat Endpoint protection není nainstalovaná na počítačích mimo Azure][4]

2. V části **Endpoint protection není nainstalovaná na počítačích mimo Azure**, vyberte pracovní prostor. Vyhledávací dotaz Log Analytics vyfiltrovaný tak, aby pracovní prostor se otevře a zobrazí seznam počítačů chybí antimalwarové. Vyberte počítač ze seznamu pro další informace.

   ![Hledání log Analytics][5]

Další výsledek vyhledávání se otevře s informacemi filtrovat pouze pro daný počítač.

  ![Hledání log Analytics][6]

> [!NOTE]
> Doporučujeme vám, že aplikace endpoint protection je možné zřídit za všechny virtuální počítače a počítače pomáhá s identifikací a likvidací virů, spywaru a dalšího škodlivého softwaru.
>
>

## <a name="next-steps"></a>Další postup
Tento článek vám ukázali, jak implementovat doporučení služby Security Center "Nainstalovat Endpoint Protection." Další informace o povolení Microsoft Antimalware v Azure, najdete v následujícím dokumentu:

* [Microsoft Antimalware pro Cloud Services a Virtual Machines](../security/azure-security-antimalware.md) – zjistěte, jak nasadit Microsoft Antimalware.

Další informace o službě Security Center, najdete v následujících dokumentech:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – přečtěte si blog příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
