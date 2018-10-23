---
title: Vytvoření virtuálního pevného disku kompatibilního s Azure pro Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak vytvořit virtuální pevný disk pro nabídky virtuálních počítačů na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: pbutlerm
ms.openlocfilehash: e06b6412061702e7f2d57dc1528ebf043a815516
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639631"
---
# <a name="create-an-azure-compatible-vhd"></a>Vytvoření virtuálního pevného disku kompatibilního s Azure

Tento článek podrobně popisuje kroky potřebné k vytvoření virtuálního pevného disku (VHD) pro nabídky virtuálních počítačů (VM) na webu Azure Marketplace.  Zahrnuje také osvědčené postupy pro různé funkce, jako je například pomocí protokol RDP (Remote Desktop), výběr velikosti virtuálního počítače, instalace nejnovějších aktualizací Windows a zobecňuje se image virtuálního pevného disku.  V dalších částech zaměřuje na systému windows virtuální pevné disky; Další informace o vytváření virtuálních pevných disků založených na Linuxu najdete v tématu [Linux v distribucích schválených pro Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Důrazně doporučujeme, abyste postupovali podle pokynů v tomto tématu můžete použít Azure k vytvoření virtuálního počítače obsahující předem nakonfigurované, potvrzená operační systém.  Pokud to není kompatibilní s řešením, je možné k vytvoření a konfigurace virtuálního počítače s místní použití schválené operačního systému.  Potom můžete nakonfigurovat a připravený k nahrání, jak je popsáno v [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Vyberte schválené základní
Operační systém virtuálního pevného disku pro vaši image virtuálního počítače musí být založené na Azure schválené základní imagi, která obsahuje Windows Server nebo SQL Server.
Chcete-li začít, vytvořte virtuální počítač z jednoho z následujících imagí nacházejí na portálu Microsoft Azure:

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/), [2012 Datacenter](http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/), [2008 R2 SP1](http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Pokud používáte aktuální portál Azure nebo PowerShell, Image Windows serveru publikované 8. září 2014 a později jsou schválené.

Azure také nabízí celou řadu Linuxových distribucí schválených.  Aktuální seznam najdete v tématu [Linux v distribucích schválených pro Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Vytvoření virtuálního počítače na webu Azure Portal 

V Microsoftu [webu Azure portal](https://ms.portal.azure.com/), vytvořte základní image, pomocí následujících kroků.

1. Přihlaste se k portálu pomocí účtu Microsoft pro předplatné Azure, že který chcete publikovat vaši nabídku virtuálního počítače.
2. Vytvořit novou skupinu prostředků a poskytnout vaší **název skupiny prostředků**, **předplatné**, a **umístění skupiny prostředků**.  Další informace najdete v tématu [Správa skupin prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal#manage-resource-groups).
3. Klikněte na **virtuálních počítačů** v levém řádku nabídek zobrazíte na stránce podrobností virtuálních počítačů. 
4. Na tuto novou stránku, klikněte na **+ přidat** zobrazíte **Compute** okno.  Pokud se na úvodní obrazovce se nezobrazí typ virtuálního počítače, můžete vyhledat název vašeho základního virtuálního počítače, například:

    ![Okno Nový virtuální počítač COMPUTE](./media/publishvm_014.png)

5. Po výběru správné virtuální image, zadejte následující hodnoty:
  * Na **Základy** okno, zadejte **název** u virtuálních počítačů mezi 1 až 15 znaků. (Tento příklad používá `DemoVm009`.)
  * Zadejte **uživatelské jméno** a silné **heslo**, které se používají k vytvoření místního účtu ve virtuálním počítači.  (Tady `adminUser` se používá.)  Heslo musí mít 8 až 123 znaků a musí splňovat tři ze čtyř bezpečnostních požadavků: jedno malé písmeno, jedno velké písmeno, jedna číslice a jeden speciální znak. Další informace najdete v tématu [požadavcích na uživatelské jméno a heslo](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
  * Vyberte skupinu prostředků, kterou jste vytvořili (zde `DemoResourceGroup`).
  * Vyberte adresu Datacentra Azure **umístění** (zde `West US`).
  * Klikněte na tlačítko **OK** uložte tyto hodnoty. 

6.  Vyberte velikost virtuálního počítače k nasazení můžete použít následující doporučení:
  * Pokud se chystáte vyvíjet virtuální pevný disk v místním, velikost není důležitá. Zvažte možnost použít jeden z menších virtuálních počítačů.
  * Pokud se chystáte vyvíjet image v Azure, zvažte možnost použít pro zvolenou image jednu z doporučených velikostí virtuálního počítače.
  * Informace o cenách najdete **doporučené cenové úrovně** selektoru zobrazeném na portálu. Zobrazí tři doporučené velikosti poskytnuté vydavatelem. (Tady je vydavatelem Microsoft.)

    ![Okno velikost nového virtuálního počítače](./media/publishvm_015.png)

7. V **nastavení** okno, nastavte **použití spravovaného disku** umožňuje **ne**.  To umožňuje spravovat ručně nový virtuální pevný disk. ( **Nastavení** okna také umožňuje změnit jiné změny úložiště a síťové možnosti, například výběrem **úrovně Premium (SSD)** v **typ disku**.)  Klikněte na tlačítko **OK** pokračujte.

    ![Okno nastavení z nového virtuálního počítače](./media/publishvm_016.png)

8. Klikněte na **Souhrn** a zkontrolujte zvolené volby. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, klikněte na **OK**.

    ![Okno s přehledem z nového virtuálního počítače](./media/publishvm_017.png)

Azure zahájí vytváření virtuálního počítače, které jste zadali.  Průběh můžete sledovat kliknutím na **virtuálních počítačů** karty na levé straně.  Po jeho vytvoření, stav se změní na **systémem**.  V tomto okamžiku můžete [připojení k virtuálnímu počítači](./cpp-connect-vm.md).


## <a name="next-steps"></a>Další postup

Pokud jste narazili na problémy s dokončením vytváření nových založené na Azure virtuální pevný disk, přečtěte si téma [běžné problémy při vytváření virtuálního pevného disku](./cpp-common-vhd-creation-issues.md).  V opačném případě dále musí [připojit k virtuálním počítačům](./cpp-connect-vm.md) jste vytvořili v Azure. 
