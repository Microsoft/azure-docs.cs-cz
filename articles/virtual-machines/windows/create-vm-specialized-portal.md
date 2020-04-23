---
title: Vytvoření virtuálního počítače s Windows ze specializovaného virtuálního pevného disku na webu Azure Portal
description: Vytvořte nový virtuální počítač s Windows z virtuálního pevného disku na webu Azure Portal.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: d360ac5a57fe72b092a6694721905c066527bba3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086449"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače z virtuálního pevného disku pomocí portálu Azure

Existuje několik způsobů, jak vytvořit virtuální počítač (VM) v Azure: 

- Pokud už máte virtuální pevný disk (VHD) k použití nebo chcete zkopírovat virtuální pevný disk z existujícího virtuálního počítače, můžete vytvořit nový virtuální počítač *připojením* virtuálního pevného disku k novému virtuálnímu počítači jako disk s os. 

- Můžete vytvořit nový virtuální virtuální hod z virtuálního pevného disku virtuálního virtuálního movitého virtuálního movitého virtuálního disku, který byl odstraněn. Například pokud máte virtuální počítač Azure, který nefunguje správně, můžete odstranit virtuální počítač a použít jeho Virtuální pevný disk k vytvoření nového virtuálního počítače. Můžete buď znovu použít stejný virtuální pevný disk nebo vytvořit kopii virtuálního pevného disku vytvořením snímku a vytvořením nového spravovaného disku ze snímku. Přestože vytvoření snímku trvá několik dalších kroků, zachová původní virtuální pevný disk a poskytuje záložní.

- Vezměte klasický virtuální počítače a pomocí virtuálního pevného disku vytvořte nový virtuální počítače, který používá model nasazení Resource Manageru a spravované disky. Nejlepších výsledků dosáhnete, než před vytvořením snímku **zastavíte** klasický virtuální počítač na webu Azure Portal.
 
- Virtuální počítač Azure můžete vytvořit z místního virtuálního pevného disku nahráním místního virtuálního pevného disku a jeho připojením k novému virtuálnímu počítači. Pomocí prostředí PowerShell nebo jiného nástroje nahrajete virtuální pevný disk do účtu úložiště a pak vytvoříte spravovaný disk z virtuálního pevného disku. Další informace naleznete [v tématu Uploads a specialize VHD](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Nepoužívejte specializovaný disk, pokud chcete vytvořit více virtuálních počítačů. Místo toho pro větší nasazení [vytvořte bitovou kopii](capture-image-resource.md) a [pak ji použijte k vytvoření více virtuálních počítačů](create-vm-generalized-managed.md).

Doporučujeme omezit počet souběžných nasazení na 20 virtuálních počítače z jednoho snímku nebo virtuálního pevného disku. 

## <a name="copy-a-disk"></a>Kopírování disku

Vytvořte snímek a potom vytvořte disk ze snímku. Tato strategie umožňuje zachovat původní virtuální pevný disk jako záložní:

1. Na [portálu Azure](https://portal.azure.com)v levém menu vyberte **Všechny služby**.
2. Do vyhledávacího pole **Všechny služby** zadejte **disky** a pak vyberte **Disky,** chcete-li zobrazit seznam dostupných disků.
3. Vyberte disk, který chcete použít. Zobrazí se stránka **Disk** pro tento disk.
4. V nabídce nahoře vyberte **Vytvořit snímek**. 
5. Zadejte **název** snímku.
6. Zvolte **skupinu prostředků** pro snímek. Můžete použít existující skupinu prostředků nebo vytvořit novou.
7. Pro **typ účtu**zvolte úložiště Standard **(HDD)** nebo Premium **(SSD).**
8. Až budete hotovi, vyberte **Vytvořit** a vytvořte snímek.
9. Po vytvoření snímku vyberte **vytvořit prostředek** v levé nabídce.
10. Do vyhledávacího pole zadejte **spravovaný disk** a ze seznamu vyberte **spravované disky.**
11. Na stránce **Spravované disky** vyberte **Vytvořit**.
12. Zadejte **název** disku.
13. Zvolte **skupinu prostředků** pro disk. Můžete použít existující skupinu prostředků nebo vytvořit novou. Tento výběr se také použije jako skupina prostředků, kde vytvoříte virtuální hod z disku.
14. Pro **typ účtu**zvolte úložiště Standard **(HDD)** nebo Premium **(SSD).**
15. V **typu Zdroj**zkontrolujte, zda je **vybránsnímek.**
16. V rozevíracím přehledu **Zdrojový snímek** vyberte snímek, který chcete použít.
17. Podle potřeby proveďte další úpravy a pak vyberte **Vytvořit,** chcete-li disk vytvořit.

## <a name="create-a-vm-from-a-disk"></a>Vytvoření virtuálního počítače z disku

Po vytvoření spravovaného disku, který chcete použít, můžete vytvořit virtuální modul na portálu:

1. Na [portálu Azure](https://portal.azure.com)v levém menu vyberte **Všechny služby**.
2. Do vyhledávacího pole **Všechny služby** zadejte **disky** a pak vyberte **Disky,** chcete-li zobrazit seznam dostupných disků.
3. Vyberte disk, který chcete použít. Otevře se stránka **Disk** pro tento disk.
4. Na stránce **Přehled** zkontrolujte, zda je **stav disků** uveden jako **Nepřipojený**. Pokud tomu tak není, možná budete muset disk odpojit od virtuálního počítače nebo virtuální ho odstranit, abyste disk uvolnili.
4. V nabídce v horní části stránky vyberte **Vytvořit virtuální hospic**.
5. Na stránce **Základy** pro nový virtuální počítač zadejte **název virtuálního počítače** a vyberte existující **skupinu prostředků** nebo vytvořte novou.
6. V **části Velikost**vyberte Změnit **velikost,** abyste měli přístup ke stránce **Velikost.**
7. Vyberte řádek velikosti virtuálního počítače a pak zvolte **Vybrat**.
8. Na stránce **Síť** můžete buď nechat portál vytvořit všechny nové prostředky, nebo vybrat existující **skupinu zabezpečení** **virtuální sítě** a sítě . Portál vždy vytvoří nové síťové rozhraní a veřejnou IP adresu pro nový virtuální modul. 
9. Na stránce **Správa** proveďte změny možností monitorování.
10. Na stránce **Konfigurace hosta** přidejte podle potřeby všechna rozšíření.
11. Až budete hotovi, vyberte **Zkontrolovat + vytvořit**. 
12. Pokud konfigurace virtuálního počítače projde ověřením, vyberte **Vytvořit** a spusťte nasazení.


## <a name="next-steps"></a>Další kroky

Pomocí PowerShellu můžete taky [nahrát virtuální pevný disk do Azure a vytvořit specializovaný virtuální počítač](create-vm-specialized.md).


