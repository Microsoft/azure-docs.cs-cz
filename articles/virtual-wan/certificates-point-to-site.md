---
title: Generování a export certifikátů pro připojení VPN uživatelů Azure Virtual WAN | Microsoft Docs
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pomocí prostředí PowerShell ve Windows 10 nebo Windows serveru 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 57d730a92c687a297a35b8cd6cccd955025694af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514911"
---
# <a name="generate-and-export-certificates-for-virtual-wan-user-vpn-connections"></a>Generování a export certifikátů pro připojení virtuálních sítí VPN uživatele sítě WAN

Uživatelská připojení VPN používají k ověřování certifikáty. V tomto článku se dozvíte, jak vytvořit kořenový certifikát podepsaný svým držitelem a jak vygenerovat klientské certifikáty pomocí prostředí PowerShell ve Windows 10 nebo Windows serveru 2016.

Kroky v tomto článku je nutné provést v počítači se systémem Windows 10 nebo Windows Server 2016. Rutiny PowerShellu, které použijete k vygenerování certifikátů, jsou součástí operačního systému a nefungují v ostatních verzích Windows. Počítač s Windows 10 nebo Windows serverem 2016 je potřeba jenom k vygenerování certifikátů. Po vygenerování certifikátů je můžete nahrát nebo nainstalovat na libovolný podporovaný klientský operační systém.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Další kroky

Pokračovat s [kroky virtuální sítě WAN pro připojení k síti VPN uživatele](virtual-wan-about.md)
