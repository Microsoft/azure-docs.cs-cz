---
title: 'Generování a export certifikátů pro Point-to-site: Linux: CLI Azure | Microsoft Docs'
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pomocí rozhraní příkazového řádku Linux (klient strongswan).
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: alzam
ms.openlocfilehash: 79840010561746f2885909123012ffb4efbea5f6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990315"
---
# <a name="generate-and-export-certificates"></a>Generování a export certifikátů

Připojení Point-to-site používají k ověřování certifikáty. V tomto článku se dozvíte, jak vytvořit kořenový certifikát podepsaný svým držitelem a jak vygenerovat klientské certifikáty pomocí rozhraní příkazového řádku Linux a klient strongswan. Pokud hledáte jiné pokyny k certifikátu, přečtěte si článek [](vpn-gateway-certificates-point-to-site.md) o PowerShellu nebo nástroji [Makecert](vpn-gateway-certificates-point-to-site-makecert.md) . Informace o tom, jak nainstalovat klient strongswan pomocí grafického uživatelského rozhraní (GUI) místo CLI, najdete v postupu v článku o [konfiguraci klienta](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="generate-and-export"></a>Generovat a exportovat
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Další postup

Pokračujte v konfiguraci Point-to-site a [vytvořte a nainstalujte konfigurační soubory klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
