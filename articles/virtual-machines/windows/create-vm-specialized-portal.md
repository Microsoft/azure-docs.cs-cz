---
title: Vytvoření virtuálního počítače s Windows ze specializovaného VHD na webu Azure Portal | Dokumentace Microsoftu
description: Vytvořte nový virtuální počítač s Windows ze souboru VHD na webu Azure Portal.
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
ms.date: 09/20/2018
ms.author: cynthn
ms.openlocfilehash: 905f00842c5ce74f681a6c5c09ff8bf6c7a9e162
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091245"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače z VHD pomocí webu Azure portal

Existuje několik způsobů, jak vytvořit virtuální počítač (VM) v Azure: 

- Pokud už máte virtuální pevný disk (VHD), který chcete použít, nebo chcete zkopírovat virtuální pevný disk z existujícího virtuálního počítače používat, můžete vytvořit nový virtuální počítač pomocí *připojení* virtuálního pevného disku k novému virtuálnímu počítači jako disku s operačním systémem. 

- Můžete vytvořit nový virtuální počítač z virtuálního pevného disku virtuálního počítače, který je odstraněný. Například pokud máte virtuální počítač Azure, ale nefunguje správně, můžete odstranit virtuální počítač a použít jeho virtuální pevný disk k vytvoření nového virtuálního počítače. Můžete znovu použít stejný virtuální pevný disk nebo vytvoření kopie VHD vytvořením snímku a následným vytvořením nového spravovaného disku ze snímku. I když vytvoření snímku trvá několik kroků, zachová původní virtuální pevný disk a poskytne vám záložní.
 
- Virtuální počítač Azure můžete vytvořit z virtuálního pevného disku místní nahrání místního virtuálního pevného disku a připojení k novému virtuálnímu počítači. Použijte PowerShell nebo jiný nástroj k nahrání virtuálního pevného disku do účtu úložiště a pak vytvoření spravovaného disku z virtuálního pevného disku. Další informace najdete v tématu [nahrání specializovaného virtuálního pevného disku](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Nepoužívejte specializovaného disku, pokud chcete vytvořit několik virtuálních počítačů. Místo toho pro rozsáhlejší nasazení [vytvořit bitovou kopii](capture-image-resource.md) a potom [použít tuto bitovou kopii k vytvoření několika virtuálních počítačů](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Zkopírujte disk

Vytvoření snímku a pak vytvořte disk ze snímku. Tato strategie umožňuje zachovat původní virtuální pevný disk jako záložní:

1. Z [webu Azure portal](https://portal.azure.com), v nabídce vlevo vyberte **všechny služby**.
2. V **všechny služby** vyhledávací pole, zadejte **disky** a pak vyberte **disky** zobrazíte seznam dostupných disků.
3. Vyberte disk, který chcete použít. **Disku** stránka se zobrazí tento disk.
4. V nabídce v horní části vyberte **vytvořit snímek**. 
5. Zadejte **název** snímku.
6. Zvolte **skupiny prostředků** snímku. Můžete použít buď existující skupinu prostředků nebo vytvořte novou.
7. Pro **typ účtu**, zvolte buď **Standard (HDD)** nebo **úrovně Premium (SSD)** úložiště.
8. Jakmile budete hotovi, vyberte **vytvořit** k vytvoření snímku.
9. Po vytvoření snímku, vyberte **vytvořit prostředek** v levé nabídce.
10. Do vyhledávacího pole zadejte **spravovaného disku** a pak vyberte **Managed Disks** ze seznamu.
11. Na **Managed Disks** stránce **vytvořit**.
12. Zadejte **název** disku.
13. Zvolte **skupiny prostředků** disku. Můžete použít buď existující skupinu prostředků nebo vytvořte novou. Tento výběr se taky použije jako skupina prostředků ve kterém vytvoříte virtuální počítač z disku.
14. Pro **typ účtu**, zvolte buď **Standard (HDD)** nebo **úrovně Premium (SSD)** úložiště.
15. V **typ zdroje**, zkontrolujte **snímku** zaškrtnuto.
16. V **zdrojový snímek** rozevíracího seznamu, vyberte snímek, který chcete použít.
17. Provést jiné úpravy podle potřeby a potom vyberte **vytvořit** k vytvoření disku.

## <a name="create-a-vm-from-a-disk"></a>Vytvoření virtuálního počítače z disku

Jakmile budete mít spravovaného disku virtuální pevný disk, který chcete použít, můžete vytvořit virtuální počítač na portálu:

1. Z [webu Azure portal](https://portal.azure.com), v nabídce vlevo vyberte **všechny služby**.
2. V **všechny služby** vyhledávací pole, zadejte **disky** a pak vyberte **disky** zobrazíte seznam dostupných disků.
3. Vyberte disk, který chcete použít. **Disku** stránky pro tento disk se otevře.
4. V **přehled** stránky, ujistěte se, že **stav disku** je uveden jako **Unattached**. Pokud tomu tak není, můžete potřebovat k odpojení disku od virtuálního počítače nebo odstranění virtuálního počítače uvolnit disku.
4. V nabídce v horní části stránky vyberte **vytvořit virtuální počítač**.
5. Na **Základy** stránky pro nový virtuální počítač, zadejte **název virtuálního počítače** a buď vyberte existující **skupiny prostředků** nebo vytvořte novou.
6. Pro **velikost**vyberte **změnit velikost** přístup **velikost** stránky.
7. Vyberte řádek velikosti virtuálního počítače a pak zvolte **vyberte**.
8. Na **sítě** stránky, můžete buď nechat na portálu vytvořit všechny nové prostředky nebo můžete vybrat existující **virtuální síť** a **skupinu zabezpečení sítě**. Na portálu vždy vytváří nové síťové rozhraní a veřejnou IP adresu nového virtuálního počítače. 
9. Na **správu** stránce, provést změny možností monitorování.
10. Na **hosta config** stránce, podle potřeby přidejte žádná rozšíření.
11. Jakmile budete hotovi, vyberte **revize + vytvořit**. 
12. Pokud konfigurace virtuálního počítače úspěšně proběhne ověření, vyberte **vytvořit** ke spuštění nasazení.

## <a name="next-steps"></a>Další postup

Můžete také použít PowerShell k [nahrání virtuálního pevného disku do Azure a vytvoření specializovaného virtuálního počítače](create-vm-specialized.md).


