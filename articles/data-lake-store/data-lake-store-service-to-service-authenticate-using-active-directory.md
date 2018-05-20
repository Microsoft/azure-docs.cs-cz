---
title: 'Ověřování služby služby: Data Lake Store s Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak dosáhnout service-to-service ověřování s Data Lake Store pomocí Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 7a4c94b5279962d9bf5524270c12e0c1ec512e3c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Service-to-service ověřování s Data Lake Store pomocí Azure Active Directory
> [!div class="op_single_selector"]
> * [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Store využívá Azure Active Directory pro ověřování. Před vytváření aplikace, která funguje s Azure Data Lake Store, musíte rozhodnout, jak ověřovat vaší aplikace pomocí Azure Active Directory (Azure AD). Jsou dvě hlavní možnosti k dispozici:

* Ověřování koncových uživatelů 
* Do služby ověřování (v tomto článku) 

Obě tyto možnosti má za následek aplikace se součástí tokenu OAuth 2.0, který získá připojen k každý požadavek do Azure Data Lake Store.

Tento článek pojednává o tom, jak vytvořit **webové aplikace Azure AD pro ověřování service-to-service**. Pokyny pro konfiguraci aplikace Azure AD pro ověřování koncového uživatele v tématu [ověřování koncového uživatele s Data Lake Store pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Krok 1: Vytvoření webové aplikace služby Active Directory

Vytvořit a nakonfigurovat webovou aplikaci Azure AD pro service-to-service ověřování s Azure Data Lake Store pomocí Azure Active Directory. Pokyny najdete v tématu [vytvořit aplikaci Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Při postupovat podle pokynů v předchozí odkaz, zkontrolujte, zda jste vybrali **webovou aplikaci nebo API** pro typ aplikace, jak je znázorněno na následujícím snímku obrazovky:

![Vytvořit webovou aplikaci](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "vytvořit webovou aplikaci")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Krok 2: Získání ID aplikace, kód ověřování a ID klienta
Pokud protokolování prostřednictvím kódu programu, je potřeba ID pro vaši aplikaci. Pokud je aplikace spuštěná v části svoje vlastní přihlašovací údaje, musíte taky ověřovací klíč.

* Pokyny týkající se načtení ID a ověřovací klíč aplikace (také nazývané tajný klíč klienta) pro vaši aplikaci najdete v tématu [ID a ověřovací klíč aplikace Get](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Pokyny týkající se načtení ID klienta najdete v tématu [získání ID tenanta](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>Krok 3: Přiřaďte aplikaci Azure AD do Azure Data Lake Store účtu souboru nebo složky


1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). Otevřete účet Azure Data Lake Store, který chcete přidružit k aplikaci Azure Active Directory, které jste vytvořili dříve.
2. V okně účtu Data Lake Store klikněte na možnost **Průzkumník dat**.
   
    ![Vytváření adresářů v účtu Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "vytváření adresářů v účtu Data Lake")
3. V **Průzkumníku dat** okně klikněte na soubor nebo složku, pro který chcete poskytnout přístup k aplikaci Azure AD a potom klikněte na **přístup**. Pokud chcete konfigurovat přístup k souboru, musíte kliknout na **přístup** z **náhled souboru** okno.
   
    ![Nastavit seznamy ACL v systému souborů Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "nastavit seznamy ACL v Data Lake systému souborů")
4. **Přístup** okno obsahuje standardní přístup a vlastní přístup již přiřazen do kořenového adresáře. Klikněte **přidat** ikonu, čímž přidáte vlastní úroveň seznamy ACL.
   
    ![Standardní a vlastní přístup](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "standardní a vlastní přístup")
5. Klikněte na tlačítko **přidat** ikonu Otevřít **přidat vlastní přístup** okno. V tomto okně klikněte na tlačítko **vybrat uživatele nebo skupinu**a potom v **vybrat uživatele nebo skupinu** okno, podívejte se na aplikace Azure Active Directory, který jste vytvořili dříve. Pokud máte více skupin pro vyhledávání z, použijte k filtrování název skupiny v horní části textového pole. Klikněte na skupinu, kterou chcete přidat a pak klikněte na **vyberte**.
   
    ![Přidat skupinu](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "přidat skupinu")
6. Klikněte na tlačítko **vyberte oprávnění**, vybrat oprávnění a zda chcete přiřadit oprávnění jako výchozí seznam ACL, přístup k seznamu ACL, nebo obojí. Klikněte na **OK**.
   
    ![Přiřazení oprávnění k seskupení](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "přiřadit oprávnění k seskupení")
   
    Další informace o oprávněních v Data Lake Store a seznamy ACL výchozí nebo přístupu najdete v tématu [řízení přístupu v Data Lake Store](data-lake-store-access-control.md).
7. V **přidat vlastní přístup** okně klikněte na tlačítko **OK**. Nově přidané skupiny s příslušnými oprávněními, jsou uvedeny v **přístup** okno.
   
    ![Přiřazení oprávnění k seskupení](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "přiřadit oprávnění k seskupení")

> [!NOTE]
> Pokud máte v plánu na omezení aplikace Azure Active Directory do určené složky, budete také muset na poskytnout tu samou aplikaci Azure Active directory **Execute** permisison do kořenového adresáře povolit přístup k vytvoření souboru prostřednictvím. NET SDK.

> [!NOTE]
> Pokud chcete vytvořit účet Data Lake Store pomocí sady SDK, je nutné přiřadit ke skupině prostředků, ve kterém vytvoříte účet Data Lake Store webové aplikace Azure AD jako role.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Krok 4: Získání koncový bod tokenu OAuth 2.0 (pouze pro aplikace založené na jazyce Java)

1. Přihlaste se k [portál Azure](https://portal.azure.com) a v levém podokně klikněte na tlačítko služby Active Directory.

2. V levém podokně klikněte na tlačítko **registrace aplikace**.

3. V horní části okna registrace aplikace, klikněte na **koncové body**.

    ![Koncový bod tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "koncový bod tokenu OAuth")

4. Ze seznamu koncových bodů zkopírujte koncový bod tokenu OAuth 2.0.

    ![Koncový bod tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "koncový bod tokenu OAuth")   

## <a name="next-steps"></a>Další postup
V tomto článku jste vytvořili webovou aplikaci Azure AD a shromáždit informace, které budete potřebovat v klientských aplikacích vytvářet pomocí .NET SDK, Java, Python, REST API atd. Nyní můžete přejít na následující články, které mluvit o tom, k používání nativní aplikace Azure AD nejprve ověřit pomocí Data Lake Store a poté provést jiné operace v úložišti.

* [Service-to-service ověřování s Data Lake Store pomocí jazyka Java](data-lake-store-service-to-service-authenticate-java.md)
* [Service-to-service ověřování s Data Lake Store pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Service-to-service ověřování s Data Lake Store pomocí jazyka Python](data-lake-store-service-to-service-authenticate-python.md)
* [Service-to-service ověřování s Data Lake Store pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


