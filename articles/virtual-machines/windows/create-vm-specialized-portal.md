---
title: Vytvoření virtuálního počítače s Windows z specializované virtuálního pevného disku na portálu Azure | Microsoft Docs
description: Vytvořte nový virtuální počítač Windows z disku VHD na portálu Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: cynthn
ms.openlocfilehash: 428003747b7c746a2849a042e54647e86361c562
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716556"
---
# <a name="create-a-vm-from-a-vhd-using-the-azure-portal"></a>Vytvoření virtuálního počítače z disku VHD pomocí portálu Azure


Existuje několik způsobů, jak vytvořit virtuální počítače v Azure. Pokud již máte virtuální pevný disk nebo chcete zkopírovat virtuálního pevného disku z a stávajícího virtuálního počítače, které mají být použity, můžete vytvořit nový virtuální počítač připojením virtuální pevný disk jako disk operačního systému. Tento proces *připojí* virtuálního pevného disku na nový virtuální počítač jako disk operačního systému.

Můžete také vytvořit nový virtuální počítač z virtuálního pevného disku virtuálního počítače, který byl odstraněn. Například pokud máte virtuální počítač Azure, ale nefunguje správně, můžete odstranit virtuální počítač a použít virtuální pevný disk k vytvoření nového virtuálního počítače. Můžete znovu použít stejné virtuální pevný disk nebo vytvoření kopie virtuálního pevného disku pomocí vytvoření snímku a pak vytvořit nový disk spravované ze snímku. To je potřeba provést několik další kroky, ale zajišťuje, můžete zachovat původní virtuální pevný disk a také vám dává snímek, který chcete vrátit zpět v případě potřeby.

Máte místní virtuální počítač, který chcete použít k vytvoření virtuálního počítače v Azure. Můžete nahrát virtuální pevný disk a připojte ji k vytvoření nového virtuálního počítače. A nahrát VHD, budete muset použít PowerShell nebo jiný nástroj nahrajte ho do účtu úložiště a pak vytvořit spravované disk z virtuálního pevného disku. Další informace najdete v tématu [nahrát specializované virtuálního pevného disku](create-vm-specialized.md#option-2-upload-a-specialized-vhd)

Pokud chcete vytvořit víc virtuálních počítačů pomocí virtuálních počítačů nebo virtuální pevný disk, byste neměli používat tuto metodu. U větších nasazení, měli byste [vytvoření image](capture-image-resource.md) a potom [pomocí této bitové kopie můžete vytvořit víc virtuálních počítačů](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Zkopírujte disk

Vytvoření snímku a potom vytvořit disk ze snímku. To umožňuje zachovat původní virtuální pevný disk jako podzim zpět.

1. V nabídce vlevo klikněte na **všechny prostředky**.
2. V **všechny typy** rozevíracího seznamu, zrušte výběr **Vybrat vše** a posuňte se dolů a vyberte **disky** k vyhledání dostupných disků.
3. Klikněte na disk, který chcete použít. **Přehled** stránky pro otevře disku.
4. Na stránce Přehled v nabídce v horní části, klikněte na tlačítko **+ vytvořit snímek**. 
5. Zadejte název snímku.
6. Vyberte **skupiny prostředků** pro snímku. Můžete buď použít existující skupinu prostředků nebo vytvořte novou.
7. Zvolte, zda se má použít (HDD) standard nebo Premium (SDD) úložiště.
8. Až budete hotovi, klikněte na tlačítko **vytvořit** pro vytvoření snímku.
9. Po vytvoření snímku, klikněte na **+ vytvořit prostředek** v levé nabídce.
10. V panelu vyhledávání, zadejte **spravovaných disků na** a vyberte **spravované disky** ze seznamu.
11. Na **spravované disky** klikněte na **vytvořit**.
12. Zadejte název disku.
13. Vyberte **skupiny prostředků** disku. Můžete buď použít existující skupinu prostředků nebo vytvořte novou. Také bude skupina prostředků kde vytvoření virtuálního počítače z disku.
14. Zvolte, zda se má použít (HDD) standard nebo Premium (SDD) úložiště.
15. V **typ zdroje**, zajistěte, aby **snímku** je vybrána.
16. V **zdroj snímku** rozevíracího seznamu, vyberte snímek, který chcete použít.
17. Podle potřeby nastavte další možnosti a pak klikněte na **vytvořit** k vytvoření disku.

## <a name="create-a-vm-from-a-disk"></a>Vytvoření virtuálního počítače z disku

Jakmile máte spravovaných disků VHD, který chcete použít, můžete vytvořit virtuální počítač na portálu.

1. V nabídce vlevo klikněte na **všechny prostředky**.
2. V **všechny typy** rozevíracího seznamu, zrušte výběr **Vybrat vše** a posuňte se dolů a vyberte **disky** k vyhledání dostupných disků.
3. Klikněte na disk, který chcete použít. **Přehled** stránky pro otevře disku.
Na stránce Přehled, ujistěte se, že **stav disku** je uveden jako **Unattached**. Pokud tomu tak není, může být nutné odpojit disk z virtuálního počítače nebo odstranění virtuálního počítače uvolnit disku.
4. V nabídce v horní části podokna klikněte na tlačítko **+ vytvořit virtuální počítač**.
5. Na **Základy** stránky pro nový virtuální počítač, zadejte název a vyberte buď existující skupinu prostředků nebo vytvořte novou.
6. Na **velikost** vyberte na stránce velikost virtuálního počítače a pak klikněte na tlačítko **vyberte**.
7. Na **nastavení** stránky, můžete buď nechat portálu vytvářet všechny nové prostředky, nebo můžete vybrat existující **virtuální síť** a **skupinu zabezpečení sítě**. Na portálu kdykoli vytvořit nový síťových Adaptérů a veřejnou IP adresu pro nový virtuální počítač. 
8. Změnit možnosti monitorování a přidejte libovolná rozšíření, podle potřeby.
9. Až to budete mít, klikněte na **OK**. 
10. Pokud ověřením úspěšně projde konfigurace virtuálního počítače, klikněte na tlačítko **OK** ke spuštění nasazení.

## <a name="next-steps"></a>Další postup

Můžete taky použít PowerShell k [nahrání virtuálního pevného disku do Azure a vytvoření virtuálního počítače s specializované](create-vm-specialized.md).


