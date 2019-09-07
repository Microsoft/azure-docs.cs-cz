---
title: Konfigurace Galerie sdílených imagí v Azure DevTest Labs | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat galerii sdílených imagí v Azure DevTest Labs
services: devtest-lab
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 6e0c1419e5656f184d27dce8d185a86bea71d173
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389951"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurace galerie sdílených imagí v Azure DevTest Labs
DevTest Labs teď podporuje funkci [Galerie sdílených imagí](../virtual-machines/windows/shared-image-galleries.md) . Uživatelům testovacího prostředí umožňuje přístup k imagím ze sdíleného umístění při vytváření prostředků testovacího prostředí. Pomáhá také vytvořit strukturu a zajistit organizaci související s imagemi virtuálních počítačů s vlastní správou. Funkce Galerie sdílených imagí podporuje:

- Spravovaná globální replikace imagí
- Správa verzí a seskupování imagí pro zjednodušení správy
- Zajistěte si vysokou dostupnost imagí díky účtům zónově redundantního úložiště v oblastech, které podporují zóny dostupnosti. Zónově redundantní úložiště nabízí vyšší odolnost proti selháním jednotlivých zón.
- Sdílení imagí mezi předplatnými a dokonce i mezi tenanty, a to s využitím řízení přístupu na základě role (RBAC).

Další informace najdete v [dokumentaci Galerie sdílených imagí](../virtual-machines/windows/shared-image-galleries.md). 
 
Pokud máte velký počet spravovaných imagí, které potřebujete spravovat, a chcete je zpřístupnit v rámci vaší společnosti, můžete galerii sdílených imagí využít jako úložiště, které usnadňuje jejich aktualizaci a sdílení. Jako vlastník testovacího prostředí můžete galerii sdílených imagí k tomuto prostředí připojit. Jakmile je galerie připojená, uživatelé mohou z těchto imagí vytvářet počítače. Klíčovou výhodou této funkce je, že DevTest Labs může využívat výhody sdílení imagí mezi testovacími prostředími, předplatnými i oblastmi. 

> [!NOTE]
> Další informace o nákladech souvisejících se službou Galerie sdílených imagí najdete v tématu [fakturace pro galerii sdílených imagí](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Požadavky
- Současně můžete k testovacímu prostředí připojit jenom jednu galerii sdílených imagí. Pokud chcete připojit jinou galerii, budete muset odpojit stávající a připojit další. 
- DevTest Labs aktuálně nepodporuje nahrávání imagí do galerie prostřednictvím testovacího prostředí. 
- Při vytváření virtuálního počítače pomocí Image Galerie sdílených imagí DevTest Labs vždycky používá nejnovější publikovanou verzi tohoto obrázku. Pokud má však image více verzí, uživatel se může pokusit vytvořit počítač ze starší verze tak, že v průběhu vytváření virtuálního počítače zobrazí kartu Pokročilá nastavení.  
- I když DevTest Labs automaticky vytvoří nejlepší pokus o zajištění, že Galerie sdílených imagí replikuje image do oblasti, ve které testovací prostředí existuje, není vždy možné. Aby uživatelům nedocházelo k problémům s vytvářením virtuálních počítačů z těchto imagí, zajistěte, aby byly image již replikovány do oblasti testovacího prostředí. "

## <a name="use-azure-portal"></a>Použití webu Azure Portal
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levé navigační nabídce vyberte **všechny služby** .
1. Ze seznamu vyberte **DevTest Labs**.
1. V seznamu cvičení vyberte **testovací prostředí**.
1. V části **Nastavení** v levé nabídce vyberte **Konfigurace a zásady** .
1. V nabídce vlevo vyberte v části **virtuální počítače** možnost **Galerie sdílených imagí** .

    ![Nabídka Galerie sdílených imagí](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Kliknutím na tlačítko **připojit** a výběrem galerie v rozevíracím seznamu připojte existující galerii sdílených imagí k testovacímu prostředí.

    ![Připojit](./media/configure-shared-image-gallery/attach-options.png)
1. Přejít do připojené galerie a nakonfigurovat galerii pro **Povolení nebo zakázání** sdílených imagí pro vytváření virtuálních počítačů. Vyberte galerii obrázků ze seznamu a nakonfigurujte ji. 

    Ve výchozím nastavení **povolíte použití všech imagí jako základů virtuálních počítačů** na **Ano**. To znamená, že všechny image dostupné v galerii sdílených imagí budou k dispozici pro uživatele testovacího prostředí při vytváření nového testovacího virtuálního počítače. Pokud potřebujete omezit přístup k určitým imagím, změňte možnost **povoluje použití všech imagí jako základů virtuálních počítačů** na **ne**a vyberte Image, které chcete při vytváření virtuálních počítačů použít, a pak vyberte tlačítko **Uložit** .

    ![Povolit nebo zakázat](./media/configure-shared-image-gallery/enable-disable.png)
1. Uživatelé testovacího prostředí pak můžou vytvořit virtuální počítač pomocí povolených imagí kliknutím na **+ Přidat** a najít obrázek na stránce **Zvolte základní** stránku.

    ![Uživatelé testovacího prostředí](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Použití šablon Azure Resource Manageru

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Připojit galerii sdílených imagí k testovacímu prostředí
Pokud k připojení Galerie sdílených imagí do testovacího prostředí používáte šablonu Azure Resource Manager, je nutné ji přidat do části Resources (prostředky) vaší Správce prostředků šablony, jak je znázorněno v následujícím příkladu:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Úplný příklad šablony Správce prostředků najdete v těchto Správce prostředků vzorových šablonách v našem úložišti GitHub: [Nakonfigurujte galerii sdílených imagí při vytváření testovacího prostředí](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>Použití rozhraní API

### <a name="shared-image-galleries---create-or-update"></a>Galerie sdílených imagí – vytvořit nebo aktualizovat

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Obrázky galerie sdílených imagí – seznam 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Další postup
Přečtěte si následující články, jak vytvořit virtuální počítač pomocí image z připojené sdílené Image Galerie: [Vytvoření virtuálního počítače pomocí sdílené image z Galerie](add-vm-use-shared-image.md)
