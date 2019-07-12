---
title: MATLAB clusterů na virtuálních počítačích | Dokumentace Microsoftu
description: Pomocí Microsoft Azure virtual machines k vytvoření clusterů MATLAB distribuované výpočetní serveru spouštět úlohy náročné na výpočetní paralelní MATLAB
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 857382c09192e71d9727a91274993091f2f903b2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718690"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Vytvoření MATLAB distribuovaný Server výpočetních clusterů na virtuálních počítačích Azure
Pomocí Microsoft Azure virtual machines můžete vytvořit jeden nebo více MATLAB distribuované výpočetní clustery spouštět úlohy náročné na výpočetní paralelní MATLAB. Nainstalovat software serveru distribuované výpočetní MATLAB na virtuálním počítači používat jako základní image a použít šablonu Azure pro rychlý start nebo skriptu Azure Powershellu (k dispozici na [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) k nasazení a správě clusteru. Po nasazení připojte se ke clusteru pro spouštění vašich úloh.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>O MATLAB a MATLAB distribuované výpočetní Server
[MATLAB](https://www.mathworks.com/products/matlab/) platformy je optimalizovaný pro řešení problémů technické a vědecké. MATLAB uživatele pomocí simulace ve velkém měřítku a úloh zpracování dat můžete použít společnost MathWorks paralelní výpočetní produkty a urychlit tak jejich úlohy náročné na výpočetní prostředky s využitím výpočetních clusterů a služeb mřížky. [Paralelní výpočty nástrojů](https://www.mathworks.com/products/parallel-computing/) MATLAB uživatelům umožňuje paralelní aplikace a využít výhod vícejádrových procesorů, která využívá GPU a výpočetních clusterů. [Server distribuované výpočetní MATLAB](https://www.mathworks.com/products/distriben/) umožňuje uživatelům MATLAB využívat mnoho počítačů ve výpočetním clusteru.

Využití Azure virtual machines, můžete vytvořit MATLAB distribuované výpočetní clustery, které mají stejné mechanismy, které jsou k dispozici pro odeslání paralelní práce jako s místními clustery, jako je například interaktivní úlohy, dávkových úloh Hive, nezávislých úloh a komunikaci úlohy. Pomocí Azure v kombinaci s platformou MATLAB má mnoho výhod oproti zřizování a pomocí tradiční v místním hardwaru: rozsah virtuální počítač o velikosti, vytváření clusterů na vyžádání, platíte jenom za výpočetní prostředky, které používáte, a schopnost testování modely ve velkém měřítku.  

## <a name="prerequisites"></a>Požadavky
* **Klientský počítač** -počítače klienta se systémem Windows ke komunikaci s Azure a cluster MATLAB distribuovaný Server výpočetního prostředí po nasazení budete potřebovat.
* **Prostředí Azure PowerShell** -naleznete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview) instalace na klientském počítači.
* **Předplatné Azure** – Pokud nemáte předplatné, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/) během několika minut. Pro větší clustery zvažte předplatné s průběžnými platbami nebo jiné možnosti nákupu.
* **kvóty virtuálních procesorů** – možná budete muset zvýšit kvótu virtuálních procesorů nasadit velký cluster nebo více než jeden cluster MATLAB distribuovaný Server výpočetního prostředí. Na zvýšení této kvóty, [otevřete žádost o online zákaznickou podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) bez poplatků.
* **Licence MATLAB, paralelní výpočty nástrojů a MATLAB distribuovaný Server computingu** – skripty se předpokládá, že [společnost MathWorks hostovaný správce licencí](https://www.mathworks.com/help/install/license-management.html) se používá pro všechny licence.  
* **Software serveru distribuované výpočetní MATLAB** – se nainstaluje na virtuálním počítači, který se použije jako základní image virtuálního počítače pro virtuální počítače clusteru.

## <a name="high-level-steps"></a>Postup vysoké úrovně
K použití virtuálních počítačů Azure pro své clustery serverů distribuované výpočetní MATLAB, je potřeba následující postup vysoké úrovně. Podrobné pokyny naleznete v dokumentaci šablonu pro rychlý start a skripty na [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Vytvoření základní image virtuálního počítače**  

   * Stáhněte a nainstalujte software MATLAB distribuovaný Server computingu do tohoto virtuálního počítače.

     > [!NOTE]
     > Tento proces může trvat několik hodin, ale budete muset provést jednou pro každou verzi MATLAB použijete.   
     >
     >
2. **Vytvořte jeden nebo více clusterů**  

   * Použijte zadaný skript prostředí PowerShell nebo šablonu pro rychlý start k vytvoření clusteru ze základní image virtuálního počítače.   
   * Správa clusterů pomocí zadaný skript prostředí PowerShell, který umožňuje seznam, pozastavení, obnovení a odstranění clusterů.

## <a name="cluster-configurations"></a>Konfigurace clusteru
V současné době skriptu pro vytváření clusteru a šablony umožňují vytvořit jeden Server distribuované výpočetní MATLAB topologie. Pokud chcete, vytvořte jeden nebo více dalších clusterů s každý cluster má jiný počet pracovních procesů virtuálních počítačů, pomocí různých velikostí virtuálních počítačů a tak dále.

### <a name="matlab-client-and-cluster-in-azure"></a>Klient MATLAB a clusteru v Azure
Uzel klienta MATLAB, Plánovač úloh MATLAB uzlu a MATLAB distribuovaný Server computingu "pracovní" uzly jsou všechny nakonfigurované jako virtuální počítače Azure ve virtuální síti, jak je znázorněno na následujícím obrázku.


* Použití clusteru, připojte k uzlu klienta pomocí vzdálené plochy. Uzel klienta MATLAB klienta využívají.
* Uzel klienta má sdílenou složku, která je možný přes všechny pracovní procesy.
* Společnost MathWorks hostovaný správce licencí se používá pro kontroly licenci pro všechny MATLAB softwaru.
* Ve výchozím nastavení jeden pracovní proces serveru distribuované výpočetní MATLAB na virtuální procesor je vytvořen v pracovním procesu virtuálních počítačů, ale můžete zadat libovolný počet.

## <a name="use-an-azure-based-cluster"></a>Použití clusteru služby založené na Azure
Jako u jiných typů MATLAB distribuované výpočetní clustery, budete muset použít profil Správce clusteru v klientovi MATLAB (na straně klienta virtuálního počítače) k vytvoření profilu cluster MATLAB Plánovač úloh.

![Profil Správce clusteru](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Další postup
* Podrobné pokyny k nasazení a správě MATLAB distribuované výpočetní clustery v Azure najdete v tématu [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) úložiště, který obsahuje šablony a skripty.
* Přejděte [společnost MathWorks lokality](https://www.mathworks.com/) podrobnou dokumentaci pro MATLAB a MATLAB distribuovaný Server výpočetního prostředí.
