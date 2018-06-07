---
title: 'Ověřování koncového uživatele: Data Lake Store s Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak zajistit ověření koncového uživatele s Data Lake Store pomocí Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 7280cd971e9857c494dfd1cb77d528e4737ed9d2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624144"
---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Ověření koncových uživatelů s Data Lake Store pomocí Azure Active Directory
> [!div class="op_single_selector"]
> * [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store využívá Azure Active Directory pro ověřování. Před vytváření aplikace, která funguje s Azure Data Lake Store nebo Azure Data Lake Analytics, musíte rozhodnout, jak ověřovat vaší aplikace pomocí Azure Active Directory (Azure AD). Jsou dvě hlavní možnosti k dispozici:

* Ověření koncových uživatelů (v tomto článku)
* Ověřování Service-to-service (vyberte tuto možnost rozevíracím výše)

Obě tyto možnosti má za následek aplikace se součástí tokenu OAuth 2.0, který získá připojen k každou žádost odeslanou Azure Data Lake Store nebo Azure Data Lake Analytics.

Tento článek pojednává o tom, jak vytvořit **nativní aplikaci Azure AD pro ověřování koncového uživatele**. Pokyny pro konfiguraci aplikace Azure AD pro ověřování služby pro služby, v tématu [Service-to-service ověřování s Data Lake Store pomocí Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* ID vašeho předplatného. Můžete ji načíst z portálu Azure. Například je k dispozici v okně účtu Data Lake Store.
  
    ![Získání ID předplatného](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Název domény vaší služby Azure AD. Můžete ji načíst podržením ukazatele myši v pravém horním rohu portálu Azure. Z na následující snímek obrazovky, je název domény **contoso.onmicrosoft.com**, a identifikátor GUID v závorkách je ID klienta. 
  
    ![Získat doménu AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Vaše ID klienta Azure. Pokyny týkající se načtení ID klienta najdete v tématu [získání ID tenanta](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
Tento mechanismus ověřování se o doporučený postup, pokud chcete, aby koncový uživatel pro přihlášení do aplikace pomocí Azure AD. Aplikace je pak mít přístup k prostředkům Azure se stejnou úrovní přístupu jako koncový uživatel, přihlášení. Koncový uživatel musí zadat své přihlašovací údaje pravidelně v pořadí pro vaši aplikaci k získání přístupu.

Výsledek toho, že přihlášení koncového uživatele je, že aplikace je daná přístupový token a obnovovací token. Získá přístupový token připojena k každý požadavek na Data Lake Store nebo Data Lake Analytics, a je platná pro jednu hodinu, ve výchozím nastavení. Token obnovení lze použít k získání nového tokenu přístupu, a je platná dobu až dvou týdnů ve výchozím nastavení. Dva různé přístupy můžete použít pro přihlášení koncového uživatele.

### <a name="using-the-oauth-20-pop-up"></a>Pomocí automaticky otevírané okno OAuth 2.0
Aplikace můžete aktivovat o OAuth 2.0 autorizační automaticky otevírané okno, ve kterém může koncový uživatel zadat své přihlašovací údaje. Toto automaticky otevírané okno taky spolupracuje se službou Azure AD dvoufaktorové ověřování (2FA) procesu, v případě potřeby. 

> [!NOTE]
> Tato metoda není dosud podporována v Azure AD Authentication Library (ADAL) pro Python nebo Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Předávání přímo v uživatelských přihlašovacích údajů
Aplikace můžete přímo zadat přihlašovací údaje uživatele do služby Azure AD. Tato metoda funguje jenom s účty organizace ID uživatele; není kompatibilní s osobní / "live ID" uživatelské účty, včetně účtů končící na @outlook.com nebo @live.com. Kromě toho tato metoda není kompatibilní s uživatelskými účty, které vyžadují Azure AD dvoufaktorové ověřování (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Co je třeba pro tento přístup?
* Název domény Azure AD. Tento požadavek již uveden v požadovaných součástí tohoto článku.
* Azure AD ID klienta. Tento požadavek již uveden v požadovaných součástí tohoto článku.
* Azure AD **nativní aplikace**
* ID aplikací pro nativní aplikaci Azure AD
* Identifikátor URI přesměrování pro nativní aplikaci Azure AD
* Nastavte delegovaná oprávnění.


## <a name="step-1-create-an-active-directory-native-application"></a>Krok 1: Vytvoření nativní aplikaci služby Active Directory

Vytvořit a nakonfigurovat nativní aplikaci Azure AD pro ověřování koncového uživatele s Azure Data Lake Store pomocí Azure Active Directory. Pokyny najdete v tématu [vytvořit aplikaci Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Zkontrolujte, zda jste vybrali při postupovat podle pokynů v odkazu, **nativní** pro typ aplikace, jak je znázorněno na následujícím snímku obrazovky:

![Vytvořit webovou aplikaci](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "vytvořit nativní aplikace")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Krok 2: Získání ID aplikace a identifikátor URI pro přesměrování

V tématu [získání ID aplikace](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) načíst ID aplikace.

Chcete-li získat identifikátor URI přesměrování, proveďte následující kroky.

1. Z portálu Azure vyberte **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**a najít a klikněte na nativní aplikaci Azure AD, kterou jste vytvořili.

2. Z **nastavení** pro aplikaci, klikněte na **identifikátory URI přesměrování**.

    ![Identifikátor URI pro přesměrování GET](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Zkopírujte zobrazené hodnoty.


## <a name="step-3-set-permissions"></a>Krok 3: Nastavte oprávnění.

1. Z portálu Azure vyberte **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**a najít a klikněte na nativní aplikaci Azure AD, kterou jste vytvořili.

2. Z **nastavení** pro aplikaci, klikněte na **požadovaná oprávnění**a potom klikněte na **přidat**.

    ![ID klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. V **přidat přístup pomocí rozhraní API** okně klikněte na tlačítko **vybrat rozhraní API**, klikněte na tlačítko **Azure Data Lake**a potom klikněte na **vyberte**.

    ![ID klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  V **přidat přístup pomocí rozhraní API** okně klikněte na tlačítko **vyberte oprávnění**, zaškrtněte políčko umožnit **úplný přístup do Data Lake Store**a potom klikněte na **vyberte**.

    ![ID klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klikněte na **Done** (Hotovo).

5. Zopakujte poslední dva kroky k udělení oprávnění pro **Windows Azure Service Management API** také.
   
## <a name="next-steps"></a>Další postup
V tomto článku jste vytvořili nativní aplikaci Azure AD a shromáždit informace, které budete potřebovat v klientských aplikacích vytvářet pomocí .NET SDK, sady Java SDK, REST API atd. Nyní můžete přejít na následující články, které komunikovat o tom, jak používat webové aplikace Azure AD nejprve ověřit pomocí Data Lake Store a poté provést jiné operace v úložišti.

* [Koncových-uživatelů-ověřování s Data Lake Store pomocí sady Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Ověřování koncového uživatele s Data Lake Store pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Ověření koncových uživatelů s Data Lake Store pomocí jazyka Python](data-lake-store-end-user-authenticate-python.md)
* [Ověřování koncového uživatele s Data Lake Store pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)

