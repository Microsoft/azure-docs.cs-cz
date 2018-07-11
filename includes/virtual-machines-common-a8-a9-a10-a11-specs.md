---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep
ms.custom: include file
ms.openlocfilehash: 296e92d803bb69376f286aa60cfb4a955b08010f
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "34669338"
---
## <a name="deployment-considerations"></a>Aspekty nasazování
* **Předplatné Azure** – Chcete-li nasadit více než několik výpočetně náročných instancí, zvažte předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

* **Ceny a dostupnost** – velikosti virtuálních počítačů tyto jsou nabízeny jen v standardní cenová úroveň. Zkontrolujte dostupné produkty v jednotlivých oblastech] (https://azure.microsoft.com/regions/services/) pro dostupnost v oblastech Azure. 
* **Kvóta pro jádra** – možná budete muset zvýšili kvótu jader v předplatném Azure z výchozí hodnoty. Vaše předplatné může také omezit počet jader, které můžete nasadit v určitých rodinách velikostí virtuálních počítačů, včetně řady H-series. Požádat o zvýšení kvóty, [otevřete žádost o online zákaznickou podporu](../articles/azure-supportability/how-to-create-azure-support-request.md) bez poplatků. (Výchozí omezení může lišit v závislosti na vaše předplatné kategorie).
  
  > [!NOTE]
  > Pokud máte velkou kapacitu, obraťte se na podporu Azure. Azure kvóty jsou kredit limity, ne kapacita záruky. Bez ohledu na vaší kvóty pouze účtují jádra použít.
  > 
  > 
* **Virtuální síť** – což je Azure [virtuální sítě](https://azure.microsoft.com/documentation/services/virtual-network/) není nutné používat výpočetně náročných instancí. Ale v mnoha nasazeních musíte alespoň cloudové virtuální síť Azure, nebo připojení site-to-site Pokud potřebujete přístup k místním prostředkům. Pokud je nepotřebujete, vytvořte novou virtuální síť k nasazení instancí. Není podporováno přidávání virtuálních počítačů náročných na výpočetní prostředky pro virtuální síť ve skupině vztahů.
* **Změna velikosti** – z důvodu specializovaný hardware, můžete pouze změnit velikost instance náročné na výpočetní prostředky v rámci stejné řady velikostí (řada H-series nebo náročné na výpočetní prostředky řady A-series). Například můžete pouze změnit velikost řady H-series virtuálního počítače s od velikosti řady H-series na jiný. Kromě toho se nepodporuje změnu velikosti z výpočetně náročné velikost na velikost náročné na výpočetní.  

## <a name="rdma-capable-instances"></a>Instance s podporou RDMA
Podmnožinu instance náročné na výpočty (H16r, H16mr, instance A8 a A9) funkcí síťového rozhraní pro připojení k vzdálené paměti přístup (počítače RDMA). (Označeny jako "r", jako je například NC24r velikosti vybraných N-series jsou také podporující RDMA). Toto rozhraní je kromě rozhraní standardní síť Azure k dispozici pro ostatní velikosti virtuálních počítačů. 
  
Toto rozhraní podporuje RDMA podporovat instance komunikovat přes síť InfiniBand (IB), provoz se sazbami díky technologii FDR H16r, H16mr a podporou RDMA N-series virtuálních počítačů a QDR sazby za virtuální počítače A8 a a9 pro aplikace. Tyto možnosti přístupu RDMA může zvýšit škálovatelnost a výkon určitých aplikací rozhraní MPI (Message Passing Interface).

> [!NOTE]
> V Azure IP over IB nepodporuje. Je podporován pouze RDMA přes IB.
>

Nasazení podporující RDMA virtuální počítače HPC ve stejné skupině dostupnosti nebo škálovací sady virtuálních počítačů (Pokud používáte model nasazení Azure Resource Manageru) nebo stejné cloudové služby (Pokud používáte model nasazení classic). Pokud použijete škálovací sady virtuálních počítačů, ujistěte se, že omezíte na jediné skupiny umístění; nasazení například v šabloně Resource Manageru, nastavte *singlePlacementGroup* vlastnost *true*. Následují další požadavky pro virtuální počítače s podporou RDMA HPC pro přístup k síti Azure RDMA.