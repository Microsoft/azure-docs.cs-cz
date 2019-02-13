---
title: Kurz získání dat pomocí API generování sestav Azure AD s certifikáty | Dokumentace Microsoftu
description: Tento kurz vysvětluje, jak používat API generování sestav Azure AD s přihlašovacími údaji certifikátu k získání dat z adresářů bez zásahu uživatele.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b6fcdb117033e69db82d8f9bb083891794ed72
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187079"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Kurz: Získání dat pomocí rozhraní API pro generování sestav Azure Active Directory s certifikáty

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](concept-reporting-api.md) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů. Pokud chcete získat přístup Azure AD API pro generování sestav bez zásahu uživatele, musíte nakonfigurovat váš přístup k používání certifikátů.

V tomto kurzu se dozvíte, jak používat testovací certifikát pro přístup k rozhraní Graph API MS pro vytváření sestav. Nedoporučujeme používat testovací certifikáty v produkčním prostředí. 

## <a name="prerequisites"></a>Požadavky

1. Pro přístup k datům přihlašování, ujistěte se, že máte tenanta služby Azure Active Directory premium (P1/P2) licenci. Zobrazit [Začínáme se službou Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) upgradovat edici Azure Active Directory. Všimněte si, že pokud nemáte žádná data aktivity před upgradem, bude trvat několik dní daná data zobrazit v sestavách po upgradu na licenci premium. 

2. Vytvořit nebo přepnout na uživatelský účet v **globálního správce**, **správce zabezpečení**, **Čtenář zabezpečení** nebo **čtečky sestav** role pro příslušného tenanta. 

3. Dokončení [požadavky pro přístup k API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

4. Stáhněte a nainstalujte [Azure AD PowerShell verze 2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Nainstalujte [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Tento modul poskytuje několik rutin nástrojů, jako jsou:
    - Knihovny ADAL, které jsou potřebné pro ověřování
    - Přístupové tokeny od uživatele, klíče aplikace a certifikáty pomocí ADAL
    - Rozhraní Graph API zpracovávající stránkové výsledky

6. Pokud je vaše první přihlášení pomocí modulu spustit **instalace MSCloudIdUtilsModule**, jinak jej importujte **Import-Module** příkaz prostředí Powershell. Vaše relace by měl vypadat podobně jako tato obrazovka: ![Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Použití **New-SelfSignedCertificate** rutinu Powershellu k vytvoření testovacího certifikátu.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Použití **Export certifikátu** rutiny a jejich export do souboru certifikátu.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Získání dat pomocí rozhraní API pro generování sestav Azure Active Directory s certifikáty

1. Přejděte [webu Azure portal](https://portal.azure.com)vyberte **Azure Active Directory**a pak vyberte **registrace aplikací** a vyberte aplikaci ze seznamu. 

2. Vyberte **nastavení** > **klíče** a vyberte **nahrát veřejný klíč**.

3. Vyberte soubor certifikátu v předchozím kroku a vyberte **Uložit**. 

4. Poznamenejte si ID aplikace a kryptografický otisk certifikátu, který jste právě zaregistrovali s vaší aplikací. Pokud chcete najít kryptografický otisk ze stránky aplikace na portálu přejděte na **nastavení** a klikněte na tlačítko **klíče**. Kryptografický otisk bude probíhat **veřejné klíče** seznamu.

5. Otevřete v editoru manifestu vloženého manifestu aplikace a nahraďte *keyCredentials* vlastnost s použitím následujícího schématu informacemi o novém certifikát. 

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

6. Uložte manifest. 
  
7. Teď je můžete získání přístupového tokenu pro rozhraní Graph API MS pomocí tohoto certifikátu. Použití **Get-MSCloudIdMSGraphAccessTokenFromCert** rutiny z modulu MSCloudIdUtils PowerShell předávání do ID aplikace a kryptografický otisk jste získali v předchozím kroku. 

 ![portál Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Přístupový token ve skriptu prostředí Powershell použijte k dotazování rozhraní Graph API. Použití **Invoke-MSCloudIdMSGraphQuery** rutiny z MSCloudIDUtils výčet přehledu přihlášení a directoryAudits koncový bod. Tato rutina zpracuje vícestránkové výsledky a tyto výsledky odešle do kanálu Powershellu.

9. Dotazování na koncový bod directoryAudits načíst protokoly auditu. 
 ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Dotazování na koncový bod přehledu přihlášení k načtení protokolů přihlášení.
 ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Teď můžete tato data exportovat do sdíleného svazku clusteru a uložit do systému SIEM. Můžete také zabalit váš skript do naplánované úlohy, abyste získávali data Azure AD z vašeho klienta pravidelně bez nutnosti ukládat klíče aplikace ve zdrojovém kódu. 

## <a name="next-steps"></a>Další postup

* [Získejte představu o rozhraní API pro generování sestav](concept-reporting-api.md)
* [Referenční informace k rozhraní API auditu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Sestavy aktivit přihlašování reference k rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
