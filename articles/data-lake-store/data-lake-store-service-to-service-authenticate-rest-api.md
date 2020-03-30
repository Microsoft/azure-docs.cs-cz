---
title: Ověřování mezi službami – úložiště datových jezer Gen1 – rozhraní REST API
description: Zjistěte, jak dosáhnout ověřování mezi službami pomocí Azure Data Lake Storage Gen1 a Azure Active Directory pomocí rozhraní REST API.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 59d0bf20b16beda47d76e6a9940ac9fa4436da3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904513"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Ověřování mezi službami pomocí Azure Data Lake Storage Gen1 pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

V tomto článku se dozvíte, jak pomocí rozhraní REST API provést ověřování mezi službami pomocí Azure Data Lake Storage Gen1. Ověřování u koncových uživatelů pomocí rozhraní Data Lake Storage Gen1 pomocí rozhraní REST API najdete [v tématu Ověřování koncových uživatelů pomocí rozhraní NOK 1 pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte "webovou" aplikaci služby Azure Active Directory**. Kroky ověřování mezi službami musíte provést [pomocí služby Data Lake Storage Gen1 pomocí služby Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Ověřování služba-služba

V tomto scénáři aplikace poskytuje vlastní pověření k provedení operací. Za tímto způsobem musíte vydat požadavek POST, jako je ten, který je uveden v následujícím fragmentu:

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Výstup požadavku obsahuje autorizační token (označený `access-token` ve výstupu níže), který následně předáte s voláním rozhraní REST API. Uložte ověřovací token do textového souboru. budete potřebovat při volání REST do data lake storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Tento článek používá **neinteraktivní** přístup. Další informace o neinteraktivním přístupu (volání služba-služba) najdete v tématu [Volání služba-služba pomocí přihlašovacích údajů](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak používat ověřování služby ke službě k ověření pomocí Data Lake Storage Gen1 pomocí rozhraní REST API. Teď se můžete podívat na následující články, které hovoří o tom, jak používat rozhraní REST API pro práci s Data Lake Storage Gen1.

* [Operace správy účtů v zařízení Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md)
* [Datové operace na Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-data-operations-rest-api.md)
