---
title: 'Generování a exportování certifikátů pro Point-to-Site: MakeCert: Azure | Microsoft Docs'
description: Vytvořit certifikát podepsaný svým držitelem kořenové, exportujte veřejný klíč a generovat klientské certifikáty pomocí nástroje MakeCert.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: b2f31761e4560cf4b9b9a5b92f5de9982a663a75
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "29345698"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Generování a exportování certifikátů pro připojení Point-to-Site pomocí nástroje MakeCert

Připojení point-to-Site používají certifikáty k ověření. Tento článek ukazuje, jak vytvořit certifikát podepsaný svým držitelem kořenové a generovat klientské certifikáty pomocí nástroje MakeCert. Pokud hledáte kroků konfigurace Point-to-Site, například jak nahrát kořenových certifikátů, vyberte jeden z článků ' konfigurace Point-to-Site, z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Vytvořit certifikáty podepsané svým držitelem – prostředí PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Vytvořit certifikáty podepsané svým držitelem - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Konfigurace Point-to-Site - Resource Manager – portál Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Konfigurace Point-to-Site - Resource Manager – prostředí PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Konfigurace Point-to-Site - Classic - portálu Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Když vám doporučujeme používat [kroky PowerShell systému Windows 10](vpn-gateway-certificates-point-to-site.md) k vytvoření certifikátů, poskytujeme pokyny tyto MakeCert jako metodu volitelné. Certifikáty, které můžete vygenerovat pomocí obou těchto metod se dá nainstalovat na [žádný operační systém podporovaný klient](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). MakeCert však má následující omezení:

* MakeCert je zastaralý. To znamená, že tento nástroj nebylo možné odebrat kdykoli. Všechny certifikáty, které již vygenerován pomocí nástroje MakeCert nebude mít vliv, pokud MakeCert již není k dispozici. MakeCert slouží pouze k vytvoření certifikátů, ne jako mechanismus ověřování.

## <a name="rootcert"></a>Vytvořit certifikát podepsaný svým držitelem kořenové

Následující kroky ukazují, jak vytvořit certifikát podepsaný svým držitelem pomocí nástroje MakeCert. Tyto kroky nejsou specifické pro model nasazení. Jsou platné pro Resource Manager a Klasický model.

1. Stáhněte a nainstalujte [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Po instalaci se obvykle nachází nástroj makecert.exe pod touto cestou: ' C:\Program Files (x86) \Windows Kits\10\bin\<architektura >'. Přestože je možné, že byl nainstalován do jiného umístění. Otevřete příkazový řádek jako správce a přejděte do umístění nástroj MakeCert. Můžete použít následující příklad a úpravě pro správné umístění:

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. Vytvoření a instalaci certifikátu v úložišti osobních certifikátů v počítači. Následující příklad vytvoří odpovídající *.cer* soubor, který nahrajete do Azure při konfiguraci P2S. Nahraďte 'P2SRootCert' a 'P2SRootCert.cer' název, který chcete použít pro certifikát. Certifikát je umístěn ve vaší 'certifikáty – aktuální User\Personal\Certificates'.

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
  ```

## <a name="cer"></a>Export veřejného klíče (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Soubor exported.cer musí být nahrán do Azure. Pokyny najdete v tématu [konfigurace připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Pokud chcete přidat další důvěryhodný kořenový certifikát, najdete v části [v této části](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) článku.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportovat certifikát podepsaný svým držitelem a privátní klíč uložit (volitelné)

Můžete chtít exportovat certifikát podepsaný svým držitelem kořenové a bezpečně uložit. Pokud třeba, můžete později jej nainstalovat na jiný počítač a generovat další klientské certifikáty nebo exportovat jiný soubor .cer. Pokud chcete exportovat certifikát podepsaný svým držitelem kořenové jako .pfx, vyberte kořenový certifikát a použít stejný postup, jak je popsáno v [exportovat certifikát klienta](#clientexport).

## <a name="create-and-install-client-certificates"></a>Vytvoření a instalace klientských certifikátů

Certifikát podepsaný svým držitelem neinstalujte přímo na klientském počítači. Musíte pro vytvoření klientského certifikátu z certifikát podepsaný svým držitelem. Budete pak exportovat a nainstalovat certifikát klienta do klientského počítače. Tyto kroky nejsou specifické pro model nasazení. Jsou platné pro Resource Manager a Klasický model.

### <a name="clientcert"></a>Vygenerování klientského certifikátu

Každý klientský počítač, který se připojuje k virtuální síti pomocí připojení Point-to-Site, musí mít nainstalovaný klientský certifikát. Vygenerujte certifikát klienta z podepsaného svým držitelem kořenový certifikát a pak je exportovat a nainstalovat certifikát klienta. Pokud není nainstalován klientský certifikát, ověření se nezdaří. 

Následující kroky vás provedou vygenerování klientského certifikátu z certifikát podepsaný svým držitelem kořenové. Z se stejným kořenovým certifikátem může generovat více klientských certifikátů. Při generování klientské certifikáty pomocí následujícího postupu klientský certifikát se automaticky nainstaluje na počítači, který jste použili k vygenerování certifikátu. Pokud chcete nainstalovat certifikát klienta v jiném počítači klienta, můžete exportovat certifikát.
 
1. Na stejném počítači, který jste použili k vytvoření certifikátu podepsaného svým držitelem otevřete příkazový řádek jako správce.
2. Upravit a spustit ukázkový pro vytvoření klientského certifikátu.
  * Změna *"P2SRootCert"* k názvu podepsaný svým držitelem kořenové, které generují klientský certifikát z. Zajistěte, aby se pomocí názvu kořenového certifikátu, který je to bez ohledu "CN =' hodnota byla, kterou jste zadali při vytvoření vlastních kořenových.
  * Změna *P2SChildCert* na název, kterou chcete vygenerovat certifikát klienta.

  Pokud spustíte následující příklad beze změny jeho, výsledkem je klientský certifikát s názvem P2SChildcert v osobním úložišti certifikátů generované z kořenového certifikátu P2SRootCert.

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
  ```

### <a name="clientexport"></a>Export certifikátu klienta

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Nainstalovat certifikát exportovaného klienta

Chcete-li nainstalovat certifikát klienta, přečtěte si téma [nainstalovat certifikát klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Další postup

V konfiguraci Point-to-Site pokračujte. 

* Pro **Resource Manager** postup nasazení modelu najdete v tématu [P2S konfigurace ověřování pomocí certifikátů nativní Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Pro **classic** postup nasazení modelu najdete v tématu [konfigurace připojení typu Point-to-Site VPN do virtuální sítě (klasické)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).