---
title: Ověřování koncových uživatelů – Data Lake Storage Gen1 s využitím Azure AD
description: Naučte se, jak dosáhnout ověřování koncovými uživateli pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: a5272f7d580a3f8a68afda9150a1e95c1807eba6
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103777"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Ověřování koncových uživatelů s Azure Data Lake Storage Gen1 pomocí Azure Active Directory
> [!div class="op_single_selector"]
> * [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)
>
>

Azure Data Lake Storage Gen1 používá pro ověřování Azure Active Directory. Před vytvořením aplikace, která funguje s Data Lake Storage Gen1 nebo Azure Data Lake Analytics, se musíte rozhodnout, jak aplikaci ověřit pomocí Azure Active Directory (Azure AD). K dispozici jsou dvě hlavní možnosti:

* Ověřování koncových uživatelů (Tento článek)
* Ověřování služba-služba (vyberte tuto možnost z rozevíracího seznamu výše)

Obě tyto možnosti mají za následek, že vaše aplikace bude k dispozici s tokenem OAuth 2,0, který se připojí ke každému požadavku, který jste odeslali Data Lake Storage Gen1 nebo Azure Data Lake Analytics.

Tento článek pojednává o tom, jak vytvořit **nativní aplikaci Azure AD pro ověřování koncových uživatelů**. Pokyny týkající se konfigurace aplikací Azure AD pro ověřování služby a služby najdete v tématu [ověřování služby-služba pomocí Data Lake Storage Gen1 pomocí Azure Active Directory](./data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Předpoklady
* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* ID vašeho předplatného. Můžete ho načíst z Azure Portal. Například je k dispozici v okně Data Lake Storage Gen1 účet.

    ![Získat ID předplatného](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Název domény služby Azure AD. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. Na snímku obrazovky níže je název domény **contoso.onmicrosoft.com**a identifikátor GUID v závorkách je ID tenanta.

    ![Získat doménu AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Vaše ID tenanta Azure. Pokyny k načtení ID tenanta najdete v tématu [získání ID tenanta](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
Tento mechanismus ověřování je doporučený postup, pokud chcete, aby se koncový uživatel přihlásil k aplikaci přes Azure AD. Vaše aplikace pak může získat přístup k prostředkům Azure se stejnou úrovní přístupu jako koncový uživatel, který se přihlásil. Koncový uživatel musí zadat své přihlašovací údaje pravidelně, aby mohla vaše aplikace zachovat přístup.

Výsledkem přihlášení koncového uživatele je, že aplikace má přístup k přístupovému tokenu a obnovovacímu tokenu. Přístupový token se připojí ke všem žádostem provedeným na Data Lake Storage Gen1 nebo Data Lake Analytics a ve výchozím nastavení platí pro jednu hodinu. Obnovovací token lze použít k získání nového přístupového tokenu a ve výchozím nastavení je platný až pro dva týdny. Pro přihlášení koncového uživatele můžete použít dva různé přístupy.

### <a name="using-the-oauth-20-pop-up"></a>Použití automaticky otevíraného okna OAuth 2,0
Vaše aplikace může aktivovat automaticky otevírané okno pro autorizaci OAuth 2,0, ve kterém může koncový uživatel zadat své přihlašovací údaje. Toto automaticky otevírané okno funguje v případě potřeby i s procesem Azure AD Authentication (2FA).

> [!NOTE]
> Tato metoda není ještě podporovaná v knihovně ADAL (Azure AD Authentication Library) pro Python nebo Java.
>
>

### <a name="directly-passing-in-user-credentials"></a>Přímé předání přihlašovacích údajů uživatele
Vaše aplikace může přímo poskytnout přihlašovací údaje uživatele do služby Azure AD. Tato metoda funguje jenom s uživatelskými účty ID organizace. není kompatibilní s uživatelskými účty osobní/"Live ID", včetně účtů končících @outlook.com nebo @live.com . Tato metoda navíc není kompatibilní s uživatelskými účty, které vyžadují dvojúrovňové ověřování Azure AD (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Co potřebuji pro tento přístup?
* Název domény služby Azure AD. Tento požadavek je již uveden v předpokladech tohoto článku.
* ID tenanta Azure AD. Tento požadavek je již uveden v předpokladech tohoto článku.
* **Nativní aplikace** Azure AD
* ID aplikace pro nativní aplikaci Azure AD
* Identifikátor URI pro přesměrování pro nativní aplikaci Azure AD
* Nastavte delegovaná oprávnění.


## <a name="step-1-create-an-active-directory-native-application"></a>Krok 1: Vytvoření nativní aplikace služby Active Directory

Vytvořte a nakonfigurujte nativní aplikaci Azure AD pro ověřování koncových uživatelů pomocí Data Lake Storage Gen1 pomocí Azure Active Directory. Pokyny najdete v tématu [Vytvoření aplikace Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Při použití pokynů v odkazu se ujistěte, že jste vybrali možnost **nativní** pro typ aplikace, jak je znázorněno na následujícím snímku obrazovky:

![Vytvoření webové aplikace](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Vytvořit nativní aplikaci")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Krok 2: získání ID aplikace a identifikátoru URI pro přesměrování

Viz [získání ID aplikace](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) pro načtení ID aplikace.

Pro načtení identifikátoru URI přesměrování proveďte následující kroky.

1. V Azure Portal vyberte **Azure Active Directory**, klikněte na **Registrace aplikací**a pak vyhledejte a klikněte na nativní aplikaci Azure AD, kterou jste vytvořili.

2. V okně **Nastavení** aplikace klikněte na možnost **přesměrovat identifikátory URI**.

    ![Získat identifikátor URI pro přesměrování](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Zkopírujte zobrazenou hodnotu.


## <a name="step-3-set-permissions"></a>Krok 3: nastavení oprávnění

1. V Azure Portal vyberte **Azure Active Directory**, klikněte na **Registrace aplikací**a pak vyhledejte a klikněte na nativní aplikaci Azure AD, kterou jste vytvořili.

2. V okně **Nastavení** aplikace klikněte na **požadovaná oprávnění**a pak klikněte na **Přidat**.

    ![Snímek obrazovky okna nastavení s parametrem redirect U R I s názvem a přesměrování U R I v okně s skutečným U R, které jsem vyvolal](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. V okně **Přidat přístup přes rozhraní API** klikněte na **Vybrat rozhraní API**, klikněte na **Azure Data Lake**a pak klikněte na **Vybrat**.

    ![Snímek obrazovky okna přidat přístup přes rozhraní API pomocí možnosti vyberte rozhraní API, které se vyvolaly, a vyberte okno vybrat rozhraní API s možností Azure Data Lake a možnost vybrat s názvem.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

4.  V okně **Přidat přístup přes rozhraní API** klikněte na **vybrat oprávnění**. Zaškrtnutím políčka udělíte **úplný přístup k Data Lake Store**a potom klikněte na **Vybrat**.

    ![Snímek obrazovky okna přidat přístup přes rozhraní API s možností vybrat oprávnění a v okně Povolit přístup s úplným přístupem k možnosti služby Azure Data Lake a s možností vybrat s názvem.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klikněte na **Hotovo**.

5. Zopakováním posledních dvou kroků udělíte taky oprávnění pro **Windows Azure rozhraní API pro správu služeb** .

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili nativní aplikaci Azure AD a shromáždili jste potřebné informace v klientských aplikacích, které vytváříte pomocí sady .NET SDK, sady Java SDK, REST API atd. Nyní můžete přejít k následujícím článkům, které se seznámí s postupem použití webové aplikace Azure AD k prvnímu ověření pomocí Data Lake Storage Gen1 a následnému provádění dalších operací na Storu.

* [Koncoví uživatelé – ověřování pomocí Data Lake Storage Gen1 pomocí sady Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Ověřování koncových uživatelů s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Ověřování koncových uživatelů pomocí Data Lake Storage Gen1 s využitím Pythonu](data-lake-store-end-user-authenticate-python.md)
* [Ověřování koncových uživatelů s Data Lake Storage Gen1 pomocí REST API](data-lake-store-end-user-authenticate-rest-api.md)