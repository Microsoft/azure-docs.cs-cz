---
title: Připojení dat brány firewall webových aplikací k Azure Sentinelu
description: Přečtěte si, jak připojit data brány firewall webových aplikací Microsoftu k Azure Sentinelu.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588174"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Připojení dat z brány firewall webové aplikace společnosti Microsoft



Protokoly můžete streamovat z brány firewall webové aplikace Azure Application Gateway pro microsoft (WAF). Tento waf chrání vaše aplikace před běžnými webovými chybami zabezpečení, jako je vkládání SQL a skriptování mezi weby, a umožňuje přizpůsobit pravidla pro snížení falešných poplachů. Podle těchto pokynů můžete streamovat protokoly brány firewall webových aplikací Microsoft do Azure Sentinelu.


## <a name="prerequisites"></a>Požadavky

- Existující prostředek aplikační brány

## <a name="connect-to-microsoft-web-application-firewall"></a>Připojení k bráně firewall webových aplikací společnosti Microsoft

Pokud již máte bránu firewall webové aplikace společnosti Microsoft, ujistěte se, že máte existující prostředek brány.
Po nasazení brány firewall webových aplikací Microsoftu a získání dat lze data výstrah snadno streamovat do Azure Sentinelu.
    
1. Na portálu Azure Sentinel vyberte **Datové konektory**.
1. Na stránce Datové konektory vyberte dlaždici **WAF.**
1. Přejděte na [prostředek](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) Aplikační brány a zvolte waf.
    1. Vyberte **Nastavení diagnostiky**.
    1. Vyberte **+ Přidat diagnostické nastavení** pod tabulkou.
    1. Na stránce **Nastavení diagnostiky** zadejte **příkaz Název** a vyberte **Odeslat do analýzy protokolů**.
    1. V části **Pracovní prostor Analýzy protokolů** vyberte pracovní prostor Azure Sentinel.
    1. Vyberte typy protokolů, které chcete analyzovat. Doporučujeme: ApplicationGatewayAccessLog a ApplicationGatewayFirewallLog.
1. Chcete-li použít příslušné schéma v Log Analytics pro výstrahy brány firewall webové aplikace Microsoft, vyhledejte **AzureDiagnostics**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se naučili, jak připojit bránu firewall webové aplikace Microsoft k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
