---
title: Připojit data brány firewall webových aplikací Microsoft Azure Preview Sentinelu | Dokumentace Microsoftu
description: Zjistěte, jak se připojit k Azure ověřovací data brány firewall webových aplikací Microsoft.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5316fa7e3aa4465349b762b99bec9171f821062f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798993"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Připojíte si data z firewallu webových aplikací Microsoft

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete Streamovat protokoly z firewallu webových aplikací Microsoft Azure Application Gateway (WAF). Tato WAF chrání vaše aplikace před běžným webovým ohrožením zabezpečení jako jsou Injektáž SQL a skriptování napříč weby a umožňuje přizpůsobit pravidla, která snížil počet falešných poplachů. Postupujte podle těchto pokynů ke streamování protokolů brány firewall na aplikace, Microsoft Web do Azure Sentinelu.


## <a name="prerequisites"></a>Požadavky

- Existující prostředek služby application gateway

## <a name="connect-to-microsoft-web-application-firewall"></a>Připojte se k Microsoft firewallu webových aplikací

Pokud už máte brány firewall webových aplikací Microsoftu, ujistěte se, že máte existující prostředek brány.
Po nasazení vašeho firewallu webových aplikací Microsoft a získávání dat, data upozornění můžete snadno Streamovat do ověřovacích Azure.
    
1. Na portálu Azure Sentinelu vyberte **datové konektory**.
1. Na stránce konektory dat, vyberte **WAF** dlaždici.
1. Přejděte na [prostředku Application Gateway](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) a vyberte svou bránu WAF.
    1. Vyberte **nastavení diagnostiky**.
    1. Vyberte **+ přidat nastavení diagnostiky** pod tabulkou.
    1. V **nastavení diagnostiky** stránky, zadejte **název** a vyberte **odesílat do Log Analytics**.
    1. V části **pracovní prostor Log Analytics** vyberte pracovní prostor Sentinelu Azure.
    1. Vyberte typy protokolů, které chcete analyzovat. Doporučujeme: ApplicationGatewayAccessLog a ApplicationGatewayFirewallLog.
1. Použít příslušné schéma v Log Analytics pro výstrahy brány firewall webové aplikace Microsoft, vyhledejte **AzureDiagnostics**.

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak připojit Microsoft firewallu webových aplikací k Sentinelu Azure. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).
