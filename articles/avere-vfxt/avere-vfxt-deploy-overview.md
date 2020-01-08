---
title: Přehled nasazení – avere vFXT pro Azure
description: Přehled nasazení avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 46e6828710c5951cdd7ec3a029272a0e3d68c477
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415408"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT pro Azure – přehled nasazení

Tento článek obsahuje přehled kroků potřebných k zprovoznění služby avere vFXT pro cluster Azure.

Před a po vytvoření clusteru vFXT z Azure Marketplace je třeba provést několik úloh. Jasným smyslem procesu spuštění až po dokončení bude pomáhat s rozsahem potřebného úsilí.

## <a name="deployment-steps"></a>Kroky nasazení

Po [Naplánování systému](avere-vfxt-deploy-plan.md)můžete začít vytvářet cluster avere vFXT.

Azure Resource Manager šablona v Azure Marketplace shromažďuje potřebné informace a automaticky nasadí celý cluster.

Po zprovoznění clusteru vFXT budete chtít zjistit, jak připojit klienty k tomuto a (volitelně) jak přesunout data do nového kontejneru úložiště objektů BLOB. Pokud používáte úložný systém NAS, budete ho muset přidat po vytvoření clusteru.

Tady je přehled všech kroků.

1. Konfigurovat požadavky

   Před vytvořením virtuálního počítače musíte pro projekt avere vFXT vytvořit nové předplatné, nakonfigurovat vlastnictví předplatného, kontrolovat kvóty a v případě potřeby požádat o zvýšení platnosti a přijmout podmínky pro používání softwaru avere vFXT. Podrobné pokyny najdete [v tématu Příprava k vytvoření avere vFXT](avere-vfxt-prereqs.md) .

1. Vytvoření clusteru avere vFXT

   K vytvoření clusteru avere vFXT pro Azure použijte Azure Marketplace. Šablona shromažďuje požadované informace a spouští skripty pro vytvoření konečného produktu.

   Vytvoření clusteru zahrnuje tyto kroky, které jsou všechny dokončené šablonou Marketplace:

   * Vytvoření nové síťové infrastruktury a skupin prostředků (v případě potřeby)
   * Vytvoření *řadiče clusteru*  

     Řadič clusteru je jednoduchý virtuální počítač, který se nachází ve stejné virtuální síti jako cluster avere vFXT, a má vlastní software potřebný k vytvoření a správě clusteru. Kontrolér vytvoří uzly vFXT a vytvoří cluster a také rozhraní příkazového řádku pro správu clusteru během své životnosti.

     Pokud během nasazení vytvoříte novou virtuální síť nebo podsíť, bude mít řadič veřejnou IP adresu. To znamená, že řadič může sloužit jako skokový hostitel pro připojení ke clusteru mimo podsíť.

   * Vytvoření virtuálních počítačů uzlu clusteru

   * Konfigurace virtuálních počítačů uzlů clusteru pro vytvoření clusteru

   * Volitelně můžete vytvořit nový kontejner objektů BLOB a nakonfigurovat ho jako back-end úložiště pro cluster.

1. Konfigurace clusteru

   K přizpůsobení nastavení clusteru se připojte k rozhraní avere vFXT Configuration Interface (avere Control Panel). Pokud používáte místní datové centrum, přihlaste se ke sledování podpory a přidejte svůj systém úložiště.

   * [Přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md)
   * [Povolit podporu](avere-vfxt-enable-support.md)
   * [Konfigurace úložiště](avere-vfxt-add-storage.md) (v případě potřeby)

1. Připojení klientů

   Pokud se chcete dozvědět o vyrovnávání zatížení a jak by měly klientské počítače připojit cluster, postupujte podle pokynů v tématu [připojení clusteru avere vFXT](avere-vfxt-mount-clients.md) .

1. Přidat data (v případě potřeby)

   Vzhledem k tomu, že avere vFXT je škálovatelná mezipaměť s více klienty, nejlepším způsobem, jak přesunout data do nového záložního kontejneru úložiště, je s více klienty, strategii kopírování souborů s více vlákny.
   
   Pokud potřebujete přesunout pracovní sadu dat do nového kontejneru objektů BLOB nebo jiného záložního úložného systému, postupujte podle pokynů v tématu [přesun dat do clusteru vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Další kroky

Pokračujte v [přípravě na vytvoření avere vFXT](avere-vfxt-prereqs.md) k dokončení požadovaných úloh.
