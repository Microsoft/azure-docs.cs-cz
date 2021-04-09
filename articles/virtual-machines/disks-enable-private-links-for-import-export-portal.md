---
title: Azure Portal – omezení přístupu pro import/export ke spravovaným diskům pomocí privátních odkazů
description: Pomocí Azure Portal povolte privátní odkazy na spravované disky. Umožňuje bezpečně exportovat a importovat disky v rámci vaší virtuální sítě.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b80100216003e91fde54b5e555bafb755c942810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682915"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Použití Azure Portal k omezení přístupu pro import/export u spravovaných disků s privátními odkazy

Podpora privátních odkazů u spravovaných disků umožňuje omezit export a import spravovaných disků tak, aby se staly jenom v rámci vaší virtuální sítě Azure. Můžete vygenerovat identifikátor URI sdíleného přístupového podpisu (SAS) pro nepřipojené spravované disky a snímky pro export dat do jiné oblasti pro místní rozšiřování, zotavení po havárii a pro čtení dat pro forenzní analýzu. Identifikátor URI SAS můžete použít také k přímému nahrání virtuálního pevného disku na prázdný disk z místního prostředí. Síťový provoz mezi klienty ve své virtuální síti a spravovanými disky se přesměruje jenom přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejnému Internetu.

Vytvořením privátního koncového bodu můžete vytvořit prostředek pro přístup k disku a propojit ho s virtuální sítí ve stejném předplatném. Abyste mohli exportovat a importovat data prostřednictvím privátních odkazů, musíte k přístupu k disku přidružit disk nebo snímek. Také je nutné nastavit vlastnost NetworkAccessPolicy disku nebo snímku na `AllowPrivate` . 

Vlastnost NetworkAccessPolicy můžete nastavit tak, aby `DenyAll` nedocházelo k tomu, aby kdokoli vygeneroval identifikátor URI SAS pro disk nebo snímek. Výchozí hodnota pro vlastnost NetworkAccessPolicy je `AllowAll` .

## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>Vytvoření prostředku pro přístup k disku

1. Přihlaste se k Azure Portal a přejděte na adresu **přístup k disku** pomocí [tohoto odkazu](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > K přechodu do okna přístup k disku je nutné použít [poskytnutý odkaz](https://aka.ms/disksprivatelinks) . Není aktuálně vidět na veřejném portálu bez použití odkazu.

1. Vyberte **+ Přidat** a vytvořte nový prostředek pro přístup k disku.
1. V okně vytvořit vyberte vaše předplatné, skupinu prostředků, zadejte název a vyberte oblast.
1. Vyberte **Zkontrolovat a vytvořit**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Snímek obrazovky s oknem pro vytvoření přístupu k disku Zadejte požadovaný název, vyberte oblast, vyberte skupinu prostředků a pokračujte.":::

Po vytvoření prostředku přejděte přímo na něj.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Snímek obrazovky s tlačítkem přejít na prostředek na portálu":::

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

Teď, když máte prostředek pro přístup k disku, můžete ho použít k tomu, abyste mohli zpracovávat přístup k exportu/importům na disku, to se provádí prostřednictvím privátních koncových bodů. Proto budete muset vytvořit privátní koncový bod a nakonfigurovat ho pro přístup k disku.

1. V prostředku pro přístup k disku vyberte **připojení privátního koncového bodu**.
1. Vyberte **+ soukromý koncový bod**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Snímek obrazovky okna s přehledem pro prostředek přístupu k disku Připojení privátního koncového bodu je zvýrazněné.":::

1. Výběr skupiny prostředků
1. Zadejte název a vyberte stejnou oblast, ve které byl prostředek pro přístup k disku vytvořen.
1. Vyberte **Další: prostředek >**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Snímek pracovního postupu pro vytvoření privátního koncového bodu, první okno Pokud nevyberete příslušnou oblast, může dojít k problémům později.":::

1. V okně **prostředek** vyberte **připojit k prostředku Azure ve složce Můj adresář**.
1. Jako **typ prostředku** vyberte **Microsoft. COMPUTE/diskAccesses** .
1. Vyberte **prostředek pro přístup k disku** , který jste vytvořili dříve.
1. Ponechte **cílový dílčí prostředek** jako **disky** .
1. Vyberte **Další: >konfigurace**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Snímek pracovního postupu pro vytvoření privátního koncového bodu, druhé okno Se všemi zvýrazněnými hodnotami (typ prostředku, prostředek, cílový dílčí prostředek)":::

1. Vyberte virtuální síť, na kterou chcete omezit export disku, ostatní virtuální sítě nebudou moct disk exportovat.

    > [!NOTE]
    > Pokud máte pro vybranou podsíť povolenou skupinu zabezpečení sítě (avit), bude zakázána pouze pro privátní koncové body v této podsíti. Další prostředky v této podsíti budou mít i nadále vynucené NSG.

1. Vyberte příslušnou podsíť.
1. Vyberte **Zkontrolovat a vytvořit**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Snímek pracovního postupu vytvoření privátního koncového bodu, třetí okno Virtuální síť a podsíť jsou zvýrazněné.":::

## <a name="enable-private-endpoint-on-your-disk"></a>Povolení privátního koncového bodu na disku

1. Přejděte na disk, který chcete nakonfigurovat.
1. Výběr **sítě**
1. Vyberte **privátní koncový bod (přes přístup k disku)** a vyberte přístup k disku, který jste vytvořili dříve.
1. Vyberte **Uložit**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Snímek obrazovky okna sítě spravovaného disku. Zvýrazní se výběr privátního koncového bodu i přístup k vybranému disku. Při uložení se nakonfiguruje disk pro tento přístup.":::

Právě jste dokončili konfiguraci privátních odkazů, které můžete použít při importu/exportu spravovaného disku.

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy týkající se privátních odkazů](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Export/kopírování spravovaných snímků jako VHD do účtu úložiště v jiné oblasti pomocí PowerShellu](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)