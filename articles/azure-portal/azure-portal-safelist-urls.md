---
title: Safelist adresy URL Azure Portal | Microsoft Docs
description: Přidejte tyto adresy URL, aby bylo možné proxy server nepoužívat ke komunikaci s Azure Portal a jeho službami.
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667477"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist adresy URL Azure Portal v bráně firewall nebo proxy server

Pro zajištění dobrého výkonu a připojení mezi vaší místní sítí a cloudem Azure nakonfigurujte místní zařízení zabezpečení tak, aby se pro adresy URL Azure Portal obcházela omezení zabezpečení. Správci sítě často nasazují proxy servery, brány firewall nebo jiná zařízení, aby pomohly zabezpečení a poskytovaly kontrolu nad tím, jak uživatelé přistupují k Internetu. Pravidla určená k ochraně uživatelů ale můžou někdy blokovat nebo zpomalit legitimní internetový provoz související s obchodem, včetně komunikace mezi vámi a Azure. K optimalizaci připojení mezi vaší sítí a Azure Portal a jejími službami doporučujeme přidat do svého Safelist Azure Portal adresy URL.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal adresy URL pro obejití proxy serveru

Do proxy server nebo brány firewall přidejte následující seznam adres URL, které umožní síťovému přenosu do těchto koncových bodů obejít omezení:

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> Provoz do těchto koncových bodů používá standardní porty TCP pro protokol HTTP (80) a HTTPS (443).
>
>
## <a name="next-steps"></a>Další kroky

* Potřebujete Safelist IP adresy? Stáhněte si seznam [rozsahů IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* Další služby Microsoftu používají pro připojení další adresy URL a IP adresy. Informace o optimalizaci síťového připojení pro Microsoft 365 Services najdete v tématu [nastavení sítě pro Office 365](/office365/enterprise/set-up-network-for-office-365).
