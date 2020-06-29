---
title: Ověřování službou REST – Data Lake Storage Gen1 – Azure
description: Naučte se, jak dosáhnout ověřování služby-služba pomocí Azure Data Lake Storage Gen1 a Azure Active Directory pomocí REST API.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 08b5e915902fb4e2002bd20699cdaca9f0f35e3e
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85505062"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Ověřování služba-služba pomocí Azure Data Lake Storage Gen1 s využitím REST API
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

V tomto článku se dozvíte, jak pomocí REST API provádět ověřování pomocí služby s Azure Data Lake Storage Gen1. Ověřování koncových uživatelů pomocí Data Lake Storage Gen1 pomocí REST API najdete v tématu [ověřování koncových uživatelů pomocí Data Lake Storage Gen1 pomocí REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte Azure Active Directory aplikaci "Web"**. Musíte dokončit kroky v [ověřování služba-služba s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Ověřování služba-služba

V tomto scénáři aplikace poskytuje vlastní přihlašovací údaje k provedení operací. V tomto případě musíte vydat požadavek POST podobný tomu, jak je znázorněno v následujícím fragmentu kódu:

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Výstup požadavku zahrnuje autorizační token (označený `access-token` ve výstupu níže), který následně projdete REST API volání. Uložte ověřovací token do textového souboru. budete ho potřebovat při volání REST Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Tento článek používá **neinteraktivní** přístup. Další informace o neinteraktivním přístupu (volání služba-služba) najdete v tématu [Volání služba-služba pomocí přihlašovacích údajů](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat ověřování služby pro službu k ověřování pomocí Data Lake Storage Gen1 pomocí REST API. Nyní se můžete podívat na následující články, které vás seznámí s postupem použití REST API k práci s Data Lake Storage Gen1.

* [Operace správy účtů u Data Lake Storage Gen1 pomocí REST API](data-lake-store-get-started-rest-api.md)
* [Operace s daty při Data Lake Storage Gen1 pomocí REST API](data-lake-store-data-operations-rest-api.md)
