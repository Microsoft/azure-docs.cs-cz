---
title: Získání dat pomocí rozhraní API pro generování sestav Azure AD s certifikáty | Dokumentace Microsoftu
description: Vysvětluje, jak používat rozhraní API pro generování sestav Azure AD s přihlašovacími údaji ve formě certifikátů k získání dat z adresářů bez zásahu uživatele.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0da0e5d4b7dd8ff000d6c56716bea1b36935af01
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928902"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Získání dat pomocí rozhraní API pro generování sestav Azure Active Directory s certifikáty

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](active-directory-reporting-api-getting-started-azure-portal.md) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů. Pokud chcete získat přístup Azure AD API pro generování sestav bez zásahu uživatele, můžete nakonfigurovat váš přístup k používání certifikátů.

To zahrnuje následující kroky:

1. [Nainstalujte příslušné požadované součásti](#install-prerequisites)
2. [Registrace certifikátu v aplikaci](#register-the-certificate-in-your-app)
3. [Získání přístupového tokenu pro rozhraní Graph API MS](#get-an-access-token-for-ms-graph-api)
4. [Dotazování rozhraní Graph API MS koncových bodů](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>Požadavky na instalaci

1. Nejprve se ujistěte, že jste dokončili [požadavky pro přístup k API pro vytváření sestav Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md). 

2. Stáhněte a nainstalujte Azure AD Powershell verze 2, postupujte podle pokynů v [Powershellu pro Azure Active Directory](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md)

3. Nainstalujte MSCloudIDUtils z [PowerShellGallery - MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Tento modul poskytuje několik rutin nástrojů, jako jsou:
    - Knihovny ADAL, které jsou potřebné pro ověřování
    - Přístupové tokeny od uživatele, klíče aplikace a certifikáty pomocí ADAL
    - Rozhraní Graph API zpracovávající stránkové výsledky

4. Pokud je vaše první přihlášení pomocí modulu spustit **instalace MSCloudIdUtilsModule** k dokončení instalace, v opačném případě můžete jednoduše importovat pomocí **Import-Module** příkaz prostředí Powershell.

Vaše relace by měl vypadat podobně jako tato obrazovka:

  ![Windows Powershell](./media/active-directory-reporting-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>Registrace certifikátu v aplikaci

1. Nejprve přejděte na stránku registrace vaší aplikace. Můžete to provést tak, že přejdete na [webu Azure portal](https://portal.azure.com), kliknutím na příkaz **Azure Active Directory**, pak levým na **registrace aplikací** a zvolíte aplikace ze seznamu. 

2. Pak použijte postup [registrace certifikátu v Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad) pro aplikaci. 

3. Poznamenejte si ID aplikace a kryptografický otisk certifikátu, který jste právě zaregistrovali s vaší aplikací. Pokud chcete najít kryptografický otisk ze stránky aplikace na portálu přejděte na **nastavení** a klikněte na tlačítko **klíče**. Kryptografický otisk bude probíhat **veřejné klíče** seznamu.

  
## <a name="get-an-access-token-for-ms-graph-api"></a>Získání přístupového tokenu pro rozhraní Graph API MS

K získání přístupového tokenu pro rozhraní Graph API MS, použijte **Get-MSCloudIdMSGraphAccessTokenFromCert** rutiny z modulu MSCloudIdUtils PowerShell. 

>[!NOTE]
>Budete muset použít ID aplikace (také označované jako ClientID) a kryptografický otisk certifikátu s privátním klíčem nainstalován v úložišti certifikátů počítače (CurrentUser nebo LocalMachine úložiště certifikátů).
>

 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Použití přístupového tokenu pro volání rozhraní Graph API

Teď můžete použít přístupový token Powershellového skriptu pro dotazování rozhraní Graph API. Níže najdete příklad používá **Invoke-MSCloudIdMSGraphQuery** rutiny z MSCloudIDUtils výčet přehledu přihlášení nebo diectoryAudits koncového bodu. Tato rutina zpracuje vícestránkové výsledky a pak tyto výsledky odešle do kanálu PowerShellu.

### <a name="query-the-directoryaudits-endpoint"></a>Dotaz na koncový bod DirectoryAudits
 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>Dotaz na koncový bod přehledu přihlášení
 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/query-signins.png)

Teď můžete tato data exportovat do sdíleného svazku clusteru a uložit do systému SIEM. Můžete také zabalit váš skript do naplánované úlohy, abyste získávali data Azure AD z vašeho klienta pravidelně bez nutnosti ukládat klíče aplikace ve zdrojovém kódu. 


## <a name="next-steps"></a>Další postup

- [Získejte představu o rozhraní API pro generování sestav](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Vytvořte vlastní řešení](active-directory-reporting-api-getting-started-azure-portal.md#customize)




