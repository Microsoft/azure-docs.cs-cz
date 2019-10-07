---
title: Posílit stav zabezpečení pomocí Azure Security Center | Microsoft Docs
description: Tento článek vám pomůže posílit stav zabezpečení monitorováním vašich prostředků v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 57d47712fe20fac0bf370a9a100a07a37d7aa753
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996679"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Posílení zabezpečení stav pomocí Azure Security Center
Tento článek vám pomůže posílit stav zabezpečení. Využijte možnosti monitorování v Azure Security Center, abyste se ujistili, že zabezpečení prostředků je co nejtěsnější a jak monitorovat dodržování zásad.

## <a name="how-do-you-strengthen-your-security-posture"></a>Jak posílit stav zabezpečení?
Často se jedná o monitorování, které čeká na výskyt události, aby bylo možné reagovat na situaci. Posílení zabezpečení stav odkazuje na proaktivní strategii, která Audituje vaše prostředky a identifikuje systémy, které nesplňují standardy organizace nebo osvědčené postupy.

Po povolení [zásad zabezpečení](tutorial-security-policy.md) pro prostředky předplatného Security Center analyzuje zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení. Informace o konfiguraci sítě jsou k dispozici okamžitě. V závislosti na počtu virtuálních počítačů a počítačů s nainstalovaným agentem může trvat několik hodin, než se shromáždí informace o konfiguraci virtuálních počítačů a počítačů, jako je stav aktualizace zabezpečení nebo konfigurace operačního systému, aby byly dostupné. Můžete si prohlédnout úplný seznam problémů a způsobů, jak posílit síť a opravit rizika na dlaždici **doporučení** .

Můžete zobrazit stav zabezpečení svých prostředků a jakékoli problémy na typ prostředku:

- Pokud chcete monitorovat stav svých prostředků počítače a vašich aplikací a dostávat doporučení pro zlepšení zabezpečení, přečtěte si téma [ochrana počítačů a aplikací v Azure Security Center](security-center-virtual-machine-protection.md)
- Pokud chcete monitorovat vaše síťové prostředky, jako jsou virtuální počítače, skupiny zabezpečení sítě a koncové body a dostávat doporučení pro zlepšení zabezpečení, přečtěte si téma [Ochrana sítě v Azure Security Center](security-center-network-recommendations.md) , kde najdete další informace. 
- Další informace najdete [Azure Security Center v tématu Ochrana](security-center-sql-service-recommendations.md) dat a prostředků úložiště, například serverů SQL a účtů úložiště, a přijetí doporučení pro zlepšení zabezpečení. 
- Pokud chcete monitorovat identitu a získat přístup k prostředkům, včetně vícefaktorového ověřování a oprávnění účtu, a získáte doporučení pro zlepšení jejich zabezpečení, přečtěte si téma [monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md) , kde najdete další informace. 
- Pokud chcete monitorovat přístup k prostředkům za běhu, přečtěte si další informace v tématu [Správa přístupu k virtuálnímu počítači pomocí nástroje](security-center-just-in-time.md) za běhu. 


Další informace o tom, jak používat doporučení, najdete [v článku Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).



![Dlaždice stavu zabezpečení prostředků](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Viz také:
V tomto článku jste zjistili, jak používat možnosti monitorování v Azure Security Center. Další informace o Azure Security Center najdete v následujících tématech:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md): Naučte se konfigurovat nastavení zabezpečení v Azure Security Center.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Naučte se spravovat výstrahy zabezpečení a reagovat na ně.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Naučte se monitorovat stav vašich partnerských řešení.
* [Nejčastější dotazy Azure Security Center](security-center-faq.md): Přečtěte si nejčastější dotazy týkající se používání služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si blogové příspěvky o zabezpečení Azure a dodržování předpisů.
