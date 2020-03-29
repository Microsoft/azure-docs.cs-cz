---
title: 'Ověřování koncových uživatelů: Azure Data Lake Storage Gen1 s Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak dosáhnout ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4c2b774c304e46f9fc68f3beaf64218e614ecad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66234057"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
> [!div class="op_single_selector"]
> * [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Storage Gen1 používá Azure Active Directory pro ověřování. Před vytvořením aplikace, která pracuje s Data Lake Storage Gen1 nebo Azure Data Lake Analytics, musíte se rozhodnout, jak ověřit vaši aplikaci pomocí Azure Active Directory (Azure AD). K dispozici jsou dvě hlavní možnosti:

* Ověřování koncových uživatelů (tento článek)
* Ověřování mezi službami (tuto možnost vyberte z výše uvedeného rozevíracího přehledu)

Obě tyto možnosti mají za následek, že vaše aplikace je k dispozici s tokenem OAuth 2.0, který se připojí ke každé žádosti provedené do Data Lake Storage Gen1 nebo Azure Data Lake Analytics.

Tento článek popisuje, jak vytvořit **nativní aplikaci Azure AD pro ověřování koncových uživatelů**. Pokyny týkající se konfigurace aplikace Azure AD pro ověřování mezi službami najdete v [tématu ověřování mezi službami pomocí služby Data Lake Storage Gen1 pomocí služby Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* Vaše ID předplatného. Můžete načíst z portálu Azure. Například je k dispozici z úložiště datového jezera Gen1 účtu blade.
  
    ![Získání ID předplatného](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Název domény Azure AD. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. Z níže uvedeného snímku obrazovky je název domény **contoso.onmicrosoft.com**a identifikátor GUID v závorkách je ID klienta. 
  
    ![Získat doménu AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* ID vašeho klienta Azure. Pokyny k načtení ID klienta najdete [v tématu získání ID klienta](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
Tento mechanismus ověřování je doporučený přístup, pokud chcete, aby se koncový uživatel přihlásil k vaší aplikaci prostřednictvím azure ad. Vaše aplikace pak může přistupovat k prostředkům Azure se stejnou úrovní přístupu jako koncový uživatel, který se přihlásil. Koncový uživatel musí pravidelně zajišťovat svá pověření, aby aplikace mohla udržovat přístup.

Výsledkem přihlášení koncového uživatele je, že vaší aplikaci je přidělen přístupový token a obnovovací token. Přístupový token získá připojené ke každému požadavku na Data Lake Storage Gen1 nebo Data Lake Analytics a je platný po dobu jedné hodiny ve výchozím nastavení. Obnovovací token lze použít k získání nového přístupového tokenu a ve výchozím nastavení je platný až dva týdny. Pro přihlášení koncového uživatele můžete použít dva různé přístupy.

### <a name="using-the-oauth-20-pop-up"></a>Použití automaticky oauth 2.0
Vaše aplikace může aktivovat automaticky otevírané okno autorizace OAuth 2.0, ve kterém může koncový uživatel zadat svá pověření. Toto automaticky otevírané okno funguje také s procesem dvoufaktorového ověřování Azure AD (2FA), pokud je to nutné. 

> [!NOTE]
> Tato metoda ještě není podporována v azure ad ověřování knihovny (ADAL) pro Python nebo Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Přímé předání pověření uživatele
Vaše aplikace může přímo poskytnout přihlašovací údaje uživatele do služby Azure AD. Tato metoda funguje pouze s uživatelskými účty ID organizace; není kompatibilní s osobními uživatelskými účty / "live ID", včetně účtů končících @outlook.com na nebo @live.com. Kromě toho tato metoda není kompatibilní s uživatelskými účty, které vyžadují Azure AD dvoufaktorové ověřování (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Co potřebuji pro tento přístup?
* Název domény Azure AD. Tento požadavek je již uveden v předpokladech tohoto článku.
* ID klienta Azure AD. Tento požadavek je již uveden v předpokladech tohoto článku.
* **Nativní aplikace** Azure AD
* ID aplikace pro nativní aplikaci Azure AD
* Přesměrovat identifikátor URI pro nativní aplikaci Azure AD
* Nastavte delegovaná oprávnění.


## <a name="step-1-create-an-active-directory-native-application"></a>Krok 1: Vytvoření nativní aplikace služby Active Directory

Vytvořte a nakonfigurujte nativní aplikaci Azure AD pro ověřování koncových uživatelů pomocí služby Data Lake Storage Gen1 pomocí služby Azure Active Directory. Pokyny najdete [v tématu Vytvoření aplikace Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Při následování pokynů v odkazu se ujistěte, že jste vybrali **nativní** pro typ aplikace, jak je znázorněno na následujícím snímku obrazovky:

![Vytvoření webové aplikace](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Vytvoření nativní aplikace")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Krok 2: Získání ID aplikace a přesměrování identifikátoru URI

Viz [Získání ID aplikace](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in) pro načtení ID aplikace.

Chcete-li načíst identifikátor URI přesměrování, proveďte následující kroky.

1. Na webu Azure Portal vyberte **Azure Active Directory**, klikněte na Registrace **aplikací**a pak najděte a klikněte na nativní aplikaci Azure AD, kterou jste vytvořili.

2. V okně **Nastavení** aplikace klepněte na tlačítko **Přesměrovat identifikátory URI**.

    ![Získat identifikátor URI přesměrování](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Zkopírujte zobrazenou hodnotu.


## <a name="step-3-set-permissions"></a>Krok 3: Nastavení oprávnění

1. Na webu Azure Portal vyberte **Azure Active Directory**, klikněte na Registrace **aplikací**a pak najděte a klikněte na nativní aplikaci Azure AD, kterou jste vytvořili.

2. V okně **Nastavení** aplikace klepněte na **položku Požadovaná oprávnění**a potom klepněte na tlačítko **Přidat**.

    ![ID klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. V okně **Přidat přístup k rozhraní API** klikněte na Vybrat rozhraní **API**, klikněte na Azure **Data Lake**a potom klikněte na **Vybrat**.

    ![ID klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  V okně **Přidat přístup k rozhraní API** klepněte na tlačítko Vybrat **oprávnění**, zaškrtněte políčko a udělte úplný přístup **k úložišti Data Lake Store**a potom klepněte na tlačítko **Vybrat**.

    ![ID klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klikněte na **Done** (Hotovo).

5. Opakujte poslední dva kroky a udělte oprávnění také pro **rozhraní API pro správu služeb Windows Azure.**
   
## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili nativní aplikaci Azure AD a shromáždili informace, které potřebujete v klientských aplikacích, které vytváříte pomocí .NET SDK, Java SDK, REST API atd. Teď můžete přejít k následujícím článkům, které hovoří o tom, jak používat webovou aplikaci Azure AD k prvnímu ověření pomocí Data Lake Storage Gen1 a pak provést další operace v úložišti.

* [Ověřování koncových uživatelů pomocí aplikace Data Lake Storage Gen1 pomocí sady Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Ověřování koncových uživatelů pomocí sady Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Ověřování koncových uživatelů pomocí aplikace Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
* [Ověřování koncových uživatelů pomocí funkce Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)

