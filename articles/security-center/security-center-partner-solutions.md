---
title: Správa připojených partnerských řešení ve službě Azure Security Center | Dokumentace Microsoftu
description: V tomto dokumentu se dozvíte, jak vám Azure Security Center umožňuje přehledně sledovat stav partnerských řešení integrovaných ve vašem předplatném Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/20/2018
ms.author: terrylan
ms.openlocfilehash: 3174f2d6562030702b14ef1fa3708a1a4e8e373b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301945"
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Správa připojených partnerských řešení pomocí Azure Security Center
Tento článek vás provede postupy při správě a monitorování řešení připojená zabezpečení ve službě Azure Security Center.

## <a name="monitoring-partner-solutions"></a>Sledování partnerských řešení
Chcete-li sledovat stav zabezpečení připojené řešení a provádět základní správu:

1. V části **Security Center – přehled**vyberte **řešení zabezpečení**.

  ![Vyberte řešení zabezpečení][1]

  **Připojená řešení** část obsahuje řešení zabezpečení, které jsou připojeny k Security Center a informace o stavu jednotlivých řešení.

  ![Partnerská řešení][2]

   Stav partnerského řešení může být:

   * V pořádku (zelený) – není žádný problém se stavem.
   * Není v pořádku (červená) – Existuje problém stavu, které si žádá okamžitou pozornost.
   * Problémy se stavem (oranžová) – řešení přestalo hlásit svůj stav.
   * Neuveden (šedá) – řešení neohlásil cokoli, ale stav řešení nemusí být uvedený, pokud byl nedávno připojený a ještě probíhá jeho nasazení, nebo je k dispozici žádná data o stavu.

   > [!NOTE]
   > Pokud není k dispozici data stavu, Security Center zobrazuje datum a čas poslední událost přijata označující, zda je řešení hlásí, nebo ne. Pokud je k dispozici žádná data o stavu a nejsou během posledních 14 dní přijaty žádné výstrahy, Security Center upozorňuje, že řešení není v pořádku, nebo není generování sestav.
   >
   >

2. Vyberte **zobrazení** pro další informace a možnosti, které obsahuje:

  - **Konzola řešení**. Otevře prostředí pro správu pro toto řešení.
  - **Propojení virtuálních počítačů**. Otevřete okno propojit aplikace. Tady můžete ke svému partnerskému řešení připojit prostředky.
  - **Odstranit řešení**.
  - **Konfigurace**.

   ![Podrobné zobrazení partnerského řešení][3]

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak spravovat a monitorovat řešení připojená zabezpečení ve službě Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Přehled řešení zabezpečení](security-center-partner-integration.md) – zjistěte, jak se připojit a Správa řešení zabezpečení.
* [Propojení Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) – zjistěte, jak připojit výstrahy z ATA.
* [Připojení služby Azure Active Directory (AD) Identity Protection ](security-center-aadip-integration.md) – zjistěte, jak připojit oznámení ze služby Azure AD Identity Protection.
* [Partnerská řešení a jejich integrace](security-center-partner-integration.md) – získejte přehled o integraci jiných řešení zabezpečení.
* [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reagovat na výstrahy zabezpečení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
