---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "76118118"
---
## <a name="about-the-user-delegation-sas"></a>O SAS delegování uživatele

Token SAS pro přístup ke kontejneru nebo objektu BLOB může být zabezpečený buď pomocí přihlašovacích údajů Azure AD, nebo klíče účtu. SAS zabezpečený pomocí přihlašovacích údajů Azure AD se nazývá SAS pro delegování uživatelů, protože za uživatele se požaduje token OAuth 2,0, který se používá k podepsání SAS.

Microsoft doporučuje používat přihlašovací údaje Azure AD, pokud je to možné, a ne používat klíč účtu, který může být snáze ohrožen. Když návrh aplikace vyžaduje sdílené přístupové podpisy, použijte přihlašovací údaje Azure AD k vytvoření SAS delegování uživatelů pro vynikající zabezpečení. Další informace o SAS delegování uživatelů najdete v tématu [Vytvoření SAS pro delegování uživatelů](/rest/api/storageservices/create-user-delegation-sas).

> [!CAUTION]
> Každý klient, který má platné SAS, má přístup k datům ve vašem účtu úložiště, který je povolený tímto SAS. Je důležité chránit SAS před škodlivým nebo nezamýšleným použitím. Využijte uvážení při distribuci SAS a naplánujte si místo pro odvolání ohroženého SAS.

Další informace o sdílených přístupových podpisech najdete v článku [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../articles/storage/common/storage-sas-overview.md).
