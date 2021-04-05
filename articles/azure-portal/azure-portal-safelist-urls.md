---
title: Přidání adres URL webu Azure Portal na seznam bezpečných adres v bráně firewall nebo na proxy serveru
description: Přidejte tyto adresy URL, aby bylo možné proxy server nepoužívat ke komunikaci s Azure Portal a jeho službami.
ms.date: 04/10/2020
ms.topic: conceptual
ms.openlocfilehash: 7d9c8222ee85c0c16ec1e1926335ac06e0389797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745872"
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
