---
title: 'Ověřování služba služba: Azure Data Lake Storage Gen1 službou Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak dokončit ověřování služba služba pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
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
ms.openlocfilehash: ae1fdb6239b7d04dc366d023a26d1307cbae68f3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123721"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Ověřování služba služba pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
> [!div class="op_single_selector"]
> * [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 používá Azure Active Directory pro ověřování. Před vytváříte aplikaci, která funguje s Data Lake Storage Gen1, musíte rozhodnout, jak k ověřování vaší aplikace s Azure Active Directory (Azure AD). Jsou k dispozici dvě hlavní možnosti:

* Ověřování koncových uživatelů 
* Ověřování služba služba (Tento článek) 

Obě tyto možnosti má za následek poskytované k tokenu OAuth 2.0, který získá připojen k každého požadavku na Data Lake Storage Gen1 aplikace.

Tento článek pojednává o tom, jak vytvořit **webovou aplikaci Azure AD pro ověřování služba služba**. Pokyny pro konfiguraci aplikace Azure AD pro ověřování koncového uživatele najdete v tématu [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Krok 1: Vytvoření webové aplikace služby Active Directory

Vytvoření a konfigurace webové aplikace Azure AD pro ověřování služba služba s Azure Data Lake Storage Gen1 pomocí Azure Active Directory. Pokyny najdete v tématu [vytvořit aplikaci Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Při pokynů uvedených v předchozí odkaz, ujistěte se, že vyberete **webovou aplikaci nebo API** pro typ aplikace, jak je znázorněno na následujícím snímku obrazovky:

![Vytvoření webové aplikace](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "vytvoření webové aplikace")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Krok 2: Získání ID aplikace, ověřovací klíč a ID tenanta
Při programovém přihlášení potřebujete ID pro vaši aplikaci. Pokud aplikace běží pod svoje vlastní přihlašovací údaje, potřebujete také ověřovací klíč.

* Pokyny o tom, jak načíst ID a ověřovací klíč aplikace (také nazývané tajný klíč klienta) pro vaši aplikaci najdete v tématu [Get aplikace ID a ověřovacího klíče](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Pokyny o tom, jak získat ID tenanta najdete v tématu [získání ID tenanta](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Krok 3: Přiřaďte aplikaci Azure AD do Azure Data Lake Storage Gen1 účet souboru nebo složky


1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). Otevřete účet Data Lake Storage Gen1, kterou chcete přidružit k aplikaci Azure Active Directory, kterou jste vytvořili dříve.
2. V okně účtu Data Lake Storage Gen1, klikněte na tlačítko **Průzkumník dat**.
   
    ![Vytváření adresářů v účtu Data Lake Storage Gen1](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "vytvoření adresářů v účtu Data Lake")
3. V **Průzkumník dat** okna, klikněte na soubor nebo složku, pro které chcete poskytnout přístup k aplikacím Azure AD a potom klikněte na **přístup**. Pokud chcete nakonfigurovat přístup k souboru, musíte kliknout na **přístup** z **náhled souboru** okno.
   
    ![Nastavení seznamů ACL v systému souborů Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "nastavit seznamy ACL v systému souborů Data Lake")
4. **Přístup** okno uvádí standardní přístup a vlastní přístup již přiřazen do kořenového adresáře. Klikněte na tlačítko **přidat** ikonu, čímž přidáte vlastní úroveň seznamy řízení přístupu.
   
    ![Seznam standardních a vlastních přístup](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "seznam standardních a vlastních přístup")
5. Klikněte na tlačítko **přidat** ikony otevřete **přidat vlastní přístup** okno. V tomto okně klikněte na tlačítko **vybrat uživatele nebo skupinu**a potom v **vybrat uživatele nebo skupinu** okno, vyhledejte aplikaci Azure Active Directory, kterou jste vytvořili dříve. Pokud máte mnoho skupin má vyhledat, použijte k filtrování názvu skupiny do textového pole v horní části. Klikněte na skupinu, kterou chcete přidat a potom klikněte na tlačítko **vyberte**.
   
    ![Přidat skupinu](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "přidat skupinu")
6. Klikněte na tlačítko **oprávnění Select**, vyberte oprávnění a jestli se má přiřadit oprávnění jako výchozí seznam ACL, přístup k seznamu ACL, nebo obojí. Klikněte na **OK**.
   
    ![Přiřadit oprávnění k seskupení](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "přiřadit oprávnění k seskupení")
   
    Další informace o oprávněních v Data Lake Storage Gen1 a výchozí/přístupové seznamy ACL, naleznete v tématu [řízení přístupu v Data Lake Storage Gen1](data-lake-store-access-control.md).
7. V **přidat vlastní přístup** okna, klikněte na tlačítko **OK**. Nově přidaná skupina se příslušná oprávnění, jsou uvedeny v **přístup** okno.
   
    ![Přiřadit oprávnění k seskupení](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "přiřadit oprávnění k seskupení")

> [!NOTE]
> Pokud máte v plánu na omezení aplikace Azure Active Directory, do konkrétní složky, budete také muset poskytnout tu samou aplikaci Azure Active directory **Execute** oprávnění do kořenového adresáře pro povolení souboru vytváření přístup přes rozhraní .NET SADA SDK.

> [!NOTE]
> Pokud chcete vytvořit účet Data Lake Storage Gen1 používání sad SDK, musíte přiřadit skupinu prostředků, ve kterém vytvoříte účet Data Lake Storage Gen1 webovou aplikaci Azure AD jako role.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Krok 4: Získejte koncový bod tokenu OAuth 2.0 (pouze pro aplikace založené na jazyce Java)

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a v levém podokně klikněte na tlačítko služby Active Directory.

2. V levém podokně klikněte na tlačítko **registrace aplikací**.

3. V horní části okna registrace aplikace, klikněte na **koncové body**.

    ![Koncový bod tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "koncový bod tokenu OAuth")

4. Ze seznamu koncových bodů zkopírujte koncový bod tokenu OAuth 2.0.

    ![Koncový bod tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "koncový bod tokenu OAuth")   

## <a name="next-steps"></a>Další postup
V tomto článku jste vytvořili webovou aplikaci Azure AD a shromáždit informace, které potřebujete v klientské aplikace, že vytvoříte pomocí sady .NET SDK, Java, Python, rozhraní REST API, atd. Teď můžete přejít na následující články, které mluvit o tom, jak používat nativní aplikaci Azure AD začíná ověřením v Data Lake Storage Gen1 a potom provádění jiných operací v úložišti.

* [Ověřování služba služba s Data Lake Storage Gen1 s využitím Javy](data-lake-store-service-to-service-authenticate-java.md)
* [Ověřování služba služba Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Ověřování služba služba Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
* [Ověřování služba služba Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


