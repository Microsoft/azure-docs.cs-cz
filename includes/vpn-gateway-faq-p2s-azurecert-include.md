---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 93f6bc8533218af7f0e6dcd1c5f7be6fe8c00e29
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "69520825"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Můžu použít vlastní interní kořenovou certifikační autoritu PKI ke generování certifikátů pro připojení Point-to-site?

Ano. Dříve bylo možné používat pouze kořenové certifikáty podepsané svým držitelem. I nyní je možné nahrát 20 kořenových certifikátů.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Můžu použít certifikáty z Azure Key Vault?

Ne.

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
