---
title: Přidat shluků uzly do clusteru HPC Pack | Microsoft Docs
description: Zjistěte, jak rozbalte cluster služby HPC Pack v Azure na vyžádání přidáním instancí role pracovního procesu spuštěných v rámci cloudové služby
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
ms.openlocfilehash: eee9183321f21676271c8a9c7e023c80c4daf554
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915102"
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Přidat na vyžádání "shluků" uzly do clusteru HPC Pack v Azure
Pokud jste nastavili [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) clusteru v Azure, můžete způsob, jak rychle změnit velikost clusteru kapacitu nahoru nebo dolů, bez zachování sadu předkonfigurované výpočetním uzlu virtuálních počítačů. Tento článek ukazuje, jak přidat uzly na vyžádání "shluků" (pracovní role instancí spuštěných v rámci cloudové služby) jako výpočetní prostředky k hlavnímu uzlu v Azure. 

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![Uzly shluku][burst]

Postup v tomto článku vám rychle přidání uzlů Azure do cloudu HPC Pack hlavního uzlu virtuálního počítače pro testování konceptu nasazení nebo testovací pomůže. Hlavní kroky jsou stejné jako kroky k "rozšíření do služby Azure" Přidat cloudové výpočetní kapacitu ke clusteru HPC Pack místně. Podívejte se kurz [nastavení hybridního výpočetního clusteru pomocí sady Microsoft HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Podrobné pokyny pro nasazení v produkčním prostředí, najdete v části [rozšíření do služby Azure pomocí sady Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Požadavky
* **Nasadit virtuální počítač Azure hlavního uzlu HPC Pack** – můžete použít samostatné hlavního uzlu virtuálního počítače nebo ten, který je součástí clusteru s podporou větší. Vytvoření samostatné hlavního uzlu naleznete v tématu [nasazení HPC Pack hlavní uzel virtuální počítač Azure](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Automatizované možnosti nasazení clusteru HPC Pack najdete v tématu [možnosti k vytváření a správě Windows HPC cluster v Azure pomocí sady Microsoft HPC Pack](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Pokud použijete [skript nasazení HPC Pack IaaS](hpcpack-cluster-powershell-script.md) k vytvoření clusteru v Azure, můžete zahrnout uzlů Azure shluků automatického nasazení. Podívejte se na příklady v tomto článku.
  > 
  > 
* **Předplatné Azure** – k přidání uzlů Azure, můžete do stejného předplatného, které jsou používány k nasazení hlavního uzlu virtuálního počítače, nebo jiné předplatné (nebo odběry).
* **Kvóta jader** -může být potřeba zvýšit kvótu jádra, obzvláště pokud se rozhodnete nasadit několik uzlů Azure s vícejádrovými velikostí. Pokud chcete zvýšit kvótu, [otevřete žádosti o podporu online zákazníka](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) zdarma.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Krok 1: Vytvoření cloudové služby a účet úložiště Azure uzlů
Pomocí portálu Azure nebo ekvivalentní nástroje konfigurujte následující prostředky, které jsou nutné k nasazení Azure uzly:

* Nové služby cloudu Azure (klasický)
* Nový účet úložiště Azure (klasický)

> [!NOTE]
> Nemusíte znovu použít stávající cloudovou službu ve vašem předplatném. 
> 
> 

**Důležité informace**

* Nakonfigurujte samostatnou cloudovou službu ke každé šabloně Azure uzlu, který chcete vytvořit. Ale můžete použít stejný účet úložiště pro víc šablony uzlu.
* Doporučujeme, abyste ve stejné oblasti Azure najít cloudové služby a účet úložiště pro nasazení.

## <a name="step-2-configure-an-azure-management-certificate"></a>Krok 2: Konfigurace certifikát pro správu Azure
K přidání uzlů Azure jako výpočetní prostředky, musíte certifikát pro správu na hlavního uzlu a nahrávání, odpovídající certifikátů pro předplatné Azure použité pro nasazení.

V tomto scénáři můžete **výchozí certifikát pro správu Azure HPC** který HPC Pack nainstaluje a nakonfiguruje automaticky hlavního uzlu. Tento certifikát je užitečné pro testovací účely a testování konceptu nasazení. K použití tohoto certifikátu, nahrajte soubor C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer z hlavního uzlu virtuálního počítače k předplatnému. Odeslání certifikátu v [portál Azure](https://portal.azure.com):

1. Klikněte na tlačítko **odběry** > *your_subscription_name*.

2. Klikněte na tlačítko **certifikáty pro správu** > **nahrát**.

Další možnosti konfigurace certifikátu pro správu najdete v tématu [scénáře konfigurace certifikátu pro správu Azure pro nasazení Azure Burst](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Krok 3: Nasaďte uzlů Azure do clusteru
Postup pro přidání a spustit Azure uzly v tomto scénáři jsou obvykle stejné jako kroky se hlavnímu uzlu místní. Další informace najdete v následujících částech [kroky k nasazení uzlů Azure pomocí sady Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Vytvořit šablonu Azure uzlu
* Přidání uzlů Azure do clusteru Windows HPC
* Spuštění uzlů (zřídit) Azure

Po přidání a spustit uzly, jsou připravené pro použití ke spuštění úloh clusteru.

Pokud narazíte na potíže při nasazování uzlů Azure, najdete v části [řešení nasazení z uzlů Azure pomocí sady Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Další postup
* Použití instance náročné velikost pro uzly shluků naleznete v tématu důležité informace v [vysokovýkonné výpočetní velikosti virtuálních počítačů](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pokud chcete automaticky zvětšit nebo zmenšit Azure výpočetních prostředků podle zatížení clusteru najdete v tématu [automaticky zvětšovat a zmenšovat Azure výpočetních prostředků v clusteru služby HPC Pack](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
