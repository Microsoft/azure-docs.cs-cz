---
title: Posílení stavu zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Tento článek pomáhá posílení stavu zabezpečení díky monitorování vašich prostředků v Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 23369ac9f6cef74bac045017e8f956470e9a1159
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248856"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Posílení stavu zabezpečení v Azure Security Center
Tento článek pomáhá posílení stavu zabezpečení. Ujistěte se, že je tak vysoké jako možné a monitorování dodržování předpisů se zásadami zabezpečení vašich prostředků pomocí funkce sledování v Azure Security Center.

## <a name="how-do-you-strengthen-your-security-posture"></a>Jak je posílení stavu zabezpečení?
Pod pojmem sledování si často představujeme pozorování a čekání, až se stane nějaká událost, abychom na situaci reagovali. Posílení stavu zabezpečení odkazuje na používání proaktivní strategie, kdy se auditují vaše prostředky a identifikují systémy, které nesplňují organizační standardy nebo osvědčené postupy.

Po povolení [zásad zabezpečení](security-center-policies.md) pro prostředky předplatného služba Security Center analyzuje zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení. Informace o konfiguraci vaší sítě jsou k dispozici okamžitě. Získání informací o konfiguraci virtuálních počítačů a počítačů, jako je stav aktualizace zabezpečení nebo konfigurace operačního systému, může trvat hodinu i déle v závislosti na počtu virtuálních počítačů a počítačů, na kterých máte nainstalovaného agenta. Můžete zobrazit úplný seznam problémů a způsoby, jak posílení zabezpečení sítě a opravovat rizika **doporučení** dlaždici.

Můžete zobrazit stav zabezpečení svých prostředků a všechny problémy na typ prostředku:

- Monitorovat stav svých prostředků počítače a aplikace a dostávat doporučení pro zlepšení jejich zabezpečení, najdete v článku [ochrana vašich počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
- Sledování síťových prostředků, jako jsou virtuální počítače, skupiny zabezpečení sítě a koncových bodů a dostávat doporučení pro vylepšení zabezpečení najdete v tématu [ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md) Další informace informace. 
- Monitorovat vaše data a prostředky úložiště, jako je například SQL servery a účty úložiště a dostávat doporučení pro zlepšení jejich zabezpečení, přečtěte si téma [služby ochrany SQL Azure a dat ve službě Azure Security Center](security-center-sql-service-recommendations.md) Další informace . 
- Monitorování identity a přístupu prostředků, včetně vícefaktorového ověřování a účet oprávnění a dostávat doporučení pro vylepšení zabezpečení najdete v tématu [monitorovat identitu a přístup ve službě Azure Security Center](security-center-identity-access.md) Další informace. 
- Monitorování pouze přístup k prostředkům v najdete v tématu [spravovat přístup k virtuálním počítačům pomocí metody právě včas](security-center-just-in-time.md) Další informace. 


Další informace o tom, jak používat doporučení, najdete v článku [Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).



![Dlaždice stavu zabezpečení prostředků](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Další informace najdete v tématech
V tomto článku jste zjistili, jak ve službě Azure Security Center používat funkce sledování. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](security-center-policies.md): Zjistěte, jak se v Azure Security Center konfiguruje nastavení zabezpečení.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak sledovat stav vašich partnerských řešení.
* [Časté otázky k Azure Security Center](security-center-faq.md): Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.
