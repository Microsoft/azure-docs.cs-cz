---
title: Ověřování v Data Lake Store pomocí Azure Active Directory | Microsoft Docs
description: Zjistěte, jak k ověření s Data Lake Store pomocí Azure Active Directory
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
ms.openlocfilehash: bee65fbdc65807ac33ae425ed9d87dbf0c246d9d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625283"
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Ověřování pomocí Data Lake Store pomocí Azure Active Directory

Azure Data Lake Store využívá Azure Active Directory pro ověřování. Před vytváření aplikace, která funguje s Azure Data Lake Store, musíte rozhodnout, jak ověřovat vaší aplikace pomocí Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Možnosti ověřování

* **Koncový uživatel ověřování** -přihlašovacích údajů koncového uživatele služby Azure slouží k ověření s Data Lake Store. Aplikace, které vytvoříte pro práci s Data Lake Store zobrazí výzvu pro tyto přihlašovací údaje uživatele. V důsledku toho je tento mechanismus ověřování *interaktivní* a spuštění aplikace v kontextu přihlášeného uživatele. Další informace a pokyny najdete v tématu [ověřování koncového uživatele pro Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Ověřování služby služby** – tuto možnost použijte, pokud chcete, aby aplikace ke svému ověření s Data Lake Store. V takových případech můžete vytvořit aplikaci Azure Active Directory (AD) a používat klíče z aplikace Azure AD k ověření s Data Lake Store. V důsledku toho je tento mechanismus ověřování *neinteraktivní*. Další informace a pokyny najdete v tématu [Service-to-service ověřování pro Data Lake Store](data-lake-store-service-to-service-authenticate-using-active-directory.md).

Následující tabulka znázorňuje, jak koncový uživatel a service-to-service ověřovací mechanismy, které jsou podporovány pro Data Lake Store. Zde je, jak číst v tabulce.

* Symbol ✔ * označuje, že ověřování možnost je podporována a odkazy na článek, který ukazuje, jak použít možnost ověřování. 
* ✔ symbol označuje, že je podporována možnost ověřování. 
* U prázdných buněk označují, že není podporována možnost ověřování.


|Pomocí této možnosti ověřování s...                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Koncový uživatel (bez MFA **)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(zastaralé)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Koncový uživatel (pomocí vícefaktorového ověřování)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Service-to-service (pomocí klíče klienta)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Service-to-service (pomocí klientského certifikátu) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klikněte <b>✔\* </b> symbol. Je odkaz.</i><br>
<i>** MFA je zkratka pro službu Multi-Factor authentication</i>

V tématu [scénáře ověřování pro Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md) Další informace o tom, jak používat Azure Active Directory pro ověřování.

## <a name="next-steps"></a>Další postup

* [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)


