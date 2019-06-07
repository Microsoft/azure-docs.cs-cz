---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep;amverma
ms.custom: include file
ms.openlocfilehash: 5cbc19d5aade2bbcc8b8dca277352d1b17d1d35a
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755177"
---
## <a name="deployment-considerations"></a>Aspekty nasazování
* **Předplatné Azure** – Chcete-li nasadit více než několik výpočetně náročných instancí, zvažte předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

* **Ceny a dostupnost** – velikosti virtuálních počítačů tyto jsou nabízeny jen v standardní cenová úroveň. Zkontrolujte [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) pro dostupnost v oblastech Azure. 
* **Kvóta pro jádra** – možná budete muset zvýšili kvótu jader v předplatném Azure z výchozí hodnoty. Vaše předplatné může také omezit počet jader, které můžete nasadit v určitých rodinách velikostí virtuálních počítačů, včetně řady H-series. Požádat o zvýšení kvóty, [otevřete žádost o online zákaznickou podporu](../articles/azure-supportability/how-to-create-azure-support-request.md) bez poplatků. (Výchozí omezení může lišit v závislosti na vaše předplatné kategorie).
  
  > [!NOTE]
  > Pokud máte velkou kapacitu, obraťte se na podporu Azure. Azure kvóty jsou kredit limity, ne kapacita záruky. Bez ohledu na vaší kvóty pouze účtují jádra použít.
  > 
  > 
* **Virtuální síť** – což je Azure [virtuální sítě](https://azure.microsoft.com/documentation/services/virtual-network/) není nutné používat výpočetně náročných instancí. Ale v mnoha nasazeních musíte alespoň cloudové virtuální síť Azure, nebo připojení site-to-site Pokud potřebujete přístup k místním prostředkům. Pokud je nepotřebujete, vytvořte novou virtuální síť k nasazení instancí. Není podporováno přidávání virtuálních počítačů náročných na výpočetní prostředky pro virtuální síť ve skupině vztahů.
* **Změna velikosti** – z důvodu specializovaný hardware, můžete pouze změnit velikost instance náročné na výpočetní prostředky v rámci stejné řady velikostí (řada H-series nebo náročné na výpočetní prostředky řady A-series). Například můžete pouze změnit velikost řady H-series virtuálního počítače s od velikosti řady H-series na jiný. Kromě toho se nepodporuje změnu velikosti z výpočetně náročné velikost na velikost náročné na výpočetní.  

## <a name="rdma-capable-instances"></a>Instance s podporou RDMA
Podmnožinu výpočetně náročných instancí (A8, A9, H16r, H16mr, HB a hybridní připojení) funkcí síťového rozhraní pro připojení k vzdálené paměti přístup (počítače RDMA). Vybrané velikosti řady N-series označeny jako "r" například ke konfiguracím NC24rs (NC24rs_v2 a NC24rs_v3) jsou také podporující RDMA. Toto rozhraní je kromě rozhraní standardní síť Azure k dispozici pro ostatní velikosti virtuálních počítačů. 
  
Toto rozhraní podporuje RDMA podporovat instance komunikovat přes síť InfiniBand (i) v provozu sazby EDR HB, hybridní připojení, díky technologii FDR účtovat sazby platné pro H16r, H16mr a podporou RDMA N-series virtuálních počítačů a QDR sazby za virtuální počítače A8 a a9 pro aplikace. Tyto možnosti přístupu RDMA může zvýšit škálovatelnost a výkon určitých aplikací rozhraní MPI (Message Passing Interface). Další informace o rychlosti najdete v podrobnostech v tabulkách na této stránce.

> [!NOTE]
> V Azure, IP over IB je podporována pouze u virtuálních počítačů (aktuálně HB a HC) s povoleným rozhraní SR-IOV. RDMA přes IB platí pro všechny instance podporující RDMA.
>

