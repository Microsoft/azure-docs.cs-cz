---
title: Přehled nasazení – avere vFXT pro Azure
description: Přečtěte si, jak nasadit avere vFXT pro cluster Azure pomocí tohoto přehledu. Související články obsahují konkrétní pokyny k nasazení.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88271222"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT pro Azure – přehled nasazení

Tento článek obsahuje přehled kroků potřebných k zprovoznění služby avere vFXT pro cluster Azure.

Před a po vytvoření clusteru vFXT z Azure Marketplace je třeba provést několik úloh. Jasným smyslem procesu spuštění až po dokončení bude pomáhat s rozsahem potřebného úsilí.

## <a name="deployment-steps"></a>Kroky nasazení

Po [Naplánování systému](avere-vfxt-deploy-plan.md)můžete začít vytvářet cluster avere vFXT.

Azure Resource Manager šablona v Azure Marketplace shromažďuje potřebné informace a automaticky nasadí celý cluster.

Až bude cluster vFXT spuštěný, stále existují některé kroky konfigurace, které je nutné před jeho použitím provést. Pokud jste vytvořili nový kontejner úložiště objektů blob, budete na něj chtít přesunout data. Pokud používáte úložný systém NAS, budete ho muset přidat po vytvoření clusteru. Budete chtít připojit klienty ke clusteru.

Tady je přehled všech kroků.

1. Konfigurovat požadavky

   Před vytvořením virtuálního počítače musíte pro projekt avere vFXT vytvořit nové předplatné, nakonfigurovat vlastnictví předplatného, kontrolovat kvóty a v případě potřeby požádat o zvýšení platnosti a přijmout podmínky pro používání softwaru avere vFXT. Podrobné pokyny najdete [v tématu Příprava k vytvoření avere vFXT](avere-vfxt-prereqs.md) .

1. Vytvoření clusteru avere vFXT

   K vytvoření clusteru avere vFXT pro Azure použijte Azure Marketplace. Šablona shromažďuje požadované informace a spouští skripty pro vytvoření konečného produktu.

   Vytvoření clusteru zahrnuje tyto kroky, které jsou všechny dokončené šablonou Marketplace:

   * Vytvoření nové síťové infrastruktury a skupin prostředků (v případě potřeby)
   * Vytvoření řadiče clusteru

     Řadič clusteru je jednoduchý virtuální počítač, který se nachází ve stejné virtuální síti jako cluster avere vFXT, a má vlastní software potřebný k vytvoření a správě clusteru. Kontrolér vytvoří uzly vFXT a vytvoří cluster a také rozhraní příkazového řádku pro správu clusteru během své životnosti.

     Pokud během nasazení vytvoříte novou virtuální síť nebo podsíť, bude mít řadič veřejnou IP adresu. To znamená, že řadič může sloužit jako skokový hostitel pro připojení ke clusteru mimo virtuální síť.

   * Vytvoření virtuálních počítačů uzlu clusteru

   * Vytvoření clusteru z jednotlivých uzlů

   * Volitelně můžete vytvořit nový kontejner objektů BLOB a nakonfigurovat ho jako back-end úložiště pro cluster.

   Vytváření clusteru je podrobně popsáno v tématu [nasazení clusteru vFXT](avere-vfxt-deploy.md).

1. Konfigurace clusteru

   K přizpůsobení nastavení clusteru se připojte k rozhraní avere vFXT Configuration Interface (avere Control Panel). Pokud používáte hardwarové úložiště nebo další kontejnery objektů blob, přihlaste se ke sledování podpory a přidejte svůj systém úložiště.

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
