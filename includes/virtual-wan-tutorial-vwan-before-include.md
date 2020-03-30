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
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74896609"
---
Před zahájením konfigurace ověřte, zda splňujete následující kritéria:

* Pokud už máte virtuální síť, ke které se chcete připojit, ověřte, že se s ní žádná z podsítí místní sítě nepřekrývá. Vaše virtuální síť nevyžaduje podsíť brány a nemůže mít žádné brány virtuální sítě. Pokud nemáte virtuální síť, můžete ji vytvořit pomocí kroků v tomto článku.
* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Rozbočovač je virtuální síť a rozsah adres, který zadáte pro oblast rozbočovače, se nemůže překrývat s existující virtuální sítí, ke které se připojujete. Také se nemůže překrývat s rozsahy adres, které se připojujete k místnímu. Pokud nejste obeznámeni s rozsahy IP adres umístěnými v místní konfiguraci sítě, koordinujte je s někým, kdo vám tyto podrobnosti může poskytnout.
* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.