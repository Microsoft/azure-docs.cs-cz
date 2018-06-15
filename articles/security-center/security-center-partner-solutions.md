---
title: Správa připojených partnerských řešení v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak vám Azure Security Center umožňuje přehledně sledovat stav partnerských řešení integrovaných ve vašem předplatném Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2017
ms.locfileid: "23891024"
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Správa připojených partnerských řešení pomocí Azure Security Center
Tento článek vás provede jak spravovat a monitorovat řešení připojených zabezpečení v Azure Security Center.

## <a name="monitoring-partner-solutions"></a>Sledování partnerských řešení
Monitorovat stav řešení připojených zabezpečení a provádět základní správu:

1. V části **Security Center – přehled**, vyberte **řešení zabezpečení**.

  ![Vybrat řešení pro zabezpečení][1]

  **Připojené řešení** část obsahuje řešení zabezpečení, které jsou připojené k Security Center a informace o stav jednotlivých řešení.

  ![Partnerská řešení][2]

   Stav partnerských řešení může být:

   * Stavu v pořádku (zelený) - nejsou potíže stavu.
   * Není v pořádku (červená) – Existuje problém stavu, které si žádá okamžitou pozornost.
   * Problémy v oblasti stavu (oranžová) – řešení přestalo hlásit svůj stav.
   * Neuveden (šedá) – řešení ještě nenahlásilo nic ještě stav řešení může být uvedený, pokud se nedávno byl připojen a ještě probíhá jeho nasazení, nebo je k dispozici žádná data o stavu.

   > [!NOTE]
   > Pokud data stavu není k dispozici, Security Center zobrazuje datum a čas poslední události přijatých indikující, zda je řešení reporting nebo ne. Pokud je k dispozici žádná data o stavu a nejsou přijaty žádné výstrahy týkající se během posledních 14 dnů, Security Center označuje, že je řešení není v pořádku, nebo není generování sestav.
   >
   >

2. Vyberte **zobrazení** Další informace a možnosti, což zahrnuje:

  - **Řešení konzoly**. Otevře prostředí pro správu pro toto řešení.
  - **Propojení virtuálních počítačů**. Otevře se okno aplikace odkaz. Tady můžete ke svému partnerskému řešení připojit prostředky.
  - **Odstranit řešení**.
  - **Konfigurace**.

   ![Podrobné zobrazení partnerského řešení][3]

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak spravovat a monitorovat řešení připojených zabezpečení ve službě Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Přehled řešení zabezpečení](security-center-partner-integration.md) – zjistěte, jak připojit a spravovat řešení zabezpečení.
* [Připojování Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) – zjistěte, jak připojit výstrahy z ATA.
* [Připojení ochrany identit Azure Active Directory (AD) ](security-center-aadip-integration.md) – zjistěte, jak připojit výstrahy z Azure AD Identity Protection.
* [Integrace partnera a řešení](security-center-partner-integration.md) -získat přehled o integraci jiných řešení pro zabezpečení.
* [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reakce na výstrahy zabezpečení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
