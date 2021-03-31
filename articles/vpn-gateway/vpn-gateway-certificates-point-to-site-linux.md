---
title: 'Generování a export certifikátů pro Point-to-site: Linux: CLI'
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pomocí rozhraní příkazového řádku Linux (klient strongswan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: alzam
ms.openlocfilehash: 0be5bb042649b0fe425077b5b3feb3cea728218c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89394002"
---
# <a name="generate-and-export-certificates"></a>Generování a export certifikátů

Připojení Point-to-site používají k ověřování certifikáty. V tomto článku se dozvíte, jak vytvořit kořenový certifikát podepsaný svým držitelem a jak vygenerovat klientské certifikáty pomocí rozhraní příkazového řádku Linux a klient strongswan. Pokud hledáte jiné pokyny k certifikátu, přečtěte si článek o [PowerShellu](vpn-gateway-certificates-point-to-site.md) nebo nástroji [Makecert](vpn-gateway-certificates-point-to-site-makecert.md) . Informace o tom, jak nainstalovat klient strongswan pomocí grafického uživatelského rozhraní (GUI) místo CLI, najdete v postupu v článku o [konfiguraci klienta](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="install-strongswan"></a>Nainstalovat klient strongswan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Generování a export certifikátů

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Další kroky

Pokračujte v konfiguraci Point-to-site a [vytvořte a nainstalujte konfigurační soubory klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
