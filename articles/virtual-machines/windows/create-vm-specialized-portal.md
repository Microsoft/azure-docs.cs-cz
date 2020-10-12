---
title: Vytvoření virtuálního počítače s Windows ze specializovaného virtuálního pevného disku v Azure Portal
description: Vytvořte nový virtuální počítač s Windows z virtuálního pevného disku v Azure Portal.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: 5a541dce94cc25958e3c3a6a058e015c8c5e3db0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87283244"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače z virtuálního pevného disku pomocí Azure Portal

Existuje několik způsobů, jak vytvořit virtuální počítač (VM) v Azure: 

- Pokud už máte virtuální pevný disk (VHD), který chcete použít, nebo chcete zkopírovat virtuální pevný disk z existujícího virtuálního počítače, který chcete použít, můžete vytvořit nový virtuální počítač *připojením* virtuálního pevného disku k NOVÉmu virtuálnímu počítači jako disk s operačním systémem. 

- Můžete vytvořit nový virtuální počítač z virtuálního pevného disku, který byl odstraněn. Například pokud máte virtuální počítač Azure, který nepracuje správně, můžete virtuální počítač odstranit a použít jeho virtuální pevný disk k vytvoření nového virtuálního počítače. Můžete buď znovu použít stejný VHD nebo vytvořit kopii virtuálního pevného disku, a to vytvořením snímku a následným vytvořením nového spravovaného disku ze snímku. I když vytvoření snímku trvá několik dalších kroků, zachová původní virtuální pevný disk a poskytne vám záložní.

- Posaďte klasický virtuální počítač a pomocí virtuálního pevného disku vytvořte nový virtuální počítač, který používá model nasazení Správce prostředků a spravované disky. Nejlepších výsledků dosáhnete, když před vytvořením snímku **zastavíte** klasický virtuální počítač v Azure Portal.
 
- Virtuální počítač Azure můžete vytvořit z místního virtuálního pevného disku tak, že nahrajete místní virtuální pevný disk a připojíte ho k novému virtuálnímu počítači. Pomocí PowerShellu nebo jiného nástroje nahrajte virtuální pevný disk do účtu úložiště a pak vytvoříte spravovaný disk z virtuálního pevného disku. Další informace najdete v tématu [nahrání specializovaného virtuálního pevného disku](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Nepoužívejte specializovaný disk, pokud chcete vytvořit více virtuálních počítačů. Místo toho můžete pro rozsáhlejší nasazení [vytvořit image](capture-image-resource.md) a pak [pomocí této image vytvořit víc virtuálních počítačů](create-vm-generalized-managed.md).

Doporučujeme omezit počet souběžných nasazení na 20 virtuálních počítačů z jednoho snímku nebo virtuálního pevného disku. 

## <a name="copy-a-disk"></a>Kopírování disku

Vytvořte snímek a pak vytvořte disk ze snímku. Tato strategie vám umožní zachovat původní virtuální pevný disk jako záložní:

1. Z [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **všechny služby**.
2. Do vyhledávacího pole **všechny služby** zadejte **disky** a pak vyberte **disky** . zobrazí se seznam dostupných disků.
3. Vyberte disk, který chcete použít. Zobrazí se stránka **disku** pro tento disk.
4. V nabídce v horní části vyberte **vytvořit snímek**. 
5. Zadejte **název** snímku.
6. Vyberte **skupinu prostředků** pro snímek. Můžete použít buď existující skupinu prostředků, nebo vytvořit novou.
7. Jako **typ účtu**vyberte buď úložiště **Standard (HDD)** , nebo **Premium (SSD)** .
8. Až budete hotovi, vyberte **vytvořit** a vytvořte snímek.
9. Po vytvoření snímku vyberte v nabídce vlevo možnost **vytvořit prostředek** .
10. Do vyhledávacího pole zadejte **Managed disk** a v seznamu vyberte **Managed disks** .
11. Na stránce **Managed disks** vyberte **vytvořit**.
12. Zadejte **název** disku.
13. Vyberte **skupinu prostředků** pro disk. Můžete použít buď existující skupinu prostředků, nebo vytvořit novou. Tento výběr se použije taky jako skupina prostředků, ve které vytvoříte virtuální počítač z disku.
14. Jako **typ účtu**vyberte buď úložiště **Standard (HDD)** , nebo **Premium (SSD)** .
15. V **typ zdroje**ověřte, zda je vybrán **snímek** .
16. V rozevíracím seznamu **zdrojový snímek** vyberte snímek, který chcete použít.
17. Podle potřeby proveďte jakékoli další úpravy a potom vyberte **vytvořit** a vytvořte disk.

## <a name="create-a-vm-from-a-disk"></a>Vytvoření virtuálního počítače z disku

Po vytvoření virtuálního pevného disku spravovaného disku, který chcete použít, můžete virtuální počítač vytvořit na portálu:

1. Z [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **všechny služby**.
2. Do vyhledávacího pole **všechny služby** zadejte **disky** a pak vyberte **disky** . zobrazí se seznam dostupných disků.
3. Vyberte disk, který chcete použít. Otevře se stránka **disku** pro tento disk.
4. Na stránce **Přehled** zajistěte, aby byl **stav disku** uvedený jako **nepřipojený**. Pokud tomu tak není, možná budete muset buď odpojit disk od virtuálního počítače, nebo odstranit virtuální počítač, aby se disk uvolnil.
4. V nabídce v horní části stránky vyberte **vytvořit virtuální počítač**.
5. Na stránce **základy** nového virtuálního počítače zadejte **název virtuálního počítače** a buď vyberte existující **skupinu prostředků** , nebo vytvořte novou.
6. Pro možnost **Velikost**vyberte **změnit velikost** pro přístup na stránku **velikosti** .
7. Vyberte řádek velikosti virtuálního počítače a pak zvolte **Vybrat**.
8. Na stránce **síť** můžete buď nechat portál vytvořit všechny nové prostředky, nebo můžete vybrat existující **virtuální síť** a **skupinu zabezpečení sítě**. Portál vždy vytvoří nové síťové rozhraní a veřejnou IP adresu pro nový virtuální počítač. 
9. Na stránce **Správa** proveďte jakékoli změny možností monitorování.
10. Na stránce **Konfigurace hosta** podle potřeby přidejte libovolná rozšíření.
11. Až budete hotovi, vyberte **zkontrolovat + vytvořit**. 
12. Pokud konfigurace virtuálního počítače projde ověřením, vyberte **vytvořit** a spusťte nasazení.


## <a name="next-steps"></a>Další kroky

PowerShell můžete také použít k [nahrání virtuálního pevného disku do Azure a vytvoření specializovaného virtuálního počítače](create-vm-specialized.md).


