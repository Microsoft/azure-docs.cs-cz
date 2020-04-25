---
title: Vytvoření virtuálního pevného disku kompatibilního s Azure pro Azure Marketplace
description: Vysvětluje, jak vytvořit VHD pro nabídku virtuálního počítače v Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 642c6964aaad8d6e8750fca67efb11eb3feaf19d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147016"
---
# <a name="create-an-azure-compatible-vhd"></a>Vytvoření virtuálního pevného disku kompatibilního s Azure

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesunovat správu nabídek virtuálních počítačů Azure do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Podle pokynů v tématu [Vytvoření technických prostředků virtuálních počítačů Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) spravujte vaše migrované nabídky.

Tento článek podrobně popisuje kroky potřebné k vytvoření virtuálního pevného disku (VHD) pro nabídku virtuálního počítače v Azure Marketplace.  Zahrnuje taky osvědčené postupy pro různé aspekty, jako je například použití protokol RDP (Remote Desktop Protocol) (RDP), výběr velikosti virtuálního počítače, instalace nejnovějších aktualizací Windows a generalizace image VHD.  Následující oddíly se zaměřují hlavně na virtuální pevné disky založené na systému Windows. Další informace o vytváření VHD na discích se systémem Linux najdete v tématu [Linux v distribucích, které jsou schváleny v Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Důrazně doporučujeme postupovat podle pokynů v tomto tématu a použít Azure k vytvoření virtuálního počítače, který obsahuje předem nakonfigurovaný a schválený operační systém.  Pokud to není kompatibilní s vaším řešením, je možné vytvořit a nakonfigurovat místní virtuální počítač pomocí schváleného operačního systému.  Pak ji můžete nakonfigurovat a připravit pro nahrání, jak je popsáno v tématu [Příprava virtuálního pevného disku (VHD) Windows nebo VHDX pro nahrání do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Vybrat schválený základ
Virtuální pevný disk operačního systému pro vaši image virtuálního počítače musí být založený na základní imagi schválené pro Azure, která obsahuje Windows Server nebo SQL Server.
Začněte tím, že vytvoříte virtuální počítač z jedné z následujících imagí, která je umístěná na portál Microsoft Azure:

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-    [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Pokud používáte aktuální Azure Portal nebo PowerShell, schvalují se image Windows serveru vydané 8. září 2014 a novějšími.

Alternativně nabízí Azure rozsah schválených distribucí systému Linux.  Aktuální seznam najdete v tématu [Linux v distribucích, které jsou schváleny v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Vytvoření virtuálního počítače v Azure Portal 

V Microsoft [Azure Portal](https://ms.portal.azure.com/)vytvořte základní Image pomocí následujících kroků.

1. Přihlaste se k portálu pomocí účet Microsoft pro předplatné Azure, které chcete publikovat v nabídce virtuálních počítačů.
2. Vytvořte novou skupinu prostředků a zadejte **název skupiny prostředků**, **předplatné**a **umístění skupiny prostředků**.  Další pokyny najdete v tématu [Správa skupin prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Kliknutím na **virtuální počítače** na levém panelu nabídek zobrazíte stránku s podrobnostmi o virtuálních počítačích. 
4. Na této nové stránce klikněte na **+ Přidat** , aby se zobrazilo okno **COMPUTE** .  Pokud na úvodní obrazovce nevidíte typ virtuálního počítače, můžete vyhledat název svého základního virtuálního počítače, například:

    ![Okno COMPUTE nového virtuálního počítače](./media/publishvm_014.png)

5. Po výběru vhodné virtuální image zadejte následující hodnoty:
   * V okně **základy** zadejte **název** virtuálního počítače, mezi 1-15 alfanumerickými znaky. (Tento příklad používá `DemoVm009`.)
   * Zadejte **uživatelské jméno** a silné **heslo**, které se použije k vytvoření místního účtu na virtuálním počítači.  (Používá `adminUser` se.)  Heslo musí mít 8-123 znaků a musí splňovat tři ze čtyř následujících požadavků na složitost: jedno malé písmeno, jedno velké písmeno, jedna číslice a jeden speciální znak. Další informace najdete v tématu [požadavky na uživatelské jméno a heslo](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Vyberte skupinu prostředků, kterou jste vytvořili ( `DemoResourceGroup`tady).
   * Vyberte **umístění** datacentra Azure (tady `West US`).
   * Kliknutím na tlačítko **OK** uložte tyto hodnoty. 

6. Vyberte velikost virtuálního počítače, který chcete nasadit, a to pomocí následujících doporučení:
   * Pokud se chystáte vyvíjet virtuální pevný disk v místním prostředí, nezáleží na velikosti. Zvažte použití jednoho z menších virtuálních počítačů.
   * Pokud plánujete vývoj image v Azure, zvažte použití jedné z doporučených velikostí virtuálních počítačů pro vybranou bitovou kopii.
   * Informace o cenách najdete v selektoru **Doporučené cenové úrovně** , které se zobrazí na portálu. Zobrazí se tři Doporučené velikosti poskytnuté vydavatelem. (Tady je Vydavatel Microsoft.)

   ![Okno velikost nového virtuálního počítače](./media/publishvm_015.png)

7. V okně **Nastavení** nastavte možnost **použít spravovaný disk** na **ne**.  To vám umožní ručně spravovat nový virtuální pevný disk. (Okno **Nastavení** také umožňuje změnit další změny možností úložiště a sítě, například výběr úrovně **Premium (SSD)** v **typu disku**.)  Pokračujte kliknutím na tlačítko **OK** .

    ![Okno nastavení nového virtuálního počítače](./media/publishvm_016.png)

8. Klikněte na **Souhrn** a zkontrolujte zvolené volby. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, klikněte na **OK**.

    ![Okno souhrnu nového virtuálního počítače](./media/publishvm_017.png)

Azure zahajuje zřizování virtuálního počítače, který jste zadali.  Průběh můžete sledovat kliknutím na kartu **Virtual Machines** vlevo.  Po vytvoření se stav změní na **spuštěno**.  V tomto okamžiku se můžete [připojit k virtuálnímu počítači](./cpp-connect-vm.md).


## <a name="next-steps"></a>Další kroky

Pokud jste narazili na potíže s vytvářením nového virtuálního pevného disku založeného na Azure, přečtěte si téma [běžné problémy při vytváření VHD](./cpp-common-vhd-creation-issues.md).  V opačném případě je nutné [se připojit k virtuálním](./cpp-connect-vm.md) počítačům, které jste vytvořili v Azure. 
