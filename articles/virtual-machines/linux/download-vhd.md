---
title: Stažení virtuálního pevného disku se systémem Linux z Azure
description: Stažení virtuálního pevného disku se systémem Linux pomocí rozhraní příkazového řádku Azure a Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 8def06990b72d6e08127e8c4f16e0dfd87905d4f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565181"
---
# <a name="download-a-linux-vhd-from-azure"></a>Stažení virtuálního pevného disku se systémem Linux z Azure

V tomto článku se dozvíte, jak stáhnout soubor virtuálního pevného disku (VHD) pro Linux z Azure pomocí Azure Portal. 

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk se nedá stáhnout z Azure, pokud je připojený ke spuštěnému virtuálnímu počítači. Pokud chcete, aby byl virtuální počítač spuštěný, můžete [vytvořit snímek a potom stáhnout snímek](#alternative-snapshot-the-vm-disk).

Zastavení virtuálního počítače:

1.  Přihlaste se na [Azure Portal](https://portal.azure.com/).
2.  V nabídce vlevo vyberte **Virtual Machines**.
3.  V seznamu vyberte virtuální počítač.
4.  Na stránce pro virtuální počítač vyberte **zastavit**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Zobrazuje tlačítko nabídky k zastavení virtuálního počítače.":::

### <a name="alternative-snapshot-the-vm-disk"></a>Alternativa: vytvoření snímku disku virtuálního počítače

Pořídit snímek disku, který se má stáhnout.

1. Vyberte virtuální počítač na [portálu](https://portal.azure.com).
2. V nabídce vlevo vyberte **disky** a pak vyberte disk, který chcete snímek. Zobrazí se podrobnosti o disku.  
3. V nabídce v horní části stránky vyberte **vytvořit snímek** . Otevře se stránka **vytvořit snímek** .
4. Do pole **název** zadejte název snímku. 
5. Jako **typ snímku** vyberte **úplné** nebo **přírůstkové**.
6. Až budete hotovi, vyberte **zkontrolovat + vytvořit**.

Vaše snímky se vytvoří krátce a pak se můžou použít ke stažení nebo vytvoření jiného virtuálního počítače z.

> [!NOTE]
> Pokud virtuální počítač nezastavíte jako první, snímek se nevyčistí. Snímek bude ve stejném stavu, jako kdyby byl virtuální počítač v době, kdy byl snímek vytvořen, v okamžiku, kdy došlo k jeho vypnutí, nebo došlo k chybě.  Obvykle to může způsobit problémy, pokud spuštěné aplikace spuštěné v daném čase nebyly odolné proti chybám.
>  
> Tato metoda se doporučuje jenom pro virtuální počítače s jedním diskem s operačním systémem. Před stažením nebo před vytvořením snímku pro disk s operačním systémem a každý datový disk je třeba zastavit virtuální počítače s jedním nebo více datovými disky.

## <a name="generate-sas-url"></a>Vygenerovat adresu URL SAS

Pokud chcete stáhnout soubor VHD, musíte vygenerovat adresu URL [sdíleného přístupového podpisu (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) . Po vygenerování adresy URL se adresa URL přiřadí čas vypršení platnosti.

1. V nabídce stránky pro virtuální počítač vyberte **disky**.
2. Vyberte disk s operačním systémem pro virtuální počítač a pak vyberte **exportovat disk**.
1. V případě potřeby aktualizujte hodnotu adresy URL tak, že **vyprší za (sekundy)** a získáte tak dostatek času na dokončení stahování. Výchozí hodnota je 3600 sekund (jedna hodina).
3. Vyberte **generovat adresu URL**.
 
      
## <a name="download-vhd"></a>Stáhnout VHD

1.  V části vygenerovaná adresa URL vyberte **Stáhnout soubor VHD**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Zobrazuje tlačítko ke stažení virtuálního pevného disku.":::

2.  Možná budete muset vybrat **Uložit** v prohlížeči a zahájit stahování. Výchozí název souboru VHD je *abcd*.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nahrát a vytvořit virtuální počítač se systémem Linux z vlastního disku pomocí Azure CLI](upload-vhd.md). 
- [Spravujte Azure na discích Azure CLI](tutorial-manage-disks.md).
