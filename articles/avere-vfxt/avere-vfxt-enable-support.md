---
title: Povolení podpory pro avere vFXT – Azure
description: Jak povolit nahrávání podpory z avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: ac7db46a681fcde6bfcbb7695e2d66724f738918
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256217"
---
# <a name="enable-support-uploads"></a>Povolení nahrávání podpory

Avere vFXT pro Azure může automaticky nahrávat data podpory pro váš cluster. Tato nahrávání umožňují pracovníkům podpory poskytovat nejlepší možné služby zákazníkům.

## <a name="steps-to-enable-uploads"></a>Postup povolení odesílání

Chcete-li aktivovat podporu, postupujte podle těchto kroků v Ovládacích panelech avere. (Pokud chcete zjistit, jak otevřít ovládací panel avere, přečtěte si téma [přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md) .)

1. V horní části přejděte na kartu **Nastavení** .
1. Na levé straně klikněte na odkaz **Podpora** a přijměte zásady ochrany osobních údajů.

   ![Snímek obrazovky, který zobrazuje ovládací panel avere a automaticky otevírané okno s tlačítkem potvrdit pro přijetí zásad ochrany osobních údajů](media/avere-vfxt-privacy-policy.png)

1. Kliknutím na trojúhelník nalevo od **informací o zákaznících** rozbalte oddíl.
1. Klikněte na tlačítko znovu **ověřit informace o nahrání** .
1. V **jedinečném názvu clusteru** nastavte název podpory clusteru – Ujistěte se, že jednoznačně identifikuje váš cluster pro podporu pracovníků.
1. Zaškrtněte políčka pro **monitorování statistiky**, **odesílání obecných informací**a **nahrávání informací o chybách**.
1. Klikněte na **Odeslat**.

   ![Snímek obrazovky s oddílem informace o dokončených zákaznících stránky nastavení podpory](media/avere-vfxt-support-info.png)

1. Kliknutím na trojúhelník nalevo od **zabezpečené proaktivní podpory (SPS)** rozbalte oddíl.
1. Zaškrtněte políčko **Povolit odkaz na SPS**.
1. Klikněte na **Odeslat**.

   ![Snímek obrazovky s dokončenou částí zabezpečená proaktivní podpora na stránce nastavení podpory](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Další kroky

Pokud potřebujete do clusteru přidat místní nebo existující cloudový úložný systém, postupujte podle pokynů v části [Konfigurace úložiště](avere-vfxt-add-storage.md). 

Pokud jste připraveni začít s připojením klientů ke clusteru, přečtěte si téma [připojit cluster avere vFXT](avere-vfxt-mount-clients.md).