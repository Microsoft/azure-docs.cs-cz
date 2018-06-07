---
title: 'Ověřování koncového uživatele: REST API s Data Lake Store pomocí Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak zajistit ověření koncového uživatele s Data Lake Store pomocí Azure Active Directory pomocí rozhraní REST API
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
ms.openlocfilehash: 7b339c989a21abff34b885a8cba219aba701ca79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624246"
---
# <a name="end-user-authentication-with-data-lake-store-using-rest-api"></a>Ověřování koncového uživatele s Data Lake Store pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

V tomto článku se dozvíte o tom, jak proveďte ověření koncových uživatelů s Azure Data Lake Store pomocí rozhraní REST API. Service-to-service ověřování s Data Lake Store pomocí rozhraní REST API, najdete v části [Service-to-service ověřování s Data Lake Store pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Nativní"**. Musí mít dokončili postup v [ověřování koncového uživatele s Data Lake Store pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](http://curl.haxx.se/)**. Tento článek používá cURL k předvedení toho, jak provádět volání rozhraní REST API vůči účtu Data Lake Store.

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
Ověřování koncového uživatele je doporučený postup, pokud chcete, aby uživatel k přihlášení do aplikace pomocí služby Azure AD. Aplikace je mít přístup k prostředkům Azure se stejnou úrovní přístupu jako přihlášeného uživatele. Uživatel musí zadat své přihlašovací údaje pravidelně v pořadí pro vaši aplikaci k získání přístupu.

Výsledek toho, že přihlášení koncového uživatele je, že aplikace je daná přístupový token a obnovovací token. Získá přístupový token připojena k každý požadavek na Data Lake Store nebo Data Lake Analytics, a je platná pro jednu hodinu, ve výchozím nastavení. Token obnovení lze použít k získání nového tokenu přístupu, a je platná dobu až dvou týdnů ve výchozím nastavení, pokud se používá pravidelně. Dva různé přístupy můžete použít pro přihlášení koncového uživatele.

V tomto scénáři aplikace vyzve uživatele k přihlášení a všechny operace se provádějí v kontextu uživatele. Proveďte následující kroky:

1. Prostřednictvím aplikace přesměrujte uživatele na tuto adresu URL:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI&gt; musí být zakódovaný, aby se dal použít jako adresa URL. Ano, pro https://localhost, použijte `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    Pro účely tohoto kurzu můžete ve výše zobrazené adrese URL nahradit zástupné hodnoty a vložit ji do adresního řádku webového prohlížeče. Budete přesměrováni na ověření pomocí přihlášení Azure. Po úspěšném přihlášení se zobrazí v adresním řádku prohlížeče odpověď. Odpověď bude mít tento formát:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Zaznamenejte autorizační kód z odpovědi. V tomto kurzu můžete zkopírovat autorizační kód z adresního řádku webového prohlížeče a předejte jej do v příspěvku požadavek na koncový bod token, jak je znázorněno v následujícím fragmentu kódu:
   
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

3. Odpověď je objekt JSON, který obsahuje přístupový token (například `"access_token": "<ACCESS_TOKEN>"`) a obnovovací token (například `"refresh_token": "<REFRESH_TOKEN>"`). Aplikace používá přístupový token při přístupu k Azure Data Lake Store, zatímco obnovovací token používá k získání dalšího přístupového tokenu, když přístupovému tokenu vyprší platnost.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Když vyprší platnost přístupového tokenu, můžete vyžadovat nový přístupový token pomocí obnovovacího tokenu, jak je znázorněno v následujícím fragmentu kódu:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Další informace o interaktivním ověřování uživatelů najdete v tématu [Tok poskytování autorizačních kódů](https://msdn.microsoft.com/library/azure/dn645542.aspx).
   
## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat ověřování service-to-service k ověřování s Azure Data Lake Store pomocí rozhraní REST API. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat rozhraní REST API pro práci s Azure Data Lake Store.

* [Operace správy účtů v Data Lake Store pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md)
* [Operace dat v Data Lake Store pomocí rozhraní REST API](data-lake-store-data-operations-rest-api.md)

