---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 09f642a4c96781276d225cba37d71386d429d0c9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004131"
---
Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Pokud už máte virtuální síť, ke které se chcete připojit, zkontrolujte, jestli se žádná z podsítí vaší místní sítě nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Virtuální síť nevyžaduje podsíť s bránou a nesmí mít žádné brány virtuální sítě. Pokud nemáte virtuální síť, můžete ji vytvořit pomocí postupu v tomto článku.
* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Rozbočovač je virtuální síť a rozsah adres, který pro oblast rozbočovače zadáte, se nesmí překrývat s žádnými existujícími virtuálními sítěmi, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.