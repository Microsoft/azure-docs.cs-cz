---
title: Azure Portal – omezení přístupu pro import/export ke spravovaným diskům pomocí privátních odkazů
description: Povolte privátní odkazy pro vaše spravované disky pomocí Azure Portal, aktuálně ve verzi Preview. Umožňuje bezpečně exportovat a importovat disky v rámci vaší virtuální sítě.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ab861b3ed265da9060e2367bdfdeeeee7047c584
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88815857"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Použití Azure Portal k omezení přístupu pro import/export u spravovaných disků s privátními odkazy

Podpora privátních odkazů pro spravované disky je momentálně ve verzi Preview a umožňuje omezit export a import spravovaných disků tak, aby se zobrazily jenom v rámci vaší virtuální sítě Azure. Můžete vygenerovat identifikátor URI sdíleného přístupového podpisu (SAS) pro nepřipojené spravované disky a snímky pro export dat do jiné oblasti pro místní rozšiřování, zotavení po havárii a pro čtení dat pro forenzní analýzu. Identifikátor URI SAS můžete použít také k přímému nahrání virtuálního pevného disku na prázdný disk z místního prostředí. Síťový provoz mezi klienty ve své virtuální síti a spravovanými disky se přesměruje jenom přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejnému Internetu.

Vytvořením privátního koncového bodu můžete vytvořit prostředek pro přístup k disku a propojit ho s virtuální sítí ve stejném předplatném. Abyste mohli exportovat a importovat data prostřednictvím privátních odkazů, musíte k přístupu k disku přidružit disk nebo snímek. Také je nutné nastavit vlastnost NetworkAccessPolicy disku nebo snímku na `AllowPrivate` . 

Vlastnost NetworkAccessPolicy můžete nastavit tak, aby `DenyAll` nedocházelo k tomu, aby kdokoli vygeneroval identifikátor URI SAS pro disk nebo snímek. Výchozí hodnota pro vlastnost NetworkAccessPolicy je `AllowAll` .

## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>Regionální dostupnost

[!INCLUDE [virtual-machines-disks-private-links-regions](../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="prerequisites"></a>Předpoklady

Pokud chcete používat privátní koncové body pro export a import spravovaných disků, musíte mít ve svém předplatném povolenou funkci. Pokud chcete mdprivatelinks@microsoft funkci povolit pro vaše předplatná, odešlete e-mail na adresu. com s ID předplatného.

Budete si muset poznamenat virtuální síť virtuálního počítače, ke kterému jsou připojené vaše disky. Virtuální síť je nutná při konfiguraci privátního koncového bodu.

## <a name="create-a-disk-access-resource"></a>Vytvoření prostředku pro přístup k disku

1. Přihlaste se k Azure Portal a přejděte na adresu **přístup k disku** pomocí [tohoto odkazu](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > K přechodu do okna přístup k disku je nutné použít [poskytnutý odkaz](https://aka.ms/disksprivatelinks) . Není aktuálně vidět na veřejném portálu bez použití odkazu.

1. Vyberte **+ Přidat** a vytvořte nový prostředek pro přístup k disku.
1. V okně vytvořit vyberte vaše předplatné, skupinu prostředků, zadejte název a vyberte oblast.
1. Vyberte **Zkontrolovat a vytvořit**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Snímek obrazovky s oknem pro vytvoření přístupu k disku Zadejte požadovaný název, vyberte oblast, vyberte skupinu prostředků a pokračujte.":::

Po vytvoření prostředku přejděte přímo na něj.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Snímek obrazovky s oknem pro vytvoření přístupu k disku Zadejte požadovaný název, vyberte oblast, vyberte skupinu prostředků a pokračujte.":::

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

Teď, když máte prostředek pro přístup k disku, můžete ho použít k tomu, abyste mohli zpracovávat přístup k exportu/importům na disku, to se provádí prostřednictvím privátních koncových bodů. Proto budete muset vytvořit privátní koncový bod a nakonfigurovat ho pro přístup k disku.

1. V prostředku pro přístup k disku vyberte **připojení privátního koncového bodu**.
1. Vyberte **+ soukromý koncový bod**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Snímek obrazovky s oknem pro vytvoření přístupu k disku Zadejte požadovaný název, vyberte oblast, vyberte skupinu prostředků a pokračujte.":::

1. Výběr skupiny prostředků
1. Zadejte název a vyberte stejnou oblast, ve které byl prostředek pro přístup k disku vytvořen.
1. Vyberte **Další: prostředek >**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Snímek obrazovky s oknem pro vytvoření přístupu k disku Zadejte požadovaný název, vyberte oblast, vyberte skupinu prostředků a pokračujte.":::

1. V okně **prostředek** vyberte **připojit k prostředku Azure ve složce Můj adresář**.
1. Jako **typ prostředku** vyberte **Microsoft. COMPUTE/diskAccesses** .
1. Vyberte **prostředek pro přístup k disku** , který jste vytvořili dříve.
1. Ponechte **cílový dílčí prostředek** jako **disky** .
1. Vyberte **Další: >konfigurace **.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Snímek obrazovky s oknem pro vytvoření přístupu k disku Zadejte požadovaný název, vyberte oblast, vyberte skupinu prostředků a pokračujte.":::

1. Vyberte virtuální síť, na kterou chcete omezit export disku, ostatní virtuální sítě nebudou moct disk exportovat.

    > [!NOTE]
    > Pokud máte pro vybranou podsíť povolenou skupinu zabezpečení sítě (avit), bude zakázána pouze pro privátní koncové body v této podsíti. Další prostředky v této podsíti budou mít i nadále vynucené NSG.

1. Vyberte příslušnou podsíť.
1. Vyberte **Zkontrolovat a vytvořit**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Snímek obrazovky s oknem pro vytvoření přístupu k disku Zadejte požadovaný název, vyberte oblast, vyberte skupinu prostředků a pokračujte.":::

## <a name="enable-private-endpoint-on-your-disk"></a>Povolení privátního koncového bodu na disku

1. Přejděte na disk, který chcete nakonfigurovat.
1. Výběr **sítě**
1. Vyberte **privátní koncový bod (přes přístup k disku)** a vyberte přístup k disku, který jste vytvořili dříve.
1. Vyberte **Uložit**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Snímek obrazovky s oknem pro vytvoření přístupu k disku Zadejte požadovaný název, vyberte oblast, vyberte skupinu prostředků a pokračujte.":::

Právě jste dokončili konfiguraci privátních odkazů, které můžete použít při importu/exportu spravovaného disku.

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy týkající se privátních odkazů](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Export/kopírování spravovaných snímků jako VHD do účtu úložiště v jiné oblasti pomocí PowerShellu](scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md)