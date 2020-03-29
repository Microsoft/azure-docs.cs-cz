---
title: 'Ověřování mezi službami: Azure Data Lake Storage Gen1 s Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak dosáhnout ověřování mezi službami pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
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
ms.openlocfilehash: 3fbf2f2540e8f1ca84aad2759b9a1fc790e4065d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241374"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Ověřování mezi službami pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
> [!div class="op_single_selector"]
> * [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 používá Azure Active Directory pro ověřování. Před vytvořením aplikace, která pracuje s Data Lake Storage Gen1, musíte se rozhodnout, jak ověřit vaši aplikaci pomocí Azure Active Directory (Azure AD). K dispozici jsou dvě hlavní možnosti:

* Ověřování koncových uživatelů 
* Ověřování mezi službami (tento článek) 

Obě tyto možnosti za následek vaše aplikace je k dispozici s Token OAuth 2.0, který získá připojené ke každé žádosti o úložiště datového jezera Gen1.

Tento článek popisuje, jak vytvořit **webovou aplikaci Azure AD pro ověřování mezi službami**. Pokyny týkající se konfigurace aplikace Azure AD pro ověřování koncových uživatelů najdete v [tématu Ověřování koncových uživatelů pomocí data lake storage gen1 pomocí Služby Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Krok 1: Vytvoření webové aplikace služby Active Directory

Vytvořte a nakonfigurujte webovou aplikaci Azure AD pro ověřování mezi službami pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory. Pokyny najdete [v tématu Vytvoření aplikace Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Při dodržování pokynů na předchozím odkazu, ujistěte se, že vyberete **Web App / API** pro typ aplikace, jak je znázorněno na následujícím snímku obrazovky:

![Vytvoření webové aplikace](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Vytvoření webové aplikace")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Krok 2: Získání ID aplikace, ověřovacího klíče a ID klienta
Při programovém přihlášení potřebujete ID aplikace. Pokud aplikace běží pod vlastní mise, budete také potřebovat ověřovací klíč.

* Pokyny k načtení ID aplikace a ověřovacího klíče (nazývaného také tajný klíč klienta) pro vaši aplikaci naleznete v tématu [Získání ID aplikace a ověřovacího klíče](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

* Pokyny k načtení ID klienta najdete v tématu [získání ID klienta](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Krok 3: Přiřazení aplikace Azure AD k souboru nebo složce účtu azure data lake storage gen1


1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). Otevřete účet Data Lake Storage Gen1, který chcete přidružit k aplikaci Azure Active Directory, kterou jste vytvořili dříve.
2. V okně účtu Data Lake Storage Gen1 klikněte na **Průzkumník dat**.
   
    ![Vytvoření adresářů v účtu Data Lake Storage Gen1](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Vytvoření adresářů v účtu Data Lake")
3. V okně **Průzkumníka dat** klikněte na soubor nebo složku, pro kterou chcete poskytnout přístup k aplikaci Azure AD, a potom klikněte na **položku Access**. Chcete-li nakonfigurovat přístup k souboru, musíte klepnout na **tlačítko Access** z okna **Náhled souboru.**
   
    ![Nastavení aklů v systému souborů Datového jezera](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Nastavení aklů v systému souborů Datového jezera")
4. Okno **Access** obsahuje seznam standardního přístupu a vlastního přístupu, který je již kořenovému adresáři přiřazen. Kliknutím na ikonu **Přidat** přidejte seznamy ACL vlastní úrovně.
   
    ![Seznam standardního a vlastního přístupu](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Seznam standardního a vlastního přístupu")
5. Kliknutím na ikonu **Přidat** otevřete okno **Přidat vlastní přístup.** V tomto okně klikněte na **Vybrat uživatele nebo skupinu**a potom v okně **Vybrat uživatele nebo skupinu** vyhledejte dříve vytvořenou aplikaci Azure Active Directory. Pokud máte mnoho skupin, ze kterých chcete vyhledávat, vyfiltrujte podle názvu skupiny textové pole v horní části. Klikněte na skupinu, kterou chcete přidat, a potom klikněte na **Vybrat**.
   
    ![Přidání skupiny](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Přidání skupiny")
6. Klepněte na **možnost Vybrat oprávnění**, vyberte oprávnění a zda chcete oprávnění přiřadit jako výchozí přístupový kód, přístup k seznamu ACL nebo obojí. Klikněte na tlačítko **OK**.
   
    ![Přiřazení oprávnění ke skupině](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Přiřazení oprávnění ke skupině")
   
    Další informace o oprávněních v gen1 úložiště datového jezera a výchozích a přístupových hodnotách AC, najdete [v tématu Řízení přístupu v souboru Data Lake Storage Gen1](data-lake-store-access-control.md).
7. V okně **Přidat vlastní přístup** klepněte na tlačítko **OK**. Nově přidaná skupina s přidruženými oprávněními je uvedena v okně **Access.**
   
    ![Přiřazení oprávnění ke skupině](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Přiřazení oprávnění ke skupině")

> [!NOTE]
> Pokud máte v plánu omezit aplikaci Azure Active Directory na určitou složku, budete také muset udělit oprávnění ke **spuštění** stejné aplikace Azure Active directory root, abyste povolili přístup k vytváření souborů prostřednictvím sady .NET SDK.

> [!NOTE]
> Pokud chcete pomocí sad SDK použít k vytvoření účtu Data Lake Storage Gen1, musíte přiřadit webovou aplikaci Azure AD jako roli skupině prostředků, ve které vytvoříte účet Gen1 úložiště datového jezera.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Krok 4: Získejte koncový bod tokenu OAuth 2.0 (pouze pro aplikace založené na jazyce Java)

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a v levém podokně klikněte na Službu Active Directory.

2. V levém podokně klikněte na **Registrace aplikací**.

3. V horní části okna Registrace aplikací klikněte na **Koncové body**.

    ![Koncový bod tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Koncový bod tokenu OAuth")

4. Ze seznamu koncových bodů zkopírujte koncový bod tokenu OAuth 2.0.

    ![Koncový bod tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Koncový bod tokenu OAuth")   

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili webovou aplikaci Azure AD a shromáždili informace, které potřebujete v klientských aplikacích, které vytváříte pomocí .NET SDK, Java, Python, REST API atd. Teď můžete přejít k následujícím článkům, které hovoří o tom, jak používat nativní aplikaci Azure AD k prvnímu ověření pomocí Data Lake Storage Gen1 a pak provést další operace v úložišti.

* [Ověřování mezi službami pomocí data lake storage gen1 pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
* [Ověřování mezi službami pomocí služby Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Ověřování mezi službami pomocí data lake storage gen1 pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
* [Ověřování mezi službami pomocí data lake storage gen1 pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


