---
title: 'Ověřování koncových uživatelů: Rozhraní REST API s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak dosáhnout ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí rozhraní REST API
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
ms.openlocfilehash: 0ef65c23ee1bf4f064695779b71c8616427da204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877818"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1 pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

V tomto článku se dozvíte, jak pomocí rozhraní REST API provést ověřování koncových uživatelů s Azure Data Lake Storage Gen1. Ověřování mezi službami pomocí rozhraní Data Lake Storage Gen1 pomocí rozhraní REST API najdete v [tématu Ověřování mezi službami pomocí rozhraní AP úložiště datových jezer 1 pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte nativní aplikaci služby Azure Active Directory**. Kroky ověřování koncových uživatelů musíte provést [pomocí služby Data Lake Storage Gen1 pomocí služby Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](https://curl.haxx.se/)**. Tento článek používá cURL k předvedení, jak provádět volání rozhraní REST API proti účtu Gen1 úložiště datového jezera.

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
Ověřování koncových uživatelů je doporučený přístup, pokud chcete, aby se uživatel přihlásit k vaší aplikaci pomocí Azure AD. Vaše aplikace je schopna přistupovat k prostředkům Azure se stejnou úrovní přístupu jako přihlášený uživatel. Uživatel musí pravidelně zajišťovat svá pověření, aby vaše aplikace mohla udržovat přístup.

Výsledkem přihlášení koncového uživatele je, že vaše aplikace je uveden přístupový token a obnovovací token. Přístupový token získá připojené ke každému požadavku na Data Lake Storage Gen1 nebo Data Lake Analytics a je platný po dobu jedné hodiny ve výchozím nastavení. Obnovovací token lze použít k získání nového přístupového tokenu a je platný ve výchozím nastavení až dva týdny, pokud se používá pravidelně. Pro přihlášení koncového uživatele můžete použít dva různé přístupy.

V tomto scénáři aplikace vyzve uživatele k přihlášení a všechny operace se provádějí v kontextu uživatele. Proveďte následující kroky:

1. Prostřednictvím aplikace přesměrujte uživatele na tuto adresu URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > \<REDIRECT-URI&gt; musí být zakódovaný, aby se dal použít jako adresa URL. Takže pro https://localhost, `https%3A%2F%2Flocalhost`použití )

    Pro účely tohoto kurzu můžete ve výše zobrazené adrese URL nahradit zástupné hodnoty a vložit ji do adresního řádku webového prohlížeče. Budete přesměrováni na ověření pomocí přihlášení Azure. Po úspěšném přihlášení se zobrazí v adresním řádku prohlížeče odpověď. Odpověď bude mít tento formát:

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Zaznamenejte autorizační kód z odpovědi. V tomto kurzu můžete zkopírovat autorizační kód z adresního řádku webového prohlížeče a předat jej v požadavku POST koncovému bodu tokenu, jak je znázorněno v následujícím fragmentu:

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

3. Odpověď je objekt JSON, `"access_token": "<ACCESS_TOKEN>"`který obsahuje přístupový token (například) a `"refresh_token": "<REFRESH_TOKEN>"`obnovovací token (například). Vaše aplikace používá přístupový token při přístupu k Azure Data Lake Storage Gen1 a obnovovací token získat jiný přístupový token při vypršení platnosti přístupového tokenu.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Po vypršení platnosti přístupového tokenu můžete požádat o nový přístupový token pomocí obnovovacího tokenu, jak je znázorněno v následujícím fragmentu:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Další informace o interaktivním ověřování uživatelů najdete v tématu [Tok poskytování autorizačních kódů](https://msdn.microsoft.com/library/azure/dn645542.aspx).

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak používat ověřování mezi službami k ověření pomocí Azure Data Lake Storage Gen1 pomocí rozhraní REST API. Teď se můžete podívat na následující články, které popisují, jak používat rozhraní REST API pro práci s Azure Data Lake Storage Gen1.

* [Operace správy účtů v zařízení Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md)
* [Datové operace na Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-data-operations-rest-api.md)

