---
title: Přidání shlukových uzlů do clusteru HPC Pack | Dokumentace Microsoftu
description: Zjistěte, jak rozšířit přidáním instancí rolí pracovního procesu v cloudové službě s clusteru HPC Pack v Azure na vyžádání
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 24b79a8a-24ad-4002-ae76-75abc9b28c83
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: 7d42c026975a18c7574e4bc64ec28ab3ed0082bc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248448"
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Přidat on-demand "rozšíření" uzly do clusteru HPC Pack v Azure
Pokud jste nastavili [sady Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) clusteru v Azure, můžete potřebovat způsob, jak se dají rychle škálovat kapacitu clusteru směrem nahoru nebo dolů, bez zachování sadu předkonfigurovaných výpočetního uzlu virtuálních počítačů. Tento článek ukazuje, jak přidat uzly na vyžádání "rozšíření" (spuštěných v cloudové službě instancí role pracovního procesu.) jako výpočetní prostředky k hlavnímu uzlu v Azure. 

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![Shlukových uzlů][burst]

Kroky v tomto článku vám pomůžou přidat uzly Azure rychle založené na cloudu sady HPC Pack hlavního uzlu virtuálního počítače pro testování a nasazení testování konceptu. Postup vysoké úrovně jsou stejné jako postup "Přejít na Azure" Přidat cloudové výpočetní kapacita, aby v místním clusteru HPC Pack. Podívejte se kurz [nastavení hybridního výpočetního clusteru pomocí sady Microsoft HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Podrobné pokyny pro nasazení v produkčním prostředí, najdete v článku [Burst to Azure se sadou Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Požadavky
* **Hlavní uzel HPC Pack nasazený ve Virtuálním počítači Azure** – můžete použít samostatné hlavního uzlu virtuálního počítače nebo ten, který je součástí větší cluster. Vytvoření samostatného hlavního uzlu, najdete v tématu [nasadit hlavní uzel HPC Pack ve Virtuálním počítači Azure](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Automatické možnosti nasazení clusteru HPC Pack najdete v tématu [možnosti, jak vytvářet a spravovat Windows HPC clusteru v Azure pomocí sady Microsoft HPC Pack](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Pokud používáte [skriptem nasazení IaaS sady HPC Pack](hpcpack-cluster-powershell-script.md) k vytvoření clusteru v Azure, můžete zahrnout do automatického nasazení Azure shlukových uzlů. Podívejte se na příklady v tomto článku.
  > 
  > 
* **Předplatné Azure** – k přidání uzlů Azure, můžete použít stejné předplatné použité k nasazení hlavního uzlu virtuálního počítače, nebo jiné předplatné (nebo předplatná).
* **Kvóta pro jádra** – možná muset zvýšit kvótu jader, zejména v případě, že budete chtít nasadit několik uzlů Azure s vícejádrovými velikosti. Na zvýšení této kvóty, [otevřete žádost o online zákaznickou podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) bez poplatků.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Krok 1: Vytvoření cloudové služby a účet úložiště Azure uzlů
Konfigurovat následující prostředky, které jsou nutné k nasazení svých uzlech Azure pomocí webu Azure portal nebo ekvivalentní nástroje:

* Nové Azure cloudové služby (klasické)
* Nový účet úložiště Azure (klasické)

> [!NOTE]
> Nepoužívejte soubory existující cloudové služby v rámci vašeho předplatného. 
> 
> 

**Důležité informace**

* Nakonfigurujte samostatnou cloudovou službu pro každý uzel Azure šablony, který chcete vytvořit. Můžete však použít stejný účet úložiště pro více šablon uzel.
* Doporučujeme ve stejné oblasti Azure vyhledejte cloudovou službu a účet úložiště pro nasazení.

## <a name="step-2-configure-an-azure-management-certificate"></a>Krok 2: Konfigurace certifikát pro správu Azure
K přidání uzlů Azure jako výpočetní prostředky, musíte certifikát pro správu na hlavním uzlem a nahrávání, odpovídající certifikátu na předplatné Azure použité pro nasazení.

V tomto scénáři můžete použít **výchozí certifikát pro správu Azure HPC** , která sady HPC Pack nainstaluje a nakonfiguruje automaticky hlavního uzlu. Tento certifikát je užitečné pro testovací účely a nasazení testování konceptu. K použití tohoto certifikátu, nahrajte soubor C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer z hlavního uzlu virtuálního počítače k předplatnému. Nahrát certifikát [webu Azure portal](https://portal.azure.com):

1. Klikněte na tlačítko **předplatná** > *your_subscription_name*.

2. Klikněte na tlačítko **certifikáty pro správu** > **nahrát**.

Další možnosti konfigurace certifikátu pro správu najdete v tématu [scénáře ke konfiguraci certifikátu pro správu Azure pro nasazení Azure Burst](https://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Krok 3: Nasazení Azure uzly do clusteru
Postup pro přidání a spuštění uzlů Azure v tomto scénáři jsou obvykle stejné jako kroky, hlavní uzel v místním. Další informace najdete v tématu v následujících částech [postup nasazení uzlů Azure pomocí sady Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Vytvoření šablony uzlu Azure
* Přidání uzlů Azure do clusteru Windows HPC
* Spustit uzly (zřizování) Azure

Po přidání a spuštění uzlů, jsou připravené k použití pro spouštění úloh clusteru.

Pokud narazíte na potíže při nasazení Azure uzly, přečtěte si téma [řešení potíží s nasazení z uzlů Azure pomocí sady Microsoft HPC Pack](https://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Další postup
* Velikosti náročné na výpočetní instance pro shlukových uzlů používat, naleznete v tématu na základě aspektů v [vysoce výkonné výpočetní velikosti virtuálních počítačů](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pokud chcete automaticky zvětšovat nebo zmenšovat výpočetní prostředky podle zatížení clusteru Azure, přečtěte si [automatické zvětšení a zmenšení výpočetní prostředky Azure v clusteru HPC Pack](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
