---
title: 'Instalace klientského certifikátu Point-to-Site: Azure | Dokumentace Microsoftu'
description: Nainstalujte klientský certifikát pro ověřování P2S certifikátu – Windows, Mac, Linux.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: eec15b84e4bdb8df3fe84a53909d5da4b39545ff
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294438"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalace klientských certifikátů pro připojení P2S certifikát ověřování

Všichni klienti, které se připojují k virtuální síti pomocí ověřování certifikátů Azure Point-to-Site vyžadují klientský certifikát. Tento článek vám pomůže nainstalovat klientský certifikát, který se používá k ověřování při připojení k virtuální síti metodou P2S.

## <a name="generate"></a>Získat certifikát klienta

Bez ohledu na to, jaký klientský operační systém chcete připojit z musí mít vždy klientský certifikát. Vygenerování klientského certifikátu z kořenového certifikátu, který byl vygenerován pomocí řešení Certifikační autority nebo certifikát podepsaný svým držitelem. Najdete v článku [PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md), nebo [Linux](vpn-gateway-certificates-point-to-site-linux.md) pokyny k provedení kroků pro vytvoření klientského certifikátu. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Klienti Mac VPN podporují pouze modelu nasazení Resource Manager. Nejsou podporovány pro model nasazení classic.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

Certifikát klienta pro Linux je nainstalován na straně klienta jako součást konfigurace klienta. Zobrazit [konfigurace klienta - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) pokyny.

## <a name="next-steps"></a>Další postup

Pokračujte kroky konfigurace Point-to-Site k [vytvoření a instalace konfiguračních souborů klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md).