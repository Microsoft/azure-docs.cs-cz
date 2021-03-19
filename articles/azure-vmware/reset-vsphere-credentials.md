---
title: Resetovat přihlašovací údaje vSphere pro řešení Azure VMware
description: Naučte se resetovat přihlašovací údaje vSphere pro privátní cloud řešení Azure VMware a zajistěte, aby měl konektor HCX nejnovější přihlašovací údaje vSphere.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 1376b6322250da506d32b8ced0a62ddbf60ba9f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587623"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Resetovat přihlašovací údaje vSphere pro řešení Azure VMware

V tomto článku Vás provedeme kroky pro resetování přihlašovacích údajů správce Azure VMware pro váš privátní cloud v vCenter Server a NSX. To vám umožní zajistit, aby konektor HCX měl nejnovější přihlašovací údaje vCenter Server.

## <a name="reset-your-azure-vmware-solution-credentials"></a>Resetování přihlašovacích údajů řešení Azure VMware

 Nejdřív si resetujte přihlašovací údaje vašich komponent řešení Azure VMare. Platnost přihlašovacích údajů správce CloudAdmin a NSX-T nekončí. vCenter Server pomocí těchto kroků ale můžete pro tyto účty vygenerovat nová hesla.

> [!NOTE]
> Pokud používáte přihlašovací údaje CloudAdmin pro připojené služby, jako je HCX, vRealize Orchestrator, vRealizae Operations Manager nebo VMware Horizon, po aktualizaci hesla přestane připojení fungovat.  Tyto služby by měly být před zahájením rotace hesla zastaveny.  V takovém případě může dojít k dočasnému blokování účtů správce vCenter CloudAdmin a NSX-T, protože tyto služby budou průběžně volat pomocí vašich starých přihlašovacích údajů.  Další informace o nastavení samostatných účtů pro připojené služby najdete v tématu [Koncepty přístupu a identity](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. Z Azure Portal otevřete relaci Azure Cloud Shell.

2. Spusťte následující příkaz, který aktualizuje heslo CloudAdmin vCenter.  Budete muset nahradit {SubscriptionID}, {resourceName} a {PrivateCloudName} skutečnými hodnotami privátního cloudu, ke kterému patří účet CloudAdmin.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Spuštěním následujícího příkazu aktualizujte heslo správce NSX-T. Budete muset nahradit {SubscriptionID}, {resourceName} a {PrivateCloudName} skutečnými hodnotami privátního cloudu, ke kterému patří účet správce NSX-T.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>Ujistěte se, že konektor HCX má vaše nejnovější přihlašovací údaje vCenter Server.

Teď, když jste obnovili přihlašovací údaje, postupujte podle těchto kroků, abyste zajistili, že konektor HCX má vaše aktualizované přihlašovací údaje.

1. Po změně hesla přejdete na místní webové rozhraní konektoru HCX pomocí protokolu https://{IP zařízení konektoru HCX}: 443. Nezapomeňte použít port 443. Přihlaste se pomocí nových přihlašovacích údajů.

2. Na řídicím panelu VMware HCX vyberte **párování lokalit**.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Snímek obrazovky s řídicím panelem VMware HCX a zvýrazněným párováním webu":::
 
3. Vyberte správné připojení k řešení VMware Azure (pokud existuje více než jeden) a vyberte **Upravit připojení**.
 
4. Zadejte nové přihlašovací údaje uživatele vCenter Server CloudAdmin a vyberte **Upravit**. tím se přihlašovací údaje uloží. Uložení by se mělo zobrazit úspěšné.

## <a name="next-steps"></a>Další kroky

Teď, když jste si pokryli resetování vCenter Server a přihlašovacích údajů správce NSX-T pro řešení Azure VMware, se můžete seznámit s:

- [Konfigurace síťových součástí NSX v řešení Azure VMware](configure-nsx-network-components-azure-portal.md).
- [Správa životního cyklu virtuálních počítačů řešení Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Nasazení zotavení po havárii virtuálních počítačů pomocí řešení Azure VMware](disaster-recovery-for-virtual-machines.md).
