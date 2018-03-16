---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ee32886ddb74bdbbe0f240310629c8ef26230a68
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
## <a name="deployment-considerations"></a>Aspekty nasazování
* **Předplatné Azure** – jak nasadit víc než několik instancí náročné, zvažte průběžnými platbami předplatné nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

* **Ceny a dostupnosti** -velikosti tyto virtuálních počítačů jsou nabízena pouze v standardní cenovou úroveň. Zkontrolujte [produkty dostupné podle oblasti] (https://azure.microsoft.com/regions/services/) pro dostupnost v oblastech Azure. 
* **Kvóta jader** – možná budete muset zvýšit kvótu jader v předplatném Azure z výchozí hodnoty. Vaše předplatné může také omezit počet jader, který můžete nasadit v určité rodiny velikost virtuálního počítače, včetně H-series. Požádat o zvýšení kvóty, [otevřete žádosti o podporu online zákazníka](../articles/azure-supportability/how-to-create-azure-support-request.md) zdarma. (Výchozí omezení se liší v závislosti na vaše předplatné kategorie).
  
  > [!NOTE]
  > Pokud máte požadavků na kapacitu ve velkém měřítku, obraťte se na podporu Azure. Kvótách Azure jsou platební limity, není kapacity záruky. Bez ohledu na vaší kvóty vám jsou pouze účtovat jader použít.
  > 
  > 
* **Virtuální síť** – na Azure [virtuální sítě](https://azure.microsoft.com/documentation/services/virtual-network/) není potřeba použít instance náročné. Však v mnoha nasazeních musíte mít aspoň cloudové virtuální síť Azure nebo připojení site-to-site Pokud budete potřebovat pro přístup k místním prostředkům. V případě potřeby vytvořte nové virtuální sítě pro nasazení instance. Přidávání virtuálních počítačů náročné do virtuální sítě ve skupině vztahů není podporováno.
* **Změna velikosti** – kvůli speciální hardware, můžete pouze změnit velikost náročné instancí v rámci stejné rodiny velikost (H-series nebo náročné A-series). Například můžete nastavit velikost pouze H-series virtuální počítač z jednoho velikost řady H do jiného. Kromě toho není podporována Změna velikosti z výpočetní náročné velikost na velikost náročné.  

## <a name="rdma-capable-instances"></a>RDMA podporovat instancí
Podmnožinu náročné instance (H16r, H16mr, A8 a A9) funkce síťové rozhraní pro připojení do paměti vzdáleného přímý přístup do (počítače RDMA). (Vybrané N-series velikosti označeny 'r, jako je například NC24r jsou také podporuje RDMA). Toto rozhraní je kromě standardní Azure síťové rozhraní, které jsou dostupné další velikosti virtuálních počítačů. 
  
Toto rozhraní umožňuje podporu rdma instance komunikovat přes síť InfiniBand (IB), provoz se FDR sazby za H16r, H16mr a podporou RDMA N-series virtuální počítače a QDR sazby A8 a A9 virtuálních počítačů. Tyto funkce RDMA může zvýšit škálovatelnost a výkon některých aplikací rozhraní MPI (Message Passing).

> [!NOTE]
> V Azure IP přes IB nepodporuje. Je podporován pouze RDMA přes IB.
>

Nasazení podporující RDMA HPC virtuální počítače ve stejné skupině dostupnosti nebo sadu škálování virtuálního počítače (při použití modelu nasazení Azure Resource Manager) nebo stejné cloudové služby (při použití modelu nasazení classic). Následují další požadavky na podporu rdma HPC virtuálních počítačů pro přístup k síti Azure RDMA.