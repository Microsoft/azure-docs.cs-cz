---
title: Nainstalovat certifikát klienta P2S | Azure
description: Nainstalujte certifikát klienta Mac nebo Windows pro P2S ověření certifikátu.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: bf2788fff64ab8b3a5ccf75b8a80f2bd5aba5151
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Nainstalovat certifikát klienta pro připojení ověřování Azure certifikát Point-to-Site

Všichni klienti, kteří připojit k virtuální síti pomocí ověřování Azure certifikát Point-to-Site vyžadují klientský certifikát. Tento článek vám pomůže nainstalovat klientský certifikát, který se používá k ověřování při připojení k virtuální síti pomocí P2S.

## <a name="generate"></a>Generování a exportujte certifikát klienta

Certifikát klienta můžete vygenerovat z kořenového certifikátu, který byl vytvořen pomocí řešení podnikové certifikační Autority nebo certifikát podepsaný svým držitelem kořenové. Najdete v článku [prostředí PowerShell](vpn-gateway-certificates-point-to-site.md) nebo [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) pokyny k provedení kroků. Po generování klientské certifikáty, je exportujte jako soubory PFX. Nezapomeňte zahrnout celý řetěz certifikátů při exportu.

## <a name="installwin"></a>Nainstalujte certifikát – Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Nainstalujte certifikát - Mac

Klienti Mac VPN jsou podporovány pouze modelu nasazení Resource Manager. Některé funkce nejsou podporovány pro model nasazení classic.

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Další postup

Pokračujte kroky konfigurace Point-to-Site.

* [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Azure Portal (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
