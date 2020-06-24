---
title: 'Generování a export certifikátů pro Point-to-site: Linux: CLI'
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pomocí rozhraní příkazového řádku Linux (klient strongswan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: 89f6014b548bd3dd66622d15149051e6b28e94b0
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2020
ms.locfileid: "84984606"
---
# <a name="generate-and-export-certificates"></a>Generování a export certifikátů

Připojení Point-to-site používají k ověřování certifikáty. V tomto článku se dozvíte, jak vytvořit kořenový certifikát podepsaný svým držitelem a jak vygenerovat klientské certifikáty pomocí rozhraní příkazového řádku Linux a klient strongswan. Pokud hledáte jiné pokyny k certifikátu, přečtěte si článek o [PowerShellu](vpn-gateway-certificates-point-to-site.md) nebo nástroji [Makecert](vpn-gateway-certificates-point-to-site-makecert.md) . Informace o tom, jak nainstalovat klient strongswan pomocí grafického uživatelského rozhraní (GUI) místo CLI, najdete v postupu v článku o [konfiguraci klienta](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="install-strongswan"></a>Nainstalovat klient strongswan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Generování a export certifikátů

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Další kroky

Pokračujte v konfiguraci Point-to-site a [vytvořte a nainstalujte konfigurační soubory klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
