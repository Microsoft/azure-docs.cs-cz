---
title: Přidat brána firewall příští generace v Azure Security Center | Microsoft Docs
description: Tento dokument ukazuje, jak implementovat doporučení Azure Security Center **přidat Brána Firewall příští generace** a **trasy traffice prostřednictvím NGFW pouze**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 25601b01489b95de0e86b314b4b934d3cffd38c2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330736"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Přidat Brána Firewall příští generace v Azure Security Center
Azure Security Center může doporučujeme, abyste přidali brána firewall příští generace (NGFW) od partnera Microsoftu ke zvýšení ochrany vaší zabezpečení. Tento dokument vás příklad toho, jak to udělat provede.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** vyberte **přidat Brána Firewall příští generace**.
   ![Přidání brány firewall příští generace][1]
2. V **přidat Brána Firewall příští generace** okně vyberte koncový bod.
   ![Vyberte koncový bod][2]
3. Druhý **přidat Brána Firewall příští generace** otevře se okno. Můžete použít existující řešení, pokud je k dispozici nebo můžete vytvořit nový. V tomto příkladu nejsou žádná existující řešení k dispozici, vytvoříme NGFW.
   ![Vytvoření Brána Firewall příští generace][3]
4. Vytvořit NGFW, vyberte ze seznamu partnerů integrované řešení. V tomto příkladu jsme vyberte **kontrolní bod**.
   ![Vybrat řešení Brána Firewall příští generace][4]
5. **Kontrolní bod** otevře se okno, takže získáte informace o partnerských řešení. Vyberte **vytvořit** v okně informace.
   ![Okno informace o brány firewall][5]
6. **Vytvořit virtuální počítač** otevře se okno. Zde můžete zadat informace požadované pro číselníku virtuálního počítače (VM), který spouští NGFW. Postupujte podle kroků a zadejte požadované informace NGFW. Kliknutím na tlačítko OK použít.
   ![Vytvoření virtuálního počítače ke spuštění NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Směrování provozu jenom přes NGFW
Vraťte se na **doporučení** okno. Nový záznam vygenerovalo po přidání NGFW prostřednictvím Security Center, nazývá **směrování provozu prostřednictvím NGFW pouze**. Toto doporučení je vytvořen jen v případě, že jste nainstalovali vaší NGFW prostřednictvím Security Center. Pokud máte internetových koncových bodů, Security Center doporučí konfiguraci pravidel skupin zabezpečení sítě, které vynutit příchozí provoz do virtuálního počítače prostřednictvím vaší NGFW.

1. V **doporučení okno**, vyberte **směrování provozu prostřednictvím NGFW pouze**.
   ![Směrování provozu jenom přes NGFW][7]
2. Otevře se okno pro **směrování provozu prostřednictvím NGFW pouze**, který obsahuje seznam virtuálních počítačů, které je možné směrovat provoz. Vyberte virtuální počítač ze seznamu.
   ![Vyberte virtuální počítač][8]
3. Otevře se okno pro vybraný virtuální počítač, zobrazení související příchozích pravidel. Popis vám poskytne další informace o možných další kroky. Vyberte **upravit příchozí pravidla** pokračujte úpravy příchozího pravidla. Předpokládají, že **zdroj** není nastavený na **žádné** pro koncové body internetového propojené s NGFW. Další informace o vlastnostech příchozí pravidlo, najdete v části [pravidla zabezpečení](../virtual-network/security-overview.md#security-rules).
   ![Umožňuje konfigurovat pravidla k omezení přístupu][9]
   ![příchozí pravidlo úpravy][10]

## <a name="see-also"></a>Další informace najdete v tématech
Tento dokument vám ukázal, jak provést doporučení Security Center "Přidat Brána Firewall příští generace". Další informace o NGFWs a kontrolní bod partnerského řešení, naleznete v následujících tématech:

* [Brána Firewall příští generace](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Kontrolní bod vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – zjistěte, jak nakonfigurovat zásady zabezpečení.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

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
