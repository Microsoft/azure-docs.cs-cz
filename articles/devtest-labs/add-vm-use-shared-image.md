---
title: Přidání virtuálního počítače pomocí sdílené image v Azure DevTest Labs | Microsoft Docs
description: Naučte se, jak přidat virtuální počítač s použitím obrázku z Galerie připojených sdílených imagí v Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9421a1e21be9446b0e59328bd9a3730b57655274
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483852"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Přidání virtuálního počítače pomocí obrázku z Galerie připojených sdílených imagí
Azure DevTest Labs umožňuje připojit galerii sdílených imagí k vašemu testovacímu prostředí a potom použít image v galerii jako základ pro virtuální počítače, které vytvoříte v testovacím prostředí. Informace o tom, jak připojit galerii sdílených imagí k testovacímu prostředí, najdete v tématu [Konfigurace Galerie sdílených imagí](configure-shared-image-gallery.md). V tomto článku se dozvíte, jak do testovacího prostředí přidat virtuální počítač pomocí image z připojené sdílené image z galerie jako základní. 

## <a name="azure-portal"></a>portál Azure
V této části se dozvíte, jak pomocí Azure Portal přidat virtuální počítač do testovacího prostředí na základě obrázku z Galerie připojené sdílené bitové kopie. Tato část neposkytuje podrobné pokyny k vytvoření virtuálního počítače pomocí Azure Portal. Podrobnosti najdete v tématu [Vytvoření virtuálního počítače – Azure Portal](devtest-lab-add-vm.md). Jenom zvýrazní kroky, ve kterých jste vybrali obrázek z Galerie připojené sdílené image, a vyberte verzi image, kterou chcete použít. 

Když do testovacího prostředí přidáte virtuální počítač, můžete vybrat obrázek z Galerie připojené sdílené Image jako základní Image: 

![Vybrat sdílenou bitovou kopii pro základní](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Pak na kartě **Upřesnit nastavení** na stránce **vytvořit prostředek testovacího prostředí** můžete vybrat verzi image, kterou chcete použít jako základní bitovou kopii:

![Vybrat verzi image](./media/add-vm-use-shared-image/select-version-shared-image.png)

Po vytvoření virtuálního počítače můžete přepnout na používání jiné verze image. 

## <a name="resource-manager-template"></a>Šablona Resource Manageru
Pokud k vytvoření virtuálního počítače pomocí Image Galerie sdílených imagí používáte Azure Resource Manager šablonu, zadejte v části **Properties (vlastnosti** ) hodnotu **sharedImageId** . Prohlédněte si následující příklad: 

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

Úplný příklad šablony Správce prostředků najdete v tématu [Vytvoření virtuálního počítače pomocí sdílené Image Galerie sdílených imagí](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) v našem úložišti GitHub. 

## <a name="rest-api"></a>REST API

1. Nejdřív je potřeba získat ID image v galerii sdílených imagí. Jedním ze způsobů je zobrazit seznam všech imagí v připojené sdílené imagi Galerie pomocí následujícího příkazu GET. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Volejte metodu PUT na virtuálních počítačích předáním ID sdílené bitové kopie, kterou jste obdrželi od předchozího volání do `properties.SharedImageId` .

## <a name="next-steps"></a>Další kroky
Informace o tom, jak připojit galerii sdílených imagí k testovacímu prostředí a nakonfigurovat ji, najdete v tématu [Konfigurace Galerie sdílených imagí](configure-shared-image-gallery.md).