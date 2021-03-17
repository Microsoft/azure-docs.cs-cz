---
title: Resetovat přihlašovací údaje vSphere pro řešení Azure VMware
description: Naučte se resetovat přihlašovací údaje vSphere pro privátní cloud řešení Azure VMware a zajistěte, aby měl konektor HCX nejnovější přihlašovací údaje vSphere.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e5a15caa98a46b0ae75b68ee7b568dabdbf1956c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603078"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Resetovat přihlašovací údaje vSphere pro řešení Azure VMware

V tomto článku Vás provedeme kroky k resetování přihlašovacích údajů vSphere pro privátní cloud řešení Azure VMware. To vám umožní zajistit, aby konektor HCX měl nejnovější přihlašovací údaje vSphere.

## <a name="reset-your-vsphere-credentials"></a>Resetování přihlašovacích údajů pro vSphere

 Nejdřív si resetujte přihlašovací údaje pro vSphere. Přihlašovací údaje správce vCenter CloudAdmin a NSX-T vyprší. pomocí těchto kroků ale můžete pro tyto účty vygenerovat nová hesla.

> [!NOTE]
> Pokud používáte přihlašovací údaje CloudAdmin pro připojené služby, jako je HCX, vCenter Orchestrator, vCloud Director nebo vRealize, vaše připojení po aktualizaci hesla přestane fungovat.  Tyto služby by měly být před zahájením rotace hesla zastaveny.  V takovém případě může dojít k dočasnému blokování účtů správce vCenter CloudAdmin a NSX-T, protože tyto služby budou průběžně volat pomocí vašich starých přihlašovacích údajů.  Další informace o nastavení samostatných účtů pro připojené služby najdete v tématu [Koncepty přístupu a identity](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. Na portálu řešení Azure VMware otevřete příkazový řádek.

2. Spusťte následující příkaz, který aktualizuje heslo CloudAdmin vCenter.  Budete muset nahradit {SubscriptionID}, {resourceName} a {PrivateCloudName} skutečnými hodnotami privátního cloudu, ke kterému patří účet CloudAdmin.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Spuštěním následujícího příkazu aktualizujte heslo správce NSX-T. Budete muset nahradit {SubscriptionID}, {resourceName} a {PrivateCloudName} skutečnými hodnotami privátního cloudu, ke kterému patří účet správce NSX-T.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vsphere-credentials"></a>Ujistěte se, že konektor HCX má vaše nejnovější přihlašovací údaje pro vSphere.

Teď, když jste obnovili přihlašovací údaje, postupujte podle těchto kroků, abyste zajistili, že konektor HCX má vaše aktualizované přihlašovací údaje.

1. Po změně hesla přejdete na místní webové rozhraní konektoru HCX pomocí protokolu https://{IP zařízení konektoru HCX}: 443. Nezapomeňte použít port 443. Přihlaste se pomocí nových přihlašovacích údajů.

2. Na řídicím panelu VMware HCX vyberte **párování lokalit**.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Snímek obrazovky s řídicím panelem VMware HCX a zvýrazněným párováním webu":::
 
3. Vyberte správné připojení k funkci AVS (pokud existuje více než jedna) a vyberte **Upravit připojení**.
 
4. Zadejte nové přihlašovací údaje vSphere a vyberte **Upravit**, které uloží přihlašovací údaje. Uložení by se mělo zobrazit úspěšné.

## <a name="next-steps"></a>Další kroky

Teď, když jste si pokryli resetování přihlašovacích údajů vSphere pro řešení Azure VMware, si možná budete chtít přečíst:

- [Konfigurace síťových součástí NSX v řešení Azure VMware](configure-nsx-network-components-azure-portal.md).
- [Správa životního cyklu virtuálních počítačů řešení Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Nasazení zotavení po havárii virtuálních počítačů pomocí řešení Azure VMware](disaster-recovery-for-virtual-machines.md).
