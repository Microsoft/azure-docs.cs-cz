---
title: Konfigurace řízení přístupu k síti
titleSuffix: Azure SignalR Service
description: Nakonfigurujte řízení přístupu k síti pro službu Azure Signal.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 24e56736e0d033420f9aaf976b0fb7d9727c1a5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92143217"
---
# <a name="configure-network-access-control"></a>Konfigurace řízení přístupu k síti

Služba signalizace Azure vám umožňuje zabezpečit a řídit úroveň přístupu ke koncovému bodu služby na základě typu požadavku a podmnožiny používaných sítí. Při konfiguraci síťových pravidel mají přístup ke službě Azure Signaler jenom aplikace požadující data přes zadanou sadu sítí.

Služba signalizace Azure má veřejný koncový bod, který je přístupný prostřednictvím Internetu. [Pro službu Azure Signal Service můžete také vytvořit soukromé koncové body](howto-private-endpoints.md). Privátní koncový bod přiřadí privátní IP adresu z vaší virtuální sítě ke službě Azure Signal a zabezpečuje veškerý provoz mezi vaší virtuální sítí a službou Azure Signal prostřednictvím privátního odkazu. Řízení přístupu k síti služby Azure Signal Service poskytuje řízení přístupu pro veřejný koncový bod i privátní koncové body.

Volitelně můžete zvolit, že chcete povolit nebo zakázat určité typy žádostí pro veřejný koncový bod a každý privátní koncový bod. Můžete například blokovat všechna [připojení serveru](signalr-concept-internals.md#server-connections) z veřejného koncového bodu a ujistit se, že pocházejí jenom z konkrétní virtuální sítě.

Aplikace, která přistupuje ke službě Azure Signal, když jsou pravidla řízení přístupu k síti platná, vyžaduje pro požadavek správnou autorizaci.

## <a name="scenario-a---no-public-traffic"></a>Scénář A – bez veřejného provozu

Pokud chcete úplně Odepřít všechny veřejné přenosy, musíte nejdřív nakonfigurovat pravidlo veřejné sítě, aby nepovolovala žádný typ žádosti. Pak byste měli nakonfigurovat pravidla, která udělí přístup k provozu z konkrétních virtuální sítě. Tato konfigurace umožňuje vytvořit zabezpečenou hranici sítě pro vaše aplikace.

## <a name="scenario-b---only-client-connections-from-public-network"></a>Scénář B: připojení klientů z veřejné sítě

V tomto scénáři můžete nakonfigurovat pravidlo veřejné sítě tak, aby povolovalo pouze [připojení klientů](signalr-concept-internals.md#client-connections) z veřejné sítě. Pak můžete nakonfigurovat pravidla privátní sítě tak, aby povolovala jiné typy požadavků pocházejících z konkrétní virtuální sítě. Tato konfigurace skryje vaše aplikační servery z veřejné sítě a vytvoří zabezpečená připojení mezi vašimi aplikačními servery a službou Azure Signal.

## <a name="managing-network-access-control"></a>Správa řízení přístupu k síti

Řízení přístupu k síti pro službu Azure Signal Service můžete spravovat prostřednictvím Azure Portal.

### <a name="azure-portal"></a>portál Azure

1. Přejít na službu Azure Signal, kterou chcete zabezpečit.

1. Klikněte na nabídku nastavení s názvem **řízení přístupu k síti**.

    ![Seznam ACL sítě na portálu](media/howto-network-access-control/portal.png)

1. Chcete-li upravit výchozí akci, přepněte tlačítko **Povolit/odepřít** .

    > [!TIP]
    > Výchozí akce je akce, kterou provedeme v případě, že se neshodují žádné pravidlo seznamu ACL. Například pokud je výchozí akce **Zamítnutá**, pak typy požadavků, které nejsou explicitně schváleny, budou odepřeny.

1. Chcete-li upravit pravidlo veřejné sítě, vyberte možnost povolené typy požadavků v rámci **veřejné sítě**.

    ![Upravit seznam ACL veřejné sítě na portálu ](media/howto-network-access-control/portal-public-network.png)

1. Pokud chcete upravit pravidla sítě privátního koncového bodu, vyberte v části **připojení privátního koncového bodu** povolené typy požadavků v každém řádku.

    ![Upravit seznam ACL privátního koncového bodu na portálu ](media/howto-network-access-control/portal-private-endpoint.png)

1. Kliknutím na **Uložit** použijte změny.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [privátních odkazech Azure](../private-link/private-link-overview.md).