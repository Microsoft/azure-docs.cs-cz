---
title: 'Azure VPN Gateway: instalace klientského certifikátu typu Point-to-site'
description: Instalace certifikátu klienta pro ověřování P2S certifikátů – Windows, Mac, Linux
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: a0cfbc39bbbd54e225879bd479bf0ba47186aee8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987126"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Nainstalovat klientské certifikáty pro připojení ověřování certifikátů P2S

Všichni klienti, kteří se připojují k virtuální síti pomocí ověřování certifikátů typu Point-to-site, vyžadují klientský certifikát. Tento článek vám pomůže s instalací klientského certifikátu, který se používá k ověřování při připojování k virtuální síti pomocí P2S.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Získání klientského certifikátu

Bez ohledu na to, ze kterého klientského operačního systému se chcete připojit, je nutné vždy mít klientský certifikát. Certifikát klienta můžete vygenerovat buď z kořenového certifikátu, který byl vygenerován pomocí podnikového řešení CA, nebo kořenového certifikátu podepsaného svým držitelem. Postup pro vygenerování klientského certifikátu najdete v pokynech k [prostředí PowerShell](vpn-gateway-certificates-point-to-site.md), [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)nebo [Linux](vpn-gateway-certificates-point-to-site-linux.md) . 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Klienti VPN typu Mac jsou podporováni pouze pro model nasazení Správce prostředků. Nejsou podporované pro model nasazení Classic.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Klientský certifikát pro Linux je nainstalován v klientovi jako součást konfigurace klienta. Pokyny najdete v tématu [Konfigurace klienta – Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) .

## <a name="next-steps"></a>Další kroky

Pokračujte podle kroků konfigurace Point-to-site pro [Vytvoření a instalaci konfiguračních souborů klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md).
