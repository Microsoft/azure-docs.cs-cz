---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 946ff043828034340ae3273fc0629e32de755540
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025938"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Vytvoření kořenového certifikátu podepsaného svým držitelem

K vytvoření kořenového certifikátu podepsaného svým držitelem použijte rutinu New-SelfSignedCertificate. Další informace o parametrech najdete v článku [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

1. Z počítače se systémem Windows 10 nebo Windows Server 2016 otevřete konzolu prostředí Windows PowerShell se zvýšenými oprávněními. Tyto příklady nefungují v Azure Cloud Shell "vyzkoušet". Tyto příklady musíte spustit místně.
1. K vytvoření kořenového certifikátu podepsaného svým držitelem použijte následující příklad. Následující příklad vytvoří kořenový certifikát podepsaný svým držitelem s názvem "P2SRootCert", který je automaticky nainstalován v ' Certificates-Current User\Personal\Certificates '. Certifikát můžete zobrazit otevřením nástroje *certmgr. msc* nebo *správou uživatelských certifikátů*.

   Přihlaste se pomocí `Connect-AzAccount` rutiny. Pak spusťte následující příklad se všemi potřebnými úpravami.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

1. Nechejte konzolu PowerShellu otevřenou a pokračujte dalšími kroky pro vygenerování klientských certifikátů.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Vygenerování klientského certifikátu

Každý klientský počítač, který se připojuje k virtuální síti pomocí připojení Point-to-Site, musí mít nainstalovaný klientský certifikát. Vygenerujete klientský certifikát z kořenového certifikátu podepsaného svým držitelem a pak vyexportujete a nainstalujete certifikát klienta. Pokud klientský certifikát není nainstalován, ověřování se nezdařilo. 

Následující kroky vás provedou vytvořením klientského certifikátu z kořenového certifikátu podepsaného svým držitelem. Můžete vygenerovat více klientských certifikátů ze stejného kořenového certifikátu. Když vygenerujete klientské certifikáty pomocí následujících kroků, klientský certifikát se automaticky nainstaluje na počítač, který jste použili k vygenerování certifikátu. Pokud chcete nainstalovat klientský certifikát do jiného klientského počítače, můžete certifikát exportovat.

V příkladech se pomocí rutiny New-SelfSignedCertificate vygeneruje klientský certifikát, jehož platnost vyprší v jednom roce. Další informace o parametrech, jako je například nastavení jiné hodnoty vypršení platnosti pro klientský certifikát, naleznete v části [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

### <a name="example-1---powershell-console-session-still-open"></a>Příklad 1 – relace konzoly PowerShellu je pořád otevřená

Tento příklad použijte v případě, že jste po vytvoření kořenového certifikátu podepsaného svým držitelem nezavřeli konzolu PowerShellu. Tento příklad pokračuje z předchozí části a používá deklaraci deklarované ' $cert '. Pokud jste konzolu PowerShell zavřeli po vytvoření kořenového certifikátu podepsaného svým držitelem nebo vytváříte další klientské certifikáty v nové relaci konzoly PowerShellu, použijte postup uvedený v [příkladu 2](#ex2).

Upravte a spusťte příklad pro vygenerování klientského certifikátu. Pokud následující příklad spustíte beze změny, je výsledkem klientský certifikát s názvem "P2SChildCert".  Pokud chcete pojmenovat podřízený certifikát něco jiného, upravte hodnotu CN. Při spuštění tohoto příkladu neměňte TextExtension. Certifikát klienta, který vygenerujete, se ve vašem počítači automaticky nainstaluje do ' Certificates-Current User\Personal\Certificates '.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>Příklad 2 – nová relace konzoly PowerShellu

Pokud vytváříte další klientské certifikáty nebo nepoužíváte stejnou relaci prostředí PowerShell, kterou jste použili k vytvoření kořenového certifikátu podepsaného svým držitelem, použijte následující postup:

1. Identifikujte kořenový certifikát podepsaný svým držitelem, který je nainstalovaný v počítači. Tato rutina vrátí seznam certifikátů, které jsou nainstalované ve vašem počítači.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```

1. Vyhledejte název subjektu ze seznamu vrácených a potom zkopírujte kryptografický otisk, který se nachází vedle něj, do textového souboru. V následujícím příkladu jsou k dispozici dva certifikáty. Název CN je název kořenového certifikátu podepsaného svým držitelem, ze kterého chcete vygenerovat podřízený certifikát. V tomto případě "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```

1. Deklarujte proměnnou pro kořenový certifikát pomocí kryptografického otisku z předchozího kroku. Nahraďte kryptografický otisk pomocí kryptografického otisku kořenového certifikátu, ze kterého chcete vygenerovat podřízený certifikát.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Například při použití kryptografického otisku pro P2SRootCert v předchozím kroku proměnná vypadá takto:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```

1. Upravte a spusťte příklad pro vygenerování klientského certifikátu. Pokud následující příklad spustíte beze změny, je výsledkem klientský certifikát s názvem "P2SChildCert". Pokud chcete pojmenovat podřízený certifikát něco jiného, upravte hodnotu CN. Při spuštění tohoto příkladu neměňte TextExtension. Certifikát klienta, který vygenerujete, se ve vašem počítači automaticky nainstaluje do ' Certificates-Current User\Personal\Certificates '.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>Exportovat veřejný klíč kořenového certifikátu (. cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Export kořenového certifikátu podepsaného svým držitelem a privátního klíče pro jeho uložení (volitelné)

Můžete chtít exportovat kořenový certifikát podepsaný svým držitelem a bezpečně ho uložit jako zálohu. V případě potřeby ho můžete později nainstalovat na jiný počítač a vygenerovat další klientské certifikáty. Chcete-li exportovat kořenový certifikát podepsaný svým držitelem jako soubor. pfx, vyberte kořenový certifikát a použijte stejný postup, jak je popsáno v tématu [Export certifikátu klienta](#clientexport).

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Export klientského certifikátu

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]