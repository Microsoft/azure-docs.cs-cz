---
title: Posilte svůj postoj k zabezpečení pomocí Azure Security Center | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže posílit stav zabezpečení sledováním prostředků v Azure Security Center.
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
ms.openlocfilehash: 40352b7ae1f3fb6b1178f2dfe70cdca9871c76b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603768"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Posílení stavu zabezpečení s využitím služby Azure Security Center
Tento článek vám pomůže posílit stav zabezpečení. Pomocí možností monitorování v Azure Security Center se ujistěte, že zabezpečení prostředků je co nejtěsnější a sledujte dodržování zásad.

## <a name="how-do-you-strengthen-your-security-posture"></a>Jak posilujete svůj bezpečnostní postoj?
Pod pojmem sledování si často představujeme pozorování a čekání, až se stane nějaká událost, abychom na situaci reagovali. Posílení stavu zabezpečení znamená proaktivní strategii, která audituje vaše prostředky a identifikuje systémy, které nesplňují organizační standardy nebo osvědčené postupy.

Po povolení [zásad zabezpečení](tutorial-security-policy.md) pro prostředky předplatného služba Security Center analyzuje zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení. Informace o konfiguraci vaší sítě jsou k dispozici okamžitě. Získání informací o konfiguraci virtuálních počítačů a počítačů, jako je stav aktualizace zabezpečení nebo konfigurace operačního systému, může trvat hodinu i déle v závislosti na počtu virtuálních počítačů a počítačů, na kterých máte nainstalovaného agenta. Úplný seznam problémů a způsobů posílení sítě a nápravy rizik můžete zobrazit na dlaždici **Doporučení.**

Můžete zobrazit stav zabezpečení prostředků a všechny problémy podle typu prostředku:

- Informace o stavu prostředků počítače a aplikací a získání doporučení pro zlepšení jejich zabezpečení najdete [v tématu Ochrana počítačů a aplikací v Centru zabezpečení Azure.](security-center-virtual-machine-protection.md)
- Další informace najdete v tématu Ochrana sítě v Centru zabezpečení Azure najdete v tématu [Ochrana sítě v Centru zabezpečení Azure.](security-center-network-recommendations.md) 
- Další informace najdete v [tématu Ochrana služby Azure SQL a dat v Centru zabezpečení Azure](security-center-sql-service-recommendations.md) najdete v článku Ochrana služeb Azure SQL a dat v Centru zabezpečení Azure. 
- Další informace najdete v tématu Sledování identity a přístupu v Centru zabezpečení Azure najdete v článku [Sledování identity a přístupu v Centru zabezpečení Azure.](security-center-identity-access.md) 
- Další informace najdete v tématu [Správa přístupu k virtuálnímu počítači pomocí funkce just-in-time.](security-center-just-in-time.md) 


Další informace o tom, jak používat doporučení, najdete v článku [Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).



![Dlaždice stavu zabezpečení prostředků](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Viz také
V tomto článku jste zjistili, jak ve službě Azure Security Center používat funkce sledování. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md): Zjistěte, jak se v Azure Security Center konfiguruje nastavení zabezpečení.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak sledovat stav vašich partnerských řešení.