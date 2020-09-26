---
title: Přidání adres URL webu Azure Portal na seznam bezpečných adres v bráně firewall nebo na proxy serveru
description: Přidejte tyto adresy URL, aby bylo možné proxy server nepoužívat ke komunikaci s Azure Portal a jeho službami.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 16acedc8fad97c1752d71c8643b1655015484e5d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330855"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Přidání adres URL webu Azure Portal na seznam bezpečných adres v bráně firewall nebo na proxy serveru

Můžete nakonfigurovat místní zařízení zabezpečení a obejít omezení zabezpečení pro Azure Portal adresy URL. Tato konfigurace může zlepšit výkon a konektivitu mezi vaší místní sítí a cloudem Azure.

Správci sítě často nasazují proxy servery, brány firewall nebo jiná zařízení. Tato zařízení vám pomůžou zabezpečit a poskytovat kontrolu nad tím, jak uživatelé přistupují k Internetu. Pravidla určená k ochraně uživatelů můžou někdy blokovat nebo zpomalit legitimní internetový provoz související s firmou. Tento provoz zahrnuje komunikaci mezi vámi a Azure. K optimalizaci připojení mezi vaší sítí a Azure Portal a jejími službami doporučujeme přidat do svého Safelist Azure Portal adresy URL.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal adresy URL pro obejití proxy serveru

Koncové body adresy URL, které se Safelist pro Azure Portal, jsou specifické pro cloud Azure, ve kterém je vaše organizace nasazená. Pokud chcete, aby síťový provoz do těchto koncových bodů mohl obejít omezení, vyberte svůj Cloud. Pak přidejte seznam adres URL do proxy server nebo brány firewall.

#### <a name="public-cloud"></a>[Veřejný cloud](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azurefd.net
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

#### <a name="us-government-cloud"></a>[Cloud pro státní správu USA](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Čína – Cloud pro státní správu](#tab/china-government-cloud)

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
