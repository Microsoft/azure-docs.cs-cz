---
title: Integrace řešení zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak Azure Security Center umožňuje integrací s partnerskými řešeními zvýšit celkové zabezpečení vašich prostředků Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758006"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrace řešení zabezpečení v Azure Security Center
Tento dokument vám pomůže se správou řešení zabezpečení již propojených s Azure Security Center a s přidáním nových řešení.

## <a name="integrated-azure-security-solutions"></a>Integrovaná řešení zabezpečení Azure
Security Center umožňuje snadné povolení integrovaných řešení zabezpečení v Azure. Mezi výhody patří:

- **Zjednodušené nasazení:** Security Center nabízí zjednodušené zřízení integrovaných partnerských řešení. U řešení, jako je antimalwarový program a posouzení ohrožení zabezpečení, může Centrum zabezpečení zřídit agenta ve virtuálních počítačích. U zařízení brány firewall se centrum zabezpečení může postarat o velkou část požadované konfigurace sítě.
- **Integrovaná zjišťování**: Události zabezpečení z partnerských řešení jsou automaticky shromažďovány, agregovány a zobrazovány jako součást výstrah a incidentů centra zabezpečení. Tyto události jsou také sloučeny s detekcemi z jiných zdrojů a poskytují pokročilé možnosti detekce hrozeb.
- **Sjednocená správa a monitorování stavu:** Zákazníci můžou využít integrované události týkající se stavu k monitorování všech partnerských řešení na první pohled. Je dostupná základní správa se snadným přístupem k pokročilému nastavení s použitím partnerského řešení.

V současné době integrovaná řešení zabezpečení zahrnují posouzení zranitelnosti společností [Qualys](https://www.qualys.com/public-cloud/#azure) a [Rapid7](https://www.rapid7.com/products/insightvm/) a brána firewall webových aplikací Microsoft Application Gateway.

> [!NOTE]
> Centrum zabezpečení nenainstaluje agenta Log Analytics do partnerských virtuálních zařízení, protože většina dodavatelů zabezpečení zakazuje externí agenty spuštěné na svých zařízeních.

Další informace o integraci nástrojů pro vyhledávání chyb zabezpečení od společnosti Qualys, včetně integrovaného skeneru dostupného zákazníkům standardní úrovně, naleznete v následujících tématech: 

- [Integrovaný skener zranitelnosti pro virtuální počítače](built-in-vulnerability-assessment.md).
- [Nasazení řešení pro vyhledávání chyb zabezpečení partnera](partner-vulnerability-assessment.md).

Security Center také nabízí analýzu zranitelnosti pro vaše:

* Databáze SQL – viz [Prozkoumání zpráv o posouzení zranitelnosti na řídicím panelu pro posouzení zranitelnosti](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Image registru kontejnerů Azure – viz [integrace registru kontejnerů Azure s Centrem zabezpečení (Preview)](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>Způsob integrace řešení zabezpečení
Řešení zabezpečení Azure nasazená ze služby Security Center se automaticky připojí. Můžete také připojit další zdroje dat zabezpečení, včetně počítačů spuštěných místně nebo v jiných cloudech.

[![Integrace partnerských řešení](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Správa integrovaných řešení zabezpečení Azure a dalších zdrojů dat

1. Na [portálu Azure](https://azure.microsoft.com/features/azure-portal/)otevřete **Centrum zabezpečení**.

1. V nabídce Centra zabezpečení vyberte **možnost Řešení zabezpečení**.

Na stránce **Řešení zabezpečení** můžete zobrazit stav integrovaných řešení zabezpečení Azure a spustit základní úlohy správy.

### <a name="connected-solutions"></a>Připojená řešení

Část **Připojená řešení** obsahuje řešení zabezpečení, která jsou aktuálně připojena k centru zabezpečení. Zobrazuje také stav každého řešení.  

![Připojená řešení](./media/security-center-partner-integration/connected-solutions.png)

Stav partnerského řešení může být:

* **Zdravé** (zelené) - žádné zdravotní problémy.
* **Nezdravé** (červené) - je tu zdravotní problém, který vyžaduje okamžitou pozornost.
* **Zastaveno hlášení** (oranžová) - řešení přestalo hlásit své zdraví.
* **Není hlášeno** (šedé) - řešení zatím nic nenahlásilo a nejsou k dispozici žádné údaje o zdravotním stavu. Stav řešení může být nehlášené, pokud byl připojen v poslední době a je stále nasazuje.

> [!NOTE]
> Pokud nejsou k dispozici údaje o stavu, centrum zabezpečení zobrazí datum a čas poslední přijaté události, které označují, zda se řešení hlásí či nikoli. Pokud nejsou k dispozici žádná data o stavu a během posledních 14 dnů nebyly přijaty žádné výstrahy, Centrum zabezpečení indikuje, že řešení není v pořádku nebo není nahlašováno.
>
>

Vyberte **ZOBRAZIT** pro další informace a možnosti, jako jsou:

   - **Konzola řešení** – otevře prostředí pro správu tohoto řešení.
   - **Virtuální virtuální soud propojení** – otevře stránku Aplikace propojení. Tady můžete ke svému partnerskému řešení připojit prostředky.
   - **Odstranění řešení**
   - **Konfigurace**

   ![Podrobné zobrazení partnerského řešení](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Zjištěná řešení

Security Center automaticky zjišťuje řešení zabezpečení spuštěná v Azure, ale není připojena k Centru zabezpečení, a zobrazí řešení v části **Zjištěná řešení.** Mezi tato řešení patří řešení Azure, jako je [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)a partnerská řešení.

> [!NOTE]
> Funkce Zjištěná řešení vyžaduje úroveň Standard služby Security Center na úrovni předplatného. Další informace o cenových úrovních najdete v [tématu Ceny.](security-center-pricing.md)
>

Vyberte **CONNECT** v části řešení pro integraci s Security Center a být upozorněni na výstrahy zabezpečení.

### <a name="add-data-sources"></a>Přidat zdroje dat

Část **Přidat zdroje dat** obsahuje další dostupné zdroje dat, které je možné připojit. Pokyny k přidání dat z některého z těchto zdrojů získáte kliknutím na **PŘIDAT**.

![Zdroje dat](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili integrovat partnerská řešení do služby Security Center. Související informace naleznete v následujících článcích:

* [Export výstrah a doporučení zabezpečení](continuous-export.md). Zjistěte, jak nastavit integraci s Azure Sentinelem nebo jiným SIEM.
* [Monitorování stavu zabezpečení ve službě Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.