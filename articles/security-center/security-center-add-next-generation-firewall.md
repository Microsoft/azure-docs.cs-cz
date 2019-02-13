---
title: Přidání brány firewall příští generace ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení Azure Security Center **přidání brány Firewall příští generace** a **směrování provozu jenom přes NGFW**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 731102037b596091b80fbdfa02a8ff3c111b556e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105514"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Přidání brány Firewall příští generace ve službě Azure Security Center
Azure Security Center může doporučit přidání brány firewall příští generace (NGFW) od partnera Microsoftu o navýšení vaší ochrany zabezpečení. Tento dokument vás příklad toho, jak to udělat.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** okně vyberte **přidání brány Firewall příští generace**.
   ![Přidání brány firewall příští generace][1]
2. V **přidání brány Firewall příští generace** okno, vyberte koncový bod.
   ![Vybrat koncový bod][2]
3. Sekundy **přidání brány Firewall příští generace** se otevře okno. Můžete použít existující řešení, pokud je k dispozici nebo vytvořit nové. V tomto příkladu nejsou žádná existující řešení k dispozici, vytvoříme NGFW.
   ![Vytvoření brány Firewall příští generace][3]
4. K vytvoření NGFW, vyberte ze seznamu integrovaných partnerů řešení. V tomto příkladu vybereme **Check Point**.
   ![Vyberte řešení brány Firewall příští generace][4]
5. **Check Point** otevře se okno, že poskytuje informace o partnerských řešení. Vyberte **vytvořit** v okně informace.
   ![Informace o okno Brána firewall][5]
6. **Vytvořit virtuální počítač** se otevře okno. Tady můžete zadat informace požadované pro zprovoznění virtuálního počítače (VM), na kterém běží NGFW. Postupujte podle pokynů a zadejte požadované informace NGFW. Vyberte OK, chcete-li použít.
   ![Vytvoření virtuálního počítače ke spuštění NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Směrování provozu jenom přes NGFW
Vraťte se **doporučení** okno. Po přidání NGFW prostřednictvím centra zabezpečení, nazývá se vygeneroval nový záznam **směrování provozu jenom přes NGFW**. Toto doporučení se vytvoří pouze v případě, že jste nainstalovali vaše NGFW prostřednictvím centra zabezpečení. Pokud máte internetových koncových bodů, Security Center doporučuje nakonfigurovat pravidla skupiny zabezpečení sítě, které vynucují příchozí provoz k vašemu virtuálnímu počítači přes vaše NGFW.

1. V **okno doporučení**vyberte **směrování provozu jenom přes NGFW**.
   ![Směrování provozu jenom přes NGFW][7]
2. Otevře se okno **směrování provozu jenom přes NGFW**, která uvádí seznam virtuálních počítačů, které může směrovat provoz. V seznamu vyberte virtuální počítač.
   ![Vyberte virtuální počítač][8]
3. Otevře se okno pro vybraný virtuální počítač, zobrazení související příchozí pravidla. Popis vám poskytne další informace o možných další kroky. Vyberte **upravit příchozí pravidla** pokračujte s úpravami příchozí pravidlo. Předpokladem je, že **zdroj** není nastavená na **jakékoli** pro internetových koncových bodů propojené s NGFW. Další informace o vlastnostech příchozí pravidlo, najdete v článku [pravidla zabezpečení](../virtual-network/security-overview.md#security-rules).
   ![Konfigurace pravidel pro omezení přístupu][9]
   ![upravit příchozí pravidla][10]

## <a name="see-also"></a>Další informace najdete v tématech
Tento dokument vám ukázali, jak implementovat doporučení služby Security Center "Přidání brány Firewall příští generace." Další informace o NGFWs a partnerského řešení Check Point, naleznete v následujících tématech:

* [Firewall nové generace](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [VSEC Check Point](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – přečtěte si blog příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
