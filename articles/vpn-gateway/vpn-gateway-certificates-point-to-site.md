---
title: 'Generování a export certifikátů pro P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pro P2S pomocí prostředí PowerShell ve Windows 10 nebo Windows serveru 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: c1c69b301199b054fe6b1ef42cfcf7878a7a161c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306680"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generování a export certifikátů pro připojení typu point-to-site pomocí PowerShellu

Připojení Point-to-site používají k ověřování certifikáty. V tomto článku se dozvíte, jak vytvořit kořenový certifikát podepsaný svým držitelem a jak vygenerovat klientské certifikáty pomocí prostředí PowerShell ve Windows 10 nebo Windows serveru 2016. Pokud hledáte jiné pokyny k certifikátu, přečtěte si téma [certifikáty-Linux](vpn-gateway-certificates-point-to-site-linux.md) nebo [certifikáty-Makecert](vpn-gateway-certificates-point-to-site-makecert.md).

Kroky v tomto článku je nutné provést v počítači se systémem Windows 10 nebo Windows Server 2016. Rutiny PowerShellu, které použijete k vygenerování certifikátů, jsou součástí operačního systému a nefungují v ostatních verzích Windows. Počítač s Windows 10 nebo Windows serverem 2016 je potřeba jenom k vygenerování certifikátů. Po vygenerování certifikátů je můžete nahrát nebo nainstalovat na libovolný podporovaný klientský operační systém.

Pokud nemáte přístup k počítači s Windows 10 nebo Windows Server 2016, můžete k vygenerování certifikátů použít [Makecert](vpn-gateway-certificates-point-to-site-makecert.md) . Certifikáty, které vygenerujete pomocí kterékoli z těchto metod, můžete nainstalovat na libovolný [podporovaný](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) klientský operační systém.

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalace exportovaného klientského certifikátu

Každý klient, který se připojuje k virtuální síti přes připojení P2S, vyžaduje místní instalaci klientského certifikátu.

Informace o instalaci klientského certifikátu najdete v tématu [instalace klientského certifikátu pro připojení typu Point-to-site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Další kroky

Pokračujte v konfiguraci Point-to-site.

* Postup **správce prostředkůho** modelu nasazení najdete v tématu [Konfigurace P2S pomocí nativního ověřování certifikátů Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Postup pro model nasazení **Classic** najdete v tématu [Konfigurace připojení VPN typu Point-to-site k virtuální síti (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).