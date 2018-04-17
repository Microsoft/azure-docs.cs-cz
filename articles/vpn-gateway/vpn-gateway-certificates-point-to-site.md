---
title: 'Generování a exportování certifikátů pro Point-to-Site: prostředí PowerShell: Azure | Microsoft Docs'
description: Vytvořit certifikát podepsaný svým držitelem kořenové, exportujte veřejný klíč a generovat klientské certifikáty pomocí prostředí PowerShell na Windows 10 nebo Windows Server 2016.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: cherylmc
ms.openlocfilehash: 385b6ed2e8104fd2e15e6e55d46dcd12b963ec6b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generování a exportování certifikátů pro Point-to-Site pomocí prostředí PowerShell

Připojení point-to-Site používají certifikáty k ověření. Tento článek ukazuje, jak vytvořit certifikát podepsaný svým držitelem kořenové a generovat klientské certifikáty pomocí prostředí PowerShell na Windows 10 nebo Windows Server 2016. Pokud hledáte kroků konfigurace Point-to-Site, například jak nahrát kořenových certifikátů, vyberte jeden z článků ' konfigurace Point-to-Site, z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Vytvořit certifikáty podepsané svým držitelem – prostředí PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Vytvořit certifikáty podepsané svým držitelem - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Konfigurace Point-to-Site - Resource Manager – portál Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Konfigurace Point-to-Site - Resource Manager – prostředí PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Konfigurace Point-to-Site - Classic - portálu Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Musíte provést kroky v tomto článku v počítači se systémem Windows 10 nebo Windows Server 2016. Rutiny prostředí PowerShell, které používáte pro generování certifikátů jsou součástí operačního systému a nefungují v jiných verzích Windows. K vytvoření certifikátů, je potřeba jenom počítače Windows 10 nebo Windows Server 2016. Po vygenerování certifikátů jsou můžete odešlete, nebo je nainstalovat pro všechny podporované klientské operační systémy. 

Pokud nemáte přístup k počítači se systémem Windows 10 nebo Windows Server 2016, můžete použít [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) pro generování certifikátů. Certifikáty, které můžete vygenerovat pomocí obou těchto metod se dá nainstalovat na všechny [podporované](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) klientský operační systém.

## <a name="rootcert"></a>1. Vytvořit certifikát podepsaný svým držitelem kořenové

Pomocí rutiny New-SelfSignedCertificate vytvořit certifikát podepsaný svým držitelem kořenové. Parametr Další informace najdete v tématu [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Z počítače se systémem Windows 10 nebo Windows Server 2016 otevřete konzolu prostředí Windows PowerShell se zvýšenými oprávněními.
2. Použijte následující příklad k vytvoření certifikátu podepsaného svým držitelem kořenové. Následující příklad vytvoří certifikát podepsaný svým držitelem kořenové s názvem 'P2SRootCert', který je automaticky nainstalován v 'Certifikáty – aktuální User\Personal\Certificates'. Certifikát můžete zobrazit tak, že otevřete *certmgr.msc*, nebo *Správa uživatelských certifikátů*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

## <a name="clientcert"></a>2. Vygenerování klientského certifikátu

Každý klientský počítač, který se připojuje k virtuální síti pomocí připojení Point-to-Site, musí mít nainstalovaný klientský certifikát. Vygenerujte certifikát klienta z podepsaného svým držitelem kořenový certifikát a pak je exportovat a nainstalovat certifikát klienta. Pokud není nainstalován klientský certifikát, ověření se nezdaří. 

Následující kroky vás provedou vygenerování klientského certifikátu z certifikát podepsaný svým držitelem kořenové. Z se stejným kořenovým certifikátem může generovat více klientských certifikátů. Při generování klientské certifikáty pomocí následujícího postupu klientský certifikát se automaticky nainstaluje na počítači, který jste použili k vygenerování certifikátu. Pokud chcete nainstalovat certifikát klienta v jiném počítači klienta, můžete exportovat certifikát.

Příklady pomocí rutiny New-SelfSignedCertificate pro vytvoření klientského certifikátu, který vyprší za jeden rok. Dalším parametr informace, například nastavení hodnoty různých vypršení platnosti certifikátu klienta najdete v části [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Příklad 1

Tento příklad používá proměnnou deklarované '$cert' z předchozí části. Pokud po vytvoření certifikátu podepsaného svým držitelem kořenové uzavřít konzolu PowerShell nebo vytváření dalších klientských certifikátů v nové relaci konzoly prostředí PowerShell, použijte kroky v [příklad 2](#ex2).

Upravit a spustit v příkladu pro vytvoření klientského certifikátu. Pokud spustíte následující příklad beze změny jeho, výsledkem je klientský certifikát s názvem 'P2SChildCert'.  Pokud chcete název certifikátu podřízené něco jiného, změňte hodnotu CN. Neměňte TextExtension při spuštění v tomto příkladu. Certifikát klienta, který je generovat se automaticky nainstaluje v 'Certifikáty – aktuální User\Personal\Certificates' ve vašem počítači.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Příklad 2

Pokud vytváříte další klientské certifikáty, nebo nejsou pomocí stejné relace prostředí PowerShell, který jste použili k vytvoření vašeho certifikátu podepsaného svým držitelem kořenové, použijte následující postup:

1. Identifikujte podepsaný svým držitelem kořenový certifikát, který je nainstalován v počítači. Tato rutina vrátí seznam certifikátů, které jsou nainstalovány ve vašem počítači.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. Vyhledejte název subjektu z vráceném seznamu, a poté zkopírujte kryptografický otisk, který je umístěný vedle ho do textového souboru. V následujícím příkladu jsou dva certifikáty. Název CN je název certifikátu podepsaného svým držitelem kořenové ze kterého chcete generovat certifikát podřízené. V tomto případě 'P2SRootCert'.

  ```
  Thumbprint                                Subject
  
  AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
  7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
  ```
3. Deklarujte proměnnou pro kořenový certifikát pomocí kryptografického otisku z předchozího kroku. Nahraďte OTISK kryptografický otisk kořenového certifikátu, ze kterého chcete generovat certifikát podřízené.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  Například pomocí kryptografického otisku pro P2SRootCert v předchozím kroku, proměnná vypadá takto:

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```
4.  Upravit a spustit v příkladu pro vytvoření klientského certifikátu. Pokud spustíte následující příklad beze změny jeho, výsledkem je klientský certifikát s názvem 'P2SChildCert'. Pokud chcete název certifikátu podřízené něco jiného, změňte hodnotu CN. Neměňte TextExtension při spuštění v tomto příkladu. Certifikát klienta, který je generovat se automaticky nainstaluje v 'Certifikáty – aktuální User\Personal\Certificates' ve vašem počítači.

  ```powershell
  New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="cer"></a>3. Exportujte veřejný klíč kořenového certifikátu (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportovat certifikát podepsaný svým držitelem kořenové a privátní klíč uložit (volitelné)

Můžete uložit a exportujte certifikát podepsaný svým držitelem kořenové bezpečně zálohování. Pokud třeba, můžete později jej nainstalovat na jiný počítač a generovat další certifiates klienta. Pokud chcete exportovat certifikát podepsaný svým držitelem kořenové jako .pfx, vyberte kořenový certifikát a použít stejný postup, jak je popsáno v [exportovat certifikát klienta](#clientexport).

## <a name="clientexport"></a>4. Export klientského certifikátu

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Instalace exportovaného klientského certifikátu

Každý klient, který se připojuje k virtuální síti přes připojení P2S vyžaduje místně nainstalovaný klientský certifikát.

Chcete-li nainstalovat certifikát klienta, přečtěte si téma [nainstalovat certifikát klienta pro připojení Point-to-Site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Pokračujte kroky konfigurace P2S

V konfiguraci Point-to-Site pokračujte.

* Pro **Resource Manager** postup nasazení modelu najdete v tématu [P2S konfigurace ověřování pomocí certifikátů nativní Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Pro **classic** postup nasazení modelu najdete v tématu [konfigurace připojení typu Point-to-Site VPN do virtuální sítě (klasické)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
* P2S řešení potíží s informace najdete v tématu [připojení point-to-site řešení potíží s Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
