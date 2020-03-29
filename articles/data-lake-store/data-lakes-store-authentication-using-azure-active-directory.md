---
title: Ověřování v Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, jak se ověřit pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory
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
ms.openlocfilehash: f83cf183bee930dd07c707b0eb49125cecd70b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60193590"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Ověřování pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory

Azure Data Lake Storage Gen1 používá Azure Active Directory pro ověřování. Před vytvořením aplikace, která pracuje s Data Lake Storage Gen1, musíte se rozhodnout, jak ověřit vaši aplikaci pomocí Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Možnosti ověřování

* **Ověřování koncových uživatelů** – přihlašovací údaje koncového uživatele Azure se používají k ověření pomocí data lake storage gen1. Aplikace, kterou vytvoříte pro práci s Data Lake Storage Gen1 výzvy pro tato pověření uživatele. V důsledku toho je tento mechanismus ověřování *interaktivní* a aplikace běží v kontextu přihlášeného uživatele. Další informace a pokyny naleznete [v tématu Ověřování koncových uživatelů pro data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Ověřování mezi službami** – tuto možnost použijte, pokud chcete, aby se aplikace ověřovala pomocí data lake storage gen1. V takových případech vytvoříte aplikaci Azure Active Directory (AD) a pomocí klíče z aplikace Azure AD ověřte pomocí data Lake Storage Gen1. V důsledku toho je tento mechanismus ověřování *neinteraktivní*. Další informace a pokyny naleznete v [tématu Ověřování mezi službami pro úložiště datových jezer Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

Následující tabulka ukazuje, jak jsou podporovány mechanismy ověřování koncového uživatele a služby služby pro úložiště datového jezera Gen1. Zde je návod, jak číst tabulku.

* Symbol ✔* označuje, že možnost ověřování je podporována a odkazuje na článek, který ukazuje, jak používat možnost ověřování. 
* Symbol ✔ označuje, že možnost ověřování je podporována. 
* Prázdné buňky označují, že možnost ověřování není podporována.


|Tuto možnost ověřování použijte s...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Koncový uživatel (bez vícefaktorové ověřování**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(zastaralé)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Koncový uživatel (s vícefaktorové ověřování)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Servis-servis (pomocí klientského klíče)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Servis-servis (pomocí klientského certifikátu) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klikněte na <b>symbol ✔.\* </b> Je to spojení.</i><br>
<i>** MFA je zkratka pro vícefaktorovou autentizaci</i>

Další informace o tom, jak používat Azure Active Directory k ověřování, najdete v tématu [Scénáře ověřování pro Azure Active Directory.](../active-directory/develop/authentication-scenarios.md)

## <a name="next-steps"></a>Další kroky

* [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)


