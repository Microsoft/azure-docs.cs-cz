---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 0930fa371500125c15cd969b9e9f4b7a2853612e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174940"
---
## <a name="deployment-considerations"></a>Aspekty nasazování
* **Předplatné Azure** – Chcete-li nasadit více než několik několika instancí náročných na výpočetní výkon, vezměte v úvahu předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

* **Ceny a dostupnost** – tyto velikosti virtuálních počítačů se nabízejí jenom v cenové úrovni Standard. Podívejte se na [produkty dostupné v oblasti a](https://azure.microsoft.com/global-infrastructure/services/) dostupnost v oblastech Azure. 
* **Kvóty jader** – možná bude potřeba zvýšit kvótu jader v předplatném Azure z výchozí hodnoty. Vaše předplatné může také omezit počet jader, které můžete nasadit v určitých rodinách velikostí virtuálních počítačů, včetně řady H-Series. Chcete-li požádat o zvýšení kvóty, [otevřete online žádost o zákaznickou podporu](../articles/azure-supportability/how-to-create-azure-support-request.md) zdarma. (Výchozí omezení se můžou lišit v závislosti na vaší kategorii předplatného.)
  
  > [!NOTE]
  > Pokud máte velké nároky na kapacitu, obraťte se na podporu Azure. Kvóty Azure jsou úvěrovými limity, které nezaručují kapacitu. Bez ohledu na vaši kvótu se účtují jenom ty jádra, které používáte.
  > 
  > 
* **Virtual Network** – [virtuální síť](https://azure.microsoft.com/documentation/services/virtual-network/) Azure není nutná k používání instancí náročných na výpočetní výkon. Pro mnoho nasazení ale potřebujete alespoň cloudovou virtuální síť Azure nebo připojení typu Site-to-site, pokud potřebujete přístup k místním prostředkům. V případě potřeby vytvořte novou virtuální síť pro nasazení instancí. Přidání virtuálních počítačů náročných na výpočetní výkon do virtuální sítě ve skupině vztahů se nepodporuje.
* **Změna velikosti** – z důvodu jejich specializovaného hardwaru můžete změnit velikost jenom těch instancí náročných na výpočetní výkon v rámci stejné řady velikostí (H-Series nebo výpočetní výkon a-Series). Například můžete změnit velikost virtuálního počítače H-Series jenom z jedné velikosti řady H-Series na jinou. Kromě toho se nepodporují změny velikosti z nevýpočetní velikosti s náročnou na výpočetní výkon.  

## <a name="rdma-capable-instances"></a>Instance s podporou RDMA
Podmnožina instancí COMPUTE s náročnou zátěží (A8, doH16r, H16mr, Geta HC) funguje jako síťové rozhraní pro připojení vzdáleného přímého přístupu do paměti (RDMA). Vybrané velikosti řady N-Series označené písmenem r, jako jsou například konfigurace NC24rs (NC24rs_v2 a NC24rs_v3), jsou také podporující technologii RDMA. Toto rozhraní je navíc ke standardním síťovým rozhraním Azure, které je dostupné pro jiné velikosti virtuálních počítačů. 
  
Toto rozhraní umožňuje, aby instance s podporou RDMA komunikovaly přes síť InfiniBand (IB), která pracuje na EDR sazbách pro třídy H16r, H16mr a RDMA a na virtuálních počítačích řady N-Series s podporou RDMA a QDR. Tyto možnosti RDMA můžou zvýšit škálovatelnost a výkon určitých aplikací MPI (Message Passing Interface). Další informace o rychlosti najdete v podrobnostech v tabulkách na této stránce.

> [!NOTE]
> V Azure se IP přes IB podporuje jenom u virtuálních počítačů s podporou SR-IOV (SR-IOV pro InfiniBand, které jsou v současnosti k dispozici a HC). RDMA přes IB se podporuje pro všechny instance podporující RDMA.
>

