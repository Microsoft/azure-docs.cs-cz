---
title: Safelist adresy URL Azure Portal | Microsoft Docs
description: Přidejte tyto adresy URL, aby bylo možné proxy server nepoužívat ke komunikaci s Azure Portal a jeho službami.
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 9618d99495e65231de22eb719355e2ca75a17600
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218037"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist adresy URL Azure Portal v bráně firewall nebo proxy server

Pro zajištění dobrého výkonu a připojení mezi vaší místní sítí a cloudem Azure nakonfigurujte místní zařízení zabezpečení tak, aby se pro adresy URL Azure Portal obcházela omezení zabezpečení. Správci sítě často nasazují proxy servery, brány firewall nebo jiná zařízení, aby pomohly zabezpečení a poskytovaly kontrolu nad tím, jak uživatelé přistupují k Internetu. Pravidla určená k ochraně uživatelů ale můžou někdy blokovat nebo zpomalit legitimní internetový provoz související s obchodem, včetně komunikace mezi vámi a Azure. K optimalizaci připojení mezi vaší sítí a Azure Portal a jejími službami doporučujeme přidat do svého Safelist Azure Portal adresy URL.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal adresy URL pro obejití proxy serveru

Koncové body adresy URL, které se Safelist pro Azure Portal, jsou specifické pro cloud Azure, ve kterém je vaše organizace nasazená. Vyberte svůj Cloud a pak přidejte seznam adres URL do proxy server nebo brány firewall, aby síťový provoz těchto koncových bodů mohl obejít omezení.

#### <a name="public-cloudtabpublic-cloud"></a>[Veřejný cloud](#tab/public-cloud)
```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloudtabus-government-cloud"></a>[STÁTŮ Cloud pro státní správu](#tab/us-government-cloud)
```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloudtabchina-government-cloud"></a>[Čína – Cloud pro státní správu](#tab/china-government-cloud)
```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Provoz do těchto koncových bodů používá standardní porty TCP pro protokol HTTP (80) a HTTPS (443).
>
>
## <a name="next-steps"></a>Další kroky

Potřebujete Safelist IP adresy? Stáhněte si seznam rozsahů IP adres datového centra Microsoft Azure pro svůj Cloud:

* [Po celém světě](https://www.microsoft.com/download/details.aspx?id=56519)
* [STÁTŮ Schod](https://www.microsoft.com/download/details.aspx?id=57063)
* [Německo](https://www.microsoft.com/download/details.aspx?id=57064)
* [Čína](https://www.microsoft.com/download/details.aspx?id=57062)

Další služby Microsoftu používají pro připojení další adresy URL a IP adresy. Informace o optimalizaci síťového připojení pro Microsoft 365 Services najdete v tématu [nastavení sítě pro Office 365](/office365/enterprise/set-up-network-for-office-365).
