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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118118"
---
## <a name="about-the-user-delegation-sas"></a>O uživatelské delegování SAS

Token SAS pro přístup ke kontejneru nebo objektu blob může být zabezpečen pomocí přihlašovacích údajů Azure AD nebo klíče účtu. SAS zabezpečené přihlašovacími údaji Azure AD se nazývá uživatelské delegování SAS, protože token OAuth 2.0 používaný k podpisu SAS je požadován jménem uživatele.

Společnost Microsoft doporučuje používat přihlašovací údaje Azure AD, pokud je to možné, jako osvědčený postup zabezpečení, nikoli pomocí klíče účtu, který může být snadněji ohrožen. Když návrh aplikace vyžaduje sdílené přístupové podpisy, použijte přihlašovací údaje Azure AD k vytvoření uživatelskédelegace SAS pro vyšší zabezpečení. Další informace o uživatelskédelegování SAS naleznete [v tématu Vytvoření uživatelskédelegace SAS](/rest/api/storageservices/create-user-delegation-sas).

> [!CAUTION]
> Každý klient, který vlastní platnou Službu SAS, může přistupovat k datům ve vašem účtu úložiště, jak to toto SAS povoluje. Je důležité chránit SAS před škodlivým nebo nezamýšleným použitím. Při distribuci SAS použijte diskrétnost a mějme plán pro zrušení ohroženého SAS.

Další informace o sdílených přístupových podpisech najdete v tématu [Udělení omezeného přístupu k prostředkům úložiště Azure pomocí sdílených přístupových podpisů (SAS).](../articles/storage/common/storage-sas-overview.md)
