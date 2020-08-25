---
title: Konfigurace rozšířené ochrany před internetovými útoky
titleSuffix: Azure Storage
description: Konfigurace rozšířené ochrany před internetovými útoky pro Azure Storage k detekci anomálií v aktivitě účtu a upozornění na potenciálně nebezpečné pokusy o přístup k vašemu účtu.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e037607d1f86e6df4d3f5b12e29ba8fde447ebc9
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757927"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Konfigurace rozšířené ochrany před internetovými útoky pro Azure Storage

Rozšířená ochrana před internetovými útoky pro Azure Storage poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby bez nutnosti být odborníkem na zabezpečení nebo správou systémů monitorování zabezpečení.

Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. Tyto výstrahy zabezpečení jsou integrovány do [Azure Security Center](https://azure.microsoft.com/services/security-center/)a jsou také odesílány prostřednictvím e-mailu správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak tyto hrozby prozkoumat a opravit.

Služba ingestuje protokoly prostředků žádostí o čtení, zápis a odstranění do úložiště objektů BLOB a do souborů Azure (Preview) pro detekci hrozeb. Pokud chcete prozkoumat výstrahy od rozšířené ochrany před internetovými útoky, můžete zobrazit související aktivitu úložiště pomocí Analýza úložiště protokolování. Další informace najdete v tématu **Konfigurace protokolování** v [monitorování účtu úložiště v Azure Portal](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Dostupnost

Rozšířená ochrana před internetovými útoky pro Azure Storage je aktuálně dostupná pro úložiště objektů blob, soubory Azure (Preview) a Azure Data Lake Storage Gen2 (Preview). Mezi typy účtů, které podporují rozšířenou ochranu před internetovými útoky, patří obecné účely v2, objekty blob bloku a účty BLOB Storage. Rozšířená ochrana před internetovými útoky je dostupná ve všech veřejných cloudech a v cloudech pro státní správu USA, ale ne v jiných oblastech nebo Azure Government cloudových oblastech.

Účty s hierarchickými obory názvů povolené pro Data Lake Storage podporují transakce pomocí rozhraní API služby Azure Blob Storage i Data Lake Storage rozhraní API. Azure File Shares podporuje transakce přes SMB.

Podrobnosti o cenách, včetně bezplatné 30denní zkušební verze, najdete na [stránce s cenami Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Následující seznam shrnuje dostupnost rozšířené ochrany před internetovými útoky pro Azure Storage:

- Stav vydaných verzí:
  - [BLOB Storage](https://azure.microsoft.com/services/storage/blobs/) (Obecná dostupnost)
  - [Soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (Preview podporuje transakce SMB a REST)
  - Azure Data Lake Storage Gen2 (Preview)
- Cloud<br>
    ✔ Komerční cloudy<br>
    ✔ US Gov<br>
    ✘ Čína gov, ostatní gov

## <a name="set-up-advanced-threat-protection"></a>Nastavení rozšířené ochrany před internetovými útoky

Rozšířenou ochranu před internetovými útoky můžete nakonfigurovat libovolným z několika způsobů popsaných v následujících částech.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Když se přihlásíte k odběru úrovně Standard v Azure Security Center, Rozšířená ochrana před internetovými útoky se automaticky nastaví na všechny vaše účty úložiště. Rozšířenou ochranu před internetovými útoky pro účty úložiště v rámci určitého předplatného můžete povolit nebo zakázat následujícím způsobem:

1. Spusťte **Azure Security Center** v [Azure Portal](https://portal.azure.com).
1. V hlavní nabídce klikněte na **ceny & nastavení**.
1. Klikněte na předplatné, u kterého chcete povolit nebo zakázat ochranu před hrozbami pro své účty úložiště.

    ![Výběr předplatného](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Klikněte na **cenová úroveň**.
1. V části **Vybrat cenovou úroveň podle typu prostředku** na řádku **účty úložiště** klikněte na **povoleno** nebo **zakázáno**.

    ![Povolit ATP v Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Klikněte na **Uložit**.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Spusťte [Azure Portal](https://portal.azure.com/).
1. Přejděte na účet Azure Storage. V části **Nastavení**vyberte **Rozšířené zabezpečení**.
1. Na stránce Upřesnit konfiguraci zabezpečení vyberte odkaz **Nastavení** .
1. Nastavte **Rozšířené zabezpečení** **na zapnuto**.
1. Kliknutím na **Uložit** uložte nové nebo aktualizované zásady.

    ![Zapnutí Azure Storage rozšířené ochrany před internetovými útoky](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="template"></a>[Šablona](#tab/template)

Pomocí šablony Azure Resource Manager nasaďte účet Azure Storage s povolenou rozšířenou ochranou hrozeb. Další informace najdete v tématu [účet úložiště s pokročilou ochranou hrozeb](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Pomocí Azure Policy můžete povolit rozšířenou ochranu před internetovými útoky napříč účty úložiště v rámci konkrétního předplatného nebo skupiny prostředků.

1. Spusťte stránku Azure **Policy-definitions** .

1. Vyhledejte zásadu **nasazení rozšířené ochrany před internetovými útoky na účty úložiště** .

     ![Zásady hledání](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Vyberte předplatné nebo skupinu prostředků Azure.

    ![Vybrat předplatné nebo skupinu](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Přiřaďte zásadu.

    ![Stránka definice zásad](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Pomocí příkazů rozhraní REST API můžete vytvořit, aktualizovat nebo získat nastavení rozšířené ochrany před internetovými útoky pro určitý účet úložiště.

* [Rozšířená ochrana před internetovými útoky – vytvořit](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Rozšířená ochrana před internetovými útoky – získání](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Použijte následující rutiny PowerShellu:

* [Povolit rozšířenou ochranu před internetovými útoky](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Získat rozšířenou ochranu před internetovými útoky](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Zakázat rozšířenou ochranu před internetovými útoky](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

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

Aktuální výstrahy zabezpečení můžete zkontrolovat a spravovat z [dlaždice výstrahy zabezpečení](../../security-center/security-center-managing-and-responding-alerts.md)Azure Security Center. Kliknutím na konkrétní výstrahu získáte podrobnosti a akce pro zkoumání aktuální hrozby a vyřešení budoucích hrozeb.

![Azure Storage e-mailové výstrahy rozšířené ochrany před internetovými útoky](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Výstrahy zabezpečení

Výstrahy jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k účtům úložiště nebo jejich zneužití. Seznam výstrah pro Azure Storage najdete v části **úložiště** v tématu [Ochrana před hrozbami pro datové služby v Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Další kroky

* Další informace o [protokolech v Azure Storage účtech](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Další informace o [Azure Security Center](../../security-center/security-center-intro.md)
