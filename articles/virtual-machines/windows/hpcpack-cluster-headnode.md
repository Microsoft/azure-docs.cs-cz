---
title: Vytvořit virtuální počítač Azure hlavnímu uzlu HPC Pack | Microsoft Docs
description: Další informace o použití portálu Azure a modelu nasazení Resource Manager k vytvoření hlavního uzlu Microsoft HPC Pack 2012 R2 ve virtuálním počítači Azure.
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
ms.openlocfilehash: acd4cd44dd35a5b1755d9456f683076567d62165
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915255"
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Vytvoření hlavního uzlu clusteru HPC Pack ve virtuálním počítači Azure s imagí Marketplace
Použití [bitovou kopii virtuálního počítače Microsoft HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) z Azure Marketplace a portálu Azure k vytvoření hlavního uzlu clusteru prostředí HPC. Tuto bitovou kopii virtuálního počítače HPC Pack je založená na Windows Server 2012 R2 Datacenter s HPC Pack 2012 R2 Update 3 předinstalován. Pomocí tohoto hlavního uzlu pro testování konceptu nasazení sady HPC Pack v Azure. Výpočetní uzly potom můžete přidat do clusteru ke spuštění úlohy v prostředí HPC.

> [!TIP]
> Nasazování dokončení clusteru HPC Pack 2012 R2 v Azure, který zahrnuje hlavního uzlu a výpočetní uzly, doporučujeme použít automatizované metodu. Mezi možnosti patří [skript nasazení HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) a šablony správce prostředků, jako [HPC Pack clusteru pro úlohy Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Šablony Resource Manageru jsou také k dispozici pro [clusterů Microsoft HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Aspekty plánování
Jak je znázorněno na následujícím obrázku, nasadíte hlavního uzlu HPC Pack v doméně služby Active Directory v virtuální sítě Azure.

![Hlavního uzlu HPC Pack][headnode]

* **Doména služby Active Directory**: HPC Pack 2012 R2 hlavního uzlu musí být připojené k doméně služby Active Directory v Azure, než začnete prostředí HPC služeb ve virtuálním počítači. Jak je znázorněno v tomto článku, testování konceptu nasazení, můžete zvýšit úroveň virtuálního počítače, vytvořte pro hlavní uzel jako řadič domény před spuštěním služby HPC. Další možností je nasadit řadič samostatné domény a doménové struktury v Azure, ke kterému jste připojení k hlavního uzlu virtuálního počítače.

* **Model nasazení**: pro většinu nová nasazení, Microsoft doporučuje používat model nasazení Resource Manager. Tento článek předpokládá, že používáte tento model nasazení.

* **Virtuální síť Azure**: při nasazení hlavního uzlu pomocí modelu nasazení Resource Manager, kterou zadáte, nebo vytvořte virtuální síť Azure. Virtuální sítě použijte, pokud potřebujete připojení k hlavnímu uzlu do existující domény služby Active Directory. Je také nutné ho později na výpočetním uzlu virtuální počítače přidat do clusteru.

## <a name="steps-to-create-the-head-node"></a>Postup vytvoření hlavního uzlu
Následují základní kroky pro vytvoření virtuálního počítače Azure pro hlavního uzlu HPC Pack pomocí modelu nasazení Resource Manager pomocí portálu Azure. 

1. Pokud chcete vytvořit novou doménovou strukturu služby Active Directory v Azure s řadičem domény samostatné virtuální počítače, jednou z možností je používat [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Pro jednoduché ověření konceptu nasazení je v pořádku vynechat tento krok a konfigurace hlavního uzlu virtuální počítač jako řadič domény. Tato možnost je popsán dále v tomto článku.
2. Na [HPC Pack 2012 R2 na Windows Server 2012 R2 stránce](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) v Azure Marketplace, klikněte na tlačítko **vytvořit virtuální počítač**. 
3. Na portálu na **HPC Pack 2012 R2 na Windows Server 2012 R2** vyberte **Resource Manager** modelu nasazení a pak klikněte na tlačítko **vytvořit**.
   
    ![Obrázek HPC Pack][marketplace]
4. Použití portálu ke konfiguraci nastavení a vytvoření virtuálního počítače. Pokud jste Azure ještě nepoužívali, postupujte podle kurzu [vytvoření virtuálního počítače s Windows v portálu Azure](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pro ověření konceptu nasazení můžete obvykle přijmout výchozí nastavení nebo doporučené nastavení.
   
   > [!NOTE]
   > Pokud se chcete připojit k hlavnímu uzlu do existující domény služby Active Directory v Azure, ujistěte se, že při vytváření virtuálního počítače zadat virtuální sítě pro tuto doménu.
   > 
   > 
5. Po můžete vytvořit virtuální počítač a je virtuální počítač spuštěný, [připojit k virtuálnímu počítači](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pomocí vzdálené plochy. 
6. Připojení virtuálního počítače do domény doménové struktury služby Active Directory volbou jedné z následujících možností:
   
   * Pokud jste vytvořili virtuální počítač do virtuální sítě Azure s existující doménové struktuře domény, připojte virtuální počítač do doménové struktury pomocí standardních nástrojů Správce serveru nebo prostředí Windows PowerShell. Potom restartujte.
   * Pokud jste vytvořili virtuální počítač v nové virtuální sítě (bez existující doménové struktuře domény), pak povýšíte virtuální počítač jako řadič domény. Použijte standardní postup instalace a konfigurace role Active Directory Domain Services z hlavního uzlu. Podrobné pokyny najdete v tématu [nainstalovat nové Windows Server 2012 Active Directory doménové struktury](https://technet.microsoft.com/library/jj574166.aspx).
7. Když virtuální počítač běží a je připojen k doménové struktuře služby Active Directory, spusťte službu HPC Pack takto:
   
    a. Připojení k hlavnímu uzlu virtuálního počítače pomocí účtu domény, který je členem místní skupiny Administrators. Například použijte účet správce, kterou vytvoříte, když jste vytvořili z hlavního uzlu virtuálního počítače.
   
    b. Pro výchozí konfigurace hlavního uzlu spusťte prostředí Windows PowerShell jako správce a zadejte následující příkaz:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Ho může trvat několik minut pro služby HPC Pack spustit.
   
    Pro další hlavního uzlu možnosti konfigurace, zadejte `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Další postup
* Teď můžete pracovat s hlavního uzlu clusteru HPC Pack. Například spusťte Správce clusteru HPC a dokončete [nasazení na seznam úkolů](https://technet.microsoft.com/library/jj884141.aspx).
* Pokud chcete zvýšit clusteru výpočetní kapacity na vyžádání, přidejte [Azure burst uzly](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) v cloudové službě. 
* Zkuste spustit test zatížení v clusteru. Příklad najdete v tématu HPC Pack [Příručka Začínáme](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
