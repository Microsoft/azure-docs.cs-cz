---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 2ea936ba489024648583cf22c5ab73ae06bb1537
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011970"
---
## <a name="about-the-user-delegation-sas-preview"></a>O SAS delegování uživatele (Preview)

Token SAS pro přístup ke kontejneru nebo objektu BLOB může být zabezpečený buď pomocí přihlašovacích údajů Azure AD, nebo klíče účtu. SAS zabezpečený pomocí přihlašovacích údajů Azure AD se nazývá SAS pro delegování uživatelů, protože za uživatele se požaduje token OAuth 2,0, který se používá k podepsání SAS.

Microsoft doporučuje používat přihlašovací údaje Azure AD, pokud je to možné, a ne používat klíč účtu, který může být snáze ohrožen. Když návrh aplikace vyžaduje sdílené přístupové podpisy, použijte přihlašovací údaje Azure AD k vytvoření SAS delegování uživatelů pro vynikající zabezpečení. Další informace o SAS delegování uživatelů najdete v tématu [Vytvoření SAS pro delegování uživatelů](/rest/api/storageservices/create-a-user-delegation-sas).

> [!NOTE]
> Podpis SAS pro delegování uživatelů je určený jenom pro neprodukční použití.

> [!CAUTION]
> Každý klient, který má platné SAS, má přístup k datům ve vašem účtu úložiště, který je povolený tímto SAS. Je důležité chránit SAS před škodlivým nebo nezamýšleným použitím. Využijte uvážení při distribuci SAS a naplánujte si místo pro odvolání ohroženého SAS.

Další informace o sdílených přístupových podpisech najdete v článku [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../articles/storage/common/storage-sas-overview.md).
