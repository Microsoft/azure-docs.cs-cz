---
title: Přehled nasazení – Avere vFXT pro Azure
description: Přehled nasazení Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: aa5737d67ea2c9cb8cc7c7098764ae67fc91137d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633835"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT pro Azure – Přehled nasazení

Tento článek obsahuje přehled kroků, nutná, aby se Avere vFXT pro Azure clusteru nahoru a spuštěná.

Při prvním nasazení systému vFXT Avere, můžete si všimnout, že zahrnuje více kroků než nasazení většina jiných nástrojů Azure. S jasný smysl zahájení dokončení procesu vám pomůže určit obor úsilí potřebné. Jakmile je systém zprovoznit, jeho výkon ohledně urychlení jejich zpracování cloudové výpočetní úlohy filtrovacího řetězce se vyplácet.

## <a name="deployment-steps"></a>Kroky nasazení

Po [plánování vašeho systému](avere-vfxt-deploy-plan.md), můžete začít vytvářet svůj cluster vFXT Avere. 

Začněte tím, že vytvoření kontroleru clusteru virtuálního počítače, který se používá k vytvoření clusteru vFXT.

Jakmile je vFXT cluster zprovoznění, budete chtít vědět, jak se k němu připojte klienty a v případě potřeby, jak přesunout data do nového kontejneru úložiště objektů Blob.  

Tady je přehled o všech kroků.

1. Konfigurovat požadavky související s 

   Před vytvořením virtuálního počítače, musíte vytvořit nový odběr pro projekt vFXT Avere, nakonfigurovat vlastnictví předplatného, zkontrolujte kvóty a požádat o zvýšení v případě potřeby a přijměte podmínky a za používání softwaru vFXT Avere. Čtení [Příprava k tvorbě Avere vFXT](avere-vfxt-prereqs.md) podrobné pokyny.

1. Vytvoření clusteru kontroleru

   *Řadič clusteru* je jednoduchý virtuální počítač, který se nachází ve stejné virtuální síti jako Avere vFXT cluster. Kontroler vytvoří cluster vFXT uzly a formuláře a také poskytuje rozhraní příkazového řádku ke správě clusteru během celé jeho životnosti.

   Při konfiguraci řadiče s použitím veřejné IP adresy, je také může sloužit jako jump hostitele pro připojení ke clusteru ze mimo virtuální síť.

   Veškerý software, které jsou potřeba k vytvoření clusteru vFXT a správě jeho uzly je předinstalován v řadiči clusteru.

   Čtení [vytvoření virtuálního počítače řadiče clusteru](avere-vfxt-deploy.md#create-the-cluster-controller-vm) podrobnosti.

1. Umožňuje vytvořit roli modulu runtime pro uzly clusteru 

   Azure používá [řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) k autorizaci, uzlem clusteru virtuálních počítačů určených k provádění určitých úloh. Například uzly clusteru musí mít možnost přiřazení nebo znovu přiřadit IP adresy do jiných uzlů clusteru. Před vytvořením clusteru, je nutné definovat roli, která mu odpovídající oprávnění.

   Kontroler clusteru předinstalovaný software obsahuje prototyp role si můžete přizpůsobit. Čtení [vytvořit role clusteru uzel přístupu](avere-vfxt-deploy.md#create-the-cluster-node-access-role) pokyny.

1. Vytvoření clusteru vFXT Avere 

   V řadiči upravit skript pro vytvoření příslušného clusteru a proveďte jej k vytvoření clusteru. [Upravit skript nasazení](avere-vfxt-deploy.md#edit-the-deployment-script) obsahuje podrobné pokyny. 

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