---
title: Předpoklady pro nastavení partnerského vztahu s Microsoftem
titleSuffix: Azure
description: Předpoklady pro nastavení partnerského vztahu s Microsoftem
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775404"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Předpoklady pro nastavení partnerského vztahu s Microsoftem

Ujistěte se, že níže uvedené požadavky jsou splněny před žádostí o nový partnerský vztah nebo převést starší partnerský vztah na prostředek Azure.

## <a name="azure-related-prerequisites"></a>Předpoklady související s Azure
* **Účet Microsoft Azure:** Pokud účet Microsoft Azure nemáte, vytvořte si [účet Microsoft Azure](https://azure.microsoft.com/free). K nastavení partnerského vztahu je potřeba platné a aktivní předplatné Microsoft Azure, protože partnerské partnerské servery jsou modelovány jako prostředky v rámci předplatných Azure. Je důležité si uvědomit, že:
    * Typy prostředků Azure používané k nastavení partnerského vztahu jsou vždy bez azure produkty, tj. **PeerAsn** **Peering** To není zaměňovat s peering dohody pro přímé peering mezi vámi a microsoft, podmínky, pro které jsou explicitně projednány s naším týmem partnerského vztahu. Pokud máte v tomto ohledu nějaké dotazy, obraťte se na [partnerský vztah společnosti Microsoft.](mailto:peering@microsoft.com)
    * Stejné předplatné Azure můžete použít pro přístup k dalším produktům Nebo cloudovým službám Azure, které můžou být zdarma nebo placené. Při přístupu k placenému produktu vám budou účtovány poplatky.
    * Pokud vytváříte nový účet Azure nebo předplatné, můžete mít nárok na bezplatný kredit Azure během zkušebního období, které můžete využít k vyzkoušení služeb Azure Cloud. Pokud chcete, navštivte [účet Microsoft Azure,](https://azure.microsoft.com/free) kde najdete další informace.

* **Přidružení hlavní ho přidružení partnera ASN:** Než požádáte o partnerský vztah, nejprve přidružte k vašemu předplatnému asn a kontaktní údaje. Postupujte podle pokynů v [přidružené peer ASN k předplatnému Azure](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Další předpoklady
* **Profil PeeringDB:** Očekává se, že partneři budou mít úplný a aktuální profil v [PeeringDB](https://www.peeringdb.com). Tyto informace používáme v našem registračním systému k ověření podrobností partnera, jako jsou informace o NOC, technické kontaktní informace a jejich přítomnost v zařízeních peeringu atd.

## <a name="next-steps"></a>Další kroky

* [Vytvořte nebo upravte přímý partnerský vztah pomocí portálu](howto-direct-portal.md).
* [Převod starší verze peeringu typu Direct na prostředek Azure s využitím portálu](howto-legacy-direct-portal.md)
* [Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu](howto-exchange-portal.md)
* [Převod starší verze peeringu typu Exchange na prostředek Azure s využitím portálu](howto-legacy-exchange-portal.md)