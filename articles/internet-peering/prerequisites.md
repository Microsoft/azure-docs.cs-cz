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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775404"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Předpoklady pro nastavení partnerského vztahu s Microsoftem

Před vyžádáním nového partnerského vztahu nebo převodem staršího partnerského vztahu na prostředek Azure se ujistěte, že jsou splněné předpoklady.

## <a name="azure-related-prerequisites"></a>Požadavky související s Azure
* **Účet Microsoft Azure:** Pokud nemáte účet Microsoft Azure, vytvořte [účet Microsoft Azure](https://azure.microsoft.com/free). K nastavení partnerského vztahu se vyžaduje platné a aktivní Microsoft Azure předplatné, protože partnerské vztahy se modelují jako prostředky v rámci předplatných Azure. Je důležité si uvědomit, že:
    * Typy prostředků Azure, které se používají k nastavení partnerského vztahu, jsou vždycky bezplatné produkty Azure, tj. neúčtují se vám žádné poplatky za vytváření účtů Azure nebo vytváření předplatného a přístup k prostředkům Azure **PeerAsn** a **partnerským vztahům** k nastavení partnerského vztahu. Nemusíte se zaměňovat s dohodou o partnerském vztahu pro přímý partnerský vztah mezi vámi a společností Microsoft, přičemž tyto smlouvy jsou výslovně popsané s naším týmem partnerských vztahů. Pokud máte v tomto ohledu jakékoli otázky, obraťte se na [partnerský vztah Microsoftu](mailto:peering@microsoft.com) .
    * Stejné předplatné Azure můžete použít pro přístup k dalším produktům Azure nebo cloudovým službám, které můžou být bezplatné nebo placené. Při přístupu k placenému produktu se vám budou účtovat poplatky.
    * Pokud vytváříte nový účet Azure nebo předplatné, můžete mít nárok na bezplatný kredit Azure během zkušebního období, které můžete využít k vyzkoušení Azure Cloud Services. Pokud vás zajímá, přečtěte si článek [Microsoft Azure účet](https://azure.microsoft.com/free) , kde najdete další informace.

* **Přidružit partnerské číslo ASN:** Před vyžádáním partnerského vztahu nejdřív přidružte své číslo ASN a kontaktní údaje k vašemu předplatnému. Postupujte podle pokynů v tématu [přidružení partnerského čísla ASN k předplatnému Azure](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Další požadavky
* **PeeringDB profil:** U partnerských vztahů se očekává, že budou mít úplný a aktuální profil pro [PeeringDB](https://www.peeringdb.com). Tyto informace používáme v našem registračním systému k ověření detailů partnerského vztahu, jako jsou NOC informace, technické kontaktní informace a jejich přítomnost v zařízeních partnerských vztahů atd.

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md).
* [Převod starší verze peeringu typu Direct na prostředek Azure s využitím portálu](howto-legacy-direct-portal.md)
* [Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí portálu](howto-exchange-portal.md)
* [Převod starší verze peeringu typu Exchange na prostředek Azure s využitím portálu](howto-legacy-exchange-portal.md)