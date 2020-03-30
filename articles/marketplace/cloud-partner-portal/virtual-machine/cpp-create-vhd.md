---
title: Vytvoření virtuálního pevného disku kompatibilního s Azure pro Azure Marketplace
description: Vysvětluje, jak vytvořit virtuální pevný disk pro nabídku virtuálních strojů na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 2014a775edd4e24f5d302d863d0b69d83009b8a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277986"
---
# <a name="create-an-azure-compatible-vhd"></a>Vytvoření virtuálního pevného disku kompatibilního s Azure

Tento článek podrobně popisuje kroky potřebné k vytvoření virtuálního pevného disku (VHD) pro nabídku virtuálního počítače (VM) na Azure Marketplace.  Obsahuje také osvědčené postupy pro různé aspekty, jako je například použití protokolu RDP (Rdp) – protokol RDP), výběr velikosti virtuálního počítače, instalace nejnovějších aktualizací systému Windows a zobecnění bitové kopie virtuálního pevného disku.  Následující části se zaměřují především na virtuální počítače založené na oknech; Další informace o vytváření virtuálních disdrátů založených na Linuxu najdete v [tématu Linux o distribucích schválených Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Důrazně doporučujeme, abyste podle pokynů v tomto tématu pomocí Azure vytvořili virtuální počítač obsahující předem nakonfigurovaný, schválený operační systém.  Pokud to není kompatibilní s vaším řešením, pak je možné vytvořit a nakonfigurovat místní virtuální počítač pomocí schváleného operačního systému.  Potom můžete nakonfigurovat a připravit ji k nahrání, jak je popsáno v [článku Příprava virtuálního pevného disku s Windows nebo VHDX pro nahrání do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Výběr schválené základny
Virtuální pevný disk operačního systému pro bitovou kopii virtuálního počítače musí být založený na základní bitové kopii schválené Azure, která obsahuje Windows Server nebo SQL Server.
Chcete-li začít, vytvořte virtuální počítač z jedné z následujících bitových kopií umístěných na portálu Microsoft Azure:

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (enterprise, standard, web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (enterprise, standard, web)

> [!TIP]
> Pokud používáte aktuální portál Azure nebo PowerShell, image Windows Serveru publikované 8.

Alternativně Azure nabízí celou řadu schválených distribucí Linuxu.  Aktuální seznam najdete v [tématu Linux o distribucích schválených Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Vytvoření virtuálního počítače na webu Azure Portal 

Na [portálu](https://ms.portal.azure.com/)Microsoft Azure vytvořte základní bitovou kopii pomocí následujících kroků.

1. Přihlaste se k portálu pomocí účtu Microsoft pro předplatné Azure, které chcete publikovat nabídku virtuálních aplikací.
2. Vytvořte novou skupinu prostředků a zadejte **název skupiny prostředků**, **Odběr**a **umístění skupiny prostředků**.  Další pokyny naleznete v [tématu Správa skupin prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Kliknutím na **Virtuální počítače** v levém panelu menu zobrazíte stránku Podrobnosti o virtuálních počítačích. 
4. Na této nové stránce klikněte na **+Přidat** a zobrazte okno **Compute.**  Pokud na úvodní obrazovce nevidíte typ virtuálního počítače, můžete vyhledat název základního virtuálního počítače, například:

    ![Výpočetní okno nového virtuálního počítače](./media/publishvm_014.png)

5. Po výběru správného virtuálního obrázku zadejte následující hodnoty:
   * V okně **Základy** zadejte **název** virtuálního počítače mezi 1 až 15 alfanumerickými znaky. (Tento příklad `DemoVm009`používá .)
   * Zadejte **uživatelské jméno** a silné **heslo**, které se používají k vytvoření místního účtu na virtuálním počítači.  (Zde `adminUser` se používá.)  Heslo musí mít dlouhou 8 až 123 znaků a musí splňovat tři ze čtyř následujících požadavků na složitost: jeden znak malá písmena, jeden znak velkých písmen, jedno číslo a jeden speciální znak. Další informace naleznete v [tématu Požadavky na uživatelské jméno a heslo](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Vyberte skupinu prostředků, `DemoResourceGroup`kterou jste vytvořili (zde).
   * Vyberte **umístění** datového `West US`centra Azure (zde).
   * Chcete-li tyto hodnoty uložit, klepněte na tlačítko **OK.** 

6. Vyberte velikost virtuálního počítače, který chcete nasadit, pomocí následujících doporučení:
   * Pokud máte v plánu vyvíjet virtuální pevný disk místně, na velikosti nezáleží. Zvažte použití jednoho z menších virtuálních zařízení.
   * Pokud máte v plánu rozvíjet image v Azure, zvažte použití jedné z doporučených velikostí virtuálních počítačů pro vybranou bitovou kopii.
   * Informace o cenách najdete v části **Doporučené selektory cenových úrovní** zobrazený na portálu. Zobrazí se tři doporučené velikosti poskytované vydavatelem. (Zde je vydavatelem společnost Microsoft.)

   ![Velikost čepele nového virtuálního počítače](./media/publishvm_015.png)

7. V okně **Nastavení** nastavte možnost **Použít spravovaný disk** na **ne**.  To vám umožní ručně spravovat nový virtuální pevný disk. (Okno **Nastavení** také umožňuje změnit další změny možností úložiště a sítě, například **výběrem možnosti Premium (SSD)** v **typu disk**.)  Chcete-li pokračovat, pokračujte klepnutím na **tlačítko OK.**

    ![Okno nastavení nového virtuálního počítače](./media/publishvm_016.png)

8. Klikněte na **Souhrn** a zkontrolujte zvolené volby. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, klikněte na **OK**.

    ![Souhrnný list nového virtuálního vztahu](./media/publishvm_017.png)

Azure začne zřizování virtuálního počítače, který jste zadali.  Jeho průběh můžete sledovat kliknutím na kartu **Virtuální počítače** vlevo.  Po vytvoření se stav změní na **Spuštěno**.  V tomto okamžiku se můžete [připojit k virtuálnímu počítači](./cpp-connect-vm.md).


## <a name="next-steps"></a>Další kroky

Pokud jste zjistili potíže s vytvořením nového virtuálního pevného disku založeného na Azure, přečtěte [si téma Běžné problémy při vytváření virtuálního pevného disku](./cpp-common-vhd-creation-issues.md).  V opačném případě se musíte [připojit k virtuálním počítačům, které](./cpp-connect-vm.md) jste vytvořili v Azure. 
