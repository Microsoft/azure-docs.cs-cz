---
title: 'Brána Azure VPN: Generování exportních certifikátů & pro P2S: MakeCert'
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pomocí makecertu.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: ad2ab31e6771efc54238d5747863fa2a9bb2f356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75833971"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Generování a export certifikátů pro připojení typu Point-to-Site pomocí makecertu

Připojení typu Point-to-Site používají k ověření certifikáty. Tento článek ukazuje, jak vytvořit kořenový certifikát podepsaný svým držitelem a generovat klientské certifikáty pomocí MakeCert. Pokud hledáte různé pokyny k certifikátu, přečtěte si [informace o certifikátech – PowerShell](vpn-gateway-certificates-point-to-site.md) nebo [Certificates – Linux](vpn-gateway-certificates-point-to-site-linux.md).

I když doporučujeme k vytvoření certifikátů použít [kroky prostředí Windows 10 PowerShell,](vpn-gateway-certificates-point-to-site.md) poskytujeme tyto pokyny MakeCert jako volitelnou metodu. Certifikáty, které generujete pomocí obou metod, lze nainstalovat do [libovolného podporovaného klientského operačního systému](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). MakeCert má však následující omezení:

* MakeCert je zastaralé. To znamená, že tento nástroj může být kdykoli odebrán. Všechny certifikáty, které jste již vygenerovali pomocí MakeCert, nebudou ovlivněny, pokud makecert již není k dispozici. MakeCert se používá pouze ke generování certifikátů, nikoli jako mechanismus ověřování.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Vytvoření kořenového certifikátu podepsaného svým držitelem

Následující kroky ukazují, jak vytvořit certifikát podepsaný svým držitelem pomocí MakeCert. Tyto kroky nejsou specifické pro model nasazení. Jsou platné jak pro Resource Manager, tak pro klasické.

1. Stáhněte a nainstalujte [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Po instalaci můžete obvykle najít nástroj makecert.exe pod touto cestou: 'C:\Program Files (x86)\Windows Kits\10\bin\<arch>'. I když je možné, že byl nainstalován do jiného umístění. Otevřete příkazový řádek jako správce a přejděte do umístění nástroje MakeCert. Můžete použít následující příklad, nastavení pro správné umístění:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Vytvořte a nainstalujte certifikát do úložiště osobních certifikátů v počítači. Následující příklad vytvoří odpovídající *soubor CER,* který nahrajete do Azure při konfiguraci P2S. Nahraďte "P2SRootCert" a P2SRootCert.cer názvem, který chcete použít pro certifikát. Certifikát je umístěn v části Certifikáty – aktuální uživatel\Osobní\Certifikáty.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>Export veřejného klíče (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Exportovaný soubor cer se musí nahrát do Azure. Pokyny naleznete [v tématu Konfigurace připojení bodu na místo](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Další důvěryhodný kořenový certifikát naleznete v [této části](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) článku.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Export certifikátu podepsaného svým držitelem a soukromého klíče pro jeho uložení (volitelné)

Kořenový certifikát podepsaný svým držitelem můžete exportovat a bezpečně jej uložit. V případě potřeby jej můžete později nainstalovat do jiného počítače a vygenerovat další klientské certifikáty nebo exportovat jiný soubor CER. Chcete-li exportovat kořenový certifikát podepsaný svým držitelem jako soubor .pfx, vyberte kořenový certifikát a použijte stejné kroky, jak je popsáno v [části Export klientského certifikátu](#clientexport).

## <a name="create-and-install-client-certificates"></a>Vytvoření a instalace klientských certifikátů

Certifikát podepsaný svým držitelem nenainstalujete přímo do klientského počítače. Je třeba vygenerovat klientský certifikát z certifikátu podepsaného svým držitelem. Potom exportovat a nainstalovat klientský certifikát do klientského počítače. Následující kroky nejsou specifické pro model nasazení. Jsou platné jak pro Resource Manager, tak pro klasické.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Vygenerování klientského certifikátu

Každý klientský počítač, který se připojuje k virtuální síti pomocí připojení Point-to-Site, musí mít nainstalovaný klientský certifikát. Z kořenového certifikátu podepsaného svým držitelem vygenerujete klientský certifikát a potom exportujete a nainstalujete klientský certifikát. Pokud klientský certifikát není nainstalován, ověřování se nezdaří. 

Následující kroky vás provedou generováním klientského certifikátu z kořenového certifikátu podepsaného svým držitelem. Můžete generovat více klientských certifikátů ze stejného kořenového certifikátu. Při generování klientských certifikátů pomocí následujících kroků je klientský certifikát automaticky nainstalován v počítači, který jste použili ke generování certifikátu. Chcete-li nainstalovat klientský certifikát do jiného klientského počítače, můžete jej exportovat.
 
1. Ve stejném počítači, který jste použili k vytvoření certifikátu podepsaného svým držitelem, otevřete příkazový řádek jako správce.
2. Upravte a spusťte ukázku pro generování klientského certifikátu.
   * Změňte *"P2SRootCert"* na název kořene podepsaného svým držitelem, ze kterého generujete klientský certifikát. Ujistěte se, že používáte název kořenového certifikátu, což je bez ohledu na hodnotu CN=, kterou jste zadali při vytváření kořenového adresáře podepsaného svým držitelem.
   * Změňte *P2SChildCert* na název, který chcete vygenerovat klientský certifikát.

   Pokud spustíte následující příklad bez jeho úpravy, výsledkem je klientský certifikát s názvem P2SChildcert v úložišti osobních certifikátů, který byl vygenerován z kořenového certifikátu P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Export klientského certifikátu

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalace exportovaného klientského certifikátu

Informace o instalaci klientského certifikátu naleznete [v tématu Instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Další kroky

Pokračujte v konfiguraci bodu na pracoviště. 

* Kroky modelu nasazení **Správce prostředků** [najdete v tématu Konfigurace P2S pomocí nativního ověřování certifikátu Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Klasické **classic** kroky modelu nasazení najdete [v tématu Konfigurace připojení VPN bodu k lokalitě k virtuální síti (klasické).](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).