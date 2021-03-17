---
title: Integrace řešení zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak Azure Security Center umožňuje integrací s partnerskými řešeními zvýšit celkové zabezpečení vašich prostředků Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.openlocfilehash: ff23a1fa4b631fc10163f22d94ccdbd8cbe657c2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099246"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrace řešení zabezpečení v Azure Security Center
Tento dokument vám pomůže se správou řešení zabezpečení již propojených s Azure Security Center a s přidáním nových řešení.

## <a name="integrated-azure-security-solutions"></a>Integrovaná řešení zabezpečení Azure
Security Center umožňuje snadné povolení integrovaných řešení zabezpečení v Azure. Mezi výhody patří:

- **Zjednodušené nasazení:** Security Center nabízí zjednodušené zřízení integrovaných partnerských řešení. Pro řešení, jako jsou antimalware a posouzení ohrožení zabezpečení, Security Center může zřídit agenta na virtuálních počítačích. U zařízení s bránou firewall může Security Center pořídit většinu požadovaných konfigurací sítě.
- **Integrované detekce**: události zabezpečení z partnerských řešení se automaticky shromažďují, agregují a zobrazují jako součást Security Center výstrah a incidentů. Tyto události jsou také sloučeny s detekcemi z jiných zdrojů a poskytují pokročilé možnosti detekce hrozeb.
- **Sjednocená správa a monitorování stavu:** Zákazníci můžou využít integrované události týkající se stavu k monitorování všech partnerských řešení na první pohled. Je dostupná základní správa se snadným přístupem k pokročilému nastavení s použitím partnerského řešení.

Integrovaná řešení zabezpečení v současné době zahrnují posouzení ohrožení zabezpečení [Qualys](https://www.qualys.com/public-cloud/#azure) a [Rapid7](https://www.rapid7.com/products/insightvm/) a [Microsoft Azure Firewall webových aplikací v Azure Application Gateway](../web-application-firewall/ag/ag-overview.md).

> [!NOTE]
> Security Center neinstaluje agenta Log Analytics na partnerských virtuálních zařízeních, protože většina dodavatelů zabezpečení brání externím agentům běžícím na jejich zařízeních.

Další informace o integraci nástrojů pro kontrolu ohrožení zabezpečení z Qualys, včetně integrovaného skeneru dostupného pro zákazníky v Azure Defenderu, najdete v tématu [posouzení ohrožení zabezpečení pro azure Virtual Machines](deploy-vulnerability-assessment-vm.md).

Security Center taky nabízí analýzu ohrožení zabezpečení pro vaše:

* Databáze SQL – Přečtěte si téma [zkoumání sestav posouzení ohrožení zabezpečení na řídicím panelu posouzení ohrožení zabezpečení](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports) .
* Azure Container Registry imagí – viz [použití Azure Defenderu pro Registry kontejnerů pro kontrolu ohrožení zabezpečení v imagí](defender-for-container-registries-usage.md)

## <a name="how-security-solutions-are-integrated"></a>Způsob integrace řešení zabezpečení
Řešení zabezpečení Azure nasazená ze služby Security Center se automaticky připojí. Můžete taky připojit další zdroje dat zabezpečení, včetně počítačů, které jsou spuštěné místně nebo v jiných cloudech.

[![Integrace partnerských řešení](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Správa integrovaných řešení zabezpečení Azure a dalších zdrojů dat

1. Z [Azure Portal](https://azure.microsoft.com/features/azure-portal/)otevřete **Security Center**.

1. V nabídce Security Center vyberte **řešení zabezpečení**.

Na stránce **řešení zabezpečení** uvidíte stav integrovaných řešení zabezpečení Azure a spouštějte základní úlohy správy.

### <a name="connected-solutions"></a>Připojená řešení

Oddíl **připojená řešení** obsahuje řešení zabezpečení, která jsou aktuálně připojená k Security Center. Zobrazuje také stav jednotlivých řešení.  

![Připojená řešení](./media/security-center-partner-integration/connected-solutions.png)

Stav partnerského řešení může být:

* **V pořádku** (zelená) – žádné problémy se stavem.
* **Chybné** (červené) – existuje problém se stavem, který vyžaduje okamžitou pozornost.
* **Zastavené generování sestav** (oranžová) – řešení zastavilo hlášení stavu.
* **Nehlášeno** (šedá) – řešení ještě neohlásilo nic a nejsou k dispozici žádná data o stavu. Stav řešení může být neohlášený, pokud byl v poslední době připojen a stále ještě probíhá jeho nasazení.

> [!NOTE]
> Pokud data o stavu nejsou k dispozici, Security Center zobrazuje datum a čas poslední přijaté události, která označuje, jestli řešení hlásí nebo ne. Pokud nejsou k dispozici žádná data o stavu a během posledních 14 dnů nebyla přijata žádná výstraha, Security Center znamená, že řešení není v pořádku nebo není hlášeno.
>
>

Vyberte **zobrazení** pro další informace a možnosti, jako například:

   - **Konzola řešení** – otevře prostředí pro správu pro toto řešení.
   - **Link VM** – otevře stránku propojit aplikace. Tady můžete ke svému partnerskému řešení připojit prostředky.
   - **Odstranění řešení**
   - **Konfigurace**

   ![Podrobné zobrazení partnerského řešení](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Zjištěná řešení

Security Center automaticky vyhledá řešení zabezpečení běžící v Azure, ale nepřipojená k Security Center a zobrazí řešení v části **zjištěná řešení** . Mezi tato řešení patří řešení Azure, jako jsou [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)a partnerská řešení.

> [!NOTE]
> Pro funkci zjištěná řešení povolte v **Azure Defenderu** na úrovni předplatného. Další informace najdete v [rychlém startu: povolení Azure Defenderu](enable-azure-defender.md).

Vyberte **připojit** v rámci řešení, které chcete integrovat s Security Center a upozorňovat na výstrahy zabezpečení.

### <a name="add-data-sources"></a>Přidat zdroje dat

Část **Přidat zdroje dat** obsahuje další dostupné zdroje dat, které je možné připojit. Pokyny k přidání dat z některého z těchto zdrojů získáte kliknutím na **PŘIDAT**.

![Zdroje dat](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili integrovat partnerská řešení do služby Security Center. Další informace o tom, jak nastavit integraci s Sentinel-Sentinel Azure nebo s ostatními SIEM, najdete v tématu [průběžné exportování dat Security Center](continuous-export.md).