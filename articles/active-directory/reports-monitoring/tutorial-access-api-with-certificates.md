---
title: Kurz rozhraní API pro vytváření sestav AD s certifikáty | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí rozhraní API pro vytváření sestav Azure AD s přihlašovacími údaji k certifikátu získat data z adresářů bez zásahu uživatele.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: bc763a99c945925b80171738f4076e6305d92df9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89229455"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Kurz: získání dat pomocí rozhraní API pro vytváření sestav Azure Active Directory s certifikáty

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](concept-reporting-api.md) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů. Pokud chcete získat přístup k rozhraní API pro vytváření sestav Azure AD bez zásahu uživatele, musíte nakonfigurovat přístup k používání certifikátů.

V tomto kurzu se dozvíte, jak pomocí testovacího certifikátu získat přístup k MS Graph API pro vytváření sestav. Nedoporučujeme používat testovací certifikáty v produkčním prostředí. 

## <a name="prerequisites"></a>Požadavky

1. Chcete-li získat přístup k přihlašovacím datům, ujistěte se, že máte klienta Azure Active Directory s licencí Premium (P1/P2). Pokud chcete upgradovat edici Azure Active Directory, přečtěte si téma [Začínáme se Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) . Všimněte si, že pokud jste před upgradem nedostali žádná data, bude trvat několik dní, než se data zobrazí v sestavách po upgradu na licenci Premium. 

2. Vytvořte nebo přepněte na uživatelský účet v roli **globální správce**, **Správce zabezpečení**, **čtecí modul zabezpečení** nebo **čtenáře sestav** pro daného klienta. 

3. Dokončete [požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

4. Stáhněte a nainstalujte [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Nainstalujte [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Tento modul poskytuje několik rutin nástrojů, jako jsou:
    - Knihovny ADAL nutné pro ověřování
    - Přístupové tokeny od uživatele, klíče aplikace a certifikáty pomocí ADAL
    - Rozhraní Graph API zpracovávající stránkové výsledky

6. Pokud je to poprvé pomocí modulu **install-MSCloudIdUtilsModule**, jinak ho importujte pomocí příkazu **Import-Module** PowerShell. Vaše relace by měla vypadat podobně jako tato obrazovka: ![ Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. K vytvoření testovacího certifikátu použijte rutinu **New-SelfSignedCertificate** prostředí PowerShell rutiny.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. K exportu do souboru certifikátu použijte rutiny **Export-Certificate** .

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Získání dat pomocí rozhraní API pro generování sestav Azure Active Directory s certifikáty

1. Přejděte na [Azure Portal](https://portal.azure.com), vyberte **Azure Active Directory**, vyberte **Registrace aplikací** a zvolte svou aplikaci ze seznamu. 

2. V okně **Spravovat** oddíl v nabídce Správa vyberte **certifikáty & tajných** kódů a vyberte **nahrát certifikát**.

3. Vyberte soubor certifikátu z předchozího kroku a vyberte **Přidat**. 

4. Poznamenejte si ID aplikace a kryptografický otisk certifikátu, který jste právě zaregistrovali ve vaší aplikaci. Pokud chcete najít kryptografický otisk, na stránce aplikace na portálu klikněte na **certifikáty & tajných** kódů v části **Spravovat** oddíl. Kryptografický otisk bude v seznamu **certifikáty** .

5. Otevřete manifest aplikace v vloženém editoru manifestu a ověřte, zda je vlastnost klíčová *pověření* aktualizována o nové informace o certifikátu, jak je uvedeno níže. 

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
6. Nyní můžete získat přístupový token pro aplikaci MS Graph API pomocí tohoto certifikátu. Pomocí rutiny **Get-MSCloudIdMSGraphAccessTokenFromCert** z modulu MSCloudIdUtils PowerShellu předejte ID aplikace a kryptografický otisk, který jste získali v předchozím kroku. 

   ![portál Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Pomocí přístupového tokenu ve skriptu PowerShellu se můžete dotazovat na Graph API. Pomocí rutiny **Invoke-MSCloudIdMSGraphQuery** z MSCloudIDUtils můžete vytvořit výčet koncového bodu nenašla a directoryAudits. Tato rutina zpracovává vícestránkové výsledky a odesílá výsledky do kanálu PowerShellu.

8. Dotaz na koncový bod directoryAudits, aby se načetly protokoly auditu 
   ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Dotaz na koncový bod nenašla, aby se načetly protokoly přihlášení
    ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

10. Nyní se můžete rozhodnout exportovat tato data do sdíleného svazku clusteru a uložit je do systému SIEM. Můžete také zabalit váš skript do naplánované úlohy, abyste získávali data Azure AD z vašeho klienta pravidelně bez nutnosti ukládat klíče aplikace ve zdrojovém kódu. 

## <a name="next-steps"></a>Další kroky

* [Získejte představu o rozhraní API pro generování sestav](concept-reporting-api.md)
* [Reference k rozhraní API auditu](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Reference k rozhraní API sestav aktivit přihlašování](/graph/api/resources/signin?view=graph-rest-beta)