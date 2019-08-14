---
title: Přidat bránu firewall nové generace v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak implementovat Azure Security Center doporučení **Přidání brány firewall nové generace** a **směrování provozu jenom přes NGFW**.
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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "60706982"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Přidání brány firewall nové generace v Azure Security Center
Azure Security Center možná doporučujeme přidat bránu firewall nové generace (NGFW) od partnera Microsoftu, aby se zvýšila ochrana zabezpečení. Tento dokument vás seznámí s příkladem toho, jak to provést.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V okně **doporučení** vyberte **přidat bránu firewall nové generace**.
   ![Přidání brány firewall příští generace][1]
2. V okně **Přidat firewall nové generace** vyberte koncový bod.
   ![Vyberte koncový bod.][2]
3. Otevře se okno **přidat bránu firewall nové generace** . Můžete použít existující řešení, pokud je k dispozici, nebo můžete vytvořit nový. V tomto příkladu nejsou k dispozici žádná stávající řešení, takže vytvoříme NGFW.
   ![Vytvořit bránu firewall nové generace][3]
4. Pokud chcete vytvořit NGFW, vyberte řešení ze seznamu integrovaných partnerů. V tomto příkladu vybereme možnost **Check Point**.
   ![Vybrat řešení brány firewall nové generace][4]
5. Otevře se okno **kontrolní bod** , ve kterém najdete informace o partnerském řešení. V okně informace vyberte **vytvořit** .
   ![Okno s informacemi o bráně firewall][5]
6. Otevře se okno **vytvořit virtuální počítač** . Tady můžete zadat informace potřebné k vytvoření virtuálního počítače, na kterém běží NGFW. Postupujte podle kroků a poskytněte požadované informace NGFW. Kliknutím na tlačítko OK použijte.
   ![Vytvořit virtuální počítač pro spuštění NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Směrovat přenosy jenom přes firewall nové generace
Vraťte se k oknu **doporučení** . Po přidání NGFW prostřednictvím Security Center se vygenerovala nová položka, která se nazývá **směrování provozu prostřednictvím NGFW**. Toto doporučení se vytvoří jenom v případě, že jste nainstalovali NGFW prostřednictvím Security Center. Pokud máte internetové koncové body, Security Center doporučuje nakonfigurovat pravidla skupiny zabezpečení sítě, která vynutí příchozí provoz na VIRTUÁLNÍm počítači přes vaše NGFW.

1. V okně **doporučení**vyberte **směrovat přenosy jenom přes NGFW**.
   ![Směrování provozu jenom přes NGFW][7]
2. Tím se otevře okno **směrovat provoz jenom přes NGFW**, ve kterém jsou uvedené virtuální počítače, do kterých můžete směrovat provoz. V seznamu vyberte virtuální počítač.
   ![Výběr virtuálního počítače][8]
3. Otevře se okno pro vybraný virtuální počítač, ve kterém se zobrazí související příchozí pravidla. Popis vám poskytne další informace o možných dalších krocích. Vyberte **Upravit příchozí pravidla** a pokračujte v úpravách příchozího pravidla. Očekává se, že **zdroj** není nastavený na **žádný** pro internetové koncové body propojené s NGFW. Další informace o vlastnostech příchozího pravidla najdete v tématu [pravidla zabezpečení](../virtual-network/security-overview.md#security-rules).
   ![Konfigurovat pravidla pro omezení přístupu][9]
   ![upravit příchozí pravidlo][10]

## <a name="see-also"></a>Viz také:
V tomto dokumentu jste si ukázali, jak implementovat Security Center doporučení přidat bránu firewall nové generace. Další informace o NGFWs a řešení partnerů Check Point najdete v následujících tématech:

* [Firewall nové generace](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Přečtěte si, jak nakonfigurovat zásady zabezpečení.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si blogové příspěvky o zabezpečení Azure a dodržování předpisů.

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
