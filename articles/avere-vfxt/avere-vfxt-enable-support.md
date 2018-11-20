---
title: Povolení podpory pro Avere vFXT – Azure
description: Jak povolit podporu ukládání z Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 0f5eee20b0487fb5fce82047f40d137effb87ead
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164418"
---
# <a name="enable-support-uploads"></a>Povolení nahrávání podpory

Data podpory o clusteru můžete nahrát automaticky vFXT Avere pro Azure. Tyto nahrávání nechat pracovníci podpory poskytují nejlepší možné péče.

## <a name="steps-to-enable-uploads"></a>Postup povolení nahrávání

Následujícím postupem z ovládacího panelu Avere k aktivaci podpory. (Čtení [přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md) se naučíte, chcete-li otevřít ovládací Panel Avere.)

1. Přejděte **nastavení** kartě v horní části.
1. Klikněte na tlačítko **podporu** odkaz na levé straně a přijměte zásady ochrany osobních údajů.

   ![Snímek obrazovky zobrazující Avere ovládací panely a automaticky otevírané okno s tlačítkem pro potvrzení přijímat zásady ochrany osobních údajů](media/avere-vfxt-privacy-policy.png)

1. Klikněte na trojúhelník nalevo od **informace o zákaznících** rozbalte v části.
1. Klikněte na tlačítko **Revalidate odesílat informace** tlačítko.
1. Nastavení podpory názvu clusteru v **jedinečný název clusteru** – Ujistěte se, že ho jednoznačně identifikuje clusteru pracovníci podpory.
1. Zaškrtněte políčka pro **statistiky monitorování**, **obecné informace o nahrání**, a **havárií nahrát informace**.
1. Klikněte na tlačítko **odeslat**.

   ![Snímek obrazovky, který obsahuje oddíl informací o dokončené Zákaznická podpora nastavení stránky](media/avere-vfxt-support-info.png)

1. Klikněte na trojúhelník nalevo od **zabezpečení proaktivní podpory (SPS)** rozbalte v části.
1. Zaškrtněte políčko u **odkaz povolit aktualizace Service PACKU**.
1. Klikněte na tlačítko **odeslat**.

   ![Snímek obrazovky obsahující dokončené zabezpečení proaktivní podporují oddíl na stránce nastavení podpory](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Další postup

Pokud je potřeba přidat místnímu systému úložiště do clusteru, postupujte podle pokynů v [konfigurace úložiště](avere-vfxt-add-storage.md). 

Pokud jste připravení začít s připojení klientů ke clusteru, přečtěte si [připojení clusteru vFXT Avere](avere-vfxt-mount-clients.md).