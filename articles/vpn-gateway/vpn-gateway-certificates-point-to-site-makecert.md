---
title: 'Generování a export certifikátů pro Point-to-Site: použití nástroje MakeCert: Azure | Dokumentace Microsoftu'
description: Vytvořit certifikát podepsaný svým držitelem, exportujte veřejný klíč a generování klientských certifikátů pomocí nástroje MakeCert.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: 3ff7e754a55e15a8fa8a32f846efbbbe5025e46e
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297855"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Generování a export certifikátů pro připojení Point-to-Site pomocí nástroje MakeCert

Připojení point-to-Site používat certifikáty k ověřování. Tento článek ukazuje, jak vytvořit certifikát podepsaný svým držitelem a generování klientských certifikátů pomocí nástroje MakeCert. Pokud hledáte pokyny jiným certifikátem, přečtěte si téma [certifikátů – PowerShell](vpn-gateway-certificates-point-to-site.md) nebo [certifikáty – Linux](vpn-gateway-certificates-point-to-site-linux.md).

Přestože doporučujeme používat [kroky Windows 10 PowerShell](vpn-gateway-certificates-point-to-site.md) k vytvoření certifikátů, poskytujeme jako volitelná metoda tyto pokyny pro MakeCert. Certifikáty, které vygenerujete pomocí některé z metod se dá nainstalovat na [všechny podporované klientské operační systémy](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). Použití nástroje MakeCert však má následující omezení:

* MakeCert je zastaralý. To znamená, že tento nástroj nebylo možné odebrat kdykoli. Všechny certifikáty, které již vygenerován pomocí příkazu MakeCert nebude mít vliv při použití nástroje MakeCert již není k dispozici. Použití nástroje MakeCert slouží pouze k vytvoření certifikátů, nikoli jako mechanismus ověřování.

## <a name="rootcert"></a>Vytvořit certifikát podepsaný svým držitelem

Následující kroky ukazují, jak vytvořit certifikát podepsaný svým držitelem pomocí nástroje MakeCert. Tyto kroky nejsou specifické pro model nasazení. Jsou platné pro Resource Manager a classic.

1. Stáhněte a nainstalujte [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Po dokončení instalace, můžete obvykle najít nástroj makecert.exe pod touto cestou: "C:\Program Files (x86) \Windows Kits\10\bin\<arch >'. I když je možné, že byla nainstalována do jiného umístění. Otevřete příkazový řádek jako správce a přejděte do umístění nástroje MakeCert. Následující příklad, můžete použít nastavení pro správné umístění:

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. Vytvořte a nainstalujte certifikát v úložišti osobních certifikátů na vašem počítači. Následující příklad vytvoří odpovídající *.cer* soubor, který nahrajete do Azure při konfigurování P2S. Nahraďte názvem, který chcete použít pro certifikát "P2SRootCert" a "P2SRootCert.cer". Certifikát je umístěn ve vaší "Certificates - Current User\Personal\Certificates".

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
  ```

## <a name="cer"></a>Export veřejného klíče (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Soubor exported.cer musí být odeslán do Azure. Pokyny najdete v tématu [konfigurace připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Chcete-li přidat další důvěryhodný kořenový certifikát, najdete v článku [v této části](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) tohoto článku.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportujte certifikát podepsaný svým držitelem a privátní klíč a uložit na (volitelné)

Můžete chtít exportovat certifikát podepsaný svým držitelem a bezpečně uložit. Pokud třeba, můžete později jej nainstalovat na jiný počítač a generovat více klientských certifikátů nebo exportovat jiný soubor .cer. Certifikát podepsaný svým držitelem exportovat jako soubor .pfx, vyberte kořenový certifikát a pomocí stejných kroků, jak je popsáno v [exportovat klientský certifikát](#clientexport).

## <a name="create-and-install-client-certificates"></a>Vytvoření a instalace klientských certifikátů

Certifikát podepsaný svým držitelem neinstalujte přímo na klientském počítači. Je potřeba vygenerovat klientský certifikát z certifikátu podepsaného svým držitelem. Můžete pak export a instalace klientského certifikátu do klientského počítače. Následující kroky nejsou specifické pro model nasazení. Jsou platné pro Resource Manager a classic.

### <a name="clientcert"></a>Vygenerování klientského certifikátu

Každý klientský počítač, který se připojuje k virtuální síti pomocí připojení Point-to-Site, musí mít nainstalovaný klientský certifikát. Vygenerování klientského certifikátu z certifikátu podepsaného svým držitelem a exportovat a instalaci klientského certifikátu. Pokud není nainstalovaný klientský certifikát, ověření se nezdaří. 

Následující kroky vás provedou vygenerování klientského certifikátu z certifikátu podepsaného svým držitelem. Více klientských certifikátů můžete nechat vygenerovat z se stejným kořenovým certifikátem. Při generování klientských certifikátů pomocí následujícího postupu klientský certifikát je automaticky nainstalován v počítači, který jste použili k vygenerování certifikátu. Pokud chcete nainstalovat klientský certifikát na jiný klientský počítač, můžete exportovat certifikát.
 
1. Ve stejném počítači, který jste použili k vytvoření certifikátu podepsaného svým držitelem otevřete příkazový řádek jako správce.
2. Upravit a spuštění ukázky pro vytvoření klientského certifikátu.
  * Změna *"P2SRootCert"* k názvu podepsaný svým držitelem, který se generuje klientský certifikát z kořenové. Ujistěte se, že používáte název kořenového certifikátu, který je bez ohledu "CN =' byla hodnota, kterou jste zadali při vytváření podepsaný svým držitelem kořenové.
  * Změna *P2SChildCert* k vygenerování klientského certifikátu na požadovaný název.

  Při spuštění v následujícím příkladu, aniž byste ho upravovali, výsledkem je klientský certifikát s názvem P2SChildcert ve vašem osobním úložišti certifikátů, který byl vytvořen kořenový certifikát P2SRootCert.

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
  ```

### <a name="clientexport"></a>Export klientského certifikátu

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Nainstalovat certifikát exportovaného klienta

Pokud chcete nainstalovat klientský certifikát, najdete v článku [instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Další postup

Pokračujte v konfiguraci Point-to-Site. 

* Pro **Resource Manageru** postup nasazení modelu najdete v tématu [konfigurace P2S pomocí nativního ověřování certifikátů Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Pro **classic** postup nasazení modelu najdete v tématu [konfigurace připojení typu Point-to-Site VPN k virtuální síti (classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).