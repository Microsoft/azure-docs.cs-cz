---
title: Konfigurace provozní kontinuity a zotavení po havárii (BCDR) na Azure Stack hraniční virtuální privátní síť (VPN)
description: Popisuje, jak nakonfigurovat BCDR na Azure Stack hraniční síti VPN.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: a35a7e5e5c7eccf006f18badad88656e8bc73453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367686"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Konfigurace kontinuity podnikových aplikací a zotavení po havárii pro síť VPN Azure Stack Edge

[!INCLUDE [applies-to-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

Tento článek popisuje, jak nakonfigurovat provozní kontinuitu a zotavení po havárii (BCDR) ve virtuální privátní síti (VPN), která je nakonfigurovaná na Azure Stack hraničním zařízení.

Tento článek se týká Azure Stack Edge pro R a Azure Stack Edge Mini R.

## <a name="configure-failover-to-a-paired-region"></a>Konfigurace převzetí služeb při selhání na spárované oblasti

Vaše zařízení Azure Stack Edge používá další služby Azure, například Azure Storage. BCDR můžete nakonfigurovat na jakékoli konkrétní službě Azure, kterou používá zařízení Azure Stack Edge. Pokud služba Azure, kterou používá Azure Stack Edge, převezme služby při selhání do své spárované oblasti, zařízení Azure Stack Edge se nyní připojí k novým IP adresám a komunikace nebude dvakrát zašifrovaná. 

Zařízení Azure Stack Edge používá dělené tunelové propojení a veškerá data a služby konfigurovaná v oblasti domů (oblast přidružená k vašemu zařízení Azure Stack Edge) prostřednictvím tunelu VPN. Pokud služby Azure převezme služby při selhání do spárované oblasti, která je mimo oblast domů, data už nebudou přes síť VPN, a proto se nešifrují dvakrát. 

V tomto scénáři jsou ovlivněné jenom několik služeb Azure. V případě, že chcete tento problém vyřešit, je třeba provést následující změny v konfiguraci sítě VPN Azure Stack Edge:

1. Přidejte rozsahy IP adres služby Azure s podporou převzetí služeb při selhání v části trasy pro síť VPN na Azure Stack Edge. Služby se pak začnou směrovat přes síť VPN.

    Chcete-li přidat zahrnuté trasy, je nutné stáhnout soubor JSON, který obsahuje trasy specifické pro službu. Nezapomeňte tento soubor aktualizovat novými trasami.
2. Přidejte odpovídající rozsahy IP adres služby Azure v tabulce směrování Azure.
3. Přidejte trasy do brány firewall.

> [!NOTE]
>
> 1. Převzetí služeb při selhání služby Azure VPN Gateway a Azure Virtual Network (VNET) se řeší v oddílu [obnovení z oblasti Azure, která selhala z důvodu havárie](#recover-from-a-failed-azure-region).
> 2. Rozsahy IP adres přidané v tabulce směrování Azure by mohly překročit limit 400. Pokud k tomu dojde, budete muset postupovat podle pokynů v části a [přesunout se z jedné oblasti Azure do jiné oblasti Azure](#move-from-an-azure-region-to-another).

## <a name="recover-from-a-failed-azure-region"></a>Obnovení z neúspěšné oblasti Azure

V případě, že dojde k selhání celé oblasti Azure v důsledku závažné události, jako je například zemětřesení, budou služby Azure v této oblasti, včetně služby Azure Stack Edge, převzetí služeb při selhání. Vzhledem k tomu, že existuje více služeb, mohou být trasy z celkového rozsahu snadno dostupné v několika stovkách. Azure má omezení 400 tras. 

Když dojde k převzetí služeb při selhání, virtuální síť (VNET) převezme služby při selhání do nové oblasti, a proto bránu virtuální sítě (VPN Gateway). Pokud chcete tuto změnu vyřešit, proveďte v konfiguraci Azure Stack hraniční sítě VPN následující změny:

1. Přesuňte virtuální síť do cílové oblasti. Další informace najdete v tématu: [přesunutí virtuální sítě Azure do jiné oblasti prostřednictvím Azure Portal](../virtual-network/move-across-regions-vnet-portal.md).
2. Nasaďte novou bránu VPN Azure v cílové oblasti, kam jste přesunuli virtuální síť. Další informace najdete v tématu [Vytvoření brány virtuální sítě](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
3. Aktualizujte konfiguraci sítě VPN Azure Stack Edge tak, aby používala výše uvedenou bránu VPN v připojení VPN, a pak vyberte cílovou oblast a přidejte trasy, které používají bránu VPN.
4. Pokud se změní i fond adres klienta, aktualizujte příchozí tabulku směrování Azure. 

## <a name="move-from-an-azure-region-to-another"></a>Přechod z oblasti Azure do jiné

Zařízení Azure Stack Edge můžete přesunout z jednoho místa do jiného umístění. Pokud chcete použít oblast, která je nejblíže místu, kde je vaše zařízení nasazené, budete muset nakonfigurovat zařízení pro novou oblast domů. Proveďte následující změny:

1. Můžete aktualizovat konfiguraci sítě VPN Azure Stack Edge tak, aby používala novou bránu sítě VPN nové oblasti, a výběrem nové oblasti přidáte trasy, které používají bránu VPN.

## <a name="next-steps"></a>Další kroky

[Zálohujte zařízení Azure Stack Edge](azure-stack-edge-gpu-prepare-device-failure.md).