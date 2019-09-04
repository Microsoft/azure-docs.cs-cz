---
title: Požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory | Microsoft Docs
description: Další informace o požadavcích pro přístup k rozhraní API pro vytváření sestav Azure AD
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/30/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7b6fab4a4a36691bbdeb11975c7a93b97ab86cb
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241533"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z programovacích jazyků a nástrojů.

Rozhraní API pro vytváření sestav používá protokol [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) k autorizaci přístupu k webovým rozhraním API.

K přípravě přístupu k rozhraní API pro vytváření sestav potřebujete:

1. [Přiřazení rolí](#assign-roles)
2. [Registrace aplikace](#register-an-application)
3. [Udělit oprávnění](#grant-permissions)
4. [Shromáždit nastavení konfigurace](#gather-configuration-settings)

## <a name="assign-roles"></a>Přiřazení rolí

Chcete-li získat přístup k datům sestav prostřednictvím rozhraní API, je nutné mít přiřazenou jednu z následujících rolí:

- Čtenář zabezpečení

- Správce zabezpečení

- Globální správce


## <a name="register-an-application"></a>Zaregistrovat aplikaci

Registrace je nutná i v případě, že přistupujete k rozhraní API pro vytváření sestav pomocí skriptu. Registrace vám poskytne **ID aplikace**, které je vyžadováno pro autorizační volání, a umožňuje kódu přijímat tokeny.

Pokud chcete nakonfigurovat adresář pro přístup k rozhraní API pro vytváření sestav Azure AD, musíte se přihlásit k [Azure Portal](https://portal.azure.com) pomocí účtu správce Azure, který je zároveň členem role adresáře **globálního správce** v tenantovi Azure AD.

> [!IMPORTANT]
> Aplikace spuštěné v rámci přihlašovacích údajů s oprávněními správce můžou být velmi výkonné, takže si nezapomeňte zachovat ID a tajné přihlašovací údaje aplikace v zabezpečeném umístění.
> 

**Registrace aplikace Azure AD:**

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory** .
   
    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stránce **Azure Active Directory** vyberte možnost **Registrace aplikací**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na stránce **Registrace aplikací** vyberte možnost **Nová registrace**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Stránka **Registrace aplikace** :

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Do textového pole **název** zadejte `Reporting API application`.

    b. V případě **typu podporované účty**vyberte **účty jenom v této organizaci**.

    c. V poli **Adresa URL pro přesměrování** vyberte **webové** textové `https://localhost`pole a zadejte.

    d. Vyberte **Zaregistrovat**. 


## <a name="grant-permissions"></a>Udělení oprávnění 

V závislosti na rozhraní API, ke kterému chcete získat přístup, musíte aplikaci udělit následující oprávnění:  

| rozhraní API | Oprávnění |
| --- | --- |
| Azure Active Directory Windows | Čtení dat z adresáře |
| Microsoft Graph | Číst všechna data protokolu auditu |


![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/36.png)

V následující části jsou uvedeny kroky pro obě rozhraní API. Pokud nechcete přístup k jednomu z rozhraní API, můžete související kroky přeskočit.

**Chcete-li udělit vaší aplikaci oprávnění k používání rozhraní API:**


1. Vyberte **oprávnění rozhraní API** a pak **přidejte oprávnění**. 

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na **stránce požádat o oprávnění API**vyhledejte **podporu starší rozhraní API** **Azure Active Directory Graph**. 

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na stránce **požadovaná oprávnění** vyberte **oprávnění aplikace**, rozbalte položku **adresář** zaškrtávací políčko **adresář. ReadAll**.  Vyberte **Přidat oprávnění**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na stránce oprávnění rozhraní API pro **vytváření sestav aplikace API** vyberte **udělit souhlas správce**. 

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Poznámka: Během registrace rozhraní API se ve výchozím nastavení přidá **Microsoft Graph** .

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Shromáždit nastavení konfigurace 

V této části se dozvíte, jak z vašeho adresáře získat následující nastavení:

- Název domény
- ID klienta
- Tajný kód klienta

Tyto hodnoty budete potřebovat při konfiguraci volání rozhraní API pro vytváření sestav. 

### <a name="get-your-domain-name"></a>Získat název domény

**Postup získání názvu domény:**

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stránce **Azure Active Directory** vyberte **vlastní názvy domén**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Zkopírujte název domény ze seznamu domén.


### <a name="get-your-applications-client-id"></a>Získat ID klienta vaší aplikace

**Chcete-li získat ID klienta aplikace:**

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně klikněte na **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stránce **Registrace aplikací** vyberte svou aplikaci.

3. Na stránce aplikace přejděte na **ID aplikace** a vyberte **Kopírovat Kliknutím**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Získání tajného kódu klienta aplikace
 Vyhněte se chybám při pokusu o přístup k protokolům auditu nebo přihlašování pomocí rozhraní API.

**Získání tajného kódu klienta aplikace:**

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně klikněte na **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Na stránce **Registrace aplikací** vyberte svou aplikaci.

3.  Na stránce **aplikace API** vyberte **certifikáty a tajné klíče** . v části **tajné klíče klienta** klikněte na **+ nový tajný klíč klienta**. 

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na stránce **Přidat tajný klíč klienta** přidejte:

    a. Do textového pole **Popis** zadejte `Reporting API`.

    b. Po **vypršení platnosti**vyberte **2 roky**.

    c. Klikněte na **Uložit**.

    d. Zkopírujte hodnotu klíče.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Řešení chyb v rozhraní API pro vytváření sestav

V této části jsou uvedené běžné chybové zprávy, se kterými se můžete setkat při přístupu k sestavám aktivit pomocí MS Graph API a kroků pro jejich řešení.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 interní chyba serveru HTTP při přístupu ke koncovému bodu Microsoft Graph v2

Momentálně nepodporujeme koncový bod Microsoft Graph v2 – zajistěte přístup k protokolům aktivit pomocí koncového bodu Microsoft Graph v1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Chyba: Nepovedlo se získat role uživatele z AD graphu.

 Přihlaste se k účtu pomocí přihlašovacích tlačítek v uživatelském rozhraní Průzkumníka graphu, abyste se vyhnuli zobrazování chyby při pokusu o přihlášení pomocí Graph Exploreru. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Chyba: Nepovedlo se provést kontrolu licence Premium ze služby AD Graph. 

Pokud při pokusu o přístup k přihlašování pomocí Graph Exploreru spustíte tuto chybovou zprávu, zvolte možnost **změnit oprávnění** pod vaším účtem na levém navigačním panelu a vyberte **Tasks.** **pročíst a adresář. Read. All**. 

![Upravit uživatelské rozhraní oprávnění](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Chyba: Tenant není B2C nebo tenant nemá licenci Premium.

Přístup k sestavám přihlášení vyžaduje licenci Azure Active Directory Premium 1 (P1). Pokud se zobrazí tato chybová zpráva při přístupu k přihlašování, ujistěte se, že je váš tenant licencován pomocí licence Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Chyba: Povolené role nezahrnují uživatele. 

 Vyhněte se chybám při pokusu o přístup k protokolům auditu nebo přihlašování pomocí rozhraní API. Ujistěte se, že je váš účet součástí role **Čtenář zabezpečení** nebo **čtecího modulu sestav** ve vašem tenantovi Azure Active Directory.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Chyba: Aplikace nemá oprávnění pro čtení dat adresáře AAD. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Chyba: V aplikaci chybí oprávnění pro čtení všech dat protokolu MSGraph API.

Postupujte podle kroků v části [požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , abyste zajistili, že vaše aplikace běží se správnou sadou oprávnění. 

## <a name="next-steps"></a>Další postup

* [Získání dat pomocí rozhraní API pro vytváření sestav Azure Active Directory s certifikáty](tutorial-access-api-with-certificates.md)
* [Reference k rozhraní API auditu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Reference k rozhraní API sestav aktivit přihlašování](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
