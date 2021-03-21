---
title: Předpoklady pro rozhraní API pro vytváření sestav Azure Active Directory | Microsoft Docs
description: Další informace o požadavcích pro přístup k rozhraní API pro vytváření sestav Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ca618dc234ff4383083ff5eb21299fb1e3e7128
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348734"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](./concept-reporting-api.md) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z programovacích jazyků a nástrojů.

Rozhraní API pro vytváření sestav používá protokol [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) k autorizaci přístupu k webovým rozhraním API.

K přípravě přístupu k rozhraní API pro vytváření sestav potřebujete:

1. [Přiřazení rolí](#assign-roles)
2. [Licenční požadavky](#license-requirements)
3. [Registrace aplikace](#register-an-application)
4. [Udělení oprávnění](#grant-permissions)
5. [Shromáždit nastavení konfigurace](#gather-configuration-settings)

## <a name="assign-roles"></a>Přiřazení rolí

Chcete-li získat přístup k datům sestav prostřednictvím rozhraní API, je nutné mít přiřazenou jednu z následujících rolí:

- Čtenář zabezpečení

- Správce zabezpečení

- Globální správce

## <a name="license-requirements"></a>Licenční požadavky

Aby bylo možné získat přístup k sestavám přihlášení pro tenanta, musí mít tenant služby Azure AD přidruženou Azure AD Premium licenci. Pro přístup k sestavám přihlášení pro libovolného tenanta Azure AD se vyžaduje licence Azure AD Premium P1 (nebo vyšší). Případně, pokud je Azure AD B2C typ adresáře, přihlašovací sestavy jsou přístupné prostřednictvím rozhraní API bez jakýchkoli dalších licenčních požadavků. 


## <a name="register-an-application"></a>Registrace aplikace

Registrace je nutná i v případě, že přistupujete k rozhraní API pro vytváření sestav pomocí skriptu. Registrace vám poskytne **ID aplikace**, které je vyžadováno pro autorizační volání, a umožňuje kódu přijímat tokeny.

Pokud chcete nakonfigurovat adresář pro přístup k rozhraní API pro vytváření sestav Azure AD, musíte se přihlásit k [Azure Portal](https://portal.azure.com) pomocí účtu správce Azure, který je zároveň členem role adresáře **globálního správce** v tenantovi Azure AD.

> [!IMPORTANT]
> Aplikace spuštěné v rámci přihlašovacích údajů s oprávněními správce můžou být velmi výkonné, takže si nezapomeňte zachovat ID a tajné přihlašovací údaje aplikace v zabezpečeném umístění.
> 

**Registrace aplikace Azure AD:**

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory** .
   
    ![Snímek obrazovky zobrazuje Azure Active Directory vybraný v nabídce Azure Portal.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stránce **Azure Active Directory** vyberte možnost **Registrace aplikací**.

    ![Snímek obrazovky ukazuje Registrace aplikací vybraný v nabídce spravovat.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na stránce **Registrace aplikací** vyberte možnost **Nová registrace**.

    ![Snímek obrazovky zobrazuje vybranou novou registraci.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Stránka **Registrace aplikace** :

    ![Snímek obrazovky se zobrazí stránka Registrovat aplikaci, kde můžete zadat hodnoty v tomto kroku.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Do textového pole **název** zadejte `Reporting API application` .

    b. V případě **typu podporované účty** vyberte **účty jenom v této organizaci**.

    c. V poli **Adresa URL pro přesměrování**  vyberte **webové** textové pole a zadejte `https://localhost` .

    d. Vyberte **Zaregistrovat**. 


## <a name="grant-permissions"></a>Udělení oprávnění 

V závislosti na rozhraní API, ke kterému chcete získat přístup, musíte aplikaci udělit následující oprávnění:  

| Rozhraní API | Oprávnění |
| --- | --- |
| Windows Azure Active Directory | Čtení dat z adresáře |
| Microsoft Graph | Číst všechna data protokolu auditu |

![Snímek obrazovky ukazuje, kde v podokně oprávnění P I můžete vybrat přidat oprávnění.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

V následující části jsou uvedeny kroky pro obě rozhraní API. Pokud nechcete přístup k jednomu z rozhraní API, můžete související kroky přeskočit.

**Chcete-li udělit vaší aplikaci oprávnění k používání rozhraní API:**


1. Vyberte **oprávnění rozhraní API** a pak **přidejte oprávnění**. 

    ![Snímek obrazovky se zobrazí stránka oprávnění P I, kde můžete vybrat možnost Přidat oprávnění.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na **stránce požádat o oprávnění API** vyhledejte **podporu starší rozhraní API** **Azure Active Directory Graph**. 

    ![Snímek obrazovky s požadavkem na stránku oprávnění P, kde můžete vybrat Azure Active Directory Graph](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na stránce **požadovaná oprávnění** vyberte **oprávnění aplikace**, rozbalte položku **adresář** zaškrtávací políčko **adresář. ReadAll**.  Vyberte **Přidat oprávnění**.

    ![Snímek obrazovky s žádostí o stránku oprávnění P I, kde můžete vybrat oprávnění aplikace](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na stránce oprávnění rozhraní API pro **vytváření sestav aplikace API** vyberte **udělit souhlas správce**. 

    ![Snímek obrazovky zobrazující stránku oprávnění p i pro aplikaci, kde můžete vybrat udělit souhlas správce.](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Poznámka: během registrace rozhraní API se ve výchozím nastavení přidá **Microsoft Graph** .

    ![Snímek obrazovky se zobrazí stránka oprávnění P I, kde můžete vybrat možnost Přidat oprávnění.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Shromáždit nastavení konfigurace 

V této části se dozvíte, jak z vašeho adresáře získat následující nastavení:

- Název domény
- ID klienta
- Tajný klíč klienta

Tyto hodnoty budete potřebovat při konfiguraci volání rozhraní API pro vytváření sestav. 

### <a name="get-your-domain-name"></a>Získat název domény

**Postup získání názvu domény:**

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.
   
    ![Snímek obrazovky zobrazuje Azure Active Directory vybraný v nabídce Azure Portal.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stránce **Azure Active Directory** vyberte **vlastní názvy domén**.

    ![Snímek obrazovky zobrazuje vlastní názvy domén vybrané z Azure Active Directory.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Zkopírujte název domény ze seznamu domén.


### <a name="get-your-applications-client-id"></a>Získat ID klienta vaší aplikace

**Chcete-li získat ID klienta aplikace:**

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně klikněte na **Azure Active Directory**.
   
    ![Snímek obrazovky zobrazuje Azure Active Directory vybraný v nabídce Azure Portal.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stránce **Registrace aplikací** vyberte svou aplikaci.

3. Na stránce aplikace přejděte na **ID aplikace** a vyberte **Kopírovat Kliknutím**.

    ![Snímek obrazovky zobrazující stránku aplikace P I, kde můžete zkopírovat aplikaci I D.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Získání tajného kódu klienta aplikace
 Vyhněte se chybám při pokusu o přístup k protokolům auditu nebo přihlašování pomocí rozhraní API.

**Získání tajného kódu klienta aplikace:**

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně klikněte na **Azure Active Directory**.
   
    ![Snímek obrazovky zobrazuje Azure Active Directory vybraný v nabídce Azure Portal.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Na stránce **Registrace aplikací** vyberte svou aplikaci.

3.  Na stránce **aplikace API** vyberte **certifikáty a tajné klíče** . v části **tajné klíče klienta** klikněte na **+ nový tajný klíč klienta**. 

    ![Snímek obrazovky se zobrazí na stránce certifikáty & tajných kódů, kde můžete přidat tajný klíč klienta.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na stránce **Přidat tajný klíč klienta** přidejte:

    a. Do textového pole **Popis** zadejte `Reporting API` .

    b. Po **vypršení platnosti** vyberte **2 roky**.

    c. Klikněte na **Uložit**.

    d. Zkopírujte si hodnotu klíče.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Řešení chyb v rozhraní API pro vytváření sestav

V této části jsou uvedené běžné chybové zprávy, se kterými se můžete setkat při přístupu k sestavám aktivit pomocí rozhraní Microsoft Graph API a postupu pro jejich řešení.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Chyba: Nepodařilo se získat role uživatele z Microsoft Graph

 Přihlaste se k účtu pomocí přihlašovacích tlačítek v uživatelském rozhraní Průzkumníka graphu, abyste se vyhnuli zobrazování chyby při pokusu o přihlášení pomocí Graph Exploreru. 

![Průzkumník grafů](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Chyba: nepovedlo se provést kontrolu licence Premium z Microsoft Graph 

Pokud při pokusu o přístup k přihlašování pomocí Graph Exploreru spustíte tuto chybovou zprávu, zvolte možnost **změnit oprávnění** pod vaším účtem na levém navigačním panelu a vyberte **Tasks.** **pročíst a adresář. Read. All**. 

![Upravit uživatelské rozhraní oprávnění](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Chyba: tenant není B2C nebo tenant nemá licenci Premium.

Přístup k sestavám přihlášení vyžaduje licenci Azure Active Directory Premium 1 (P1). Pokud se zobrazí tato chybová zpráva při přístupu k přihlašování, ujistěte se, že je váš tenant licencován pomocí licence Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Chyba: povolené role nezahrnují uživatele. 

 Vyhněte se chybám při pokusu o přístup k protokolům auditu nebo přihlašování pomocí rozhraní API. Ujistěte se, že je váš účet součástí role **Čtenář zabezpečení** nebo **čtecího modulu sestav** ve vašem tenantovi Azure Active Directory.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Chyba: aplikace postrádá oprávnění pro čtení dat adresáře služby AAD 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Chyba: v aplikaci chybí oprávnění Microsoft Graph rozhraní API pro čtení všech dat protokolu auditu.

Postupujte podle kroků v části [požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , abyste zajistili, že vaše aplikace běží se správnou sadou oprávnění. 

## <a name="next-steps"></a>Další kroky

* [Získání dat pomocí rozhraní API pro generování sestav Azure Active Directory s certifikáty](tutorial-access-api-with-certificates.md)
* [Reference k rozhraní API auditu](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Reference k rozhraní API sestav aktivit přihlašování](/graph/api/resources/signin?view=graph-rest-beta)