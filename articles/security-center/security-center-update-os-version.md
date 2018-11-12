---
title: Verze aktualizace operačního systému ve službě Azure Security Center | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak implementovat doporučení služby Azure Security Center **aktualizovat verzi operačního systému**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1898926e0488722d18a105697bead18e29e6a35d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254889"
---
# <a name="update-os-version-in-azure-security-center"></a>Aktualizovat verzi operačního systému ve službě Azure Security Center
Pro virtuální počítače (VM) ve službě cloud services Azure Security Center vám doporučí aktualizovat operační systém (OS) Pokud je k dispozici novější verze.  Pouze cloud services – webové a pracovní role běžících v produkčním prostředí, které jsou monitorovány sloty.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
> 
> 

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** okně vyberte **aktualizovat verzi operačního systému**.
   ![Aktualizace verze operačního systému][1]
2. Otevře se okno **aktualizovat verzi operačního systému**. Postupujte podle kroků v tomto okně se aktualizovat verzi operačního systému.

## <a name="see-also"></a>Další informace najdete v tématech
Tento článek vám ukázali, jak implementovat doporučení služby Security Center "Verze aktualizace operačního systému." Další informace o službách cloud services a aktualizace verze operačního systému pro cloudovou službu, naleznete v tématu:

* [Přehled Cloud Services](../cloud-services/cloud-services-choose-me.md)
* [Postup aktualizace cloudové služby](../cloud-services/cloud-services-update-azure-service.md)
* [Jak konfigurovat Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější zprávy zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
