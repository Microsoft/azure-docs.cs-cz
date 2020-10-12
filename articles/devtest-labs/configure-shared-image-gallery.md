---
title: Konfigurace Galerie sdílených imagí v Azure DevTest Labs | Microsoft Docs
description: Naučte se konfigurovat galerii sdílených imagí v Azure DevTest Labs, která uživatelům umožňuje přístup k obrázkům ze sdíleného umístění při vytváření prostředků testovacího prostředí.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7ec08fa741c1b52d3dd1d1e2b4247d3689190020
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88271035"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurace galerie sdílených imagí v Azure DevTest Labs
DevTest Labs teď podporuje funkci [Galerie sdílených imagí](../virtual-machines/windows/shared-image-galleries.md) . Umožňuje uživatelům testovacího prostředí přistupovat k obrázkům ze sdíleného umístění při vytváření prostředků testovacího prostředí. Pomůže vám taky vytvářet strukturu a organizaci kolem vašich vlastních imagí virtuálních počítačů spravovaných vaším uživatelem. Funkce Galerie sdílených imagí podporuje:

- Spravovaná globální replikace imagí
- Správa verzí a seskupování imagí pro snadnější správu
- Zpřístupněte své image vysoce dostupné v rámci účtů zóny redundantního úložiště (ZRS) v oblastech, které podporují zóny dostupnosti. ZRS nabízí lepší odolnost proti chybám v rámci oblast.
- Sdílení mezi předplatnými a dokonce i mezi klienty pomocí řízení přístupu na základě role (RBAC).

Další informace najdete v [dokumentaci Galerie sdílených imagí](../virtual-machines/windows/shared-image-galleries.md). 
 
Pokud máte velký počet spravovaných imagí, které potřebujete udržovat a chtějí je zpřístupnit v celé firmě, můžete použít galerii sdílených imagí jako úložiště, které usnadňuje aktualizaci a sdílení imagí. Jako vlastník testovacího prostředí můžete k testovacímu prostředí připojit stávající sdílenou galerii imagí. Po připojení k této galerii můžou uživatelé testovacího prostředí vytvářet počítače z těchto nejnovějších imagí. Klíčovou výhodou této funkce je, že DevTest Labs teď můžou využít výhod sdílení imagí napříč laboratořemi, mezi předplatnými a v různých oblastech. 

> [!NOTE]
> Další informace o nákladech souvisejících se službou Galerie sdílených imagí najdete v tématu [fakturace pro galerii sdílených imagí](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Důležité informace
- Současně můžete k testovacímu prostředí připojit jenom jednu galerii sdílených imagí. Pokud chcete připojit jinou galerii, budete muset odpojit stávající a připojit další. 
- DevTest Labs aktuálně nepodporuje nahrávání imagí do galerie prostřednictvím testovacího prostředí. 
- Při vytváření virtuálního počítače pomocí Image Galerie sdílených imagí DevTest Labs vždycky používá nejnovější publikovanou verzi tohoto obrázku. Pokud má však image více verzí, uživatel se může pokusit vytvořit počítač ze starší verze tak, že v průběhu vytváření virtuálního počítače zobrazí kartu Pokročilá nastavení.  
- I když DevTest Labs automaticky vytvoří nejlepší pokus o zajištění, že Galerie sdílených imagí replikuje image do oblasti, ve které testovací prostředí existuje, není vždy možné. Aby uživatelům nedocházelo k problémům s vytvářením virtuálních počítačů z těchto imagí, zajistěte, aby byly image již replikovány do oblasti testovacího prostředí. "

## <a name="use-azure-portal"></a>Použití webu Azure Portal
1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. V levé navigační nabídce vyberte **všechny služby** .
1. Ze seznamu vyberte **DevTest Labs**.
1. V seznamu cvičení vyberte **testovací prostředí**.
1. V části **Nastavení** v levé nabídce vyberte **Konfigurace a zásady** .
1. V nabídce vlevo vyberte v části **virtuální počítače** možnost **Galerie sdílených imagí** .

    ![Nabídka Galerie sdílených imagí](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Kliknutím na tlačítko **připojit** a výběrem galerie v rozevíracím seznamu připojte existující galerii sdílených imagí k testovacímu prostředí.

    ![Připojit](./media/configure-shared-image-gallery/attach-options.png)
1. Až bude Galerie obrázků připojená, vyberte ji, abyste přešli do připojené galerie. Nakonfigurujte galerii pro **Povolení nebo zakázání** sdílených imagí pro vytváření virtuálních počítačů. Vyberte galerii obrázků ze seznamu a nakonfigurujte ji. 

    Ve výchozím nastavení **povolíte použití všech imagí jako základů virtuálních počítačů** na **Ano**. To znamená, že všechny image dostupné v galerii sdílených imagí budou k dispozici pro uživatele testovacího prostředí při vytváření nového testovacího virtuálního počítače. Pokud potřebujete omezit přístup k určitým imagím, změňte možnost **povoluje použití všech imagí jako základů virtuálních počítačů** na **ne**a vyberte Image, které chcete při vytváření virtuálních počítačů použít, a pak vyberte tlačítko **Uložit** .

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="Povolení nebo zakázání imagí":::

    > [!NOTE]
    > Podporují se zobecněné i specializované image v galerii sdílených imagí. 
1. Uživatelé testovacího prostředí pak můžou vytvořit virtuální počítač pomocí povolených imagí kliknutím na **+ Přidat** a najít obrázek na stránce **Zvolte základní** stránku.

    ![Uživatelé testovacího prostředí](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

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

Úplný příklad šablony Správce prostředků najdete v těchto Správce prostředků vzorových šablonách v našem úložišti GitHub: [Konfigurace Galerie sdílených imagí při vytváření testovacího prostředí](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-rest-api"></a>Použití rozhraní REST API

### <a name="get-a-list-of-labs"></a>Získat seznam cvičení 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>Získat seznam galerií sdílených imagí přidružených k testovacímu prostředí

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>Vytvořit nebo aktualizovat galerii sdílených imagí

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

### <a name="list-images-in-a-shared-image-gallery"></a>Výpis obrázků v galerii sdílených imagí

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>Další kroky
V následujících článcích najdete informace o vytvoření virtuálního počítače pomocí image z připojené sdílené Image Galerie: [Vytvoření virtuálního počítače pomocí sdílené image z Galerie](add-vm-use-shared-image.md) .
