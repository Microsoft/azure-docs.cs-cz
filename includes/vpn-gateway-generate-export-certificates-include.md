---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e85dc8c079205484db9b7b7c43a0086f69feb3be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059938"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Vytvoření kořenového certifikátu podepsaného svým držitelem

Pomocí rutiny New-SelfSignedCertificate vytvořte kořenový certifikát podepsaný svým držitelem. Další informace o parametrech naleznete [v tématu New-SelfSignedCertificateCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Z počítače se systémem Windows 10 nebo Windows Server 2016 otevřete konzolu Prostředí Windows PowerShell se zvýšenými oprávněními. Tyto příklady nefungují v prostředí Azure Cloud Shell "Try It It". Tyto příklady je nutné spustit místně.
2. Následující příklad slouží k vytvoření kořenového certifikátu podepsaného svým držitelem. Následující příklad vytvoří kořenový certifikát podepsaný svým držitelem s názvem P2SRootCert, který je automaticky nainstalován v části Aktuální uživatel certifikáty\Osobní\Certifikáty.The following example creates a self-signed root certificate s názvem 'P2SRootCert' that is automatically installed in 'Certificates-Current User\Personal\Certificates'. Certifikát můžete zobrazit otevřením *souboru certmgr.msc*nebo *spravovat uživatelské certifikáty*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```
 3. Pokud chcete vytvořit klientský certifikát hned po vytvoření tohoto kořenového certifikátu, ponechte konzolu PowerShell otevřenou.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Vygenerování klientského certifikátu

Každý klientský počítač, který se připojuje k virtuální síti pomocí připojení Point-to-Site, musí mít nainstalovaný klientský certifikát. Z kořenového certifikátu podepsaného svým držitelem vygenerujete klientský certifikát a potom exportujete a nainstalujete klientský certifikát. Pokud klientský certifikát není nainstalován, ověřování se nezdaří. 

Následující kroky vás provedou generováním klientského certifikátu z kořenového certifikátu podepsaného svým držitelem. Můžete generovat více klientských certifikátů ze stejného kořenového certifikátu. Při generování klientských certifikátů pomocí následujících kroků je klientský certifikát automaticky nainstalován v počítači, který jste použili ke generování certifikátu. Chcete-li nainstalovat klientský certifikát do jiného klientského počítače, můžete jej exportovat.

Příklady používají rutinu New-SelfSignedCertificate ke generování klientského certifikátu, jehož platnost vyprší za jeden rok. Další informace o parametrech, například nastavení jiné hodnoty vypršení platnosti klientského certifikátu, naleznete v [tématu New-SelfSignedCertificateCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1---powershell-console-session-still-open"></a>Příklad 1 – Relace konzoly PowerShell je stále otevřená

Tento příklad použijte, pokud jste konzoli PowerShell po vytvoření kořenového certifikátu podepsaného svým držitelem nezavřeli. Tento příklad pokračuje z předchozí části a používá deklarovanou proměnnou "$cert". Pokud jste konzolu PowerShell uzavřete po vytvoření kořenového certifikátu podepsaného svým držitelem nebo vytváříte další klientské certifikáty v nové relaci konzoly PowerShellu, použijte postup uvedený v [příkladu 2](#ex2).

Upravte a spusťte příklad pro generování klientského certifikátu. Pokud spustíte následující příklad bez jeho úpravy, výsledkem je klientský certifikát s názvem P2SChildCert.  Pokud chcete název podřízeného certifikátu něco jiného, upravte hodnotu CN. Při spuštění tohoto příkladu neměňte textextension. Klientský certifikát, který vygenerujete, je v počítači automaticky nainstalován v seznamu Certifikáty - aktuální uživatel\Osobní\Certifikáty.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>Příklad 2 – Nová relace konzoly PowerShellu

Pokud vytváříte další klientské certifikáty nebo nepoužíváte stejnou relaci prostředí PowerShell, kterou jste použili k vytvoření kořenového certifikátu podepsaného svým držitelem, postupujte takto:

1. Identifikujte kořenový certifikát podepsaný svým držitelem, který je v počítači nainstalován. Tato rutina vrátí seznam certifikátů nainstalovaných v počítači.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. Vyhledejte název subjektu z vráceného seznamu a zkopírujte kryptografický otisk, který se nachází vedle něj, do textového souboru. V následujícím příkladu jsou dva certifikáty. Název kn je název kořenového certifikátu podepsaného svým držitelem, ze kterého chcete vygenerovat podřízený certifikát. V tomto případě "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Deklarujte proměnnou pro kořenový certifikát pomocí kryptografického otisku z předchozího kroku. Nahraďte thumbprint kryptografickým otiskem kořenového certifikátu, ze kterého chcete vygenerovat podřízený certifikát.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Například pomocí kryptografického otisku pro P2SRootCert v předchozím kroku, proměnná vypadá takto:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Upravte a spusťte příklad pro generování klientského certifikátu. Pokud spustíte následující příklad bez jeho úpravy, výsledkem je klientský certifikát s názvem P2SChildCert. Pokud chcete název podřízeného certifikátu něco jiného, upravte hodnotu CN. Při spuštění tohoto příkladu neměňte textextension. Klientský certifikát, který vygenerujete, je v počítači automaticky nainstalován v seznamu Certifikáty - aktuální uživatel\Osobní\Certifikáty.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>Export veřejného klíče kořenového certifikátu (.cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Export kořenového certifikátu podepsaného svým držitelem a soukromého klíče pro jeho uložení (volitelné)

Kořenový certifikát podepsaný svým držitelem můžete exportovat a bezpečně jej uložit jako zálohu. V případě potřeby jej můžete později nainstalovat do jiného počítače a vygenerovat další klientské certifikáty. Chcete-li exportovat kořenový certifikát podepsaný svým držitelem jako soubor .pfx, vyberte kořenový certifikát a použijte stejné kroky, jak je popsáno v [části Export klientského certifikátu](#clientexport).

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Export klientského certifikátu

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
