---
title: Aktualizovat verzi operačního systému v Azure Security Center | Microsoft Docs
description: V tomto článku se dozvíte, jak implementovat **verzi operačního systému Azure Security Center aktualizace**doporučení.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a17bc3cf8c8f1ffbb2a91e17944bfc1c203f439d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "60905842"
---
# <a name="update-os-version-in-azure-security-center"></a>Aktualizace verze operačního systému v Azure Security Center
U virtuálních počítačů v cloudových službách Azure Security Center doporučuje aktualizovat operační systém (OS), pokud je k dispozici novější verze.  Monitorují se jenom webové role a role pracovních procesů Cloud Services běžící v produkčních slotech.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
> 
> 

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V okně **doporučení** vyberte **Aktualizovat verzi operačního systému**.
   ![Aktualizace verze operačního systému][1]
2. Tím se otevře okno **Aktualizovat verzi operačního systému**. Podle pokynů v tomto okně aktualizujte verzi operačního systému.

## <a name="see-also"></a>Viz také:
Tento článek ukazuje, jak implementovat Security Center doporučení "aktualizace verze operačního systému". Další informace o cloudových službách a aktualizaci verze operačního systému pro cloudovou službu najdete v těchto tématech:

* [Přehled Cloud Services](../cloud-services/cloud-services-choose-me.md)
* [Jak aktualizovat cloudovou službu](../cloud-services/cloud-services-update-azure-service.md)
* [Jak konfigurovat Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější novinky a informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
