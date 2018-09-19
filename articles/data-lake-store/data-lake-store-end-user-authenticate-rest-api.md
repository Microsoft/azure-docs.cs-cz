---
title: 'Ověřování koncového uživatele: rozhraní REST API s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak dokončit ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí rozhraní REST API
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
ms.openlocfilehash: ea550c0959f5de13f013f135926251bf9f8b450f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124435"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1 pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

V tomto článku se dozvíte o tom, jak pomocí rozhraní REST API, provádět ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1. Ověřování služba služba s Data Lake Storage Gen1 pomocí rozhraní REST API, přečtěte si téma [ověřování služba služba Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Nativní"**. Je nutné dokončit kroky v [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](http://curl.haxx.se/)**. Tento článek používá cURL k předvedení jak volat rozhraní REST API vůči účtu Data Lake Storage Gen1.

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
Ověřování koncového uživatele je doporučený postup, pokud chcete, aby uživatel měl přístup do vaší aplikace pomocí Azure AD. Aplikace je mít přístup k prostředkům Azure se stejnou úrovní přístupu jako přihlášený uživatel. Uživatel musí zadat přihlašovací údaje pravidelně v pořadí pro vaši aplikaci k Udržovat přístup.

Výsledek s přihlášení koncových uživatelů je, že vaše aplikace je zadaný přístupový token a aktualizační token. Získá přístupový token připojené k každého požadavku na Data Lake Storage Gen1 nebo Data Lake Analytics a na jednu hodinu, ve výchozím nastavení je platný. Token obnovení je možné získat nový přístupový token a je platný po dobu až dvou týdnů ve výchozím nastavení, je-li používat pravidelně. Pro přihlášení koncových uživatelů můžete použít dva různé přístupy.

V tomto scénáři aplikace vyzve uživatele k přihlášení a všechny operace se provádějí v kontextu uživatele. Proveďte následující kroky:

1. Prostřednictvím aplikace přesměrujte uživatele na tuto adresu URL:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI&gt; musí být zakódovaný, aby se dal použít jako adresa URL. Ano, pro https://localhost, použijte `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    Pro účely tohoto kurzu můžete ve výše zobrazené adrese URL nahradit zástupné hodnoty a vložit ji do adresního řádku webového prohlížeče. Budete přesměrováni na ověření pomocí přihlášení Azure. Po úspěšném přihlášení se zobrazí v adresním řádku prohlížeče odpověď. Odpověď bude mít tento formát:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Zaznamenejte autorizační kód z odpovědi. Pro účely tohoto kurzu můžete zkopírovat autorizační kód z adresního řádku webového prohlížeče a předejte ho v příspěvku požadavek na koncový bod tokenu, jak je znázorněno v následujícím fragmentu kódu:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > V takovém případě nemusí být identifikátor \<REDIRECT-URI> zakódovaný.
   > 
   > 

3. Odpověď je objekt JSON, který obsahuje přístupový token (například `"access_token": "<ACCESS_TOKEN>"`) a obnovovací token (například `"refresh_token": "<REFRESH_TOKEN>"`). Vaše aplikace používá přístupový token při přístupu k Azure Data Lake Storage Gen1 a aktualizační token k získání dalšího přístupového tokenu, když vyprší platnost přístupového tokenu.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Když vyprší platnost přístupového tokenu, můžete požádat o nový přístupový token pomocí obnovovacího tokenu, jak je znázorněno v následujícím fragmentu kódu:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Další informace o interaktivním ověřování uživatelů najdete v tématu [Tok poskytování autorizačních kódů](https://msdn.microsoft.com/library/azure/dn645542.aspx).
   
## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat k ověření pomocí Azure Data Lake Storage Gen1 ověřování služba služba pomocí rozhraní REST API. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat rozhraní REST API pro práci s Azure Data Lake Storage Gen1.

* [Operace správy účtů v Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md)
* [Operace s daty v Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-data-operations-rest-api.md)

