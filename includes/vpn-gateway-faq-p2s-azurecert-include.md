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
ms.openlocfilehash: 5ef67580928a45609f50d3fe798eb9d054265c0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93375938"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Co mám dělat, když se při připojení pomocí ověřování certifikátů zobrazuje neshoda certifikátů?

Zrušte kontrolu **"ověřit identitu serveru ověřením certifikátu"** nebo **přidejte plně kvalifikovaný název domény serveru spolu s certifikátem** při vytváření profilu ručně. To můžete provést spuštěním souboru **Rasphone** z příkazového řádku a výběrem profilu z rozevíracího seznamu.

Nedoporučuje se ověřování identity serveru obecně, ale s ověřováním certifikátů Azure se stejný certifikát používá pro ověřování serveru v protokolu IKEv2 (VPN Tunneling Protocol) a protokolu EAP. Vzhledem k tomu, že je certifikát serveru a plně kvalifikovaný název domény už ověřený protokolem tunelového připojení VPN, je redundantní ho znovu ověřit v protokolu EAP.

![ověřování Point-to-site](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Certifikát serveru")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Můžu použít vlastní interní kořenovou certifikační autoritu PKI ke generování certifikátů pro připojení Point-to-site?

Ano. Dříve bylo možné používat pouze kořenové certifikáty podepsané svým držitelem. I nyní je možné nahrát 20 kořenových certifikátů.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Můžu použít certifikáty z Azure Key Vault?

Ne.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Jaké nástroje se dají použít k vytvoření certifikátů?

Můžete využít vaše podnikové řešení infrastruktury veřejných klíčů (vaše interní PKI), Azure PowerShell, MakeCert a OpenSSL.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Existují nějaké pokyny pro parametry a nastavení certifikátů?

* **Interní / podnikové řešení PKI:** Projděte si kroky pro [vytvoření certifikátů](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Příslušné kroky najdete v článku pro [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** Příslušné kroky najdete v článku pro [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL** 

    * Při exportu certifikátů nezapomeňte převést kořenový certifikát na Base64.

    * Pro klientský certifikát:

      * Při vytváření privátního klíče zadejte délku 4096.
      * Při vytváření certifikátu jako parametr *-extensions* zadejte *usr_cert*.
