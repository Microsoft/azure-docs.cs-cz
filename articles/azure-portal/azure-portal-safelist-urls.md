---
title: Na webu Azure portal Safelist adresy URL | Dokumentace Microsoftu
description: Přidejte tyto adresy URL do obcházení proxy serveru pro komunikaci pomocí webu Azure portal a jeho služeb
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305007"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Na webu Azure portal Safelist adresy URL na vaše brána firewall nebo proxy serveru

Dobrý výkon a připojení mezi vaší místní nebo celé oblasti sítí a cloudu Azure, nakonfigurujte na místních zařízeních zabezpečení obejít omezení zabezpečení na webu Azure portal adresy URL. Správci sítě často nasadit proxy servery, brány firewall nebo jiná zařízení k zabezpečení a poskytnou kontrolu nad tím, jak uživatelé přistupovat k Internetu. Pravidla navržená k ochraně uživatelů můžete však někdy zablokovat nebo zpomalit legitimní obchodní internetový provoz, včetně komunikaci mezi vámi a Azure. Pro optimalizaci spojení mezi vaší sítí a na webu Azure portal a jejích služeb, doporučujeme přidat webu Azure portal adres URL na vaše safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Obejít adresy URL pro server proxy webu Azure portal

Přidejte následující seznam adres URL do proxy serveru nebo brány firewall pro povolení provozu sítě s těmito koncovými body obejít omezení:

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
* *.wpc.azureedge.net

> [!NOTE]
> Provoz s těmito koncovými body používá standardní porty protokolu TCP pro protokol HTTP (80) a protokolu HTTPS (443).
>
>
## <a name="next-steps"></a>Další postup

* Je potřeba safelist IP adresy? Stáhněte si seznam [rozsahy IP adres datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* Jiné Microsoft services použití další adresy URL a IP adresy pro připojení. Na optimalizaci síťové konektivity pro služby Microsoft 365, najdete v článku [nastavení sítě pro Office 365](/office365/enterprise/set-up-network-for-office-365).
