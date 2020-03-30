---
title: 'Brána Azure VPN: Instalace klientského certifikátu typu Point-to-Site'
description: Nainstalujte klientský certifikát pro ověřování certifikátů P2S - Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 787b8a34ed4b232b9e6cc033e67b1a8162c85f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902851"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalace klientských certifikátů pro připojení ověřování certifikátů P2S

Všichni klienti, kteří se připojují k virtuální síti pomocí ověřování certifikátu Azure typu Point-to-Site, vyžadují klientský certifikát. Tento článek vám pomůže nainstalovat klientský certifikát, který se používá pro ověřování při připojování k virtuální síti pomocí P2S.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Získání klientského certifikátu

Bez ohledu na to, z jakého klientského operačního systému se chcete připojit, musíte mít vždy klientský certifikát. Klientský certifikát můžete vygenerovat buď z kořenového certifikátu, který byl vygenerován pomocí řešení enterprise ca, nebo z kořenového certifikátu podepsaného svým držitelem. Postup generování klientského certifikátu naleznete v pokynech k [prostředí PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)nebo [Linux.](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Klienti MAC VPN jsou podporováni pouze pro model nasazení Resource Manageru. Nejsou podporovány pro model klasického nasazení.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Klientský certifikát Linuxu je nainstalován na straně klienta jako součást konfigurace klienta. Pokyny naleznete [v části Konfigurace klienta – Linux.](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)

## <a name="next-steps"></a>Další kroky

Pokračujte v postupech konfigurace bodu na pracoviště a [vytvořte a nainstalujte konfigurační soubory klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md).
