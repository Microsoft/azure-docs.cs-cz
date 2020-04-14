---
title: Safelist adresy URL portálu Azure na firewallu nebo proxy serveru
description: Přidání těchto adres URL do obejít proxy server pro komunikaci s portálem Azure a jeho službami
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 399c64c88e78079432fcf7c09dafd199da83358b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255043"
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
