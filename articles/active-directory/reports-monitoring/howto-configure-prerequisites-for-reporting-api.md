---
title: Požadavky na rozhraní Azure Active Directory REPORTING API | Dokumenty společnosti Microsoft
description: Informace o předpokladech pro přístup k rozhraní API pro vytváření sestav Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd3580ca03fa49d428904c6da78fdf9cda202c7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991258"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Požadavky pro přístup k rozhraní API pro vytváření sestav služby Azure Active Directory

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z programovacích jazyků a nástrojů.

Rozhraní API pro vytváření sestav používá [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) k autorizaci přístupu k webovým rozhraním API.

Chcete-li připravit přístup k rozhraní API pro vytváření přehledů, je třeba:

1. [Přiřazení rolí](#assign-roles)
2. [Licenční požadavky](#license-requirements)
3. [Registrace aplikace](#register-an-application)
4. [Udělení oprávnění](#grant-permissions)
5. [Shromáždit nastavení konfigurace](#gather-configuration-settings)

## <a name="assign-roles"></a>Přiřazení rolí

Chcete-li získat přístup k datům sestav prostřednictvím rozhraní API, musíte mít přiřazenu jednu z následujících rolí:

- Čtečka zabezpečení

- Správce zabezpečení

- Globální správce

## <a name="license-requirements"></a>Licenční požadavky

Aby bylo možné získat přístup k přihlašovací sestavy pro klienta, klient Azure AD musí mít přidružené licence Azure AD Premium. Licence Azure AD Premium P1 (nebo vyšší) je vyžadována pro přístup k přihlašovacím sestavám pro všechny klienty Azure AD. Případně pokud typ adresáře je Azure AD B2C , sestavy přihlášení jsou přístupné prostřednictvím rozhraní API bez jakékoli další licenční požadavek. 


## <a name="register-an-application"></a>Registrace aplikace

Registrace je nutná i v případě, že přistupujete k rozhraní API pro vytváření sestav pomocí skriptu. Registrace vám dává **ID aplikace**, které je vyžadováno pro volání autorizace a umožňuje váš kód přijímat tokeny.

Chcete-li nakonfigurovat svůj adresář pro přístup k rozhraní API pro vytváření sestav Azure AD, musíte se přihlásit k [portálu Azure pomocí](https://portal.azure.com) účtu správce Azure, který je také členem role adresáře **globálního správce** ve vašem tenantovi Azure AD.

> [!IMPORTANT]
> Aplikace spuštěné pod přihlašovacími údaji s oprávněními správce mohou být velmi výkonné, proto nezapomeňte zachovat ID a tajné přihlašovací údaje aplikace na bezpečném místě.
> 

**Registrace aplikace Azure AD:**

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **azure active directory** v levém navigačním podokně.
   
    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stránce **Azure Active Directory** vyberte Registrace **aplikací**.

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na stránce **Registrace aplikací** vyberte **Nová registrace**.

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Stránka **Registrace přihlášky:**

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Do **Name** textového pole `Reporting API application`Název zadejte .

    b. V **části Podporované účty**vyberte možnost Účty pouze v této **organizaci**.

    c. Do **textového** pole Přesměrování vyberte `https://localhost` **webové** textové pole , zadejte .

    d. Vyberte **Zaregistrovat**. 


## <a name="grant-permissions"></a>Udělení oprávnění 

V závislosti na rozhraní API, ke kterým chcete získat přístup, musíte aplikaci udělit následující oprávnění:  

| rozhraní API | Oprávnění |
| --- | --- |
| Windows Azure Active Directory | Čtení dat z adresáře |
| Microsoft Graph | Čtení všech dat protokolu auditu |


![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/36.png)

V následující části jsou uvedeny kroky pro obě api. Pokud nechcete získat přístup k jednomu z těchto api, můžete související kroky přeskočit.

**Udělení oprávnění aplikace k používání souborů API:**


1. Vyberte **oprávnění rozhraní API** a potom **přidejte oprávnění**. 

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na **stránce Požádat o oprávnění rozhraní API**vyhledejte **službu Support Legacy API** Azure Active Directory **Graph**. 

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na stránce **Požadovaná oprávnění** vyberte **Oprávnění aplikací**, rozbalte **políčko** **Directory.**  Vyberte **Přidat oprávnění**.

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na stránce **Reporting API Application – API Permissions** vyberte **Udělit souhlas správce**. 

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Poznámka: **Microsoft Graph** je přidán ve výchozím nastavení během registrace rozhraní API.

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Shromáždit nastavení konfigurace 

V této části se zobrazí, jak z adresáře získat následující nastavení:

- Název domény
- ID klienta
- Tajný klíč klienta

Tyto hodnoty potřebujete při konfiguraci volání rozhraní API pro vytváření sestav. 

### <a name="get-your-domain-name"></a>Získejte název domény

**Jak získat název domény:**

1. Na [webu Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.
   
    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stránce **Azure Active Directory** vyberte Vlastní **názvy domén**.

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Zkopírujte název domény ze seznamu domén.


### <a name="get-your-applications-client-id"></a>Získání ID klienta aplikace

**Jak získat ID klienta vaší aplikace:**

1. Na [webu Azure Portal](https://portal.azure.com)klikněte v levém navigačním podokně na **Položku Azure Active Directory**.
   
    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Vyberte svou přihlášku na stránce **Registrace aplikací.**

3. Na stránce aplikace přejděte na **ID aplikace** a vyberte **Klepnutím kopírovat**.

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Získejte tajný klíč klienta aplikace
 Vyhněte se chybám při pokusu o přístup k protokolům auditu nebo přihlášení pomocí rozhraní API.

**Chcete-li získat tajný klíč klienta aplikace:**

1. Na [webu Azure Portal](https://portal.azure.com)klikněte v levém navigačním podokně na **Položku Azure Active Directory**.
   
    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Vyberte svou přihlášku na stránce **Registrace aplikací.**

3.  Vyberte **certifikáty a tajné klíče** na stránce **aplikace rozhraní API,** v části **Tajné klíče klienta** klepněte na + **Nový tajný klíč klienta**. 

    ![Registrace aplikace](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na stránce **Přidat tajný klíč klienta** přidejte:

    a. Do **Description** textového pole `Reporting API`Popis zadejte .

    b. As **Expires**vyberte **Za 2 roky**.

    c. Klikněte na **Uložit**.

    d. Zkopírujte si hodnotu klíče.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Poradce při potížích v rozhraní API pro vytváření přehledů

V této části jsou uvedeny běžné chybové zprávy, které můžete narazit při přístupu k sestavce aktivit pomocí rozhraní Microsoft Graph API a kroky pro jejich řešení.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Chyba: Nepodařilo se získat role uživatelů z microsoft graphu.

 Přihlaste se ke svému účtu pomocí obou přihlašovacích tlačítek v uzdu aplikace Graph Explorer, abyste se vyhnuli chybě při pokusu o přihlášení pomocí Aplikace Graph Explorer. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Chyba: Kontrola prémiových licencí z aplikace Microsoft Graph se nezdařila. 

Pokud narazíte na tuto chybovou zprávu při pokusu o přístup k přihlášení pomocí průzkumníka grafů, zvolte **Změnit oprávnění** pod svým účtem v levém nav a vyberte **Tasks.ReadWrite** a **Directory.Read.All**. 

![Upravit ui oprávnění](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Chyba: Tenant není B2C nebo klient nemá prémiovou licenci

Přístup k přihlašovacím sestavům vyžaduje licenci Služby Azure Active Directory premium 1 (P1). Pokud se při přístupu k přihlášení zobrazí tato chybová zpráva, ujistěte se, že váš tenant má licenci s licencí Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Chyba: Povolené role nezahrnují uživatele. 

 Vyhněte se chybám při pokusu o přístup k protokolům auditu nebo přihlášení pomocí rozhraní API. Ujistěte se, že váš účet je součástí **role Čtečka zabezpečení** nebo **Čtečka sestav** v tenantovi služby Azure Active Directory.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Chyba: V aplikaci chybí oprávnění AAD číst data adresáře. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Chyba: Aplikace chybí Microsoft Graph API 'Číst všechna data protokolu auditu' oprávnění

Postupujte podle pokynů v [požadavcích na přístup k rozhraní API pro vytváření sestav služby Azure Active Directory,](howto-configure-prerequisites-for-reporting-api.md) abyste zajistili, že vaše aplikace běží se správnou sadou oprávnění. 

## <a name="next-steps"></a>Další kroky

* [Získání dat pomocí rozhraní API pro generování sestav Azure Active Directory s certifikáty](tutorial-access-api-with-certificates.md)
* [Odkaz na rozhraní API auditu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Odkaz na rozhraní API sestavy přihlašovacích aktivit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
