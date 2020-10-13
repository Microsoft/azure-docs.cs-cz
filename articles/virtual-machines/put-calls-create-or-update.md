---
title: Volání PUT pro operace vytváření nebo aktualizace
description: VLOŽENÍ volání pro operace vytvoření nebo aktualizace na výpočetních prostředcích
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: d6ee4179dce905d637e933743ade7452a2484077
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978556"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>VLOŽENÍ volání pro vytváření nebo aktualizace výpočetních prostředků

`Microsoft.Compute` prostředky nepodporují konvenční definici sémantiky *Put http* . Místo toho tyto prostředky používají sémantiku opravy pro příkazy PUT a PATCH.

Operace **Create** v případě potřeby aplikuje výchozí hodnoty. Nicméně **aktualizace** prostředků provedené prostřednictvím Put nebo patch nepoužijí žádné výchozí vlastnosti. Operace **aktualizace** platí pro striktní sémantiku opravy.

Například vlastnost disk ve `caching` výchozím nastavení virtuálního počítače `ReadWrite` je v případě, že je prostředkem disk s operačním systémem.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

Nicméně pro operace **aktualizace** , když je vlastnost ponechána nebo je předána hodnota *null* , zůstane beze změny a nepoužije se výchozí hodnoty.

To je důležité při posílání operací aktualizace prostředku s úmyslem odebrat přidružení. Pokud je tento prostředek `Microsoft.Compute` prostředkem, musí být odpovídající vlastnost, kterou chcete odebrat, explicitně volána a přiřazena hodnota. Za tímto účelem mohou uživatelé předat prázdný řetězec, například " **"**. Tato akce instruuje platformu k odebrání tohoto přidružení.

> [!IMPORTANT]
> Neexistuje žádná podpora pro "opravování" elementu pole. Místo toho musí klient provést požadavek PUT nebo PATCH s celým obsahem aktualizovaného pole. Pokud například chcete odpojit datový disk od virtuálního počítače, proveďte požadavek GET pro získání aktuálního modelu virtuálního počítače, odeberte disk, který chcete odpojit `properties.storageProfile.dataDisks` , a proveďte požadavek PUT s aktualizovanou entitou virtuálního počítače.

## <a name="examples"></a>Příklady

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>Správná datová část pro odebrání přidružení skupin umístění blízkosti

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Nesprávné datové části pro odebrání přidružení skupin umístění blízkosti

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Další kroky
Další informace o tom, jak vytvořit nebo aktualizovat volání pro [Virtual Machines](/rest/api/compute/virtualmachines/createorupdate) a [Virtual Machine Scale Sets](/rest/api/compute/virtualmachinescalesets/createorupdate)