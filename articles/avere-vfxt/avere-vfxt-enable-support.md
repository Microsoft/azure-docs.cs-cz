---
title: Povolení podpory pro Avere vFXT – Azure
description: Jak povolit nahrávání podpory z Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75415371"
---
# <a name="enable-support-uploads"></a>Povolení nahrávání podpory

Avere vFXT pro Azure může automaticky nahrávat data podpory o vašem clusteru. Tato nahrávání umožňují pracovníkům podpory poskytovat nejlepší možné služby zákazníkům.

## <a name="steps-to-enable-uploads"></a>Postup povolení nahrávání

Pomocí následujících kroků z ovládacího panelu Avere aktivujte podporu. (Pro čtení [přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md) se dozvíte, jak otevřít ovládací panel.)

1. Přejděte na kartu **Nastavení** nahoře.
1. Klikněte na odkaz **Podpora** vlevo a přijměte zásady ochrany osobních údajů.

   ![Snímek obrazovky s ovládacím panelem Avere a automaticky otevíraným oknem s tlačítkem Potvrdit pro přijetí zásad ochrany osobních údajů](media/avere-vfxt-privacy-policy.png)

1. Na stránce konfigurace podpory otevřete část **Informace o zákazníkovi** kliknutím na trojúhelník vlevo.
1. Klikněte na tlačítko **Znovu ověřit informace o nahrání.**
1. Nastavte název podpory clusteru v **části Jedinečný název clusteru**. Ujistěte se, že tento název jednoznačně identifikuje váš cluster pro podporu zaměstnanců.
1. Zaškrtněte **políčka Pro sledování statistiky**, **Nahrání obecných informací**a **Informace o selhání**.
1. Klepněte na **tlačítko Odeslat**.

   ![Snímek obrazovky obsahující dokončenou část informace o zákaznících na stránce nastavení podpory](media/avere-vfxt-support-info.png)

1. Kliknutím na trojúhelník vlevo od **Secure Proactive Support (SPS)** rozbalte oddíl.
1. Zaškrtněte políčko **Povolit odkaz SPS**.
1. Klepněte na **tlačítko Odeslat**.

   ![Snímek obrazovky obsahující dokončený oddíl Zabezpečené proaktivní podpory na stránce nastavení podpory](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Další kroky

Pokud potřebujete do clusteru přidat místní nebo existující systém cloudového úložiště, postupujte podle pokynů v [části Konfigurace úložiště](avere-vfxt-add-storage.md).

Pokud jste připraveni začít připojovat klienty ke clusteru, přečtěte [si připojení clusteru Avere vFXT](avere-vfxt-mount-clients.md).
