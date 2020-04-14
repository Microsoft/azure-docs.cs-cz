---
title: Konfigurace rozšířené ochrany před hrozbami
titleSuffix: Azure Storage
description: Nakonfigurujte pokročilou ochranu před hrozbami pro Azure Storage, abyste zjistili anomálie v aktivitě účtu a byli upozorňováni na potenciálně škodlivé pokusy o přístup k vašemu účtu.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4219bb471b92e7ddae72c50403f635498c90080d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251686"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Konfigurace pokročilé ochrany před hrozbami pro Azure Storage

Pokročilá ochrana před hrozbami pro Azure Storage poskytuje další vrstvu inteligence zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití účtů úložiště. Tato vrstva ochrany umožňuje řešit hrozby, aniž byste byli odborníkem na zabezpečení nebo spravovali systémy monitorování zabezpečení.

Výstrahy zabezpečení se aktivují, když dojde k anomáliím v aktivitě. Tyto výstrahy zabezpečení jsou integrované s [Azure Security Center](https://azure.microsoft.com/services/security-center/)a jsou také odesílány e-mailem správcům předplatného, s podrobnostmi o podezřelé aktivity a doporučení, jak vyšetřovat a napravit hrozby.

Služba ingestuje diagnostické protokoly požadavků na čtení, zápis a odstranění do úložiště objektů Blob pro detekci hrozeb. Chcete-li prozkoumat výstrahy z pokročilé ochrany před hrozbami, můžete zobrazit související aktivitu úložiště pomocí protokolování Služby Storage Analytics. Další informace najdete v **tématu Konfigurace protokolování** v [monitoru účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Dostupnost

Pokročilá ochrana před hrozbami pro Azure Storage je momentálně dostupná jenom pro [úložiště objektů Blob](https://azure.microsoft.com/services/storage/blobs/). 

Tato služba je dostupná ve všech veřejných cloudech a cloudech vlády USA, ale žádné jiné suverénní nebo vládní oblasti cloudu Azure.

Podrobnosti o cenách, včetně bezplatné 30denní zkušební verze, najdete na [stránce s cenami Centra zabezpečení Azure](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="set-up-advanced-threat-protection"></a>Nastavení pokročilé ochrany před hrozbami

Pokročilou ochranu před hrozbami můžete nakonfigurovat několika způsoby popsanými v následujících částech.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Spusťte [portál Azure](https://portal.azure.com/).
1. Přejděte na svůj účet Azure Storage. V části **Nastavení**vyberte **Upřesnit zabezpečení**.
1. Na stránce konfigurace rozšířeného zabezpečení vyberte odkaz **Nastavení.**
1. Nastavte **rozšířené zabezpečení** na **ZAPNUTO**.
1. Kliknutím na **Uložit** uložte novou nebo aktualizovanou zásadu.

    ![Zapnutí pokročilé ochrany před hrozbami azure storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Když se přihlásíte k odběru úrovně Standard v Azure Security Center, pokročilá ochrana před hrozbami se automaticky nastaví na všech vašich účtech úložiště. Pokročilou ochranu před hrozbami pro účty úložiště v rámci konkrétního předplatného můžete povolit nebo zakázat následujícím způsobem:

1. Spusťte **Azure Security Center** na webu Azure [Portal](https://portal.azure.com).
1. V hlavní nabídce klikněte na **Nastavení & ceny**.
1. Klikněte na předplatné, které chcete povolit nebo zakázat ochranu před hrozbami pro jeho účty úložiště.

    ![Výběr předplatného](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Klikněte na **Cenová úroveň**.
1. V části **Vybrat cenovou úroveň podle typu prostředku** klikněte v řádku Účty **úložiště** na **Povoleno** nebo **Zakázáno**.

    ![Povolení ochrany ATP v Centru zabezpečení](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Klikněte na **Uložit**.

### <a name="template"></a>[Šablona](#tab/template)

Pomocí šablony Azure Resource Manager nasadit účet Azure Storage s rozšířenou ochranu před hrozbami povoleno. Další informace naleznete v [tématu Účet úložiště s pokročilou ochranou před hrozbami](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Pomocí zásad Azure můžete povolit pokročilou ochranu před hrozbami napříč účty úložiště v rámci konkrétního předplatného nebo skupiny prostředků.

1. Spusťte stránku Zásady Azure **– definice.**

1. Vyhledejte zásadu **Nasazení rozšířené ochrany před hrozbami u účtů úložiště.**

     ![Zásady hledání](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Vyberte předplatné Azure nebo skupinu prostředků.

    ![Vybrat předplatné nebo skupinu](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Přiřaďte zásadu.

    ![Stránka Definice zásad](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Pomocí příkazů rest API můžete vytvořit, aktualizovat nebo získat pokročilé nastavení ochrany před hrozbami pro konkrétní účet úložiště.

* [Pokročilá ochrana před hrozbami – vytvořit](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Pokročilá ochrana před hrozbami – získejte](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Použijte následující rutiny prostředí PowerShell:

* [Povolit pokročilou ochranu před hrozbami](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Získejte pokročilou ochranu před hrozbami](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Zakázání rozšířené ochrany před hrozbami](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Prozkoumejte bezpečnostní anomálie

Když dojde k anomáliím aktivity úložiště, obdržíte e-mailové oznámení s informacemi o podezřelé události zabezpečení. Podrobnosti o události zahrnují:

* Povaha anomálie
* Název účtu úložiště
* Čas události
* Typ úložiště
* Potenciální příčiny
* Vyšetřovací kroky
* Nápravné kroky

E-mail také obsahuje podrobnosti o možných příčinách a doporučených akcích k prošetření a zmírnění potenciální hrozby.

![Azure Storage pokročilé ochrany před hrozbami upozornění e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Aktuální výstrahy zabezpečení můžete zkontrolovat a spravovat z [dlaždice výstrah zabezpečení](../../security-center/security-center-managing-and-responding-alerts.md)centra zabezpečení Azure . Kliknutím na konkrétní výstrahu získáte podrobnosti a akce pro vyšetřování aktuální hrozby a řešení budoucích hrozeb.

![Azure Storage pokročilé ochrany před hrozbami upozornění e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Výstrahy zabezpečení

Výstrahy jsou generovány neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití účtů úložiště. Seznam upozornění pro Azure Storage najdete v části **Úložiště** v [části Ochrana před hrozbami pro datové služby v Centru zabezpečení Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Další kroky

* Další informace o [protokolech v účtech Azure Storage](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Další informace o [Azure Security Center](../../security-center/security-center-intro.md)
