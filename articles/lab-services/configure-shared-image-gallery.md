---
title: Konfigurace sdílené bitové kopie Galerie ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Další informace o konfiguraci sdílené bitové kopie Galerie ve službě Azure DevTest Labs
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 51b394043f88789865edea5be6376ae536f88848
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420435"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurace sdílené bitové kopie Galerie ve službě Azure DevTest Labs
DevTest Labs teď podporuje [Galerie obrázků Shared](/virtual-machines/windows/shared-image-galleries.md) funkce. Umožňuje uživatelům testovacího prostředí pro přístup k obrázkům ze sdíleného umístění při vytváření prostředků testovacího prostředí. Pomáhá také sestavit strukturu a organizace vaše vlastní spravované Image virtuálních počítačů. Podporuje funkci sdíleného Galerie Imagí:

- Spravovat globální replikací obrázků
- Správa verzí a seskupení obrázky pro snadnější správu
- Zpřístupníte vaše Image s účty Zónově redundantního úložiště (ZRS) s vysokou dostupností v oblasti, které podporují zóny dostupnosti. ZRS nabízí lepší odolnost proti chybám oblastmi.
- Sdílení napříč předplatnými a dokonce i mezi tenanty používající řízení přístupu na základě role (RBAC).

Další informace najdete v tématu [Galerie obrázků Shared dokumentaci](../virtual-machines/windows/shared-image-galleries.md). 
 
Pokud máte velký počet spravované Image, které je potřeba, abyste a chcete zpřístupnit v rámci vaší společnosti, můžete jako úložiště, který umožňuje snadno aktualizovat a sdílet vaše Image Galerie sdílené bitové kopie. Jako vlastník testovacího prostředí můžete připojit existující Galerie sdílené bitové kopie do testovacího prostředí. Po připojení této galerii uživatelé testovacího prostředí můžete vytvořit počítače z těchto imagí nejnovější. Klíčovou výhodou této funkce je, že DevTest Labs můžete nově využít výhod sdílení imagí mezi testovací prostředí, napříč předplatnými a oblastmi. 

## <a name="considerations"></a>Požadavky
- Najednou lze připojit pouze jedna Galerie sdílené bitové kopie do testovacího prostředí. Pokud se chcete připojit další galerie, je potřeba odpojit existující a jiné připojení. 
- DevTest Labs v současné době nepodporuje nahrávání imagí do Galerie přes testovací prostředí. 
- Při vytváření virtuálního počítače pomocí image Galerie sdílené bitové kopie, DevTest Labs vždy používá nejnovější publikované verzi tohoto obrázku.
- I když DevTest Labs automaticky provede nejlepší pokus Ujistěte se, že sdílené bitové kopie Galerie imagí replikuje do oblasti, ve které existuje testovacího prostředí, není vždy možné. Abyste se vyhnuli potíže při vytváření virtuálních počítačů z těchto imagí, ujistěte se, že Image se již replikují do oblasti testovacího prostředí."

## <a name="use-azure-portal"></a>Použití webu Azure Portal
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **všechny služby** v navigační nabídce vlevo.
1. Ze seznamu vyberte **DevTest Labs**.
1. V seznamu testovacích prostředí, vyberte vaše **lab**.
1. Vyberte **konfigurace a zásad** v **nastavení** části v nabídce vlevo.
1. Vyberte **Galerie obrázků Shared** pod **báze virtuálního počítače** v nabídce vlevo.
1. Kliknutím na připojit existující Galerie sdílené bitové kopie do testovacího prostředí **připojit** tlačítko a vyberete Galerie v rozevírací nabídce.
1. Přejděte na galerii připojené a nakonfigurovat galerii na **povolit nebo zakázat** sdílených bitových kopií pro vytvoření virtuálního počítače.
1. Uživatelé testovacího prostředí potom můžete vytvořit virtuální počítač pomocí Image povoleno po kliknutí na **+ přidat** a hledání obrázků v **zvolte základním** stránky.

## <a name="use-azure-resource-manager-template"></a>Použití šablon Azure Resource Manageru

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Připojení sdílené bitové kopie Galerie do testovacího prostředí
Pokud používáte šablonu Azure Resource Manageru pro připojení sdílené bitové kopie Galerie na vašem testovacím prostředí, budete muset přidat v části prostředky šablony Resource Manageru, jak je znázorněno v následujícím příkladu:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

Úplný příklad šablony Resource Manageru najdete v těchto ukázkové šablony Resource Manageru v našem veřejném úložišti Githubu: [Konfigurace sdílené bitové kopie Galerie při vytváření testovacího prostředí](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>Vytvoření virtuálního počítače pomocí image z Galerie sdílené bitové kopie
Pokud používáte šablonu Azure Resource Manageru k vytvoření virtuálního počítače pomocí image Galerie sdílené bitové kopie, použijte následující ukázku:

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

Další informace najdete v tématu tyto ukázkové šablony Resource Manageru v našich veřejných Githubu.
[Vytvoření virtuálního počítače pomocí image Galerie sdílené bitové kopie](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage).

## <a name="use-api"></a>Použití rozhraní API

- Pomocí rozhraní API verze 2018-10-15-preview.
- Připojit galerie, odešlete požadavek, jak je znázorněno v následujícím fragmentu kódu:
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- Chcete-li zobrazit všechny Image v galerii sdílené bitové kopie, můžete zobrazit seznam všech sdílené bitové kopie spolu s jejich ID prostředků podle

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- Vytvoření virtuálního počítače pomocí sdílené bitové kopie, můžete provést PUT na virtuálních počítačích a ve vlastnostech virtuálního počítače, předejte ID sdílené bitové kopie, kterou jste získali z předchozího volání. K vlastnostem. SharedImageId


## <a name="next-steps"></a>Další postup
Na artefakty naleznete v následujících článcích:

- [Zadejte povinné artefakty testovacího prostředí.](devtest-lab-mandatory-artifacts.md)
- [Přidání vlastních artefaktů](devtest-lab-artifact-author.md)
- [Přidání úložiště artefaktů do testovacího prostředí](devtest-lab-artifact-author.md)
- [Diagnostikování selhání artefaktů](devtest-lab-troubleshoot-artifact-failure.md)
