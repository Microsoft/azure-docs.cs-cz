---
title: Safelist adresy URL portálu Azure na firewallu nebo proxy serveru
description: Přidání těchto adres URL do obejít proxy server pro komunikaci s portálem Azure a jeho službami
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900658"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist adresy URL portálu Azure na firewallu nebo proxy serveru

Místní zabezpečovací zařízení můžete nakonfigurovat tak, aby obcovala omezení zabezpečení adres URL portálu Azure. Tato konfigurace může zlepšit výkon a připojení mezi místní nebo širokoplošnou sítí a cloudem Azure.

Správci sítě často nasazují proxy servery, brány firewall nebo jiná zařízení. Tato zařízení pomáhají zabezpečit a dát kontrolu nad tím, jak uživatelé přistupují k internetu. Pravidla určená k ochraně uživatelů mohou někdy blokovat nebo zpomalovat legitimní internetový provoz související s podnikáním. Tento provoz zahrnuje komunikaci mezi vámi a Azure. Chcete-li optimalizovat připojení mezi vaší sítí a portálem Azure a jeho službami, doporučujeme přidat adresy URL portálu Azure do seznamu bezpečných adres.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Adresy URL portálu Azure pro obejít proxy server

Koncové body adresy URL do seznamu safelist pro portál Azure jsou specifické pro cloud Azure, kde se vaše organizace nasadí. Chcete-li povolit síťový provoz do těchto koncových bodů obejít omezení, vyberte cloud. Pak přidejte seznam adres URL na proxy server nebo bránu firewall.

#### <a name="public-cloud"></a>[Veřejný cloud](#tab/public-cloud)

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

#### <a name="us-government-cloud"></a>[Americký vládní cloud](#tab/us-government-cloud)

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

#### <a name="china-government-cloud"></a>[Čína vláda Cloud](#tab/china-government-cloud)

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
> Přenosy do těchto koncových bodů používají standardní porty TCP pro http (80) a HTTPS (443).
>
>
## <a name="next-steps"></a>Další kroky

Potřebujete safelist IP adresy? Stáhněte si seznam rozsahů IP adres datového centra Microsoft Azure pro váš cloud:

* [Celosvětově](https://www.microsoft.com/download/details.aspx?id=56519)
* [Vláda USA](https://www.microsoft.com/download/details.aspx?id=57063)
* [Německo](https://www.microsoft.com/download/details.aspx?id=57064)
* [Čína](https://www.microsoft.com/download/details.aspx?id=57062)

Jiné služby společnosti Microsoft používají pro připojení další adresy URL a adresy IP. Informace o optimalizaci připojení k síti pro služby Microsoft 365 najdete v tématu [Nastavení sítě pro Office 365](/office365/enterprise/set-up-network-for-office-365).
