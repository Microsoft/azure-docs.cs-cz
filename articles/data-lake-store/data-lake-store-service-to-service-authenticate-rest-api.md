---
title: 'Ověřování služba služba: rozhraní REST API s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak dokončit ověřování služba služba s Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí rozhraní REST API
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
ms.openlocfilehash: dd282091d41538b7e3dc08eb0b3d82539fa0bb4f
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295593"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Ověřování služba služba s Azure Data Lake Storage Gen1 pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

V tomto článku se dozvíte o tom, jak pomocí rozhraní REST API, provádět ověřování služba služba s Azure Data Lake Storage Gen1. Ověřování koncového uživatele s Data Lake Storage Gen1 pomocí rozhraní REST API, přečtěte si téma [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Web"**. Je nutné dokončit kroky v [ověřování služba služba Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Ověřování služba-služba
V tomto scénáři aplikace poskytuje svoje vlastní přihlašovací údaje k provedení operací. V takovém případě musíte vydat požadavek POST podobný následujícímu fragmentu kódu: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Výstup tohoto požadavku obsahuje autorizační token (udávají `access-token` ve výstupu níže), že budete předávat s voláními rozhraní REST API. Uložit ověřovací token do textového souboru; budete je potřebovat při volání REST Gen1 úložiště Data Lake.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Tento článek používá **neinteraktivní** přístup. Další informace o neinteraktivním přístupu (volání služba-služba) najdete v tématu [Volání služba-služba pomocí přihlašovacích údajů](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat k ověření pomocí Data Lake Storage Gen1 ověřování služba služba pomocí rozhraní REST API. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat rozhraní REST API pro práci s Data Lake Storage Gen1.

* [Operace správy účtů v Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md)
* [Operace s daty v Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-data-operations-rest-api.md)

