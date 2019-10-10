---
title: Přehled nasazení – avere vFXT pro Azure
description: Přehled nasazení avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 9684b230b8790e36cd7442c65481c0c71ce185d6
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255428"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT pro Azure – přehled nasazení

Tento článek obsahuje přehled kroků potřebných k zprovoznění služby avere vFXT pro cluster Azure.

Před a po vytvoření clusteru vFXT z Azure Marketplace je třeba provést několik úloh. Jasným smyslem procesu spuštění až po dokončení bude pomáhat s rozsahem potřebného úsilí. 

## <a name="deployment-steps"></a>Kroky nasazení

Po [Naplánování systému](avere-vfxt-deploy-plan.md)můžete začít vytvářet cluster avere vFXT. 

Azure Resource Manager šablona v Azure Marketplace shromažďuje potřebné informace a automaticky nasadí celý cluster. 

Jakmile je cluster vFXT v provozu, budete chtít, abyste věděli, jak k němu připojit klienty, a v případě potřeby jak přesunout data do nového kontejneru úložiště objektů BLOB.  

Tady je přehled všech kroků.

1. Konfigurovat požadavky 

   Před vytvořením virtuálního počítače musíte pro projekt avere vFXT vytvořit nové předplatné, nakonfigurovat vlastnictví předplatného, kontrolovat kvóty a v případě potřeby požádat o zvýšení platnosti a přijmout podmínky pro používání softwaru avere vFXT. Podrobné pokyny najdete [v tématu Příprava k vytvoření avere vFXT](avere-vfxt-prereqs.md) .

1. Vytvoření clusteru avere vFXT 

   K vytvoření clusteru avere vFXT pro Azure použijte Azure Marketplace. Šablona shromažďuje požadované informace a spouští skripty pro vytvoření konečného produktu.

   Vytvoření clusteru zahrnuje tyto kroky, které jsou všechny dokončené šablonou Marketplace: 

   * Vytvoření nové síťové infrastruktury a skupin prostředků (v případě potřeby)
   * Vytvoření *řadiče clusteru*  

     Řadič clusteru je jednoduchý virtuální počítač, který se nachází ve stejné virtuální síti jako cluster avere vFXT, a má vlastní software potřebný k vytvoření a správě clusteru. Kontrolér vytvoří uzly vFXT a vytvoří cluster a také rozhraní příkazového řádku pro správu clusteru během své životnosti.

     Pokud během nasazení vytvoříte novou virtuální síť, bude mít řadič veřejnou IP adresu. To znamená, že řadič může sloužit jako skokový hostitel pro připojení ke clusteru mimo virtuální síť.

   * Vytváření virtuálních počítačů uzlů clusteru

   * Konfigurace virtuálních počítačů uzlů clusteru pro vytvoření clusteru

   * Volitelně můžete vytvořit nový kontejner objektů BLOB a nakonfigurovat ho jako back-end úložiště pro cluster.

1. Konfigurace clusteru 

   K přizpůsobení nastavení clusteru se připojte k rozhraní avere vFXT Configuration Interface (avere Control Panel). Pokud používáte místní datové centrum, přihlaste se ke sledování podpory a přidejte svůj systém úložiště.

   * [Přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md)
   * [Povolit podporu](avere-vfxt-enable-support.md)
   * [Konfigurace úložiště](avere-vfxt-add-storage.md) (v případě potřeby)

1. Připojení klienti

   Pokud se chcete dozvědět o vyrovnávání zatížení a jak by měly klientské počítače připojit cluster, postupujte podle pokynů v tématu [připojení clusteru avere vFXT](avere-vfxt-mount-clients.md) .

1. Přidat data (v případě potřeby)

   Vzhledem k tomu, že avere vFXT je škálovatelná mezipaměť s více klienty, nejlepším způsobem, jak přesunout data do nového záložního kontejneru úložiště, je s více klienty, strategii kopírování souborů s více vlákny. Podrobnější informace najdete [v tématu přesun dat do clusteru vFXT](avere-vfxt-data-ingest.md) .

## <a name="next-steps"></a>Další kroky

Pokračujte v [přípravě na vytvoření avere vFXT](avere-vfxt-prereqs.md) , abyste dokončili předběžné úlohy pro nasazení avere VFXT pro Azure. 