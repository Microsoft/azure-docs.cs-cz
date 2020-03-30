---
title: Přidání virtuálního počítače pomocí sdílené image v azure devtest labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak přidat virtuální počítač (VM) pomocí image z připojené galerie sdílených bitových obrázků v Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775588"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Přidání virtuálního virtuálního uživatele pomocí obrázku z připojené galerie sdílených obrázků
Azure DevTest Labs umožňuje připojit sdílenou galerii obrázků do testovacího prostředí a pak použít image v galerii jako základpro virtuální počítače, které vytvoříte v testovacím prostředí. Informace o připojení sdílené galerie obrázků do testovacího prostředí naleznete v [tématu Konfigurace galerie sdílených obrázků](configure-shared-image-gallery.md). Tento článek ukazuje, jak přidat virtuální počítač do testovacího prostředí pomocí obrázku z připojené sdílené galerie obrázků jako základ. 

## <a name="azure-portal"></a>portál Azure
V této části se dozvíte, jak pomocí portálu Azure přidat virtuální počítač do testovacího prostředí na základě image z připojené galerie sdílených bitových obrázků. Tato část neposkytuje podrobné podrobné pokyny pro vytvoření virtuálního počítače pomocí portálu Azure. Pro tyto podrobnosti najdete [v tématu vytvoření virtuálního počítače – portál Azure](devtest-lab-add-vm.md). Pouze zvýrazní kroky, kdy vyberete obrázek z připojené galerie sdílených obrázků a vyberete verzi obrázku, který chcete použít. 

Při přidávání virtuálního počítače do testovacího prostředí můžete vybrat obrázek z připojené galerie sdílených obrázků jako základní obrázek: 

![Výběr sdíleného obrázku pro základnu](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Potom na kartě **Upřesnit nastavení** na stránce **Vytvořit prostředky testovacího prostředí** můžete vybrat verzi obrazu, kterou chcete použít jako základní obraz:

![Vybrat verzi obrázku](./media/add-vm-use-shared-image/select-version-shared-image.png)

Po vytvoření virtuálního virtuálního aplikace můžete přepnout na použití jiné verze image. 

## <a name="resource-manager-template"></a>Šablona Resource Manageru
Pokud používáte šablonu Azure Resource Manager k vytvoření virtuálního počítače pomocí sdílené image galerie bitových obrázků, zadejte hodnotu pro **sharedImageId** v části **Vlastnosti.** Prohlédněte si následující příklad: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Úplný příklad šablony Správce prostředků najdete [v tématu Vytvoření virtuálního počítače pomocí ukázky obrázku sdílené galerie obrázků](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) v našem úložišti GitHub. 

## <a name="rest-api"></a>REST API

1. Nejprve musíte získat ID obrázku ve sdílené galerii obrázků. Jedním ze způsobů je vypsat všechny obrázky v připojené sdílené galerii obrázků pomocí následujícího příkazu GET. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Vyvolat PUT metoda na virtuálních počítačích předáním ID sdílené bitové `properties.SharedImageId`kopie, kterou jste obdrželi z předchozího volání na .

## <a name="next-steps"></a>Další kroky
Informace o připojení sdílené galerie obrázků k testovacímu prostředí a její konfiguraci naleznete v [tématu Konfigurace galerie sdílených obrázků](configure-shared-image-gallery.md).