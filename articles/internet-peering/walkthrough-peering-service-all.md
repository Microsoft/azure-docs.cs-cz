---
title: Podrobný průvodce programem Peering Service Partner
titleSuffix: Azure
description: Podrobný průvodce programem Peering Service Partner
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 75ec1a4ededfea4f9b40461d69b6e16d947e6919
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84712475"
---
# <a name="peering-service-partner-walkthrough"></a>Podrobný průvodce programem Peering Service Partner

V této části najdete popis kroků, které poskytovatel potřebuje k povolení přímého partnerského vztahu pro službu partnerského vztahu.

## <a name="create-direct-peering-connection-for-peering-service"></a>Vytvoření připojení s přímým partnerským vztahem pro službu peering Service
Poskytovatelé služeb mohou rozšířit svůj geografický dosah vytvořením nového přímého partnerského vztahu, který podporuje službu peering Service. K tomu je potřeba
1. Staňte se partnerem služby peering Service, pokud ještě není
1. Při [vytváření nebo úpravách přímého partnerského vztahu pomocí portálu](howto-direct-portal.md)postupujte podle pokynů. Ujistěte se, že splňuje požadavek na vysokou dostupnost.
1. Pak postupujte podle kroků a [Povolte službu partnerského vztahu v přímém partnerském vztahu pomocí portálu](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Použít starší verze přímého partnerského vztahu pro službu partnerského vztahu
Pokud máte starší verzi přímého partnerského vztahu, kterou chcete použít k podpoře služby peering Service,
1. Staňte se partnerem služby peering Service, pokud ještě není.
1. Podle pokynů [převeďte starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md). V případě potřeby si pořídit další okruhy, aby splňovaly požadavky na vysokou dostupnost.
1. Pak postupujte podle kroků a [Povolte službu partnerského vztahu v přímém partnerském vztahu pomocí portálu](howto-peering-service-portal.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [zásadách partnerského vztahu](https://peering.azurewebsites.net/peering).
* Další informace o krocích pro nastavení přímých partnerských vztahů s Microsoftem najdete v postupu [přímého partnerského vztahu](walkthrough-direct-all.md).
* Pokud se chcete dozvědět o krocích pro nastavení partnerského vztahu serveru Exchange s Microsoftem, postupujte podle pokynů pro [partnerský vztah Exchange](walkthrough-exchange-all.md).