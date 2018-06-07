---
title: 'Ověřování služby služby: REST API s Data Lake Store pomocí Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak dosáhnout service-to-service ověřování s Data Lake Store pomocí Azure Active Directory pomocí rozhraní REST API
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
ms.openlocfilehash: ffa9b7408475820735e35a82edc0b1751abeb08a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624845"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-rest-api"></a>Service-to-service ověřování s Data Lake Store pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

V tomto článku se dozvíte o tom, jak proveďte service-to-service ověřování s Azure Data Lake Store pomocí rozhraní REST API. Pro ověřování koncového uživatele s Data Lake Store pomocí rozhraní REST API, najdete v části [ověřování koncového uživatele s Data Lake Store pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Web"**. Musí mít dokončili postup v [Service-to-service ověřování s Data Lake Store pomocí Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Ověřování služba-služba
V tomto scénáři aplikace poskytuje svoje vlastní přihlašovací údaje k provedení operace. V takovém případě musíte vydat požadavek POST jako je uvedeno v následující fragment kódu: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Výstup požadavku bude obsahovat autorizační token (odlišené `access-token` níže ve výstupu), budete předávat s voláními rozhraní REST API. Uložit ověřovací token do textového souboru; je nutné ho při volání REST do Data Lake Store.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Tento článek používá **neinteraktivní** přístup. Další informace o neinteraktivním přístupu (volání služba-služba) najdete v tématu [Volání služba-služba pomocí přihlašovacích údajů](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat ověřování service-to-service k ověřování s Azure Data Lake Store pomocí rozhraní REST API. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat rozhraní REST API pro práci s Azure Data Lake Store.

* [Operace správy účtů v Data Lake Store pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md)
* [Operace dat v Data Lake Store pomocí rozhraní REST API](data-lake-store-data-operations-rest-api.md)

