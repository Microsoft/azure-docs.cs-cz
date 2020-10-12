---
title: Clustery programu MATLAB na virtuálních počítačích
description: Pomocí Microsoft Azure virtuálních počítačů můžete vytvářet clustery se systémem MATLAB Distributed Computing Server pro spouštění vašich výpočetně náročných úloh programu MATLAB.
author: mscurrell
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: c2561a6dc3ad8c0af1c266b3822a80c76f45c174
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88639677"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Vytvoření clusterů distribuovaných výpočetních serverů v programu MATLAB na virtuálních počítačích Azure
Pomocí Microsoft Azure virtuálních počítačů můžete vytvořit jeden nebo více clusterů distribuovaných výpočetních serverů programu MATLAB pro spouštění paralelních úloh programu MATLAB náročných na výpočetní výkon. Nainstalujte na virtuální počítač software pro distribuovaný výpočetní systém v programu MATLAB, který použijete jako základní image, a pro nasazení a správu clusteru použijte šablonu Azure pro rychlý Start nebo skript Azure PowerShell (k dispozici na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)). Po nasazení se připojte ke clusteru, aby se spouštěly vaše úlohy.

> [!IMPORTANT]
> Od napsání tohoto článku teď existuje oficiální podpora používání aplikací pro MATLAB v Azure. Doporučuje se použít tyto novější funkce místo šablony a skriptů, na které se odkazuje v tomto článku. Hledat v [Azure Marketplace](https://azuremarketplace.microsoft.com/) pro "MATLAB"; Další informace o spouštění aplikací pro MATLAB v Azure najdete v [MathWorks](https://www.mathworks.com/solutions/cloud.html#public-cloud).

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>O serveru s distribuovaným výpočetním serverem a MATLAB
Platforma pro [MATLAB](https://www.mathworks.com/products/matlab/) je optimalizovaná pro řešení technických a vědeckých problémů. Uživatelé programu MATLAB s vysokými simulacemi a úlohami zpracování dat můžou pomocí MathWorks paralelních výpočetních prostředků zrychlit úlohy náročné na výpočetní výkon, a to díky využití výpočetních clusterů a služby Grid. [Panel nástrojů pro paralelní výpočty](https://www.mathworks.com/products/parallel-computing/) umožňuje uživatelům programu MATLAB paralelizovat aplikace a využívat multi-core procesory, GPU a výpočetní clustery. [Distribuovaný výpočetní Server](https://www.mathworks.com/products/distriben/) v programu MATLAB umožňuje uživatelům programu MATLAB využívat mnoho počítačů ve výpočetním clusteru.

Pomocí virtuálních počítačů Azure můžete vytvářet clustery distribuovaných výpočetních serverů v programu MATLAB, které mají všechny stejné mechanismy, které jsou k dispozici pro odesílání paralelní práce jako místních clusterů, jako jsou interaktivní úlohy, dávkové úlohy, nezávislé úkoly a komunikační úkoly. Použití Azure ve spojení s platformou pro MATLAB přináší mnoho výhod v porovnání se zřizováním a používáním tradičního místního hardwaru: škála velikostí virtuálních počítačů, vytváření clusterů na vyžádání, takže platíte jenom za výpočetní prostředky, které používáte, a možnost testovat modely ve velkém měřítku.  

## <a name="prerequisites"></a>Požadavky
* **Klientský počítač** – pro komunikaci s Azure a clusterem distribuovaného výpočetního serveru s Windows po nasazení budete potřebovat klientský počítač se systémem Windows.
* **Azure PowerShell** – Přečtěte si, [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/) pro instalaci do klientského počítače.
* **Předplatné Azure** – Pokud předplatné nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/free/) . U větších clusterů Vezměte v úvahu předplatné s průběžnými platbami nebo jiné možnosti nákupu.
* **vCPU kvóta** – možná budete muset zvýšit kvótu vCPU, aby se nasadil velký cluster nebo víc než jeden cluster s distribuovaným výpočetním serverem v programu MATLAB. Chcete-li zvýšit kvótu, [otevřete online žádost zákaznická podpora](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) zdarma.
* **Sada nástrojů pro Matlab, paralelní výpočty a licence programu MATLAB pro Distributed Computing Server** – skripty předpokládají, že [Správce licencí hostovaný v MathWorks](https://www.mathworks.com/help/install/license-management.html) se používá pro všechny licence.  
* **Software systému MATLAB Distributed Computing Server** – bude nainstalován na virtuální počítač, který bude použit jako základní image virtuálního počítače pro virtuální počítače clusteru.

## <a name="high-level-steps"></a>Kroky vysoké úrovně
Aby bylo možné používat virtuální počítače Azure pro clusterované výpočetní servery programu MATLAB, je nutné použít následující kroky vysoké úrovně. Podrobné pokyny najdete v dokumentaci přiložené k šabloně a skriptům pro rychlé zprovoznění na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Vytvoření image základního virtuálního počítače**  

   * Stáhněte a nainstalujte software systému MATLAB Distributed Computing Server do tohoto virtuálního počítače.

     > [!NOTE]
     > Tento proces může trvat několik hodin, ale stačí to udělat jenom jednou pro každou verzi programu MATLAB, kterou používáte.   
     >
     >
2. **Vytvoření jednoho nebo více clusterů**  

   * Použijte dodaný skript prostředí PowerShell nebo použijte šablonu pro rychlý Start k vytvoření clusteru z image základního virtuálního počítače.   
   * Clustery můžete spravovat pomocí zadaného skriptu prostředí PowerShell, který umožňuje zobrazit, pozastavit, obnovit a odstranit clustery.

## <a name="cluster-configurations"></a>Konfigurace clusterů
Skript pro vytvoření clusteru a šablonu v současné době umožňují vytvořit jednu topologii distribuovaného výpočetního serveru programu MATLAB. Pokud chcete, vytvořte jeden nebo víc dalších clusterů s každým clusterem, který má jiný počet pracovních počítačů, a to pomocí různých velikostí virtuálních počítačů a tak dále.

### <a name="matlab-client-and-cluster-in-azure"></a>Klient a cluster pro MATLAB v Azure
Uzel klienta nástroje MATLAB, uzel plánovače úloh programu MATLAB a pracovní procesy programu MATLAB Distributed Computing Server se nakonfigurují jako virtuální počítače Azure ve virtuální síti, jak je znázorněno na následujícím obrázku.


* Chcete-li použít cluster, připojte se pomocí vzdálené plochy k klientskému uzlu. Uzel klienta spustí klienta nástroje MATLAB.
* Uzel klienta má sdílenou složku, ke které můžou používat všichni pracovníci.
* Správce licencí hostovaný v MathWorks se používá pro kontroly licencí pro veškerý software v programu MATLAB.
* Ve výchozím nastavení se na virtuálních počítačích pracovních procesů vytvoří jeden pracovní proces distribuovaného výpočetního serveru programu MATLAB, ale můžete zadat libovolné číslo.

## <a name="use-an-azure-based-cluster"></a>Použití clusteru založeného na Azure
Stejně jako u jiných typů clusterů systému MATLAB Distributed Computing Server je nutné použít Správce profilů clusterů v klientovi MATLAB (na virtuálním počítači klienta) a vytvořit profil clusteru plánovače úloh programu MATLAB.

![Správce profilů clusteru](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Další kroky
* Podrobné pokyny k nasazení a správě clusterových serverů s distribuovaným výpočetním prostředím v prostředí Azure najdete v úložišti [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) obsahujícím šablony a skripty.
* Podrobnou dokumentaci k distribuovanému výpočetnímu serveru pro MATLAB a MATLAB najdete na [webu MathWorks](https://www.mathworks.com/) .
