---
title: 'Generování a export certifikátů pro Point-to-Site: PowerShell: Azure | Dokumentace Microsoftu'
description: Vytvořit certifikát podepsaný svým držitelem, exportujte veřejný klíč a generovat klientské certifikáty pomocí prostředí PowerShell ve Windows 10 nebo Windows Server 2016.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: e574759ff8af172841db9fc94ee860a19dd14200
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415361"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generování a export certifikátů pro Point-to-Site pomocí Powershellu

Připojení point-to-Site používat certifikáty k ověřování. Tento článek ukazuje, jak vytvořit certifikát podepsaný svým držitelem a generování klientských certifikátů pomocí prostředí PowerShell ve Windows 10 nebo Windows Server 2016. Pokud hledáte pokyny jiným certifikátem, přečtěte si téma [certifikáty – Linux](vpn-gateway-certificates-point-to-site-linux.md) nebo [certifikáty – MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Musíte provést kroky v tomto článku na počítači se systémem Windows 10 nebo Windows Server 2016. Rutiny Powershellu, které můžete použít ke generování certifikátů jsou součástí operačního systému a v jiných verzích Windows, nebudou fungovat. Počítače se Windows 10 nebo Windows Server 2016 je potřeba jenom ke generování certifikátů. Po vygenerování certifikátů můžete nahrávat nebo nainstalovat pro všechny podporované klientské operační systémy. 

Pokud nemáte přístup k počítači s Windows 10 nebo Windows Server 2016, můžete použít [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) ke generování certifikátů. Certifikáty, které vygenerujete pomocí některé z metod může být nainstalován na žádném [podporované](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) klientský operační systém.

## <a name="rootcert"></a>1. Vytvořit certifikát podepsaný svým držitelem

Pomocí rutiny New-SelfSignedCertificate vytvořit certifikát podepsaný svým držitelem. Informace o dalších parametrech najdete v části [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Z počítače se systémem Windows 10 nebo Windows Server 2016 otevřete konzolu Windows Powershellu se zvýšenými oprávněními. Tyto příklady nebudou fungovat ve službě Azure Cloud Shell "Vyzkoušejte si to". Tyto příklady je nutné spustit místně.
2. Abyste mohli vytvořit certifikát podepsaný svým držitelem, použijte následující příklad. Následující příklad vytvoří certifikát podepsaný svým držitelem s názvem "P2SRootCert", který je automaticky nainstalován v úložišti "Certificates-Current User\Personal\Certificates". Certifikát můžete zobrazit tak, že otevřete *certmgr.msc*, nebo *správu uživatelských certifikátů*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

## <a name="clientcert"></a>2. Vygenerování klientského certifikátu

Každý klientský počítač, který se připojuje k virtuální síti pomocí připojení Point-to-Site, musí mít nainstalovaný klientský certifikát. Vygenerování klientského certifikátu z certifikátu podepsaného svým držitelem a exportovat a instalaci klientského certifikátu. Pokud není nainstalovaný klientský certifikát, ověření se nezdaří. 

Následující kroky vás provedou vygenerování klientského certifikátu z certifikátu podepsaného svým držitelem. Více klientských certifikátů můžete nechat vygenerovat z se stejným kořenovým certifikátem. Při generování klientských certifikátů pomocí následujícího postupu klientský certifikát je automaticky nainstalován v počítači, který jste použili k vygenerování certifikátu. Pokud chcete nainstalovat klientský certifikát na jiný klientský počítač, můžete exportovat certifikát.

V příkladech pomocí rutiny New-SelfSignedCertificate vygenerování klientského certifikátu, jejíž platnost vyprší jeden rok. Informace o další parametr, jako je například nastavení hodnoty různých vypršení platnosti certifikátu klienta najdete v části [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Příklad 1

V tomto příkladu použijte, pokud nebyly uzavřeny konzolu Powershellu po vytvoření certifikátu podepsaného svým držitelem. Tento příklad pokračuje v předchozí části a používá proměnnou deklarovanou "$cert". Pokud uzavřeno konzole PowerShell po vytvoření certifikátu podepsaného svým držitelem nebo vytváření dalších klientských certifikátů v nové relaci konzoly prostředí PowerShell, postupujte podle kroků v [příklad 2](#ex2).

Upravit a spustit příklad pro vytvoření klientského certifikátu. Při spuštění v následujícím příkladu, aniž byste ho upravovali, výsledkem je klientský certifikát s názvem "P2SChildCert".  Pokud chcete certifikát podřízené jiný název, hodnotu CN změníte. Neměňte TextExtension při spuštění v tomto příkladu. Klientský certifikát, který vygenerujete je automaticky nainstalován v "Certificates - Current User\Personal\Certificates" ve vašem počítači.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Příklad 2

Pokud vytváříte dalších klientských certifikátů, nebo nepoužívají stejné relaci Powershellu, který jste použili k vytvoření certifikátu podepsaného svým držitelem, použijte následující kroky:

1. Identifikujte kořenový certifikát podepsaný držitelem, který je nainstalován v počítači. Tato rutina vrátí seznam certifikátů, které jsou nainstalovány ve vašem počítači.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. Název subjektu z vráceném seznamu vyhledejte a pak zkopírujte kryptografický otisk, který se nachází vedle sebe do textového souboru. V následujícím příkladu jsou dva certifikáty. Je název CN název certifikátu podepsaného svým držitelem ze kterého chcete vygenerovat certifikát podřízené. V tomto případě "P2SRootCert".

  ```
  Thumbprint                                Subject
  
  AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
  7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
  ```
3. Deklarujte proměnnou pro kořenový certifikát pomocí kryptografického otisku z předchozího kroku. Nahraďte OTISK kryptografický otisk kořenového certifikátu, ze kterého chcete vygenerovat certifikát podřízené.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  Například použitím kryptografického otisku pro P2SRootCert v předchozím kroku, proměnná vypadá takto:

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```
4.  Upravit a spustit příklad pro vytvoření klientského certifikátu. Při spuštění v následujícím příkladu, aniž byste ho upravovali, výsledkem je klientský certifikát s názvem "P2SChildCert". Pokud chcete certifikát podřízené jiný název, hodnotu CN změníte. Neměňte TextExtension při spuštění v tomto příkladu. Klientský certifikát, který vygenerujete je automaticky nainstalován v "Certificates - Current User\Personal\Certificates" ve vašem počítači.

  ```powershell
  New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="cer"></a>3. Export veřejného klíče kořenového certifikátu (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Export kořenového certifikátu podepsaného držitelem a privátní klíč a uložit na (volitelné)

Můžete exportovat certifikát podepsaný svým držitelem a uloží je bezpečně zálohování. Pokud třeba, můžete později jej nainstalovat na jiném počítači a generovat více klientských certifikátů. Certifikát podepsaný svým držitelem exportovat jako soubor .pfx, vyberte kořenový certifikát a pomocí stejných kroků, jak je popsáno v [exportovat klientský certifikát](#clientexport).

## <a name="clientexport"></a>4. Export klientského certifikátu

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Instalace exportovaného klientského certifikátu

Každý klient, který se připojuje k virtuální síti přes připojení P2S vyžaduje místně nainstalovaný certifikát klienta.

Pokud chcete nainstalovat klientský certifikát, najdete v článku [nainstalovat klientský certifikát pro připojení Point-to-Site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Pokračujte v krocích konfigurace P2S

Pokračujte v konfiguraci Point-to-Site.

* Pro **Resource Manageru** postup nasazení modelu najdete v tématu [konfigurace P2S pomocí nativního ověřování certifikátů Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Pro **classic** postup nasazení modelu najdete v tématu [konfigurace připojení typu Point-to-Site VPN k virtuální síti (classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
* P2S informace, o odstraňování potíží naleznete v tématu [připojení point-to-site řešení potíží s Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
