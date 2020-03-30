---
title: Kurz pro rozhraní API pro vytváření sestav služby AD s certifikáty | Dokumenty společnosti Microsoft
description: Tento kurz vysvětluje, jak používat rozhraní API pro vytváření sestav Azure AD s pověřeními certifikátu k získání dat z adresářů bez zásahu uživatele.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d723af5d994006c4ae4f90905ede73fa87326bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014271"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Kurz: Získání dat pomocí rozhraní API pro vytváření sestav služby Azure Active Directory s certifikáty

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](concept-reporting-api.md) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů. Pokud chcete získat přístup k rozhraní API pro vytváření sestav Azure AD bez zásahu uživatele, musíte nakonfigurovat přístup k použití certifikátů.

V tomto kurzu se dozvíte, jak pomocí testovacího certifikátu pro přístup k rozhraní MS Graph API pro vytváření sestav. Nedoporučujeme používat testovací certifikáty v produkčním prostředí. 

## <a name="prerequisites"></a>Požadavky

1. Chcete-li získat přístup k přihlašovacím datům, ujistěte se, že máte klienta Služby Azure Active Directory s prémiovou licencí (P1/P2). Podívejte [se na téma Začínáme s Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) a upgradujte edici Azure Active Directory. Všimněte si, že pokud jste před upgradem neměli žádné údaje o aktivitách, bude trvat několik dní, než se data zobrazí v sestavách po upgradu na prémiovou licenci. 

2. Vytvořte nebo přepněte na uživatelský účet v **roli globálního správce**, správce **zabezpečení**, **čtečky zabezpečení** nebo **čtečky sestav** pro klienta. 

3. Dokončete [požadavky pro přístup k rozhraní Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md). 

4. Stáhněte a nainstalujte [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Nainstalujte [mscloudidutils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Tento modul poskytuje několik rutin nástrojů, jako jsou:
    - Knihovny ADAL potřebné pro ověřování
    - Přístupové tokeny od uživatele, klíče aplikace a certifikáty pomocí ADAL
    - Rozhraní Graph API zpracovávající stránkové výsledky

6. Pokud modul používáte poprvé, **spusťte modul Install-MSCloudIdUtilsModule**, importujte jej jinak pomocí příkazu **Import-Module** Powershell. Vaše relace by měla vypadat ![podobně jako tato obrazovka: Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. K vytvoření testovacího certifikátu použijte příkaz **New-SelfSignedCertificate** Powershell.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. K exportu do souboru certifikátu použijte příkaz **Export-Certificate.**

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Získání dat pomocí rozhraní API pro generování sestav Azure Active Directory s certifikáty

1. Přejděte na [portál Azure](https://portal.azure.com), vyberte Azure **Active Directory**, vyberte registrace **aplikací** a vyberte aplikaci ze seznamu. 

2. Vyberte **Nastavení** > **kláves** a vyberte **Nahrát veřejný klíč**.

3. Vyberte soubor certifikátu z předchozího kroku a vyberte **Uložit**. 

4. Poznamenejte si ID aplikace a kryptografický otisk certifikátu, který jste právě zaregistrovali u aplikace. Pokud chcete najít kryptografický otisk, přejděte na stránce aplikace na portálu na **Nastavení** a klikněte na **Klávesy**. Kryptografický otisk bude v seznamu **Veřejné klíče.**

5. Otevřete manifest aplikace v editoru vkládání manifestu a nahraďte vlastnost *keyCredentials* novými informacemi o certifikátu pomocí následujícího schématu. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Uložte seznam. 
  
7. Nyní můžete získat přístupový token pro rozhraní MS Graph API pomocí tohoto certifikátu. Použijte rutinu **Get-MSCloudIdMSGraphAccessTokenFromCert** z modulu PowerShell MSCloudIdUtils, která předá ID aplikace a kryptografický otisk, který jste získali z předchozího kroku. 

   ![portál Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Pomocí přístupového tokenu ve skriptu Powershellu můžete zadat dotaz na rozhraní GRAPH API. Pomocí rutiny **Invoke-MSCloudIdMSGraphQuery** z MSCloudIDUtils můžete vytvořit výčet koncový bod signins a directoryAudits. Tato rutina zpracovává vícestránkové výsledky a odešle tyto výsledky do kanálu Prostředí PowerShell.

9. Dotaz na koncový bod directoryAudits načíst protokoly auditu. 
   ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Dotaz koncového bodu přihlášení k načtení přihlášení protokoly.
    ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Nyní můžete zvolit export těchto dat do csv a uložit do systému SIEM. Můžete také zabalit váš skript do naplánované úlohy, abyste získávali data Azure AD z vašeho klienta pravidelně bez nutnosti ukládat klíče aplikace ve zdrojovém kódu. 

## <a name="next-steps"></a>Další kroky

* [Získejte představu o rozhraní API pro generování sestav](concept-reporting-api.md)
* [Odkaz na rozhraní API auditu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Odkaz na rozhraní API sestavy přihlašovacích aktivit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
