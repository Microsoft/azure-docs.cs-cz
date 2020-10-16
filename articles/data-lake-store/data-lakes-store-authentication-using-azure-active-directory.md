---
title: Ověřování – Data Lake Storage Gen1 s využitím Azure AD
description: Naučte se ověřovat pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6fc09f9145b7a1652b621ed38a8bf9af7c4c82a8
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106565"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Ověřování pomocí Azure Data Lake Storage Gen1 s využitím Azure Active Directory

Azure Data Lake Storage Gen1 používá pro ověřování Azure Active Directory. Před vytvořením aplikace, která funguje s Data Lake Storage Gen1, se musíte rozhodnout, jak aplikaci ověřit pomocí Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Možnosti ověřování

* **Ověřování koncových uživatelů** – přihlašovací údaje Azure koncového uživatele se používají k ověřování pomocí Data Lake Storage Gen1. Aplikace, kterou vytvoříte pro práci s Data Lake Storage Gen1 vyzývá k zadání přihlašovacích údajů uživatele. V důsledku toho je tento mechanismus ověřování *interaktivní* a aplikace běží v kontextu přihlášeného uživatele. Další informace a pokyny najdete v tématu [ověřování koncových uživatelů pro data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Ověřování služba-služba** – tuto možnost použijte, pokud chcete, aby aplikace provedla ověřování pomocí Data Lake Storage Gen1. V takových případech vytvoříte aplikaci Azure Active Directory (AD) a použijete klíč z aplikace Azure AD k ověřování pomocí Data Lake Storage Gen1. V důsledku toho je tento mechanismus ověřování *neinteraktivní*. Další informace a pokyny najdete v tématu [ověřování služby-služba pro data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

Následující tabulka ukazuje, jak se pro Data Lake Storage Gen1 podporují mechanismy ověřování koncových uživatelů a služeb. Tady je postup, jak si tabulku přečíst.

* Symbol ✔ * označuje, že možnost ověřování je podporována, a odkazuje na článek, který ukazuje, jak použít možnost ověřování. 
* Symbol ✔ označuje, že je podporována možnost ověřování. 
* Prázdné buňky označují, že možnost ověřování není podporována.


|Použijte tuto možnost ověřování s...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Koncový uživatel (bez MFA * *)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(zastaralé)     |    **[✔ *](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Koncový uživatel (s MFA)                           |    **[✔ *](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔ *](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔ *](data-lake-store-get-started-cli-2.0.md)**      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Služba-služba (s použitím klíče klienta)         |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔ *](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔ *](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔ *](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Služba-služba (používající klientský certifikát) |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klikněte na <b>symbol \* ✔</b> . Jedná se o odkaz.</i><br>
<i>* * MFA představuje službu Multi-Factor Authentication</i>

Další informace o tom, jak používat Azure Active Directory k ověřování, najdete v tématu  [scénáře ověřování pro Azure Active Directory](../active-directory/develop/authentication-vs-authorization.md) .

## <a name="next-steps"></a>Další kroky

* [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)