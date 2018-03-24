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
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Je možné používat vlastní interní kořenové certifikační autority infrastruktury veřejných klíčů pro připojení Point-to-Site?

Ano. Dříve bylo možné používat pouze kořenové certifikáty podepsané svým držitelem. I nyní je možné nahrát 20 kořenových certifikátů.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Jaké nástroje můžete použít k vytvoření certifikátů?

Můžete použít řešení infrastruktury veřejných KLÍČŮ organizace (interní infrastruktury veřejných KLÍČŮ), Azure PowerShell, MakeCert a OpenSSL.

### <a name="certsettings"></a>Jsou nějaké pokyny pro nastavení certifikátu a parametry?

* **Interní řešení infrastruktury veřejných KLÍČŮ nebo podnikové infrastruktury veřejných KLÍČŮ:** najdete v části postup [vygenerujete certifikáty](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** najdete v článku [prostředí Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) najdete v článku kroky.

* **MakeCert:** najdete v článku [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) najdete v článku kroky.

* **OpenSSL:** 

    * Při exportu certifikátů, ujistěte se, zda je kořenový certifikát převést do formátu Base64.

    * Pro certifikát klienta:

      * Při vytváření privátního klíče, zadejte délku jako 4096.
      * Při vytváření certifikátu pro *-rozšíření* parametr, zadejte *usr_cert*.