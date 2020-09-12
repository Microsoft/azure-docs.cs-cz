---
title: 'Azure VPN Gateway: generování certifikátů pro & export pro P2S: MakeCert'
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pomocí nástroje MakeCert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 926de9f3fd357cd9d9ca067e4f7beff7d03eec95
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394177"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Generování a export certifikátů pro připojení Point-to-site pomocí nástroje MakeCert

Připojení Point-to-site používají k ověřování certifikáty. V tomto článku se dozvíte, jak vytvořit kořenový certifikát podepsaný svým držitelem a jak vygenerovat klientské certifikáty pomocí nástroje MakeCert. Pokud hledáte jiné pokyny k certifikátu, přečtěte si téma [certifikáty – PowerShell](vpn-gateway-certificates-point-to-site.md) nebo [certifikáty – Linux](vpn-gateway-certificates-point-to-site-linux.md).

I když k vytvoření certifikátů doporučujeme použít [kroky Windows PowerShellu pro Windows 10](vpn-gateway-certificates-point-to-site.md) , poskytujeme tyto pokyny pro Makecert jako volitelnou metodu. Certifikáty, které vygenerujete pomocí kterékoli z těchto metod, můžete nainstalovat na [libovolný podporovaný klientský operační systém](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). MakeCert ale má následující omezení:

* MakeCert je zastaralý. To znamená, že tento nástroj lze kdykoli odebrat. Všechny certifikáty, které jste už vygenerovali pomocí nástroje MakeCert, nebudou ovlivněny, pokud již nebude k dispozici MakeCert. MakeCert se používá pouze k vygenerování certifikátů, nikoli jako mechanismu ověřování.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Vytvoření kořenového certifikátu podepsaného svým držitelem

Následující kroky ukazují, jak vytvořit certifikát podepsaný svým držitelem pomocí nástroje MakeCert. Tyto kroky nejsou specifické pro model nasazení. Jsou platné pro Správce prostředků i pro klasický systém.

1. Stáhněte a nainstalujte [Makecert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Po instalaci můžete obvykle najít nástroj makecert.exe v rámci této cesty: C:\Program Files (x86) \Windows Kits\10\bin \<arch> '. I když je možné, že byl nainstalován do jiného umístění. Otevřete příkazový řádek jako správce a přejděte do umístění nástroje MakeCert. Můžete použít následující příklad a upravit pro správné umístění:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Vytvořte a nainstalujte certifikát do osobního úložiště certifikátů na vašem počítači. Následující příklad vytvoří odpovídající soubor *. cer* , který nahrajete do Azure při konfiguraci P2S. Nahraďte ' P2SRootCert ' a ' P2SRootCert. cer ' názvem, který chcete použít pro certifikát. Certifikát se nachází ve vašem certifikátu – aktuální User\Personal\Certificates.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>Export veřejného klíče (. cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Exportovaný soubor. cer se musí nahrát do Azure. Pokyny najdete v tématu [Konfigurace připojení typu Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Postup přidání dalšího důvěryhodného kořenového certifikátu najdete v [této části](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) článku.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Export certifikátu podepsaného svým držitelem a privátního klíče pro jeho uložení (volitelné)

Můžete chtít exportovat kořenový certifikát podepsaný svým držitelem a bezpečně ho uložit. V případě potřeby ho můžete později nainstalovat na jiný počítač, vygenerovat další klientské certifikáty nebo exportovat jiný soubor. cer. Chcete-li exportovat kořenový certifikát podepsaný svým držitelem jako soubor. pfx, vyberte kořenový certifikát a použijte stejný postup, jak je popsáno v tématu [Export certifikátu klienta](#clientexport).

## <a name="create-and-install-client-certificates"></a>Vytvoření a instalace klientských certifikátů

Certifikát podepsaný svým držitelem neinstalujete přímo do klientského počítače. Musíte vygenerovat klientský certifikát z certifikátu podepsaného svým držitelem. Pak certifikát klienta exportujete a nainstalujete do klientského počítače. Následující kroky nejsou specifické pro model nasazení. Jsou platné pro Správce prostředků i pro klasický systém.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Vygenerování klientského certifikátu

Každý klientský počítač, který se připojuje k virtuální síti pomocí připojení Point-to-Site, musí mít nainstalovaný klientský certifikát. Vygenerujete klientský certifikát z kořenového certifikátu podepsaného svým držitelem a pak vyexportujete a nainstalujete certifikát klienta. Pokud klientský certifikát není nainstalován, ověřování se nezdařilo. 

Následující kroky vás provedou vytvořením klientského certifikátu z kořenového certifikátu podepsaného svým držitelem. Můžete vygenerovat více klientských certifikátů ze stejného kořenového certifikátu. Když vygenerujete klientské certifikáty pomocí následujících kroků, klientský certifikát se automaticky nainstaluje na počítač, který jste použili k vygenerování certifikátu. Pokud chcete nainstalovat klientský certifikát do jiného klientského počítače, můžete certifikát exportovat.
 
1. Na stejném počítači, který jste použili k vytvoření certifikátu podepsaného svým držitelem, otevřete příkazový řádek jako správce.
2. Upravte a spusťte ukázku pro vygenerování klientského certifikátu.
   * Změňte *"P2SRootCert"* na název kořenového adresáře podepsaného svým držitelem, ze kterého generujete certifikát klienta. Ujistěte se, že používáte název kořenového certifikátu, který je bez ohledu na hodnotu CN =, kterou jste zadali při vytváření kořenového adresáře podepsaného svým držitelem.
   * Změňte *P2SChildCert* na název, který chcete vygenerovat klientský certifikát.

   Pokud následující příklad spustíte beze změny, je výsledkem klientský certifikát s názvem P2SChildcert ve svém osobním úložišti certifikátů, který byl vygenerován z kořenového certifikátu P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Exportovat klientský certifikát

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalace exportovaného klientského certifikátu

Informace o instalaci klientského certifikátu najdete v tématu [instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Další kroky

Pokračujte v konfiguraci Point-to-site. 

* Postup **správce prostředkůho** modelu nasazení najdete v tématu [Konfigurace P2S pomocí nativního ověřování certifikátů Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Postup pro model nasazení **Classic** najdete v tématu [Konfigurace připojení VPN typu Point-to-site k virtuální síti (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).