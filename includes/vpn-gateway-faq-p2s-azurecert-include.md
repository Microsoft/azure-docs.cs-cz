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
ms.openlocfilehash: e2e91dc91cf0fbe6827808785a4c3cc25b06542b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151063"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Je možné používat vlastní interní kořenové certifikační autority infrastruktury veřejných klíčů pro připojení Point-to-Site?

Ano. Dříve bylo možné používat pouze kořenové certifikáty podepsané svým držitelem. I nyní je možné nahrát 20 kořenových certifikátů.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Jaké nástroje se dají použít k vytvoření certifikátů?

Můžete využít vaše podnikové řešení infrastruktury veřejných klíčů (vaše interní PKI), Azure PowerShell, MakeCert a OpenSSL.

### <a name="certsettings"></a>Existují nějaké pokyny pro parametry a nastavení certifikátů?

* **Interní řešení infrastruktury veřejných KLÍČŮ/podnikové infrastruktury veřejných KLÍČŮ:** Postup [generování certifikátů](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Zobrazit [prostředí Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) kde najdete kroky.

* **MakeCert:** Zobrazit [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) kde najdete kroky.

* **OpenSSL:** 

    * Při exportu certifikátů nezapomeňte převést kořenový certifikát na Base64.

    * Pro klientský certifikát:

      * Při vytváření privátního klíče zadejte délku 4096.
      * Při vytváření certifikátu jako parametr *-extensions* zadejte *usr_cert*.