---
title: Advanced Threat Protection pro Azure Storage
description: Nakonfigurujte Azure Storage pokročilou ochranu před internetovými útoky pro detekci anomálií v aktivitě účtu a upozorněte vás na potenciálně nebezpečné pokusy o přístup k vašemu účtu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 7d4f36be51591d6be2b4c42eb8a8950ab52a0258
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782569"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection pro Azure Storage

Advanced Threat Protection pro Azure Storage poskytuje další vrstvu analytických nástrojů zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo využití účtů úložiště. Tato vrstva ochrany umožňuje řešit hrozby bez nutnosti být odborníkem na zabezpečení nebo spravovat systémy monitorování zabezpečení. 

Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě.  Tyto výstrahy zabezpečení jsou integrovány do [Azure Security Center](https://azure.microsoft.com/services/security-center/)a jsou také odesílány prostřednictvím e-mailu správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak tyto hrozby prozkoumat a opravit.

> [!NOTE]
> * Rozšířená ochrana před internetovými útoky pro Azure Storage je aktuálně dostupná jenom pro úložiště objektů BLOB.
> * Podrobnosti o cenách, včetně bezplatné 30denní zkušební verze, najdete na [stránce s cenami Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).
> * Služba ATP pro službu Azure Storage není v současnosti dostupná v oblastech cloudu Azure pro státní správu a svrchované oblasti.

Rozšířená ochrana před internetovými útoky pro Azure Storage ingestuje diagnostické protokoly žádostí o čtení, zápis a odstranění do úložiště objektů BLOB pro detekci hrozeb. Pokud chcete prozkoumat výstrahy od rozšířené ochrany před internetovými útoky, můžete zobrazit související aktivitu úložiště pomocí Analýza úložiště protokolování. Další informace najdete v tématu [konfigurace analýza úložiště protokolování](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Nastavení rozšířené ochrany před internetovými útoky 

### <a name="using-the-portal"></a>Použití portálu

1. Spusťte Azure Portal v [https://portal.azure.com](https://portal.azure.com/).

2. Přejděte na stránku konfigurace Azure Storage účtu, který chcete chránit. Na stránce **Nastavení** vyberte **Rozšířená ochrana před internetovými útoky**.

3. V okně konfigurace **rozšířené ochrany před internetovými útoky**
    * Zapnout rozšířenou *ochranu před internetovými útoky*
    * Kliknutím na **Uložit** uložte nové nebo aktualizované zásady rozšířené ochrany před internetovými útoky. (Ceny v imagi jsou například jenom pro účely.)

![Zapnutí Azure Storage rozšířené ochrany před internetovými útoky](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Pomocí Azure Security Center

Když se přihlásíte k odběru úrovně Standard v Azure Security Center, Rozšířená ochrana před internetovými útoky se automaticky nastaví na všechny vaše účty úložiště. Rozšířenou ochranu před internetovými útoky pro účty úložiště v rámci určitého předplatného můžete povolit nebo zakázat následujícím způsobem:

1. Spusťte **Azure Security Center** v [Azure Portal](https://portal.azure.com).
1. V hlavní nabídce klikněte na **ceny & nastavení**.
1. Klikněte na předplatné, u kterého chcete povolit nebo zakázat ochranu před hrozbami pro své účty úložiště.

    ![Výběr předplatného](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Klikněte na **cenová úroveň**.
1. V části **Vybrat cenovou úroveň podle typu prostředku** na řádku **účty úložiště** klikněte na **povoleno** nebo **zakázáno**.

    ![Povolit ATP v Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Klikněte na **Uložit**.

### <a name="using-azure-resource-manager-templates"></a>Používání šablon Azure Resource Manager

Pomocí šablony Azure Resource Manager nasaďte účet Azure Storage s povolenou rozšířenou ochranou hrozeb. Další informace najdete v tématu [účet úložiště s pokročilou ochranou hrozeb](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Použití Azure Policy

Pomocí Azure Policy můžete povolit rozšířenou ochranu před internetovými útoky napříč účty úložiště v rámci konkrétního předplatného nebo skupiny prostředků.

1. Spusťte stránku Azure **Policy-definitions** .

1. Vyhledejte zásadu **nasazení rozšířené ochrany před internetovými útoky na účty úložiště** .

     ![Zásady hledání](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Vyberte předplatné nebo skupinu prostředků Azure.

    ![Vybrat předplatné nebo skupinu](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Přiřaďte zásadu.

    ![Stránka definice zásad](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Pomocí rozhraní REST API
Pomocí příkazů rozhraní REST API můžete vytvořit, aktualizovat nebo získat nastavení rozšířené ochrany před internetovými útoky pro určitý účet úložiště.

* [Rozšířená ochrana před internetovými útoky – vytvořit](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Rozšířená ochrana před internetovými útoky – získání](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Použijte následující rutiny PowerShellu:

  * [Povolit rozšířenou ochranu před internetovými útoky](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Získat rozšířenou ochranu před internetovými útoky](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Zakázat rozšířenou ochranu před internetovými útoky](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Prozkoumejte anomálie zabezpečení

Když se vyskytnou anomálie aktivity úložiště, obdržíte e-mailové oznámení s informacemi o podezřelé události zabezpečení. Podrobnosti události zahrnují:

* Povaha anomálií
* Název účtu úložiště
* Čas události
* Typ úložiště
* Potenciální příčiny 
* Kroky pro šetření
* Postup odstranění problému


E-mail obsahuje také podrobnosti o možných příčinách a doporučené akce pro prošetření a zmírnění potenciální hrozby.

![Azure Storage e-mailové výstrahy rozšířené ochrany před internetovými útoky](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Aktuální výstrahy zabezpečení můžete zkontrolovat a spravovat z [dlaždice výstrahy zabezpečení](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts)Azure Security Center. Kliknutím na konkrétní výstrahu získáte podrobnosti a akce pro zkoumání aktuální hrozby a vyřešení budoucích hrozeb.

![Azure Storage e-mailové výstrahy rozšířené ochrany před internetovými útoky](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Výstrahy ochrany

Výstrahy jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k účtům úložiště nebo jejich zneužití. Seznam těchto výstrah najdete v tématu [Azure Storage](../../security-center/security-center-alerts-data-services.md#azure-storage) Alerts.

## <a name="next-steps"></a>Další postup

* Další informace o [protokolech v Azure Storage účtech](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Další informace o [Azure Security Center](../../security-center/security-center-intro.md)
