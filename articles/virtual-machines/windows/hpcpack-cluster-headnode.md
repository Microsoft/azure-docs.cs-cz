---
title: Vytvořit hlavní uzel HPC Pack ve Virtuálním počítači Azure | Dokumentace Microsoftu
description: Zjistěte, jak pomocí webu Azure portal a modelu nasazení Resource Manageru k vytvoření hlavního uzlu Microsoft HPC Pack 2012 R2 ve Virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 70c1d95f704315ee6a481367188e2bb620916702
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428952"
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Vytvoření hlavního uzlu clusteru HPC Pack ve virtuálním počítači Azure s imagí Marketplace
Použití [image virtuálního počítače Microsoft HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) z Azure Marketplace a na webu Azure portal k vytvoření hlavního uzlu clusteru prostředí HPC. Tato image virtuálního počítače HPC Pack je založená na Windows Server 2012 R2 Datacenter s předinstalovanými systém HPC Pack 2012 R2 Update 3. Pomocí této hlavní uzel pro testování konceptu nasazení sady HPC Pack v Azure. Potom přidáte výpočetní uzly do clusteru pro spouštění úloh HPC.

> [!TIP]
> K nasazení celého clusteru prostředí HPC Pack 2012 R2 v Azure, která zahrnuje hlavní uzel a výpočetních uzlů, doporučujeme používat automatizované metodu. Mezi možnosti patří [skriptem nasazení IaaS sady HPC Pack](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) a šablon Resource Manageru, jako [clusteru HPC Pack pro úlohy Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Šablony Resource Manageru jsou k dispozici pro [Microsoft HPC Pack 2016 clustery](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Aspekty plánování
Jak je znázorněno na následujícím obrázku, nasadíte hlavní uzel HPC Pack v doméně služby Active Directory ve službě Azure virtual network.

![Hlavní uzel HPC Pack][headnode]

* **Doména služby Active Directory**: prostředí HPC Pack 2012 R2 hlavního uzlu musí být připojené k doméně služby Active Directory v Azure před zahájením HPC v prostředí služby na virtuálním počítači. Jak je znázorněno v tomto článku se pro testování konceptu nasazení, můžete zvýšit úroveň virtuálního počítače, vytvořte pro hlavní uzel jako řadič domény před spuštěním služby HPC. Další možností je nasadit řadič samostatné domény a doménové struktury v Azure, ke kterému připojíte k hlavnímu uzlu virtuálního počítače.

* **Model nasazení**: pro většinu nových nasazení, Microsoft doporučuje používat model nasazení Resource Manager. Tento článek předpokládá, že používáte tento model nasazení.

* **Virtuální síť Azure**: při použití modelu nasazení Resource Manager k nasazení hlavního uzlu, určete nebo vytvořte virtuální síť Azure. Virtuální sítě použijte, pokud je potřeba připojit k hlavnímu uzlu do existující domény služby Active Directory. Můžete také později potřebovat k přidání výpočetních uzlů virtuálních počítačů do clusteru.

## <a name="steps-to-create-the-head-node"></a>Postup vytvoření hlavního uzlu
Toto jsou základní kroky pro vytvoření virtuálního počítače Azure pro hlavní uzel HPC Pack pomocí modelu nasazení Resource Manager pomocí webu Azure portal. 

1. Pokud chcete vytvořit novou doménovou strukturu služby Active Directory v Azure s řadičem domény samostatné virtuální počítače, jednou z možností je použít [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Pro jednoduché testování konceptu nasazení je v pořádku, tento krok vynechat a konfigurace Virtuálního počítače k hlavnímu uzlu jako řadič domény. Tato možnost je popsána dále v tomto článku.
1. Na [HPC Pack 2012 R2 na Windows Server 2012 R2 stránce](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) na webu Azure Marketplace, klikněte na tlačítko **vytvořit virtuální počítač**. 
1. Na portálu na **HPC Pack 2012 R2 na Windows serveru 2012 R2** stránky, vyberte **Resource Manageru** modelu nasazení a pak klikněte na tlačítko **vytvořit**.
   
    ![Bitové kopie sady HPC Pack][marketplace]
1. Použití portálu ke konfiguraci nastavení a vytvoření virtuálního počítače. Pokud se službou Azure teprve začínáte, postupujte podle kurzu [vytvořit virtuální počítač s Windows na webu Azure Portal](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pro testování konceptu nasazení můžete obvykle přijmout výchozí nastavení nebo doporučené nastavení.
   
   > [!NOTE]
   > Pokud se chcete připojit k hlavnímu uzlu do existující domény služby Active Directory v Azure, ujistěte se, že při vytváření virtuálního počítače zadejte virtuální síť pro tuto doménu.
   > 
   > 
1. Po vytvoření virtuálního počítače a virtuální počítač spuštěný, [připojit k virtuálnímu počítači](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pomocí vzdálené plochy. 
1. Virtuální počítač připojte k doménové struktuře služby Active Directory domény výběrem jedné z následujících možností:
   
   * Pokud jste vytvořili virtuální počítač ve virtuální síti s existující doménové struktuře domény, Azure, připojte ho k doménové struktuře použít standardní nástroje Správce serveru nebo Windows Powershellu. Restartujte.
   * Když vytvoříte virtuální počítač v nové virtuální sítě (bez stávající doménové struktuře domény), pak ji převeďte virtuální počítač jako řadič domény. Použijte standardní postup instalace a konfigurace Active Directory Domain Services role hlavního uzlu. Podrobné pokyny najdete v článku [nainstalovat novou systému Windows Server 2012 Active Directory doménovou strukturu](https://technet.microsoft.com/library/jj574166.aspx).
1. Až se virtuální počítač běží a je připojen k doménové struktuře služby Active Directory, spusťte služby HPC Pack následujícím způsobem:
   
    a. Připojení k hlavnímu uzlu virtuálního počítače pomocí účtu domény, který je členem místní skupiny Administrators. Například použijte účet správce, který nastavíte při vytváření virtuálního počítače k hlavnímu uzlu.
   
    b. Pro výchozí konfigurace hlavního uzlu spusťte prostředí Windows PowerShell jako správce a zadejte následující příkaz:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Může trvat několik minut, než služby HPC Pack ke spuštění.
   
    Další hlavní uzel Možnosti konfigurace, zadejte `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Další postup
* Teď můžete pracovat s k hlavnímu uzlu clusteru HPC Pack. Například spusťte Správce clusterů HPC a dokončete [seznam úkolů nasazení](https://technet.microsoft.com/library/jj884141.aspx).
* Pokud chcete zvýšit clusteru výpočetní kapacitě na vyžádání, přidejte [Azure burst uzly](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) v cloudové službě. 
* Spusťte test zatížení v clusteru. Příklad najdete v tématu sady HPC Pack [– Příručka Začínáme](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
