---
title: Požadavky na přístup k službě Azure Active Directory, vytváření sestav rozhraní API | Microsoft Docs
description: Další informace o požadavcích pro přístup k Azure AD reporting rozhraní API
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
ms.openlocfilehash: 6d842b1af74c1b276f367e0ff15703880f7560aa
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224782"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Požadavky na přístup k službě Azure Active Directory, vytváření sestav rozhraní API

[Rozhraní API pro generování sestav v Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů.

Generování sestav používá rozhraní API [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) autorizovat přístup k webovému rozhraní API.

Pokud chcete připravit váš přístup k rozhraní API pro vytváření sestav, budete muset:

1. Přiřazení rolí
2. Registrace aplikace
3. Udělení oprávnění
4. Shromážděte nastavení konfigurace



## <a name="assign-roles"></a>Přiřazení rolí

Chcete-li získat přístup k data pro generování sestav prostřednictvím rozhraní API, musíte mít jeden z následujících role přiřazené:

- Čtecí zařízení pro zabezpečení

- Správce zabezpečení

- Globální správce.




## <a name="register-an-application"></a>Registrace aplikace

Je třeba zaregistrovat aplikaci, i když přistupujete k vytváření sestav rozhraní API pomocí skriptu. To vám dává **ID aplikace**, což je vyžadováno pro volání autorizace a umožňuje kódu přijímat tokeny.

Ke konfiguraci adresáře pro přístup k Azure AD reporting rozhraní API, musíte se přihlásit k portálu Azure pomocí účtu správce služby Azure, který je taky členem skupiny **globálního správce** role adresáře v klientovi služby Azure AD.

> [!IMPORTANT]
> Aplikace běžící v části přihlašovací údaje s oprávněním "admin", jako to může být velmi výkonné, takže Přesvědčte se, k zabezpečení přihlašovacích údajů ID nebo tajný klíč aplikace.
> 


**Zaregistrovat aplikaci Azure Active Directory:**

1. V [portál Azure](https://portal.azure.com), v levém navigačním podokně klikněte na tlačítko **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **Azure Active Directory** klikněte na tlačítko **registrace aplikace**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Na **registrace aplikace** stránky, na panelu nástrojů v horní části klikněte na tlačítko **nové registrace aplikace**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Na **vytvořit** proveďte následující kroky:

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. V **název** textovému poli, typ `Reporting API application`.

    b. Jako **typ aplikace**, vyberte **webovou aplikaci nebo API**.

    c. V **přihlašovací adresa URL** textovému poli, typ `https://localhost`.

    d. Klikněte na možnost **Vytvořit**. 


## <a name="grant-permissions"></a>Udělení oprávnění 

V závislosti na rozhraní API, které chcete získat přístup musíte aplikaci udělit následující oprávnění:  

| Rozhraní API | Oprávnění |
| --- | --- |
| Windows Azure Active Directory | Čtení dat adresáře |
| Microsoft Graph | Přečtěte si, že všechna data protokolu auditování |


![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


V následující části jsou uvedené kroky pro obě rozhraní API. Pokud nechcete, aby pro přístup k jednomu z rozhraní API, můžete přeskočit související kroky.
 

**K udělení oprávnění vaší aplikace pomocí rozhraní API:**

1. Na **registrace aplikace** klikněte na stránce, v seznamu aplikací **aplikace Reporting rozhraní API**.

2. Na **aplikace Reporting rozhraní API** stránky, na panelu nástrojů v horní části klikněte na tlačítko **nastavení**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Na **nastavení** klikněte na tlačítko **požadovaná oprávnění**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Na **požadovaná oprávnění** stránky v **rozhraní API** seznamu, klikněte na tlačítko **Windows Azure Active Directory**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Na **povolit přístup** vyberte **čtení dat adresáře** a zrušte výběr **přihlášení a čtení profilu uživatele**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Na panelu nástrojů v horní části klikněte na tlačítko **Uložit**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Na **požadovaná oprávnění** stránky, na panelu nástrojů v horní části klikněte na tlačítko **přidat**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. Na **přidat rozhraní API pro přístup** klikněte na tlačítko **vybrat rozhraní API**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. Na **vybrat rozhraní API** klikněte na tlačítko **Microsoft Graph**a potom klikněte na **vyberte**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. Na **povolit přístup** vyberte **načíst všechna data protokolu auditování**a potom klikněte na **vyberte**.  

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. Na **přidat rozhraní API pro přístup** klikněte na tlačítko **provést**.  

12. Na **požadovaná oprávnění** stránky, na panelu nástrojů v horní části. Klikněte na tlačítko **udělit oprávnění**a potom klikněte na **Ano**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Shromážděte nastavení konfigurace 

V této části se dozvíte, jak získat z adresáře následující nastavení:

- Název domény
- ID klienta
- Tajný klíč klienta

Je nutné tyto hodnoty při konfiguraci volání do rozhraní API pro generování sestav. 

### <a name="get-your-domain-name"></a>Získat název domény

**Pokud chcete získat název domény:**

1. V [portál Azure](https://portal.azure.com), v levém navigačním podokně klikněte na tlačítko **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **Azure Active Directory** klikněte na tlačítko **vlastní názvy domén**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Zkopírujte názvu domény ze seznamu domén.


### <a name="get-your-applications-client-id"></a>Získejte ID klienta aplikace

**Pokud chcete získat ID klienta aplikace:**

1. V [portál Azure](https://portal.azure.com), v levém navigačním podokně klikněte na tlačítko **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **registrace aplikace** klikněte na stránce, v seznamu aplikací **aplikace Reporting rozhraní API**.

3. Na **aplikace Reporting rozhraní API** stránky, na **ID aplikace**, klikněte na tlačítko **kliknutím zkopírujte**.

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Získat sdílený tajný klíč klienta vaší aplikace
Získat sdílený tajný klíč klienta aplikace, musíte vytvořit nový klíč a uložit jeho hodnota při ukládání nového klíče, protože není možné později už načíst tuto hodnotu.

**Pokud chcete získat sdílený tajný klíč klienta aplikace:**

1. V [portál Azure](https://portal.azure.com), v levém navigačním podokně klikněte na tlačítko **Azure Active Directory**.
   
    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **registrace aplikace** klikněte na stránce, v seznamu aplikací **aplikace Reporting rozhraní API**.


3. Na **aplikace Reporting rozhraní API** stránky, na panelu nástrojů v horní části klikněte na tlačítko **nastavení**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. Na **nastavení** stránky v **APIR přístup** klikněte na tlačítko **klíče**. 

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Na **klíče** proveďte následující kroky:

    ![Zaregistrovat aplikaci](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. V **popis** textovému poli, typ `Reporting API`.

    b. Jako **Expires**, vyberte **ve 2 roky**.

    c. Klikněte na **Uložit**.

    d. Zkopírujte hodnotu klíče.


## <a name="next-steps"></a>Další kroky

- [Získání dat pomocí Azure Active Directory, vytváření sestav rozhraní API s certifikáty](active-directory-reporting-api-with-certificates.md).

- [Získejte představu o rozhraní API pro generování sestav](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Vytvořte vlastní řešení](active-directory-reporting-api-getting-started-azure-portal.md#customize)

