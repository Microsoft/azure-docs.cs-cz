---
title: Povolení podpory pro Avere vFXT – Azure
description: Jak povolit podporu ukládání z Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: bc21e06b704bfe3d25132092efbbf23f342acb14
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633782"
---
# <a name="enable-support-uploads"></a>Povolení podpory nahrávání

Data podpory o clusteru můžete nahrát automaticky vFXT Avere pro Azure. Tyto nahrávání nechat pracovníci podpory poskytují nejlepší možné péče.

## <a name="steps-to-enable-uploads"></a>Postup povolení nahrávání

Následujícím postupem z ovládacího panelu Avere k aktivaci podpory. (Čtení [přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md) se naučíte, chcete-li otevřít ovládací Panel Avere.)

1. Přejděte **nastavení** kartě v horní části.
1. Klikněte na tlačítko **podporu** odkaz na levé straně a přijměte zásady ochrany osobních údajů.

   ![Snímek obrazovky k potvrzení přijetí zásady ochrany osobních údajů](media/avere-vfxt-privacy-policy.png)
1. Klikněte na trojúhelník nalevo od **informace o zákaznících** rozbalte v části.
1. Nastavení podpory názvu clusteru v **jedinečný název clusteru** – Ujistěte se, že ho jednoznačně identifikuje clusteru pracovníci podpory.
1. Zaškrtněte políčka pro **statistiky monitorování**, **obecné informace o nahrání**, a **havárií nahrát informace**.
1. Klikněte na tlačítko **ověřit informace o odeslání** tlačítko.
1. Klikněte na tlačítko **odeslat**.
1. Klikněte na trojúhelník nalevo od **zabezpečení proaktivní podpory (SPS)** rozbalte v části.
1. Zaškrtněte políčko u **odkaz povolit aktualizace Service PACKU**.
1. Klikněte na tlačítko **odeslat**.

   ![Snímek obrazovky, který obsahuje všechny kroky pro povolení podpory](media/avere-vfxt-support-info-steps.png)


## <a name="next-steps"></a>Další postup

Pokud je potřeba přidat místnímu systému úložiště do clusteru, postupujte podle pokynů v [konfigurace úložiště](avere-vfxt-add-storage.md). 

Pokud jste připravení začít s připojení klientů ke clusteru, přečtěte si [připojení clusteru vFXT Avere](avere-vfxt-mount-clients.md).