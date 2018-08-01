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
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: a9dc30f1d303c6849ed016d0bc81ce1bf3d3d9d5
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390615"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Požadavky pro přístup k API pro vytváření sestav Azure Active Directory

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů.

Vytváření sestav pomocí rozhraní API [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) k autorizaci přístupu k webovému rozhraní API.

Pokud chcete připravit váš přístup k rozhraní API pro generování sestav, budete muset:

1. Přiřazení rolí
2. Registrace aplikace
3. Udělení oprávnění
4. Shromážděte nastavení konfigurace



## <a name="assign-roles"></a>Přiřazení rolí

Chcete-li získat přístup k datům sestav prostřednictvím rozhraní API, budete muset mít jednu z následujících rolí přiřadit:

- Čtecí zařízení pro zabezpečení

- Správce zabezpečení

- Globální správce




## <a name="register-an-application"></a>Registrace aplikace

Budete muset zaregistrovat aplikaci i v případě, že přistupujete k API pro vytváření sestav pomocí skriptu. To vám dává **ID aplikace**, které jsou požadovány pro volání rozhraní autorizace a umožňuje kódu přijímat tokeny.

K nastavení konfigurace adresáře pro přístup k rozhraní API pro generování sestav Azure AD, musíte se přihlásit k webu Azure portal pomocí účtu správce služby Azure, který je taky členem skupiny **globálního správce** role adresáře v tenantovi Azure AD.

> [!IMPORTANT]
> Aplikace spuštěné v přihlašovací údaje s oprávněními "admin" tímto způsobem může být velmi účinné, a proto prosím nezapomeňte zabezpečit ID a tajný klíč aplikace přihlašovacích údajů.
> 


**Pro registraci aplikace Azure Active Directory:**

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně klikněte na tlačítko **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **Azure Active Directory** klikněte na **registrace aplikací**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Na **registrace aplikací** klikněte na stránku, na panelu nástrojů v horní části **registrace nové aplikace**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Na **vytvořit** stránce, proveďte následující kroky:

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. V **název** textové pole, typ `Reporting API application`.

    b. Jako **typ aplikace**vyberte **webovou aplikaci nebo API**.

    c. V **přihlašovací adresa URL** textové pole, typ `https://localhost`.

    d. Klikněte na možnost **Vytvořit**. 


## <a name="grant-permissions"></a>Udělení oprávnění 

V závislosti na rozhraní API, které chcete získat přístup musíte aplikaci udělit následující oprávnění:  

| Rozhraní API | Oprávnění |
| --- | --- |
| Windows Azure Active Directory | Umožňuje získat oprávnění ke čtení dat adresáře. |
| Microsoft Graph | Přečtěte si, že všechna data protokolů auditu |


![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


Následující části jsou uvedené kroky pro obě rozhraní API. Pokud nechcete, aby pro přístup k jednomu z rozhraní API, související kroky můžete přeskočit.
 

**Udělení oprávnění aplikací k použití rozhraní API:**

1. Na **registrace aplikací** stránky, v seznamu aplikací klepněte na tlačítko **aplikace API pro generování sestav**.

2. Na **aplikace API pro generování sestav** klikněte na stránku, na panelu nástrojů v horní části **nastavení**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Na **nastavení** klikněte na **požadovaná oprávnění**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Na **požadovaná oprávnění** stránku, **API** klikněte na možnost **Windows Azure Active Directory**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Na **povolit přístup z** stránce **čtení dat adresáře** a zrušte zaškrtnutí možnosti **přihlášení a čtení profilu uživatele**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Na panelu nástrojů v horní části klikněte na tlačítko **Uložit**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Na **požadovaná oprávnění** klikněte na stránku, na panelu nástrojů v horní části **přidat**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. Na **přístup přes rozhraní API přidat** klikněte na **vyberte rozhraní API**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. Na **vyberte rozhraní API** klikněte na **Microsoft Graphu**a potom klikněte na tlačítko **vyberte**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. Na **povolit přístup z** stránce **číst všechna data protokolů auditu**a potom klikněte na tlačítko **vyberte**.  

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. Na **přístup přes rozhraní API přidat** klikněte na **provádí**.  

12. Na **požadovaná oprávnění** stránky, na panelu nástrojů v horní části. Klikněte na tlačítko **udělit oprávnění**a potom klikněte na tlačítko **Ano**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Shromážděte nastavení konfigurace 

V této části se dozvíte, jak získat následující nastavení z adresáře:

- Název domény
- ID klienta
- Tajný klíč klienta

Tyto hodnoty budete potřebovat při konfiguraci volání rozhraní API pro generování sestav. 

### <a name="get-your-domain-name"></a>Získání názvu domény

**Pokud chcete získat název domény:**

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně klikněte na tlačítko **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **Azure Active Directory** klikněte na **vlastní názvy domén**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Zkopírujte váš název domény z seznam domén.


### <a name="get-your-applications-client-id"></a>Získání ID klienta vaší aplikace

**Pokud chcete získat ID klienta vaší aplikace:**

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně klikněte na tlačítko **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **registrace aplikací** stránky, v seznamu aplikací klepněte na tlačítko **aplikace API pro generování sestav**.

3. Na **aplikace API pro generování sestav** stránky, na **ID aplikace**, klikněte na tlačítko **kopírování kliknutím**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Získat tajný kód klienta aplikace
Pokud chcete získat tajný kód klienta aplikace, budete muset vytvořit nový klíč a uložit jeho hodnota uloží nový klíč, protože není možné později už načetlo tuto hodnotu.

**Pokud chcete získat tajný kód klienta aplikace:**

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně klikněte na tlačítko **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **registrace aplikací** stránky, v seznamu aplikací klepněte na tlačítko **aplikace API pro generování sestav**.


3. Na **aplikace API pro generování sestav** klikněte na stránku, na panelu nástrojů v horní části **nastavení**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. Na **nastavení** stránku, **APIR přístup** klikněte na tlačítko **klíče**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Na **klíče** stránce, proveďte následující kroky:

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. V **popis** textové pole, typ `Reporting API`.

    b. Jako **Expires**vyberte **2 roky**.

    c. Klikněte na **Uložit**.

    d. Hodnotu klíče si zkopírujte.


## <a name="next-steps"></a>Další postup

* [Získání dat pomocí Azure Active Directory API pro vytváření sestav s certifikáty](active-directory-reporting-api-with-certificates.md)
* [Získejte představu o rozhraní API pro generování sestav](active-directory-reporting-api-getting-started-azure-portal.md)
* [Referenční informace k rozhraní API auditu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Sestavy aktivit přihlašování reference k rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
