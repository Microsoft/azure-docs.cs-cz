---
title: Konfigurace Azure Defenderu pro úložiště
titleSuffix: Azure Storage
description: Nakonfigurujte v Azure Defenderu úložiště, abyste zjistili anomálie v aktivitě účtu a měli upozornění na potenciálně nebezpečné pokusy o přístup k vašemu účtu.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e2f044ab267365885260b031638572846184bc83
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063181"
---
# <a name="configure-azure-defender-for-storage"></a>Konfigurace Azure Defenderu pro úložiště

Azure Defender for Storage nabízí další vrstvu informací o zabezpečení, která zjišťuje neobvyklé a případně i škodlivé pokusy o přístup nebo využití účtů úložiště. Tato úroveň ochrany vám umožňuje řešit hrozby, aniž byste museli být odborníky na zabezpečení nebo spravovat systémy monitorování zabezpečení.

Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. Tyto výstrahy zabezpečení jsou integrovány do [Azure Security Center](https://azure.microsoft.com/services/security-center/)a jsou také odesílány prostřednictvím e-mailu správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak tyto hrozby prozkoumat a opravit.

Služba ingestuje protokoly prostředků žádostí o čtení, zápis a odstranění do úložiště objektů BLOB a do souborů Azure pro detekci hrozeb. Pokud chcete prozkoumat výstrahy z Azure Defenderu, můžete zobrazit související aktivitu úložiště pomocí protokolování Analýza úložiště. Další informace najdete v tématu **Konfigurace protokolování** v [monitorování účtu úložiště v Azure Portal](./manage-storage-analytics-logs.md#configure-logging).

## <a name="availability"></a>Dostupnost

Azure Defender for Storage je aktuálně k dispozici pro Blog Storage, Azure Files a Azure Data Lake Storage Gen2. Mezi typy účtů, které Azure Defender podporují, patří účty pro obecné účely verze 2, objekty blob bloku a účty Blob Storage. Azure Defender for Storage je k dispozici ve všech veřejných cloudech a v cloudech státní správy USA, ale není k dispozici v jiných suverénních cloudech ani v oblastech cloudu Azure Government.

Účty s hierarchickými obory názvů povolené pro Data Lake Storage podporují transakce pomocí rozhraní API služby Azure Blob Storage i rozhraní API služby Data Lake Storage. Sdílené složky Azure podporují transakce prostřednictvím SMB.

Podrobnosti o cenách, včetně bezplatné 30denní zkušební verze, najdete na [stránce s cenami Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Následující seznam shrnuje dostupnost služby Azure Defender pro úložiště:

- Stav vydaných verzí:
  - [BLOB Storage](https://azure.microsoft.com/services/storage/blobs/) (Obecná dostupnost)
  - [Soubory Azure](../files/storage-files-introduction.md) (všeobecně dostupné)
  - Azure Data Lake Storage Gen2 (Obecná dostupnost)
- Cloud<br>
    ✔ Komerční cloudy<br>
    ✔ US Gov<br>
    ✘ Čína gov, ostatní gov

## <a name="set-up-azure-defender"></a>Nastavení Azure Defenderu

Službu Azure Defender pro úložiště můžete nakonfigurovat libovolným z několika způsobů popsaných v následujících částech.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Azure Defender je integrovaný do Azure Security Center. Když v předplatném povolíte Azure Defender, bude se Azure Defender pro Azure Storage automaticky povolit pro všechny vaše účty úložiště. Můžete povolit nebo zakázat Azure Defender pro účty úložiště v rámci určitého předplatného následujícím způsobem:

1. Spusťte **Azure Security Center** v [Azure Portal](https://portal.azure.com).
1. V hlavní nabídce vyberte v části **Správa** možnost **cenové & nastavení**.
1. Vyberte předplatné, pro které chcete povolit nebo zakázat Azure Defender.
1. Pokud chcete povolit Azure Defender pro předplatné, vyberte **Azure Defender zapnuto** .
1. V části **Vybrat plán v programu Azure Defender podle typu prostředku** vyhledejte řádek **úložiště** a vyberte ve sloupci **plán** možnost **povoleno** .
1. Uložte provedené změny.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Snímek obrazovky, který ukazuje, jak povolit Azure Defender pro úložiště v Security Center":::

V tomto předplatném je teď povolený Azure Defender pro všechny účty úložiště.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Spusťte [Azure Portal](https://portal.azure.com/).
1. Přejděte na svůj účet úložiště. V části **Nastavení** vyberte **Rozšířené zabezpečení**.
1. Vyberte **Povolit Azure Defender for Storage**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Snímek obrazovky, který ukazuje, jak povolit Azure Defender pro účet Azure Storage":::

Pro tento účet úložiště je teď povolený Azure Defender.

### <a name="template"></a>[Šablona](#tab/template)

Pomocí šablony Azure Resource Manager nasaďte účet Azure Storage s povoleným Azure Defenderem. Další informace najdete v tématu [účet úložiště s pokročilou ochranou hrozeb](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Pomocí Azure Policy můžete povolit Azure Defender napříč účty úložiště v rámci určitého předplatného nebo skupiny prostředků.

1. Spusťte stránku Azure **Policy-definitions** .
1. Vyhledejte zásadu **nasadit Azure Defender na účty úložiště** .

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Použití zásad pro povolení Azure Defenderu pro účty úložiště":::

1. Vyberte předplatné nebo skupinu prostředků Azure.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Vyberte předplatné nebo skupinu prostředků pro obor zásad. ":::

1. Přiřaďte zásadu.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Přiřazení zásady pro povolení služby Azure Defender pro úložiště":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete povolit Azure Defender pro účet úložiště pomocí PowerShellu, nejdřív se ujistěte, že máte nainstalovaný modul [AZ. Security](https://www.powershellgallery.com/packages/Az.Security) . Dále zavolejte příkaz [Enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection) . Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

Pokud chcete zjistit nastavení Azure Defenderu pro účet úložiště pomocí PowerShellu, zavolejte příkaz [Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection) . Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete povolit Azure Defender pro účet úložiště pomocí Azure CLI, zavolejte příkaz [AZ Security ATP Storage Update](/cli/azure/security/atp/storage#az_security_atp_storage_update) . Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Pokud chcete zjistit nastavení Azure Defenderu pro účet úložiště pomocí Azure CLI, zavolejte příkaz [AZ Security ATP Storage show](/cli/azure/security/atp/storage#az_security_atp_storage_show) . Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>Prozkoumání anomálií zabezpečení

Když dojde k anomáliím aktivity úložiště, obdržíte e-mailové oznámení s informacemi o podezřelé události zabezpečení. Podrobnosti o události zahrnují:

- Povahu anomálie
- Název účtu úložiště
- Čas události
- Typ úložiště
- Možné příčiny
- Postup vyšetřování
- Postup nápravy

E-mail dále obsahuje podrobnosti o možných příčinách a doporučených akcích pro prošetření a zmírnění potenciální hrozby.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="E-mail s upozorněním pro úložiště v Azure Defenderu":::

Aktuální výstrahy zabezpečení můžete zkontrolovat a spravovat z [dlaždice výstrahy zabezpečení](../../security-center/security-center-managing-and-responding-alerts.md)Azure Security Center. Kliknutím na konkrétní výstrahu získáte podrobnosti a akce pro prozkoumání aktuální hrozby a vyřešení budoucích hrozeb.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Upozornění pro Azure Defender na úložiště":::

## <a name="security-alerts"></a>Výstrahy zabezpečení

Výstrahy jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k účtům úložiště nebo jejich zneužití. Seznam výstrah pro Azure Storage najdete v tématu [výstrahy pro Azure Storage](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Defenderu pro úložiště](../../security-center/defender-for-storage-introduction.md)
- [Azure Security Center](../../security-center/security-center-introduction.md)
- [Protokoly v Azure Storage účty](/rest/api/storageservices/About-Storage-Analytics-Logging)
