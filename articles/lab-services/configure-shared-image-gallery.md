---
title: Konfigurace galerie sdílených bitových obrázků v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat sdílenou galerii obrázků v Azure DevTest Labs
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
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589313"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurace galerie sdílených imagí v Azure DevTest Labs
DevTest Labs nyní podporuje funkci [Galerie sdílených obrázků.](../virtual-machines/windows/shared-image-galleries.md) Umožňuje uživatelům testovacího prostředí přístup k bitovým kopiím ze sdíleného umístění při vytváření prostředků testovacího prostředí. Pomáhá také vytvářet strukturu a organizaci podle vašich vlastních spravovaných imitek virtuálních počítače. Funkce Galerie sdílených obrázků podporuje:

- Řízená globální replikace bitových kopií
- Správa verzí a seskupování obrázků pro snadnější správu
- Zpřístupňte své obrázky vysoce dostupnými pomocí účtů zónového redundantního úložiště (ZRS) v oblastech, které podporují zóny dostupnosti. ZRS nabízí lepší odolnost proti selhání zonální.
- Sdílení mezi předplatnými a dokonce i mezi klienty pomocí řízení přístupu na základě rolí (RBAC).

Další informace naleznete v [dokumentaci k galerii sdílených obrázků](../virtual-machines/windows/shared-image-galleries.md). 
 
Pokud máte velký počet spravovaných bitových kopií, které je třeba udržovat, a chcete je zpřístupnit v celé společnosti, můžete použít sdílenou galerii obrázků jako úložiště, které usnadňuje aktualizaci a sdílení obrázků. Jako vlastník testovacího prostředí můžete k testovacímu prostředí připojit existující sdílenou galerii obrázků. Jakmile je tato galerie připojena, uživatelé testovacího prostředí mohou vytvářet počítače z těchto nejnovějších obrázků. Klíčovou výhodou této funkce je, že DevTest Labs teď můžete využít výhod sdílení ibi napříč testovacími prostředími, napříč předplatnými a napříč oblastmi. 

> [!NOTE]
> Informace o nákladech spojených se službou Galerie sdílených obrázků naleznete v [tématu Fakturace pro galerii sdílených obrázků](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Požadavky
- K testovacímu prostředí lze současně připojit pouze jednu sdílenou galerii obrázků. Pokud chcete připojit jinou galerii, budete muset odpojit stávající a připojit jinou. 
- DevTest Labs v současné době podporuje pouze zobecněné obrázky sdílené galerie obrázků.
- DevTest Labs v současné době nepodporuje nahrávání obrázků do galerie prostřednictvím laboratoře. 
- Při vytváření virtuálního počítače pomocí sdílené image galerie obrázků, DevTest Labs vždy používá nejnovější publikovanou verzi tohoto obrázku. Pokud však má bitová kopie více verzí, uživatel se může rozhodnout vytvořit počítač ze starší verze tak, že během vytváření virtuálního počítače přejde na kartu Upřesnit nastavení.  
- Přestože DevTest Labs automaticky provede nejlepší pokus zajistit sdílené galerie obrázků replikuje obrázky do oblasti, ve které lab existuje, není to vždy možné. Chcete-li se vyhnout problémům uživatelů s vytvářením virtuálních počítačů z těchto bitových kopií, ujistěte se, že image jsou již replikovány do oblasti testovacího prostředí."

## <a name="use-azure-portal"></a>Použití webu Azure Portal
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé navigační nabídce vyberte **Všechny služby.**
1. Ze seznamu vyberte **DevTest Labs**.
1. Ze seznamu testovacích prostředí vyberte **testovací prostředí**.
1. V **levé** nabídce vyberte Konfigurace **a zásady.**
1. V levé nabídce vyberte **Sdílené galerie obrázků** v části **Základy virtuálních strojů.**

    ![Nabídka Sdílené galerie obrázků](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Připojte existující sdílenou galerii obrázků do testovacího prostředí kliknutím na tlačítko **Připojit** a výběrem galerie v rozevíracím souboru.

    ![Připojit](./media/configure-shared-image-gallery/attach-options.png)
1. Přejděte do připojené galerie a nakonfigurujte galerii tak, aby **povolila nebo zakázala** vytváření sdílených bitových kopií pro vytváření virtuálních počítačů. Vyberte galerii obrázků ze seznamu a nakonfigurujte ji. 

    Ve výchozím nastavení **povolit všechny obrazy, které mají být použity jako základy virtuálních strojů** je nastavena na **Ano**. To znamená, že všechny image dostupné v připojené galerii sdílených bitových kopií budou k dispozici uživateli testovacího prostředí při vytváření nového virtuálního virtuálního počítačů testovacího prostředí. Pokud je potřeba omezit přístup k určitým obrazům, **změňte povolit použití všech bitových kopií jako základů virtuálních strojů** na **Ne**a vyberte obrázky, které chcete povolit při vytváření virtuálních počítačů, a pak vyberte tlačítko **Uložit.**

    ![Povolení nebo zakázání](./media/configure-shared-image-gallery/enable-disable.png)
1. Uživatelé testovacího prostředí pak mohou vytvořit virtuální počítač pomocí povolených obrázků kliknutím na **+Přidat** a najít obrázek na **stránce vyberte si základní** stránku.

    ![Uživatelé laboratoře](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Připojení sdílené galerie obrázků do testovacího prostředí
Pokud používáte šablonu Azure Resource Manager k připojení sdílené galerie obrázků do testovacího prostředí, musíte ji přidat do části prostředků šablony Správce prostředků, jak je znázorněno v následujícím příkladu:

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

Úplný příklad šablony Správce prostředků najdete v těchto ukázkách šablon Správce prostředků v našem veřejném úložišti GitHub: [Konfigurace sdílené galerie obrázků při vytváření testovacího prostředí](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>Použití rozhraní API

### <a name="shared-image-galleries---create-or-update"></a>Sdílené galerie obrázků – vytvoření nebo aktualizace

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

### <a name="shared-image-galleries-images---list"></a>Sdílené obrázky galerie obrázků - seznam 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Další kroky
Podívejte se na následující články o vytvoření virtuálního počítače pomocí obrázku z připojené galerie sdílených obrázků: [Vytvoření virtuálního virtuálního počítače pomocí sdíleného obrázku z galerie](add-vm-use-shared-image.md)
