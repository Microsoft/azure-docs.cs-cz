---
title: Microsoft Graph PowerShell SDK a Azure Active Directory Identity Protection
description: Naučte se, jak zadávat dotazy na Microsoft Graph detekce rizik a související informace z Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880232"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection a sada SDK pro Microsoft Graph PowerShell

Microsoft Graph je koncový bod rozhraní Microsoft Unified API a jeho Domovská stránka rozhraní API pro [Azure Active Directory Identity Protection](./overview-identity-protection.md) . V tomto článku se dozvíte, jak používat [sadu SDK Microsoft Graph PowerShellu](/graph/powershell/get-started) k získání informací o rizikových uživatelích pomocí PowerShellu. Organizace, které chtějí přímo dotazovat rozhraní Microsoft Graph API, můžou použít článek [kurz: identifikace a náprava rizik pomocí Microsoft Graph rozhraní API](/graph/tutorial-riskdetection-api) k zahájení této cesty.


## <a name="connect-to-microsoft-graph"></a>Připojení k Microsoft Graph

Existují čtyři kroky pro přístup k datům Identity Protection prostřednictvím Microsoft Graph:

- [Vytvoření certifikátu](#create-a-certificate)
- [Vytvořit novou registraci aplikace](#create-a-new-app-registration)
- [Konfigurace oprávnění rozhraní API](#configure-api-permissions)
- [Konfigurace platného pověření](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>Vytvoření certifikátu

V produkčním prostředí byste použili certifikát od vaší produkční certifikační autority, ale v této ukázce budeme používat certifikát podepsaný svým držitelem. Pomocí následujících příkazů PowerShellu vytvořte a exportujte certifikát.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>Vytvořit novou registraci aplikace

1. V Azure Portal přejděte do **Azure Active Directory**  >  **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Na stránce **vytvořit** proveďte následující kroky:
   1. Do textového pole **název** zadejte název vaší aplikace (například: rozhraní API pro detekci rizik Azure AD).
   1. V části **podporované typy účtů** vyberte typ účtů, které budou používat rozhraní API.
   1. Vyberte **Zaregistrovat**.
1. Poznamenejte si **ID aplikace (klienta)** a **ID adresáře (tenanta)** , protože tyto položky budete potřebovat později.

### <a name="configure-api-permissions"></a>Konfigurace oprávnění rozhraní API

V tomto příkladu konfigurujeme oprávnění aplikace, aby se tato ukázka mohla používat bezobslužně. Pokud přidělíte oprávnění uživateli, který bude přihlášený, vyberte místo toho delegovaná oprávnění. Další informace o různých typech oprávnění najdete v článku o [oprávněních a jejich souhlasu na platformě Microsoft Identity](../develop/v2-permissions-and-consent.md#permission-types).

1. V **aplikaci** , kterou jste vytvořili, vyberte **oprávnění rozhraní API**.
1. Na stránce **konfigurovaná oprávnění** klikněte na panelu nástrojů v horní části na **Přidat oprávnění**.
1. Na stránce **Přidat přístup přes rozhraní API** klikněte na **Vybrat rozhraní API**.
1. Na stránce **Vyberte rozhraní API** vyberte **Microsoft Graph** a pak klikněte na **Vybrat**.
1. Na stránce **oprávnění API žádosti** : 
   1. Vyberte **oprávnění aplikace**.
   1. Zaškrtněte políčka vedle `IdentityRiskEvent.Read.All` a `IdentityRiskyUser.Read.All` .
   1. Vyberte **Přidat oprávnění**.
1. Vyberte **pro doménu udělit souhlas správce** . 

### <a name="configure-a-valid-credential"></a>Konfigurace platného pověření

1. V **aplikaci** , kterou jste vytvořili, vyberte **certifikáty & tajných** kódů.
1. V části **certifikáty** vyberte **Odeslat certifikát**.
   1. V okně, které se otevře, vyberte dříve exportovaný certifikát.
   1. Vyberte **Přidat**.
1. Poznamenejte si **kryptografický otisk** certifikátu, protože budete potřebovat tyto informace v dalším kroku.

## <a name="list-risky-users-using-powershell"></a>Výpis rizikových uživatelů pomocí prostředí PowerShell

Pokud chcete povolit možnost dotazování Microsoft Graph, musíme `Microsoft.Graph` modul nainstalovat do našeho okna PowerShellu pomocí `Install-Module Microsoft.Graph` příkazu.

Upravte následující proměnné tak, aby obsahovaly informace vygenerované v předchozích krocích, a pak je spusťte jako celek, abyste získali rizikové uživatelské údaje pomocí PowerShellu.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>Další kroky

- [Začínáme s Microsoft Graph PowerShell SDK](/graph/powershell/get-started)
- [Kurz: identifikace a náprava rizik pomocí Microsoft Graph rozhraní API](/graph/tutorial-riskdetection-api)
- [Přehled Microsoft Graphu](https://developer.microsoft.com/graph/docs)
- [Získat přístup bez uživatele](/graph/auth-v2-service)
- [Kořenová služba Azure AD Identity Protection](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
