---
title: Přehled nasazení – Avere vFXT pro Azure
description: Přehled nasazení Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78140fea74272dff6056bebfbd44ed9d55b0e1db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153679"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT pro Azure – přehled nasazení

Tento článek poskytuje přehled kroků potřebných k získání Avere vFXT pro cluster Azure zprovoznění.

Před a po vytvoření clusteru vFXT z Azure Marketplace je potřeba několik úloh. Mít jasnou představu o procesu od začátku do konce vám pomůže rozsah potřebné úsilí.

## <a name="deployment-steps"></a>Kroky nasazení

Po [plánování systému](avere-vfxt-deploy-plan.md)můžete začít vytvářet cluster Avere vFXT.

Šablona Azure Resource Manager na Azure Marketplace shromažďuje potřebné informace a automaticky nasazuje celý cluster.

Po spuštění clusteru vFXT stále existují některé kroky konfigurace, které je třeba provést před jeho použitím. Pokud jste vytvořili nový kontejner úložiště objektů Blob, budete chtít přesunout data do něj. Pokud používáte systém úložiště NAS, musíte ho přidat po vytvoření clusteru. Budete chtít připojit klienty ke clusteru.

Zde je přehled všech kroků.

1. Konfigurace požadavků

   Před vytvořením virtuálního počítače musíte vytvořit nové předplatné pro projekt Avere vFXT, nakonfigurovat vlastnictví předplatného, zkontrolovat kvóty a v případě potřeby požádat o zvýšení a přijmout podmínky pro použití softwaru Avere vFXT. Podrobné pokyny najdete v části [Příprava k vytvoření avere vFXT.](avere-vfxt-prereqs.md)

1. Vytvoření clusteru Avere vFXT

   Azure Marketplace slouží k vytvoření clusteru Avere vFXT pro Azure. Šablona shromažďuje požadované informace a spouští skripty k vytvoření konečného produktu.

   Vytvoření clusteru zahrnuje tyto kroky, které jsou všechny provedené šablonou marketplace:

   * V případě potřeby vytvořte novou síťovou infrastrukturu a skupiny prostředků
   * Vytvoření řadiče clusteru

     Řadič clusteru je jednoduchý virtuální počítač, který se nachází ve stejné virtuální síti jako cluster Avere vFXT a má vlastní software potřebný k vytvoření a správě clusteru. Řadič vytvoří uzly vFXT a vytvoří cluster a také poskytuje rozhraní příkazového řádku pro správu clusteru během jeho životnosti.

     Pokud během nasazení vytvoříte novou virtuální síť nebo podsíť, bude mít řadič veřejnou IP adresu. To znamená, že řadič může sloužit jako hostitel skoku pro připojení ke clusteru mimo virtuální síť.

   * Vytvoření virtuálních virtuálních disponérů uzlů clusteru

   * Vytvoření clusteru z jednotlivých uzlů

   * Volitelně můžete vytvořit nový kontejner objektů Blob a nakonfigurovat jej jako back-endové úložiště pro cluster.

   Vytvoření clusteru je podrobně popsáno v [části Nasazení clusteru vFXT](avere-vfxt-deploy.md).

1. Konfigurace clusteru

   Připojte se ke konfiguračnímu rozhraní Avere vFXT (Ovládací panely Avere) a přizpůsobte tak nastavení clusteru. Přihlaste se k monitorování podpory a přidejte úložný systém, pokud používáte hardwarové úložiště nebo další kontejnery objektů Blob.

   * [Přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md)
   * [Povolit podporu](avere-vfxt-enable-support.md)
   * [Konfigurace úložiště](avere-vfxt-add-storage.md) (v případě potřeby)

1. Připojení klientů

   Postupujte podle pokynů v [připojení clusteru Avere vFXT](avere-vfxt-mount-clients.md) se dozvíte o vyrovnávání zatížení a jak by měly klientské počítače připojit cluster.

1. Přidání dat (v případě potřeby)

   Vzhledem k tomu, že Avere vFXT je škálovatelná mezipaměť s více klienty, nejlepší způsob, jak přesunout data do nového kontejneru úložiště back-end, je pomocí strategie kopírování souborů s více klienty s více vlákny.

   Pokud potřebujete přesunout data pracovní sady do nového kontejneru objektů Blob nebo jiného back-endového úložného systému, postupujte podle pokynů v [části Přesunutí dat do clusteru vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Další kroky

Pokračujte [v přípravě na vytvoření Avere vFXT](avere-vfxt-prereqs.md) k dokončení požadovaných úkolů.
