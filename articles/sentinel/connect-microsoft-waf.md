---
title: Připojit data brány firewall webových aplikací od Microsoftu do Azure Sentinel | Microsoft Docs
description: Naučte se připojit data brány firewall webových aplikací od Microsoftu ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: e7dc1e6c1bb1ca81ada59cb3dae8fecbc6452b7f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029789"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Připojení dat z firewallu webových aplikací Microsoftu



Protokoly můžete zasílat z brány firewall webových aplikací (WAF) v Azure Application Gateway. Tento WAF chrání vaše aplikace před běžnými webovými ohroženími zabezpečení, jako jsou například injektáže SQL a skriptování mezi weby, a umožňuje přizpůsobit pravidla pro omezení falešně pozitivních hodnot. Podle těchto pokynů můžete streamovat protokoly brány firewall webových aplikací Microsoftu do Azure Sentinel.


## <a name="prerequisites"></a>Požadavky

- Existující prostředek aplikační brány

## <a name="connect-to-microsoft-web-application-firewall"></a>Připojení k firewallu webových aplikací společnosti Microsoft

Pokud již máte bránu firewall webových aplikací Microsoft, ujistěte se, že máte existující prostředek brány.
Jakmile je vaše brána firewall webových aplikací Microsoftu nasazená a načítá data, můžou se data výstrah do Azure Sentinel snadno streamovat.
    
1. Na portálu Sentinel Azure vyberte **datové konektory**.
1. Na stránce datové konektory vyberte dlaždici **WAF** .
1. Přejít na [Application Gateway prostředek](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) a vyberte WAF.
    1. Vyberte **nastavení diagnostiky**.
    1. V tabulce vyberte **+ Přidat nastavení diagnostiky** .
    1. Na stránce **nastavení diagnostiky** zadejte **název** a vyberte **Odeslat do Log Analytics**.
    1. V části **Log Analytics pracovní prostor** vyberte pracovní prostor Azure Sentinel.
    1. Vyberte typy protokolů, které chcete analyzovat. Doporučujeme: ApplicationGatewayAccessLog a ApplicationGatewayFirewallLog.
1. Pokud chcete použít příslušné schéma v Log Analytics pro výstrahy brány firewall webových aplikací od Microsoftu, vyhledejte **AzureDiagnostics**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit bránu firewall webových aplikací od Microsoftu ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
