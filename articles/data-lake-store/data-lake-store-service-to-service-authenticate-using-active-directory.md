---
title: Ověřování služba-Služba – Data Lake Storage Gen1 – Azure
description: Naučte se, jak dosáhnout ověřování služby-služba pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 9dc195f98310e63cbde06885effe86ea3c239249
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576094"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Ověřování služba-služba pomocí Azure Data Lake Storage Gen1 s využitím Azure Active Directory
> [!div class="op_single_selector"]
> * [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 používá pro ověřování Azure Active Directory. Před vytvořením aplikace, která funguje s Data Lake Storage Gen1, se musíte rozhodnout, jak aplikaci ověřit pomocí Azure Active Directory (Azure AD). K dispozici jsou dvě hlavní možnosti:

* Ověřování koncových uživatelů 
* Ověřování služba-služba (Tento článek) 

Obě tyto možnosti mají za následek, že vaše aplikace bude k dispozici s tokenem OAuth 2,0, který se připojí ke každému požadavku, který jste odeslali do Data Lake Storage Gen1.

Tento článek pojednává o tom, jak vytvořit **webovou aplikaci Azure AD pro ověřování služby-služba**. Pokyny týkající se konfigurace aplikací Azure AD pro ověřování koncových uživatelů najdete v tématu [ověřování koncových uživatelů pomocí Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Předpoklady
* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Krok 1: Vytvoření webové aplikace Active Directory

Vytvoření a konfigurace webové aplikace Azure AD pro ověřování služba-služba pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory. Pokyny najdete v tématu [Vytvoření aplikace Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Při souladu s pokyny na předchozím odkazu se ujistěte, že jste vybrali možnost **Webová aplikace/rozhraní API** pro typ aplikace, jak je znázorněno na následujícím snímku obrazovky:

![Vytvoření webové aplikace](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Vytvoření webové aplikace")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Krok 2: získání ID aplikace, ověřovacího klíče a ID tenanta
Když se programově přihlašujete, budete potřebovat ID vaší aplikace. Pokud je aplikace spuštěna v rámci svých vlastních přihlašovacích údajů, budete také potřebovat ověřovací klíč.

* Pokyny k načtení ID aplikace a ověřovacího klíče (označovaného také jako tajný klíč klienta) pro vaši aplikaci najdete v tématu [získání ID aplikace a ověřovacího klíče](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

* Pokyny, jak načíst ID tenanta, najdete v tématu [získání ID tenanta](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Krok 3: přiřazení aplikace Azure AD k souboru nebo složce Azure Data Lake Storage Gen1 účtu


1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). Otevřete Data Lake Storage Gen1 účet, který chcete přidružit k aplikaci Azure Active Directory, kterou jste vytvořili dříve.
2. V okně Data Lake Storage Gen1 účtu klikněte na **Průzkumník dat**.
   
    ![Vytváření adresářů v Data Lake Storage Gen1m účtu](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Vytváření adresářů v Data Lakem účtu")
3. V okně **Průzkumník dat** klikněte na soubor nebo složku, pro které chcete poskytnout přístup k aplikaci Azure AD, a pak klikněte na **přístup**. Chcete-li nakonfigurovat přístup k souboru, musíte kliknout na možnost **přístup** v okně **Náhled souboru** .
   
    ![Nastavení seznamů ACL pro Data Lake systému souborů](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Nastavení seznamů ACL pro Data Lake systému souborů")
4. V okně **přístup** se zobrazí standardní přístup a vlastní přístup, který je už přiřazený ke kořenu. Kliknutím na ikonu **Přidat** přidáte seznamy řízení přístupu (ACL) na vlastní úrovni.
   
    ![Vypsat standardní a vlastní přístup](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Vypsat standardní a vlastní přístup")
5. Kliknutím na ikonu **Přidat** otevřete okno **Přidat vlastní přístup** . V tomto okně klikněte na **Vybrat uživatele nebo skupinu**a potom v okně **Vybrat uživatele nebo skupinu** vyhledejte aplikaci Azure Active Directory, kterou jste vytvořili dříve. Pokud máte více skupin, ze kterých můžete vyhledávat, použijte k filtrování názvu skupiny textové pole v horní části. Klikněte na skupinu, kterou chcete přidat, a potom klikněte na **Vybrat**.
   
    ![Přidat skupinu](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Přidat skupinu")
6. Klikněte na **vybrat oprávnění**, vyberte oprávnění a určete, jestli chcete přiřadit oprávnění jako výchozí seznam ACL, přístup k seznamu ACL nebo obojí. Klikněte na **OK**.
   
    ![Snímek obrazovky s oknem přidat vlastní přístup s možností vybrat oprávnění, která se vyvolala a okno vybrat oprávnění s možností OK s názvem.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Přiřazení oprávnění ke skupině")
   
    Další informace o oprávněních v Data Lake Storage Gen1 a o výchozích nebo přístupových seznamech řízení přístupu najdete [v tématu Access Control v Data Lake Storage Gen1](data-lake-store-access-control.md).
7. V okně **Přidat vlastní přístup** klikněte na **OK**. Nově přidané skupiny s přidruženými oprávněními jsou uvedeny v okně **přístup** .
   
    ![Snímek obrazovky s oknem Access s nově přidanou skupinou vyvolanou v oddílu vlastní přístup](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Přiřazení oprávnění ke skupině")

> [!NOTE]
> Pokud plánujete omezit Azure Active Directory aplikace na konkrétní složku, budete také muset dát ke kořenu stejné oprávnění ke **spuštění** aplikace Azure Active Directory, aby bylo možné povolit přístup pro vytváření souborů prostřednictvím sady .NET SDK.

> [!NOTE]
> Pokud chcete k vytvoření účtu Data Lake Storage Gen1 použít sady SDK, musíte webové aplikaci Azure AD přiřadit jako roli ke skupině prostředků, ve které vytvoříte účet Data Lake Storage Gen1.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Krok 4: získání koncového bodu tokenu OAuth 2,0 (jenom pro aplikace založené na jazyce Java)

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a v levém podokně klikněte na Active Directory.

2. V levém podokně klikněte na **Registrace aplikací**.

3. V horní části okna Registrace aplikací klikněte na **koncové body**.

    ![Snímek obrazovky služby Active Directory s možností Registrace aplikací a možností koncového bodu s názvem.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Koncový bod tokenu OAuth")

4. V seznamu koncových bodů zkopírujte koncový bod tokenu OAuth 2,0.

    ![Snímek obrazovky okna s koncovými body s ikonou pro kopírování KONCOVÉho bodu pro ověření 2 bodu O TOKENu s názvem.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Koncový bod tokenu OAuth")   

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili webovou aplikaci Azure AD a shromáždili jste potřebné informace v klientských aplikacích, které vytváříte pomocí sady .NET SDK, Java, Python, REST API atd. Nyní můžete přejít k následujícím článkům, které se domluví o tom, jak používat nativní aplikaci Azure AD k prvnímu ověření pomocí Data Lake Storage Gen1 a následnému provádění dalších operací na Storu.

* [Ověřování služba-služba s Data Lake Storage Gen1 pomocí jazyka Java](data-lake-store-service-to-service-authenticate-java.md)
* [Ověřování služba-služba s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Ověřování služba-služba pomocí Data Lake Storage Gen1 s využitím Pythonu](data-lake-store-service-to-service-authenticate-python.md)
* [Ověřování služba-služba pomocí Data Lake Storage Gen1 s využitím REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


