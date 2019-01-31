---
title: Přehled nasazení – Avere vFXT pro Azure
description: Přehled nasazení Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 1be11fff7139b250e85fe15cec9082a2c85cf857
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298530"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT pro Azure – Přehled nasazení

Tento článek obsahuje přehled kroků, nutná, aby se Avere vFXT pro Azure clusteru nahoru a spuštěná.

Před a po vytvoření clusteru vFXT z Azure Marketplace, jsou potřeba několik úloh. S jasný smysl zahájení dokončení procesu vám pomůže určit obor úsilí potřebné. 

## <a name="deployment-steps"></a>Kroky nasazení

Po [plánování vašeho systému](avere-vfxt-deploy-plan.md), můžete začít vytvářet svůj cluster vFXT Avere. 

Šablony Azure Resource Manageru na webu Azure Marketplace shromažďuje informace potřebné a automaticky nasadí celý cluster. 

Jakmile je vFXT cluster zprovoznění, budete chtít vědět, jak se k němu připojte klienty a v případě potřeby, jak přesunout data do nového kontejneru úložiště objektů Blob.  

Tady je přehled o všech kroků.

1. Konfigurovat požadavky související s 

   Před vytvořením virtuálního počítače, musíte vytvořit nový odběr pro projekt vFXT Avere, nakonfigurovat vlastnictví předplatného, zkontrolujte kvóty a požádat o zvýšení v případě potřeby a přijměte podmínky a za používání softwaru vFXT Avere. Čtení [Příprava k tvorbě Avere vFXT](avere-vfxt-prereqs.md) podrobné pokyny.

1. Umožňuje vytvořit roli přístup pro uzly clusteru

   Azure používá [řízení přístupu na základě rolí](../role-based-access-control/index.yml) (RBAC) k autorizaci, uzlem clusteru virtuálních počítačů určených k provádění určitých úloh. Například uzly clusteru musí mít možnost přiřazení nebo znovu přiřadit IP adresy do jiných uzlů clusteru. Před vytvořením clusteru, je nutné definovat roli, která mu odpovídající oprávnění.

   Čtení [vytvořit role clusteru uzel přístupu](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) pokyny.

   Řadič clusteru také používá přístupu k roli však můžete přijmout výchozí role vlastník, místo vytvoření vlastní. Pokud chcete vytvořit vlastní role pro správce clusteru, přečtěte si [vlastní kontroler přístup role](avere-vfxt-controller-role.md). 

1. Vytvoření clusteru vFXT Avere 

   Pomocí webu Azure Marketplace můžete vytvořit vFXT Avere pro Azure cluster. Šablona shromažďuje požadované informace a spustí skripty k vytvoření konečného produktu.

   Vytvoření clusteru zahrnuje tyto kroky, které se provádějí pomocí šablony marketplace: 

   * Vytvoření nové sítě infrastruktury a skupiny prostředků, v případě potřeby
   * Vytváření *řadič clusteru*  

     Kontroler clusteru je jednoduchý virtuální počítač, který se nachází ve stejné virtuální síti jako Avere vFXT cluster a má vlastní software potřebné k vytváření a správě clusteru. Kontroler vytvoří cluster vFXT uzly a formuláře a také poskytuje rozhraní příkazového řádku ke správě clusteru během celé jeho životnosti.

     Při konfiguraci řadiče s použitím veřejné IP adresy, je také může sloužit jako jump hostitele pro připojení ke clusteru ze mimo virtuální síť.

   * Vytvoření clusteru virtuálních počítačů s uzlu
   * Konfigurace uzlu clusteru virtuálních počítačů jako clusteru
   * Volitelně vytvořili nový kontejner objektů Blob a jeho nakonfigurováním jako back endové úložiště pro cluster

1. Konfigurace clusteru 

   Připojení k rozhraní konfigurace vFXT Avere (Avere ovládacích panelech) Chcete-li přizpůsobit nastavení clusteru. Vyjádřit výslovný souhlas pro podporu monitorování a přidat váš systém úložiště, pokud používáte v místním datovém centru.

   * [Přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md)
   * [Povolení podpory](avere-vfxt-enable-support.md)
   * [Konfigurace úložiště](avere-vfxt-add-storage.md) (v případě potřeby)

1. Připojení klientů

   Postupujte podle pokynů v [připojení clusteru vFXT Avere](avere-vfxt-mount-clients.md) Další informace o vyrovnávání zatížení a jak by měly klientské počítače připojení clusteru.

1. Přidání dat (v případě potřeby)

   Avere vFXT je škálovatelná mezipaměti více klientů, je nejlepší způsob, jak přesunout data do nového kontejneru back endové úložné strategie kopírování souboru více klientů, s více vlákny. Čtení [přesun dat do clusteru vFXT](avere-vfxt-data-ingest.md) podrobnosti.

## <a name="next-steps"></a>Další postup

I nadále [Příprava k tvorbě Avere vFXT](avere-vfxt-prereqs.md) dokončit předběžné úlohy pro nasazení Avere vFXT pro Azure. 