---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97d33bfcc8251b10ba121b7fb013800904450563
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "30197072"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Je možné používat vlastní interní kořenové certifikační autority infrastruktury veřejných klíčů pro připojení Point-to-Site?

Ano. Dříve bylo možné používat pouze kořenové certifikáty podepsané svým držitelem. I nyní je možné nahrát 20 kořenových certifikátů.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Jaké nástroje se dají použít k vytvoření certifikátů?

Můžete využít vaše podnikové řešení infrastruktury veřejných klíčů (vaše interní PKI), Azure PowerShell, MakeCert a OpenSSL.

### <a name="certsettings"></a>Existují nějaké pokyny pro parametry a nastavení certifikátů?

* **Interní / podnikové řešení PKI:** Projděte si kroky pro [vytvoření certifikátů](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Příslušné kroky najdete v článku pro [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** Příslušné kroky najdete v článku pro [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL:** 

    * Při exportu certifikátů nezapomeňte převést kořenový certifikát na Base64.

    * Pro klientský certifikát:

      * Při vytváření privátního klíče zadejte délku 4096.
      * Při vytváření certifikátu jako parametr *-extensions* zadejte *usr_cert*.