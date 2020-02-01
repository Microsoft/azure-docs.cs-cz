---
title: Safelist adresy URL Azure Portal v bráně firewall nebo proxy server
description: Přidejte tyto adresy URL, aby bylo možné proxy server nepoužívat ke komunikaci s Azure Portal a jeho službami.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900658"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist adresy URL Azure Portal v bráně firewall nebo proxy server

Můžete nakonfigurovat místní zařízení zabezpečení a obejít omezení zabezpečení pro Azure Portal adresy URL. Tato konfigurace může zlepšit výkon a konektivitu mezi vaší místní sítí a cloudem Azure.

Správci sítě často nasazují proxy servery, brány firewall nebo jiná zařízení. Tato zařízení vám pomůžou zabezpečit a poskytovat kontrolu nad tím, jak uživatelé přistupují k Internetu. Pravidla určená k ochraně uživatelů můžou někdy blokovat nebo zpomalit legitimní internetový provoz související s firmou. Tento provoz zahrnuje komunikaci mezi vámi a Azure. K optimalizaci připojení mezi vaší sítí a Azure Portal a jejími službami doporučujeme přidat do svého Safelist Azure Portal adresy URL.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal adresy URL pro obejití proxy serveru

Koncové body adresy URL, které se Safelist pro Azure Portal, jsou specifické pro cloud Azure, ve kterém je vaše organizace nasazená. Pokud chcete, aby síťový provoz do těchto koncových bodů mohl obejít omezení, vyberte svůj Cloud. Pak přidejte seznam adres URL do proxy server nebo brány firewall.

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

#### <a name="us-government-cloudtabus-government-cloud"></a>[Cloud pro státní správu USA](#tab/us-government-cloud)

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
* [Státní správa USA](https://www.microsoft.com/download/details.aspx?id=57063)
* [Německo](https://www.microsoft.com/download/details.aspx?id=57064)
* [Čína](https://www.microsoft.com/download/details.aspx?id=57062)

Další služby Microsoftu používají pro připojení další adresy URL a IP adresy. Informace o optimalizaci síťového připojení pro Microsoft 365 Services najdete v tématu [nastavení sítě pro Office 365](/office365/enterprise/set-up-network-for-office-365).
