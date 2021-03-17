---
title: Ověřování koncových uživatelů – REST s Data Lake Storage Gen1 – Azure
description: Naučte se, jak dosáhnout ověřování koncovými uživateli pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí REST API
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 462cd06c9da3b1f0a57c293d52c59181372b709b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103743"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Ověřování koncových uživatelů s Azure Data Lake Storage Gen1 pomocí REST API
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Použití REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

V tomto článku se dozvíte, jak používat REST API k ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1. Informace o ověřování služby-služba pomocí Data Lake Storage Gen1 pomocí REST API najdete v tématu [ověřování služba-služba s Data Lake Storage Gen1 pomocí REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte Azure Active Directory "nativní" aplikaci**. Musíte dokončit kroky v části [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[kudrlinkou](https://curl.haxx.se/)**. V tomto článku se pomocí oblé předvádí způsob, jak REST API volání na účet Data Lake Storage Gen1.

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
Ověřování koncových uživatelů je doporučený postup, pokud chcete, aby se uživatel přihlásil k aplikaci pomocí Azure AD. Vaše aplikace bude mít přístup k prostředkům Azure se stejnou úrovní přístupu jako přihlášený uživatel. Uživatel musí zadat své přihlašovací údaje pravidelně, aby mohla vaše aplikace zachovat přístup.

Výsledkem přihlášení koncového uživatele je, že aplikace má přístup k přístupovému tokenu a obnovovacímu tokenu. Přístupový token se připojí ke všem žádostem provedeným na Data Lake Storage Gen1 nebo Data Lake Analytics a ve výchozím nastavení platí pro jednu hodinu. Aktualizační token se dá použít k získání nového přístupového tokenu a ve výchozím nastavení je platný až pro dva týdny, pokud se pravidelně používá. Pro přihlášení koncového uživatele můžete použít dva různé přístupy.

V tomto scénáři aplikace vyzve uživatele k přihlášení a všechny operace se provádějí v kontextu uživatele. Proveďte tyto kroky:

1. Prostřednictvím aplikace přesměrujte uživatele na tuto adresu URL:

    `https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>`

   > [!NOTE]
   > \<REDIRECT-URI> musí být kódovány pro použití v adrese URL. Takže pro https://localhost , použití `https%3A%2F%2Flocalhost` )

    Pro účely tohoto kurzu můžete ve výše zobrazené adrese URL nahradit zástupné hodnoty a vložit ji do adresního řádku webového prohlížeče. Budete přesměrováni na ověření pomocí přihlášení Azure. Po úspěšném přihlášení se zobrazí v adresním řádku prohlížeče odpověď. Odpověď bude mít tento formát:

    `http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>`

2. Zaznamenejte autorizační kód z odpovědi. Pro účely tohoto kurzu můžete zkopírovat autorizační kód z panelu Adresa ve webovém prohlížeči a předat ho do koncového bodu tokenu v požadavku POST, jak je znázorněno v následujícím fragmentu kódu:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
    -F redirect_uri=<REDIRECT-URI> \
    -F grant_type=authorization_code \
    -F resource=https://management.core.windows.net/ \
    -F client_id=<APPLICATION-ID> \
    -F code=<AUTHORIZATION-CODE>
    ```

   > [!NOTE]
   > V takovém případě \<REDIRECT-URI> nemusí být nutné zakódovat.
   > 
   > 

3. Odpověď je objekt JSON, který obsahuje přístupový token (například `"access_token": "<ACCESS_TOKEN>"` ) a obnovovací token (například `"refresh_token": "<REFRESH_TOKEN>"` ). Vaše aplikace používá přístupový token při přístupu k Azure Data Lake Storage Gen1 a aktualizačnímu tokenu k získání dalšího přístupového tokenu, když vyprší platnost přístupového tokenu.

    ```json
    {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
    ```

4. Po vypršení přístupového tokenu můžete požádat o nový přístupový token pomocí obnovovacího tokenu, jak je znázorněno v následujícím fragmentu kódu:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
         -F grant_type=refresh_token \
         -F resource=https://management.core.windows.net/ \
         -F client_id=<APPLICATION-ID> \
         -F refresh_token=<REFRESH-TOKEN>
    ```

Další informace o interaktivním ověřování uživatelů najdete v tématu [Tok poskytování autorizačních kódů](/previous-versions/azure/dn645542(v=azure.100)).

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak používat ověřování služby pro službu k ověřování pomocí Azure Data Lake Storage Gen1 pomocí REST API. Nyní se můžete podívat na následující články, které vás seznámí s postupem použití REST API k práci s Azure Data Lake Storage Gen1.

* [Operace správy účtů u Data Lake Storage Gen1 pomocí REST API](data-lake-store-get-started-rest-api.md)
* [Operace s daty při Data Lake Storage Gen1 pomocí REST API](data-lake-store-data-operations-rest-api.md)