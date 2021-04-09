---
title: Neměřená priorita směrování v Azure
description: Přečtěte si, jak můžete nakonfigurovat předvolby směrování pro vaše prostředky, které budou mít data na výstupu, od poskytovatele CDN.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about enabling routing preference for my CDN origin resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 00e0d1afa2aa035fe27e73dae60e291ea53bd6b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105042845"
---
# <a name="what-is-routing-preference-unmetered"></a>Co je neměřená priorita směrování?

Neměřená priorita směrování je k dispozici pro poskytovatele Content Delivery Network (CDN) se zákazníky, kteří hostují jeho původní obsah v Azure. Služba umožňuje poskytovatelům CDN navázat přímé připojení partnerského vztahu s globálním hraničním směrovačem Microsoftu v různých umístěních.

![Neměřená priorita směrování](media/routing-preference-unmetered/unmetered.png)

Provoz vaší sítě v provozu od počátku v Azure, který je určený pro výhody poskytovatele CDN z přímého připojení.
* Poplatky za přenos dat z vašich prostředků Azure, které jsou směrovány prostřednictvím těchto přímých odkazů, jsou bezplatné.
* Přímé propojení mezi poskytovatelem sítě CDN a zdrojem v Azure poskytuje optimální výkon, protože mezi nimi nejsou žádné segmenty směrování. To je výhodné pro úlohy CDN, které často načítají data ze zdroje.

## <a name="configuring-routing-preference-unmetered"></a>Konfigurace neměřené předvolby směrování

Aby bylo možné využít výhody směrování bez měření, musí být poskytovatelé CDN součástí tohoto programu. Pokud poskytovatel CDN není součástí programu, obraťte se na poskytovatele CDN.

Dále nakonfigurujte předvolby směrování pro vaše prostředky a nastavte typ předvolby směrování na **Internet**. Můžete nakonfigurovat předvolby směrování při vytváření veřejné IP adresy a potom přidružit veřejnou IP adresu k prostředkům, jako jsou virtuální počítače, internetové nástroje pro vyrovnávání zatížení a další. [Naučte se konfigurovat předvolby směrování pro veřejnou IP adresu pomocí Azure Portal](routing-preference-portal.md)

Můžete také povolit předvolby směrování pro svůj účet úložiště a publikovat druhý koncový bod, který musí poskytovatel CDN použít k načtení dat ze zdroje úložiště. Například publikování koncového bodu specifického pro internetovou trasu pro účet úložiště *StorageAccountA* bude publikovat druhý koncový bod pro vaše služby úložiště, jak je znázorněno níže:

![Předvolby směrování pro účty úložiště](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Další kroky

* [Konfigurace předvolby směrování pro virtuální počítač pomocí Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Konfigurace předvolby směrování pro virtuální počítač pomocí Azure CLI](configure-routing-preference-virtual-machine-cli.md)
* [Konfigurace předvolby směrování pro váš účet úložiště](../storage/common/network-routing-preference.md)