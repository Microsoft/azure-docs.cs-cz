---
title: 'Generování a export certifikátů pro P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pomocí PowerShellu ve Windows 10 nebo Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279335"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generování a export certifikátů pro point-to-site pomocí prostředí PowerShell

Připojení typu Point-to-Site používají k ověření certifikáty. Tento článek ukazuje, jak vytvořit kořenový certifikát podepsaný svým držitelem a generovat klientské certifikáty pomocí PowerShellu ve Windows 10 nebo Windows Server 2016. Pokud hledáte různé pokyny k certifikátu, přečtěte si [informace o certifikátech – Linux](vpn-gateway-certificates-point-to-site-linux.md) nebo [Certificates - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Kroky uvedené v tomto článku je nutné provést v počítači se systémem Windows 10 nebo Windows Server 2016. Rutiny prostředí PowerShell, které slouží ke generování certifikátů, jsou součástí operačního systému a nefungují v jiných verzích systému Windows. Počítač se systémem Windows 10 nebo Windows Server 2016 je potřeba pouze ke generování certifikátů. Jakmile jsou certifikáty vygenerovány, můžete je nahrát nebo nainstalovat do libovolného podporovaného klientského operačního systému.

Pokud nemáte přístup k počítači se systémem Windows 10 nebo Windows Server 2016, můžete ke generování certifikátů použít [makecert.](vpn-gateway-certificates-point-to-site-makecert.md) Certifikáty, které generujete pomocí obou metod, lze nainstalovat do libovolného [podporovaného](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) klientského operačního systému.

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalace exportovaného klientského certifikátu

Každý klient, který se připojuje k virtuální síti přes připojení P2S, vyžaduje, aby byl klientský certifikát nainstalován místně.

Informace o instalaci klientského certifikátu naleznete [v tématu Instalace klientského certifikátu pro připojení typu Point-to-Site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Další kroky

Pokračujte v konfiguraci bodu na pracoviště.

* Kroky modelu nasazení **Správce prostředků** [najdete v tématu Konfigurace P2S pomocí nativního ověřování certifikátu Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Klasické **classic** kroky modelu nasazení najdete [v tématu Konfigurace připojení VPN bodu k lokalitě k virtuální síti (klasické).](vpn-gateway-howto-point-to-site-classic-azure-portal.md)