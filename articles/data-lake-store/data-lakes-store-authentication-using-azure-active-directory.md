---
title: Ověřování v Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak ověřování pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
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
ms.openlocfilehash: feb35336681376f6f4bd262418146d931f3d1e83
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294879"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Ověřování pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory

Azure Data Lake Storage Gen1 používá Azure Active Directory pro ověřování. Před vytváříte aplikaci, která funguje s Data Lake Storage Gen1, musíte rozhodnout, jak k ověřování vaší aplikace s Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Možnosti ověřování

* **Ověřování koncového uživatele** – přihlašovací údaje Azure koncového uživatele se používají k ověření pomocí Data Lake Storage Gen1. Aplikace, kterou vytvoříte pro práci s Data Lake Storage Gen1 vyzve k zadání těchto přihlašovacích údajů uživatele. V důsledku toho tento mechanismus ověřování je *interaktivní* a spuštění aplikace v kontextu přihlášeného uživatele. Další informace a pokyny najdete v tématu [ověřování koncového uživatele pro Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Ověřování služba služba** – tuto možnost použijte, pokud chcete aplikaci ke svému ověření s Data Lake Storage Gen1. V takovém případě vytvořte aplikaci Azure Active Directory (AD) a klíče z Azure AD aplikace používají k ověření Gen1 úložiště Data Lake. V důsledku toho tento mechanismus ověřování je *neinteraktivní*. Další informace a pokyny najdete v tématu [ověřování služba služba pro Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

Následující tabulka ukazuje, jak koncový uživatel a mechanismy ověřování služba služba se podporují pro Data Lake Storage Gen1. Zde je, jak číst z tabulky.

* Symbol ✔ * označuje, že možnost ověřování je podporováno a odkazuje na článek, který ukazuje, jak použít možnost ověřování. 
* ✔ symbol označuje, že se podporuje možnost ověřování. 
* U prázdných buněk označují, že možnost ověřování není podporována.


|Pomocí této možnosti ověřování s...                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Koncový uživatel (bez vícefaktorové ověřování **)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(zastaralé)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Koncový uživatel (pomocí vícefaktorového ověřování)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Služba služba (pomocí klíče klienta)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Služba služba (pomocí klientského certifikátu) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klikněte <b>✔\* </b> symbol. Je to odkaz.</i><br>
<i>** Vícefaktorové ověřování, které jsou zahrnovaného ověřování službou Multi-Factor Authentication</i>

Zobrazit [scénáře ověřování pro Azure Active Directory](../active-directory/develop/authentication-scenarios.md) Další informace o tom, jak pomocí Azure Active Directory pro ověřování.

## <a name="next-steps"></a>Další postup

* [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)


