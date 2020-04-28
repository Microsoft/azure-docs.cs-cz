---
title: Povolení podpory pro avere vFXT – Azure
description: Jak povolit nahrávání podpory z avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75415371"
---
# <a name="enable-support-uploads"></a>Povolení nahrávání podpory

Avere vFXT for Azure může automaticky nahrávat data podpory pro váš cluster. Tato nahrávání umožňují pracovníkům podpory poskytovat nejlepší možné služby zákazníkům.

## <a name="steps-to-enable-uploads"></a>Postup povolení odesílání

Chcete-li aktivovat podporu, postupujte podle těchto kroků v Ovládacích panelech avere. (K získání informací o tom, jak otevřít ovládací panely, získáte [přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md) .)

1. V horní části přejděte na kartu **Nastavení** .
1. Na levé straně klikněte na odkaz **Podpora** a přijměte zásady ochrany osobních údajů.

   ![Snímek obrazovky, který zobrazuje ovládací panel avere a automaticky otevírané okno s tlačítkem potvrdit pro přijetí zásad ochrany osobních údajů](media/avere-vfxt-privacy-policy.png)

1. Na stránce Konfigurace podpory otevřete část **informace o zákazníkovi** kliknutím na trojúhelník vlevo.
1. Klikněte na tlačítko znovu **ověřit informace o nahrání** .
1. V **jedinečném názvu clusteru**nastavte název podpory clusteru. Ujistěte se, že tento název jednoznačně identifikuje váš cluster pro podporu pracovníků.
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
