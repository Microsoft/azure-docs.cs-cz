---
title: Požadavky pro přístup k API pro vytváření sestav Azure Active Directory | Dokumentace Microsoftu
description: Informace o požadavcích pro přístup k rozhraní API pro generování sestav Azure AD
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 03acd7c283fd1296af06dd19d0170a4b3c65eeb3
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352491"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Požadavky pro přístup k API pro vytváření sestav Azure Active Directory

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů.

Vytváření sestav pomocí rozhraní API [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) k autorizaci přístupu k webovému rozhraní API.

Pokud chcete připravit váš přístup k rozhraní API pro generování sestav, budete muset:

1. [Přiřazení rolí](#assign-roles)
2. [Registrace aplikace](#register-an-application)
3. [Udělení oprávnění](#grant-permissions)
4. [Shromážděte nastavení konfigurace](#gather-configuration-settings)

## <a name="assign-roles"></a>Přiřazení rolí

Chcete-li získat přístup k datům sestav prostřednictvím rozhraní API, budete muset mít jednu z následujících rolí přiřadit:

- Čtecí zařízení pro zabezpečení

- Správce zabezpečení

- Globální správce


## <a name="register-an-application"></a>Registrace aplikace

Budete muset zaregistrovat aplikaci i v případě, že přistupujete k API pro vytváření sestav pomocí skriptu. To vám dává **ID aplikace**, který se vyžaduje pro povolení volá a umožňuje kódu přijímat tokeny.

K nastavení konfigurace adresáře pro přístup k rozhraní API pro generování sestav Azure AD, musíte se přihlásit k [webu Azure portal](https://portal.azure.com) pomocí účtu správce služby Azure, který je taky členem skupiny **globálního správce** roli adresáře. ve vašem tenantovi Azure AD.

> [!IMPORTANT]
> Aplikace spuštěné v přihlašovací údaje s oprávněními správce může být velmi účinné, a proto prosím nezapomeňte si ID aplikace a tajného kódu přihlašovacích údajů na bezpečném místě.
> 

**Postup pro registraci aplikace Azure AD:**

1. V [webu Azure portal](https://portal.azure.com)vyberte **Azure Active Directory** v levém navigačním podokně.
   
    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. V **Azure Active Directory** stránce **registrace aplikací**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Z **registrace aplikací** stránce **registrace nové aplikace**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. V **vytvořit** stránce, proveďte následující kroky:

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. V **název** textové pole, typ `Reporting API application`.

    b. Jako **typ aplikace**vyberte **webovou aplikaci nebo API**.

    c. V **přihlašovací adresa URL** textové pole, typ `https://localhost`.

    d. Vyberte **Vytvořit**. 


## <a name="grant-permissions"></a>Udělení oprávnění 

V závislosti na rozhraní API, které chcete získat přístup musíte aplikaci udělit následující oprávnění:  

| Rozhraní API | Oprávnění |
| --- | --- |
| Windows Azure Active Directory | Čtení dat adresáře |
| Microsoft Graph | Přečtěte si, že všechna data protokolů auditu |


![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/36.png)

Následující části jsou uvedené kroky pro obě rozhraní API. Pokud nechcete, aby pro přístup k jednomu z rozhraní API, související kroky můžete přeskočit.

**Udělení oprávnění aplikací k použití rozhraní API:**

1. Vyberte svou aplikaci z **registrace aplikací** stránku a vybrat **nastavení**. 

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na **nastavení** stránce **požadovaná oprávnění**. 

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na **požadovaná oprávnění** stránku, **API** klikněte na možnost **Windows Azure Active Directory**. 

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na **povolit přístup z** stránce **čtení dat adresáře** a zrušte zaškrtnutí možnosti **přihlášení a čtení profilu uživatele**. 

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Na panelu nástrojů v horní části klikněte na tlačítko **Uložit**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. Na **požadovaná oprávnění** klikněte na stránku, na panelu nástrojů v horní části **přidat**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. Na **přístup přes rozhraní API přidat** klikněte na **vyberte rozhraní API**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. Na **vyberte rozhraní API** klikněte na **Microsoft Graphu**a potom klikněte na tlačítko **vyberte**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. Na **povolit přístup z** stránce **číst všechna data protokolů auditu**a potom klikněte na tlačítko **vyberte**.  

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. Na **přístup přes rozhraní API přidat** klikněte na **provádí**.  

11. Na **požadovaná oprávnění** stránky, na panelu nástrojů v horní části. Klikněte na tlačítko **udělit oprávnění**a potom klikněte na tlačítko **Ano**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Shromážděte nastavení konfigurace 

V této části se dozvíte, jak získat následující nastavení z adresáře:

- Název domény
- ID klienta
- Tajný klíč klienta

Tyto hodnoty budete potřebovat při konfiguraci volání rozhraní API pro generování sestav. 

### <a name="get-your-domain-name"></a>Získání názvu domény

**Pokud chcete získat název domény:**

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně vyberte **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na **Azure Active Directory** stránce **vlastní názvy domén**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Zkopírujte váš název domény z seznam domén.


### <a name="get-your-applications-client-id"></a>Získání ID klienta vaší aplikace

**Pokud chcete získat ID klienta vaší aplikace:**

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně klikněte na tlačítko **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Vyberte svou aplikaci z **registrace aplikací** stránky.

3. Ze stránky aplikace, přejděte na **ID aplikace** a vyberte **kopírování kliknutím**.

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Získat tajný kód klienta aplikace
Pokud chcete získat tajný kód klienta aplikace, budete muset vytvořit nový klíč a uložit jeho hodnota uloží nový klíč, protože není možné později už načetlo tuto hodnotu.

**Pokud chcete získat tajný kód klienta aplikace:**

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně klikněte na tlačítko **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Vyberte svou aplikaci z **registrace aplikací** stránky.

3. Na stránce aplikace, na panelu nástrojů v horní části vyberte **nastavení**. 

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. Na **nastavení** stránku, **přístup přes rozhraní API** klikněte na tlačítko **klíče**. 

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na **klíče** stránce, proveďte následující kroky:

    ![Zaregistrovat aplikaci](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. V **popis** textové pole, typ `Reporting API`.

    b. Jako **Expires**vyberte **2 roky**.

    c. Klikněte na **Uložit**.

    d. Hodnotu klíče si zkopírujte.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Řešení potíží s chybami v rozhraní API pro generování sestav

Tato část uvádí běžné chybové zprávy, které můžete narazit při přístupu k sestavy aktivit pomocí rozhraní MS Graph API a kroků pro jejich řešení.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 protokolu HTTP se interní chyba serveru při přístupu k Microsoft Graphu V2 koncového bodu

Aktuálně nepodporujeme koncového bodu v2 Microsoft Graphu – Ujistěte se, že přístup k protokolům aktivit pomocí koncového bodu v1 Microsoft Graphu.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Chyba: Nepovedlo se získat uživatelské role z AD Graphu

Může zobrazit tato chybová zpráva při pokusu o přístup k přihlášení pomocí Graph Exploreru. Ujistěte se, že jste přihlášeni ke svému účtu pomocí tlačítek přihlášení v Uživatelském rozhraní Graph Explorer, jak je znázorněno na následujícím obrázku. 

![Graph Exploreru](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Chyba: Nepovedlo se provést kontrola licence premium z AD Graphu 

Pokud narazíte na tato chybová zpráva při pokusu o přístup k přihlášení pomocí Graph Exploreru zvolte **upravit oprávnění** pod svůj účet na levém navigačním podokně a vyberte **Tasks.ReadWrite** a **Directory.Read.All**. 

![Upravit oprávnění uživatelského rozhraní](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Chyba: Je tenant B2C ani klient nemá licenci premium

Přístup k sestavy přihlášení Azure Active Directory premium 1 (P1) vyžaduje licenci. Pokud se zobrazí tato chybová zpráva při přístupu k přihlášení, ujistěte se, že je váš tenant licenci na licenci Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Chyba: Uživatel není povolené role 

Pokud při pokusu o přístup k protokolům auditu nebo přihlášení pomocí rozhraní API se zobrazí tato chybová zpráva, ujistěte se, že váš účet je součástí **Čtenář zabezpečení** nebo **čtenáře sestav** role ve službě Azure Active Directory tenanta. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Chyba: Aplikace AAD oprávnění čtení dat adresáře 

Postupujte podle kroků v [požadavky pro přístup k API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) zajistit, že aplikace běží s správnou sadu oprávnění. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Chyba: Aplikace oprávnění 'Číst všechna data protokolů auditu' MSGraph rozhraní API

Postupujte podle kroků v [požadavky pro přístup k API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) zajistit, že aplikace běží s správnou sadu oprávnění. 

## <a name="next-steps"></a>Další postup

* [Získání dat pomocí Azure Active Directory API pro vytváření sestav s certifikáty](tutorial-access-api-with-certificates.md)
* [Referenční informace k rozhraní API auditu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Sestavy aktivit přihlašování reference k rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
