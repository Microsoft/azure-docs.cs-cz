---
title: 'Ověřování koncového uživatele: Azure Data Lake Storage Gen1 službou Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak dokončit ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
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
ms.openlocfilehash: 2f0638b2449bfd582cb68e26d2043b7bc85342b6
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125948"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
> [!div class="op_single_selector"]
> * [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Storage Gen1 používá Azure Active Directory pro ověřování. Před vytvářením obsahu aplikace, která funguje s Data Lake Storage Gen1 nebo Azure Data Lake Analytics, musíte rozhodnout, jak k ověřování vaší aplikace s Azure Active Directory (Azure AD). Jsou k dispozici dvě hlavní možnosti:

* Ověřování koncového uživatele (Tento článek)
* Ověřování služba služba (vyberte tuto možnost z rozevíracího seznamu výše)

Obě tyto možnosti má za následek aplikace poskytované k tokenu OAuth 2.0, který získá připojen k každého požadavku na Data Lake Storage Gen1 nebo Azure Data Lake Analytics.

Tento článek pojednává o tom, jak vytvořit **nativní aplikaci Azure AD pro ověřování koncového uživatele**. Konfigurace aplikace Azure AD pro ověřování služba služba, v tématu [ověřování služba služba Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* ID vašeho předplatného. Můžete ji načíst z portálu Azure portal. Například je k dispozici v okně účtu Data Lake Storage Gen1.
  
    ![Získejte ID předplatného](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Název domény Azure AD. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. Na snímku obrazovky níže, název domény je **contoso.onmicrosoft.com**, a identifikátor GUID v závorkách je ID tenanta. 
  
    ![Získejte doménu AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* ID vašeho tenanta Azure. Pokyny o tom, jak získat ID tenanta najdete v tématu [získání ID tenanta](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
Tento mechanismus ověřování je doporučený postup, pokud chcete, aby koncový uživatel pro přihlášení do aplikace přes Azure AD. Vaše aplikace bude mít přístup k prostředkům Azure se stejnou úrovní přístupu jako koncový uživatel, který přihlášení. Koncový uživatel musí zadat přihlašovací údaje pravidelně v pořadí pro vaši aplikaci k Udržovat přístup.

Výsledek s přihlášení koncových uživatelů je, že vaše aplikace je zadaný přístupový token a aktualizační token. Získá přístupový token připojené k každého požadavku na Data Lake Storage Gen1 nebo Data Lake Analytics a na jednu hodinu, ve výchozím nastavení je platný. Token obnovení je možné získat nový přístupový token a je platný po dobu až dvou týdnů ve výchozím nastavení. Pro přihlášení koncových uživatelů můžete použít dva různé přístupy.

### <a name="using-the-oauth-20-pop-up"></a>Pomocí automaticky otevírané okno OAuth 2.0
Aplikace můžete aktivovat automaticky otevíraném okně autorizaci OAuth 2.0 ve kterém může uživatel zadat přihlašovací údaje. Toto automaticky otevírané okno se také funguje s Azure AD dvojúrovňového ověřování (2FA) proces v případě potřeby. 

> [!NOTE]
> Tato metoda není zatím podporována v Azure AD Authentication Library (ADAL) pro Python nebo Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Předat přímo v uživatelských přihlašovacích údajů
Vaše aplikace může zadat přihlašovací údaje uživatele přímo do služby Azure AD. Tato metoda funguje jenom s účty organizace ID uživatele; není kompatibilní s osobní / "live ID" uživatelských účtů, včetně účtů s koncovkou @outlook.com nebo @live.com. Kromě toho tato metoda není kompatibilní s uživatelskými účty, které vyžadují Azure AD dvojúrovňového ověřování (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Co musím udělat pro tento přístup?
* Název domény Azure AD. Tento požadavek je již uveden v požadavku v tomto článku.
* ID tenanta Azure AD Tento požadavek je již uveden v požadavku v tomto článku.
* Azure AD **nativní aplikace**
* ID aplikace pro nativní aplikaci Azure AD
* Identifikátor URI přesměrování pro nativní aplikaci Azure AD
* Nastavte delegovaná oprávnění.


## <a name="step-1-create-an-active-directory-native-application"></a>Krok 1: Vytvořte nativní aplikaci služby Active Directory

Vytvoření a konfigurace nativní aplikaci Azure AD pro ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Azure Active Directory. Pokyny najdete v tématu [vytvořit aplikaci Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Při postupujte podle pokynů v odkazu, ujistěte se, že vyberete **nativní** pro typ aplikace, jak je znázorněno na následujícím snímku obrazovky:

![Vytvoření webové aplikace](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "vytvořit nativní aplikaci")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Krok 2: Získání ID aplikace a identifikátor URI pro přesměrování

Zobrazit [získání ID aplikace](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) načíst ID aplikace.

Pokud chcete načíst identifikátor URI pro přesměrování, proveďte následující kroky.

1. Na webu Azure Portal, vyberte **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**a potom vyberte a klikněte na nativní aplikaci Azure AD, kterou jste vytvořili.

2. Z **nastavení** okno pro aplikaci, klikněte na tlačítko **identifikátory URI přesměrování**.

    ![Identifikátor URI pro přesměrování GET](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Zkopírujte hodnotu zobrazenou.


## <a name="step-3-set-permissions"></a>Krok 3: Nastavení oprávnění

1. Na webu Azure Portal, vyberte **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**a potom vyberte a klikněte na nativní aplikaci Azure AD, kterou jste vytvořili.

2. Z **nastavení** okno pro aplikaci, klikněte na tlačítko **požadovaná oprávnění**a potom klikněte na tlačítko **přidat**.

    ![ID klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. V **přidat přístup přes rozhraní API** okna, klikněte na tlačítko **vyberte rozhraní API**, klikněte na tlačítko **Azure Data Lake**a potom klikněte na tlačítko **vyberte**.

    ![ID klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  V **přidat přístup přes rozhraní API** okna, klikněte na tlačítko **vyberte oprávnění**, zaškrtněte políčko poskytnout **úplný přístup k Data Lake Store**a potom klikněte na tlačítko **vyberte**.

    ![ID klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klikněte na **Done** (Hotovo).

5. Zopakujte poslední dva kroky k udělení oprávnění **Windows Azure Service Management API** také.
   
## <a name="next-steps"></a>Další postup
V tomto článku jste vytvořili nativní aplikaci Azure AD a získané informace, které potřebujete v klientské aplikace, vytvářet pomocí sady .NET SDK, sady Java SDK, REST API, atd. Teď můžete přejít na následující články, které mluvit o tom, jak používat webovou aplikaci Azure AD začíná ověřením v Data Lake Storage Gen1 a potom provádění jiných operací v úložišti.

* [Koncového uživatele ověřování-s Data Lake Storage Gen1 pomocí sady Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Ověřování koncového uživatele s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
* [Ověřování koncového uživatele s Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)

