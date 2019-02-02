---
title: 'Generování a export certifikátů pro Point-to-Site: Linux: CLI: Azure | Dokumentace Microsoftu'
description: Vytvořit certifikát podepsaný svým držitelem, exportujte veřejný klíč a generovat klientské certifikáty s Linuxem (strongSwan) rozhraní příkazového řádku.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: cherylmc
ms.openlocfilehash: b673be47d4951adab08f04efc56410095f549356
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564782"
---
# <a name="generate-and-export-certificates"></a>Generování a export certifikátů

Připojení point-to-Site používat certifikáty k ověřování. Tento článek ukazuje, jak vytvořit certifikát podepsaný svým držitelem a generování klientských certifikátů pomocí rozhraní příkazového řádku systému Linux a strongSwan. Pokud hledáte pokyny jiný certifikát, najdete v článku [Powershell](vpn-gateway-certificates-point-to-site.md) nebo [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) článků. Informace o tom, jak nainstalovat strongSwan místo v grafickém uživatelském rozhraní příkazového řádku najdete v tématu kroky v [konfigurace klienta](point-to-site-vpn-client-configuration-azure-cert.md#install) článku.

## <a name="generate-and-export"></a>Generování a export
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Další postup

Pokračovat v konfiguraci Point-to-Site tak, aby [vytvoření a instalace konfiguračních souborů klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
