---
title: Clustery MATLAB na virtuálních počítačích
description: Pomocí virtuálních počítačů Microsoft Azure můžete vytvářet clustery MATLAB Distributed Computing Server pro spouštění paralelních úloh MATLAB náročných na výpočetní výkon
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: a2fb2479f5544b869b51e796085fcb4d0b76121a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038140"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Vytvoření clusterů distribuovaných výpočetních serverů MATLAB na virtuálních počítačích Azure
Pomocí virtuálních počítačů Microsoft Azure můžete vytvořit jeden nebo více clusterů MATLAB Distributed Computing Server pro spouštění paralelních úloh MATLAB náročných na výpočetní výkon. Nainstalujte si software MATLAB Distributed Computing Server na virtuální počítač, který se použije jako základní image, a k nasazení a správě clusteru použijte šablonu rychlého startu Azure nebo skript Azure PowerShell (dostupný na [GitHubu).](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) Po nasazení se připojte ke clusteru a spusťte své úlohy.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>O distridčním výpočetním serveru MATLAB a MATLAB
Platforma [MATLAB](https://www.mathworks.com/products/matlab/) je optimalizována pro řešení technických a vědeckých problémů. Uživatelé MATLAB u rozsáhlých simulací a úloh zpracování dat mohou pomocí paralelních výpočetních produktů MathWorks urychlit své výpočetní úlohy využitím výpočetních clusterů a gridových služeb. [Parallel Computing Toolbox](https://www.mathworks.com/products/parallel-computing/) umožňuje uživatelům MATLAB paralelizovat aplikace a využívat vícejádrové procesory, GPU a výpočetní clustery. [MATLAB Distributed Computing Server](https://www.mathworks.com/products/distriben/) umožňuje uživatelům MATLAB využívat mnoho počítačů ve výpočetním clusteru.

Pomocí virtuálních počítačů Azure můžete vytvořit clustery MATLAB Distributed Computing Server, které mají všechny stejné mechanismy, které jsou k dispozici pro odesílání paralelní práce jako místní clustery, jako jsou interaktivní úlohy, dávkové úlohy, nezávislé úlohy a komunikace Úkoly. Používání Azure ve spojení s platformou MATLAB má mnoho výhod ve srovnání s zřizováním a používáním tradičního místního hardwaru: řada velikostí virtuálních strojů, vytváření clusterů na vyžádání, takže platíte jenom za výpočetní prostředky, které používáte, a schopnost testovat modely ve velkém měřítku.  

## <a name="prerequisites"></a>Požadavky
* **Klientský počítač** – ke komunikaci s Azure a clusterem distribuovaných výpočetních serverů MATLAB budete po nasazení potřebovat klientský počítač se systémem Windows.
* **Azure PowerShell** – přečtěte si, [jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview) pro jeho instalaci do klientského počítače.
* **Předplatné Azure** – Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/) během několika minut. U větších clusterů zvažte předplatné s průběžným platbou nebo jiné možnosti nákupu.
* **kvóta virtuálních procesorů** – možná budete muset zvýšit kvótu virtuálního procesoru, abyste mohli nasadit velký cluster nebo více než jeden cluster MATLAB Distributed Computing Server. Chcete-li zvýšit kvótu, [otevřete online žádost o zákaznickou podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) zdarma.
* **Licence MATLAB, Parallel Computing Toolbox a MATLAB Distributed Computing Server** – Skripty předpokládají, že [správce licencí MathWorks Hosted](https://www.mathworks.com/help/install/license-management.html) se používá pro všechny licence.  
* **MATLAB Distributed Computing Server software** - Bude nainstalován na virtuální mkér, který se použije jako základní image virtuálního počítače pro virtuální počítače clusteru.

## <a name="high-level-steps"></a>Kroky na vysoké úrovni
Chcete-li používat virtuální počítače Azure pro clustery distribuovaných výpočetních serverů MATLAB, jsou vyžadovány následující kroky vysoké úrovně. Podrobné pokyny jsou uvedeny v dokumentaci přiložené k šabloně rychlého startu a skriptům na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Vytvoření základní image virtuálního virtuálního virtuálního**  

   * Stáhněte a nainstalujte software MATLAB Distributed Computing Server do tohoto virtuálního počítače.

     > [!NOTE]
     > Tento proces může trvat několik hodin, ale stačí to udělat jednou pro každou verzi MATLAB, kterou používáte.   
     >
     >
2. **Vytvoření jednoho nebo více clusterů**  

   * Použijte dodaný skript PowerShellu nebo použijte šablonu rychlého startu k vytvoření clusteru ze základní image virtuálního počítače.   
   * Spravujte clustery pomocí dodaného skriptu prostředí PowerShell, který umožňuje vypsat, pozastavit, obnovit a odstranit clustery.

## <a name="cluster-configurations"></a>Konfigurace clusteru
V současné době umožňuje skript a šablona pro vytváření clusteru vytvořit jednu topologii serveru MATLAB Distributed Computing Server. Pokud chcete, vytvořte jeden nebo více dalších clusterů, přičemž každý cluster má jiný počet pracovních virtuálních počítačů, pomocí různých velikostí virtuálních počítačů a tak dále.

### <a name="matlab-client-and-cluster-in-azure"></a>Klient a cluster MATLAB v Azure
Klientský uzel MATLAB, uzel Plánovač úloh MATLAB a "pracovní" uzly serveru MATLAB distributed computing server jsou nakonfigurované jako virtuální počítače Azure ve virtuální síti, jak je znázorněno na následujícím obrázku.


* Chcete-li cluster používat, připojte se pomocí vzdálené plochy k uzlu klienta. Klientský uzel spouští klienta MATLAB.
* Uzel klienta má sdílenou složku, ke které mají přístup všichni pracovníci.
* MathWorks Hosted License Manager se používá pro kontrolu licencí pro veškerý software MATLAB.
* Ve výchozím nastavení je na pracovních virtuálních počítačích vytvořen jeden pracovník serveru distribuovaného výpočetního serveru MATLAB, ale můžete zadat libovolné číslo.

## <a name="use-an-azure-based-cluster"></a>Použití clusteru založeného na Azure
Stejně jako u jiných typů clusterů distribuovaných výpočetních serverů MATLAB je třeba použít Správce profilů clusteru v klientovi MATLAB (na virtuálním počítači klienta) k vytvoření profilu clusteru Plánovač úloh MATLAB.

![Správce profilů clusteru](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Další kroky
* Podrobné pokyny k nasazení a správě clusterů matlabdistributed computing server v Azure najdete v úložišti [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) obsahující šablony a skripty.
* Podrobnou dokumentaci pro MATLAB a MATLAB Distributed Computing Server naleznete na [webu MathWorks.](https://www.mathworks.com/)
