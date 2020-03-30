---
title: 'Generování a export certifikátů pro point-to-site: Linux: CLI'
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pomocí cli linuxového (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: a0f996ff2805da4dd5af400642eef2506c228d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779752"
---
# <a name="generate-and-export-certificates"></a>Generování a export certifikátů

Připojení typu Point-to-Site používají k ověření certifikáty. Tento článek ukazuje, jak vytvořit kořenový certifikát podepsaný svým držitelem a generovat klientské certifikáty pomocí linuxového CLI a strongSwan. Pokud hledáte různé pokyny k certifikátu, přečtěte si články o [powershellu](vpn-gateway-certificates-point-to-site.md) nebo [makecertu.](vpn-gateway-certificates-point-to-site-makecert.md) Informace o tom, jak nainstalovat strongSwan pomocí grafického uživatelského rozhraní namísto rozhraní se kšak, naleznete v postupech v článku [konfigurace klienta.](point-to-site-vpn-client-configuration-azure-cert.md#install)

## <a name="install-strongswan"></a>Nainstalujte strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Generování a export certifikátů

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Další kroky

Pokračujte v konfiguraci bodu na pracoviště a [vytvořte a nainstalujte konfigurační soubory klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
