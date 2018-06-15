---
title: MATLAB clusterů na virtuálních počítačích | Microsoft Docs
description: Použijte virtuální počítače Microsoft Azure k vytvoření clusterů MATLAB distribuovaná výpočetní Server ke spuštění úlohy náročné paralelní MATLAB
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: jeconnoc
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 695833fb12c0c7a130e98fe9b3bdfa502672ab29
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914914"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Vytvoření MATLAB distribuovaný Server výpočetních clusterů na virtuálních počítačích Azure
Pomocí virtuální počítače Microsoft Azure můžete vytvořit jeden nebo více clusterů MATLAB distribuovaná výpočetní serveru spouštět paralelní MATLAB úlohy náročné na výkon. Instalaci softwaru MATLAB distribuovaný Server Computing na virtuálním počítači používat jako základní bitové kopie a použít šablonu Azure rychlý start nebo skript Azure PowerShell (k dispozici na [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) k nasazení a správě clusteru. Po nasazení připojte se ke clusteru ke spuštění úlohy.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>O MATLAB a MATLAB distribuované Computing serveru
[MATLAB](http://www.mathworks.com/products/matlab/) platformy je optimalizovaná pro řešení problémů technické a vědecké. Uživatelé MATLAB s rozsáhlé simulace a úloh zpracování dat můžete použít společnost MathWorks paralelní výpočty produktů pro urychlení jejich výpočetně náročných úloh využitím výpočetní clustery a mřížky služeb. [Paralelní výpočty nástrojů](http://www.mathworks.com/products/parallel-computing/) umožňuje uživatelům MATLAB paralelní aplikace a využít výhod více jádry grafickými procesory a výpočetní clustery. [MATLAB distribuovaný Server Computing](http://www.mathworks.com/products/distriben/) umožňuje uživatelům MATLAB využívat mnoho počítačů ve výpočetním clusteru.

Pomocí virtuální počítače Azure můžete vytvořit MATLAB distribuovaný Server výpočetních clusterů, které mají stejné mechanismy, které jsou k dispozici pro odeslání paralelní práce jako místní clustery, jako je například interaktivní úlohy, úlohy batch, nezávislé úlohy a komunikaci úlohy. Použití Azure ve spojení s platformou MATLAB má mnoho výhod oproti zřizování a pomocí tradiční místní hardware: velikostí rozsah virtuálního počítače, vytváření clusterů na vyžádání, platíte jenom za výpočetní prostředky, které používáte, a možnost otestovat modely ve velkém měřítku.  

## <a name="prerequisites"></a>Požadavky
* **Klientský počítač** -založené na Windows klientský počítač komunikovat s Azure a MATLAB distribuovaný Server výpočetní cluster po nasazení budete potřebovat.
* **Prostředí Azure PowerShell** -najdete v části [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) k její instalaci do klientského počítače.
* **Předplatné Azure** – Pokud nemáte předplatné, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/) si během několika minut. Pro větší clustery zvažte průběžnými platbami předplatné nebo jiné možnosti nákupu.
* **kvóta Vcpu** -možná budete muset zvýšit kvótu virtuální procesor chcete nasadit více než jeden cluster MATLAB distribuovaný Server Computing nebo velké clusteru. Pokud chcete zvýšit kvótu, [otevřete žádosti o podporu online zákazníka](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) zdarma.
* **MATLAB, paralelní výpočty sada nástrojů a MATLAB distribuovaný Server Computing licence** -skripty předpokládat, že [správce licencí hostované společnost MathWorks](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) se používá pro všechny licence.  
* **Software MATLAB distribuovaný Server Computing** -bude nainstalována na virtuální počítač, který se použije jako základní image virtuálního počítače pro cluster virtuálních počítačů.

## <a name="high-level-steps"></a>Kroky vysoké úrovně
Pokud chcete používat virtuální počítače Azure pro své clustery serverů distribuované Computing MATLAB, jsou požadovány následující postup vysoké úrovně. Podrobné pokyny naleznete v dokumentaci doprovázející aplikaci rychlý start šablonu a skripty na [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Vytvořte základní image virtuálního počítače**  

   * Stáhněte a nainstalujte software MATLAB distribuovaný Server Computing na tento virtuální počítač.

     > [!NOTE]
     > Tento proces může trvat několik hodin, ale budete muset provést po pro každou verzi MATLAB použijete.   
     >
     >
2. **Vytvořte jeden nebo více clusterů**  

   * Zadaný skript prostředí PowerShell použít nebo vytvořit cluster z základní image virtuálního počítače pomocí šablony rychlý start.   
   * Správa clusterů pomocí zadaný skript prostředí PowerShell, které umožňuje seznamu, pozastavit, obnovit a odstranění clusterů.

## <a name="cluster-configurations"></a>Konfigurace clusteru
V současné době skriptu pro vytváření clusteru a šablony umožňují vytvořit jeden Server distribuované Computing MATLAB topologie. Pokud chcete, vytvořte jeden nebo více dalších clusterů s každý cluster s odlišným počtem pracovní virtuální počítače, pomocí různých velikostí virtuálních počítačů a tak dále.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB klienta a cluster v Azure
MATLAB klientský uzel, uzel MATLAB plánovače úloh a MATLAB distribuovaný Server Computing "pracovní" uzly jsou všechny nakonfigurované jako virtuální počítače Azure ve virtuální síti, jak je znázorněno na následujícím obrázku.


* Pokud chcete použít clusteru, připojte k uzlu klienta pomocí vzdálené plochy. Uzel klient spustí MATLAB klienta.
* Klientský uzel má sdílení souborů, které mají přístup všechny pracovní procesy.
* Správce licencí hostované společnost MathWorks se používá pro kontroly licence pro veškerý software MATLAB.
* Ve výchozím nastavení v pracovním procesu virtuální počítače se vytvoří jeden pracovní proces MATLAB distribuovaný Server Computing za virtuální procesory, ale můžete zadat všechny číslo.

## <a name="use-an-azure-based-cluster"></a>Použití clusteru služby založené na Azure
Jako s jinými typy MATLAB distribuovaný Server výpočetních clusterů, budete muset použít profil Správce clusteru v klientovi MATLAB (na straně klienta VM) k vytvoření profilu clusteru MATLAB plánovače úloh.

![Profil Správce clusteru](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Další postup
* Podrobné pokyny k nasazení a správě clusterů MATLAB distribuovaný Server výpočty v Azure najdete v tématu [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) úložiště, který obsahuje šablony a skripty.
* Přejděte na [společnost MathWorks lokality](http://www.mathworks.com/) pro podrobnou dokumentaci pro MATLAB a MATLAB distribuovaný Server Computing.
