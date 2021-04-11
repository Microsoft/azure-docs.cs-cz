---
title: Vytvoření virtuálního počítače Azure v Azure Marketplace pomocí vlastní image
description: Přečtěte si, jak publikovat nabídku virtuálního počítače a Azure Marketplace pomocí vlastní image.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200466"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Jak vytvořit virtuální počítač pomocí vlastní image

Tento článek popisuje, jak vytvořit a nasadit image virtuálního počítače (VM) zadaného uživatelem.

> [!NOTE]
> Před zahájením tohoto postupu si přečtěte [technické požadavky](marketplace-virtual-machines.md#technical-requirements) pro nabídky virtuálních počítačů Azure, včetně požadavků na virtuální pevný disk (VHD).

Pokud místo toho chcete použít schválenou základní bitovou kopii, postupujte podle pokynů v tématu [Vytvoření image virtuálního počítače ze schválené základny](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Nakonfigurování virtuálního počítače

Tato část popisuje, jak změnit velikost, aktualizovat a zobecnit virtuální počítač Azure. Tyto kroky jsou nezbytné k přípravě nasazení virtuálního počítače na Azure Marketplace.

### <a name="size-the-vhds"></a>Velikost virtuálních pevných disků

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Nainstalovat nejaktuálnější aktualizace

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Provedení dalších kontrol zabezpečení

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Provádění vlastních konfigurací a naplánovaných úloh

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>Generalizace bitové kopie

Všechny obrázky v Azure Marketplace musí být obecně znovu použitelné. K tomu je potřeba, aby byl virtuální pevný disk operačního systému zobecněný, operace, která odebere všechny identifikátory jednotlivých instancí a softwarové ovladače z virtuálního počítače.

## <a name="bring-your-image-into-azure"></a>Přenesení image do Azure

Existují tři způsoby, jak přenést image do Azure:

1. Nahrajte virtuální pevný disk do galerie sdílených imagí (SIG).
1. Nahrajte virtuální pevný disk do účtu služby Azure Storage.
1. Extrahujte virtuální pevný disk ze spravované Image (Pokud používáte službu pro vytváření imagí).

Tyto možnosti jsou popsány v následujících třech částech.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Možnost 1: nahrání VHD jako galerie sdílených imagí

1. Nahrajte virtuální pevný disk do účtu úložiště.
2. Na Azure Portal vyhledejte **nasazení vlastní šablony**.
3. **V editoru vyberte vytvořit vlastní šablonu**.
4. Zkopírujte následující šablonu Azure Resource Manager (ARM).

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. Vložte šablonu do editoru.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Ukázka obrazovky kódu pro virtuální počítač.":::

1. Vyberte **Uložit**.
1. Pomocí parametrů v této tabulce můžete vyplnit pole na následující obrazovce.

| Parametry | Description |
| --- | --- |
| sourceStorageAccountResourceId | ID prostředku zdrojového účtu úložiště, ve kterém se nachází virtuální pevný disk objektu BLOB.<br><br>ID prostředku získáte tak, že přejdete na svůj **účet úložiště** v **Azure Portal**, přejdete na **vlastnosti** a zkopírujete hodnotu **ResourceID** . |
| sourceBlobUri | Identifikátor URI objektu BLOB disku s operačním systémem pro virtuální pevný disk s operačním systémem (musí být uvedený v zadaném účtu úložiště).<br><br>Adresu URL objektu BLOB získáte tak, že přejdete na svůj **účet úložiště** v **Azure Portal**, přejdete do svého **objektu BLOB** a zkopírujete hodnotu **URL** . |
| sourceBlobDataDisk0Uri | Identifikátor URI objektu BLOB datového disku pro virtuální pevný disk (musí být v zadaném účtu úložiště). Pokud nemáte datový disk, odeberte tento parametr ze šablony.<br><br>Adresu URL objektu BLOB získáte tak, že přejdete na svůj **účet úložiště** v **Azure Portal**, přejdete do svého **objektu BLOB** a zkopírujete hodnotu **URL** . |
| sourceBlobDataDisk1Uri | Identifikátor URI objektu BLOB dalšího datového prostoru virtuálního pevného disku (musí být v zadaném účtu úložiště). Pokud nemáte další datový disk, odeberte tento parametr ze šablony.<br><br>Adresu URL objektu BLOB získáte tak, že přejdete na svůj **účet úložiště** v **Azure Portal**, přejdete do svého **objektu BLOB** a zkopírujete hodnotu **URL** . |
| Galerie | Název galerie sdílených imagí |
| galleryImageDefinitionName | Název definice obrázku |
| galleryImageVersionName | Název verze image, která se má vytvořit, v tomto formátu: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Zobrazuje vlastní okno nasazení.":::

8. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**.

> [!TIP]
> Aby bylo možné publikovat image SIG, musí mít účet vydavatele oprávnění Owner. V případě potřeby pomocí následujících kroků udělíte přístup:
>
> 1. Přejít do galerie sdílených imagí (SIG).
> 2. Na levém panelu vyberte **řízení přístupu** (IAM).
> 3. Vyberte **Přidat** a pak **Přidat přiřazení role**.
> 4. Jako **role** vyberte **Owner (vlastník**).
> 5. Pro **přiřazení přístupu k** vyberte možnost **uživatel, skupina nebo instanční objekt**.
> 6. Zadejte e-mail Azure pro osobu, která bude publikovat obrázek.
> 7. Vyberte **Uložit**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Zobrazí se okno Přidat přiřazení role.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Možnost 2: nahrání virtuálního pevného disku do účtu úložiště

Nakonfigurujte a připravte virtuální počítač, který se má nahrát, jak je popsáno v tématu [Příprava virtuálního pevného disku (VHD) s Windows pro nahrání do Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) nebo [Vytvoření a nahrání virtuálního pevného disku Linux](../virtual-machines/linux/create-upload-generic.md).

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Možnost 3: extrakce virtuálního pevného disku ze spravované Image (Pokud používáte služby pro vytváření imagí)

Pokud používáte službu pro vytváření imagí, jako je například [balíček](https://www.packer.io/), možná budete muset z image extrahovat virtuální pevný disk. Neexistuje žádný přímý způsob, jak to provést. Budete muset vytvořit virtuální počítač a extrahovat VHD z disku virtuálního počítače.

## <a name="create-the-vm-on-the-azure-portal"></a>Vytvoření virtuálního počítače na Azure Portal

Pomocí těchto kroků vytvořte na [Azure Portal](https://ms.portal.azure.com/)základní image virtuálního počítače.

1. Přihlaste se na [Azure Portal](https://ms.portal.azure.com/).
2. Vyberte **Virtuální počítače**.
3. Výběrem **+ Přidat** otevřete obrazovku **vytvořit virtuální počítač** .
4. Vyberte obrázek v rozevíracím seznamu nebo vyberte **Procházet všechny veřejné a soukromé image** , aby bylo možné vyhledávat nebo procházet všechny dostupné image virtuálních počítačů.
5. Virtuální počítač **2. generace** vytvoříte tak, že přejdete na kartu **Upřesnit** a vyberete možnost **Obecné 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Vyberte Obecné 1 nebo obecné 2.":::

6. Vyberte velikost virtuálního počítače, který se má nasadit.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Vyberte doporučenou velikost virtuálního počítače pro vybranou bitovou kopii.":::

7. Zadejte další požadované podrobnosti pro vytvoření virtuálního počítače.
8. Vyberte **zkontrolovat + vytvořit** a zkontrolujte své volby. Jakmile se zobrazí zpráva **ověření proběhlo** , vyberte **vytvořit**.

Azure zahájí zřizování virtuálního počítače, který jste zadali. V nabídce vlevo vyberte kartu **Virtual Machines** a sledujte její průběh. Po vytvoření se stav virtuálních počítačů změní na **spuštěno**.

## <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

Pokud se chcete připojit k virtuálnímu počítači s [Windows](../virtual-machines/windows/connect-logon.md) nebo [Linuxem](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) , přečtěte si následující dokumentaci.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Další kroky

- [Otestujte image virtuálního počítače](azure-vm-image-test.md) , abyste se ujistili, že splňuje Azure Marketplace požadavky na publikování. Tato položka je nepovinná.
- Pokud nechcete testovat image virtuálního počítače, přihlaste se k [partnerskému centru](https://partner.microsoft.com/) a publikujte image SIG (možnost #1).
- Pokud jste postupovali jako možnost #2 nebo #3, [vygenerujte identifikátor URI SAS](azure-vm-get-sas-uri.md).
- Pokud jste narazili na potíže s vytvářením nového virtuálního pevného disku s Azure, přečtěte si téma [Nejčastější dotazy Azure Marketplace k virtuálním](azure-vm-create-faq.md)počítačům s
