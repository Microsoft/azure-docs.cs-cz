---
title: Získání dat pomocí rozhraní API pro generování sestav Azure AD s certifikáty | Dokumentace Microsoftu
description: Vysvětluje, jak používat rozhraní API pro generování sestav Azure AD s přihlašovacími údaji ve formě certifikátů k získání dat z adresářů bez zásahu uživatele.
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 5ceb3d1ef99189e5b0cfcc48b38906ed28c07730
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588953"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Získání dat pomocí rozhraní API pro generování sestav Azure Active Directory s certifikáty

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů.

Pokud chcete k rozhraní API pro generování sestav v Azure AD přistupovat bez zásahu uživatele, můžete svůj přístup nakonfigurovat, aby používal certifikáty.

Tento článek:

- Poskytuje požadované kroky pro přístup k rozhraní API pro generování sestav v Azure AD pomocí certifikátů.
- Předpokládá, že jste dokončili [požadavky pro přístup k rozhraní API pro generování sestav v Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md). 


Pro přístup k rozhraní API pro generování sestav pomocí certifikátů je potřeba:

1. Instalace požadovaných součástí
2. Nastavení certifikátu v aplikaci 
3. Udělení oprávnění
4. Získání přístupového tokenu




Informace o zdrojovém kódu najdete v tématu popisujícím [využití modulu rozhraní API pro sestavy](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils). 

## <a name="install-prerequisites"></a>Požadavky na instalaci

Musíte mít nainstalované Azure AD PowerShell verze 2 a modul AzureADUtils.

1. Stáhněte a nainstalujte Azure AD PowerShell verze 2 podle pokynů pro [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).

2. Stáhněte modul Azure AD Utils z umístění [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Tento modul poskytuje několik rutin nástrojů, jako jsou:
    - Nejnovější verze ADAL pomocí Nugetu
    - Přístupové tokeny od uživatele, klíče aplikace a certifikáty pomocí ADAL
    - Rozhraní Graph API zpracovávající stránkové výsledky

**Instalace modulu Azure AD Utils:**

1. Vytvořte adresář pro uložení modulu nástrojů (například c:\azureAD) a stáhněte modul z GitHubu.
2. Otevřete relaci PowerShellu a přejděte do adresáře, který jste právě vytvořili. 
3. Naimportujte modul a nainstalujte ho v cestě modulů PowerShellu pomocí rutiny Install-AzureADUtilsModule. 

Relace by měla vypadat podobně jako tato obrazovka:

  ![Windows Powershell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>Nastavení certifikátu v aplikaci

**K nastavení certifikátu v aplikaci:**

1. [Získejte ID objektu](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id) vaší aplikace z portálu Azure Portal. 

  ![Azure Portal](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Otevřete relaci PowerShellu a připojte se ke službě Azure AD pomocí rutiny Connect-AzureAD.

  ![Azure Portal](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Pomocí rutiny New-AzureADApplicationCertificateCredential z AzureADUtils do ni přidejte přihlašovací údaje ve formě certifikátu. 

>[!Note]
>Je třeba zadat ID objektu aplikace, který jste si poznamenali dříve, a také objekt certifikátu (ten získáte pomocí jednotky Cert:).
>


  ![Azure Portal](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>Získání přístupového tokenu

Pokud chcete získat přístupový token, použijte rutinu **Get-AzureADGraphAPIAccessTokenFromCert** z AzureADUtils. 

>[!NOTE]
>Budete muset použít ID aplikace, místo ID objektu použitého v poslední části.
>

 ![Azure Portal](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Použití přístupového tokenu pro volání rozhraní Graph API

Teď můžete vytvořit skript. Dále je uvedený příklad používající rutinu **Invoke-AzureADGraphAPIQuery** z AzureADUtils. Tato rutina zpracuje vícestránkové výsledky a pak tyto výsledky odešle do kanálu PowerShellu. 

 ![Azure Portal](./media/active-directory-report-api-with-certificates/script-completed.png)

Nyní jste připraveni k exportu do souboru CSV a jeho uložení do systému SIEM. Můžete také zabalit váš skript do naplánované úlohy, abyste získávali data Azure AD z vašeho klienta pravidelně bez nutnosti ukládat klíče aplikace ve zdrojovém kódu. 

## <a name="next-steps"></a>Další kroky

- [Získejte představu o rozhraní API pro generování sestav](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Vytvořte vlastní řešení](active-directory-reporting-api-getting-started-azure-portal.md#customize)




