---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d4075f644ed1ce42479e35544f0e1442488f78eb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29958907"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Spouštění v levém horním, klikněte na tlačítko **vytvořit prostředek** > **výpočetní** > **Windows Server 2016 Datacenter**.

    ![Přechod k imagím virtuálních počítačů Azure na portálu](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. Ve Windows Serveru 2016 Datacenter vyberte model nasazení Classic. Klikněte na Vytvořit.

    ![Snímek obrazovky zobrazující image virtuálního počítače Azure dostupné na Portálu](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. Okno Základy

Okno Základy požaduje informace pro správu virtuálního počítače.

1. Zadejte **název** pro virtuální počítač. Virtuální počítač v tomto příkladu má název _HeroVM_. Název musí mít 1 až 15 znaků a nesmí obsahovat speciální znaky.

2. Zadejte **uživatelské jméno** a silné **heslo**, které se použijí k vytvoření místního účtu ve virtuálním počítači. Místní účet slouží k přihlášení k virtuálnímu počítači a jeho správě. Uživatelské jméno v tomto příkladu je _azureuser_.

 Heslo musí mít 8 až 123 znaků a musí splňovat tři ze čtyř bezpečnostních požadavků: jedno malé písmeno, jedno velké písmeno, jedna číslice a jeden speciální znak. Přečtěte si další informace o [požadavcích na uživatelské jméno a heslo](../articles/virtual-machines/windows/faq.md).

3. **Předplatné** je volitelné. Jedním z běžných nastavení jsou průběžné platby.

4. Vyberte existující **skupinu prostředků** nebo zadejte název nové skupiny prostředků. Skupina prostředků v tomto příkladu má název _HeroVMRG_.

5. Vyberte **umístění** datového centra Azure, kde chcete virtuální počítač spustit. V tomto příkladu je použité umístění **Východní USA**.

6. Až to budete mít, přejděte kliknutím na **OK** k dalšímu oknu.

    ![Snímek obrazovky, který zobrazuje nastavení v okně Základy pro konfiguraci virtuálního počítače Azure](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. Okno Velikost

Okno Velikost určuje konfigurační detaily virtuálního počítače a zobrazuje různé možnosti, jako je operační systém, počet procesorů, typ diskového úložiště a odhadované měsíční náklady na využití.  

Zvolte velikost virtuálního počítače a poté pokračujte kliknutím na **Vybrat**. V tomto příkladu je použitý virtuální počítač s velikostí _DS1_\__V2 Standard_.

  ![Snímek obrazovky okna Velikost zobrazující velikosti virtuálního počítače Azure, ze kterých můžete vybírat](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. Okno Nastavení

Okno Nastavení vyžaduje možnosti úložiště a sítě. Můžete přijmout výchozí nastavení. Azure v případě potřeby vytvoří odpovídající položky.

Pokud jste vybrali velikost virtuálního počítače, která to podporuje, a jako typ disku vyberete Premium (SSD), můžete vyzkoušet službu Azure Storage úrovně Premium.

Jakmile budete se změnami hotovi, klikněte na **OK**.

## <a name="4-summary-blade"></a>4. Okno Souhrn

Okno Souhrn zobrazuje nastavení zadaná v předchozích oknech. Až budete připravení vytvořit image, klikněte na **OK**.

 ![Sestava okna Souhrn s konkrétními nastaveními virtuálního počítače](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

Po vytvoření se nový virtuální počítač zobrazí na portálu v části **Všechny prostředky** a na řídicím panelu se zobrazí dlaždice tohoto virtuálního počítače. Současně se vytvoří a zobrazí odpovídající účet služby a cloudová služba. Virtuální počítač i cloudová služba se automaticky spustí a jejich stav bude uvedený jako **spuštěný**.

 ![Konfigurace agenta virtuálního počítače a koncových bodů virtuálního počítače](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
