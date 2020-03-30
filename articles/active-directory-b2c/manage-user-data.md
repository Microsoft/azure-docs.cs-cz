---
title: Správa uživatelských dat ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Přečtěte si, jak odstranit nebo exportovat uživatelská data v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e245b58449ab773914fc60be056082b82f05035a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184481"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Správa uživatelských dat ve službě Azure Active Directory B2C

 Tento článek popisuje, jak můžete spravovat uživatelská data ve službě Azure Active Directory B2C (Azure AD B2C) pomocí operací, které jsou poskytovány [rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api). Správa uživatelských dat zahrnuje odstranění nebo export dat z protokolů auditu.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Odstranění uživatelských dat

Uživatelská data se ukládají v adresáři Azure AD B2C a v protokolech auditu. Všechna data auditování uživatelů se uchovávají po dobu 7 dnů v Azure AD B2C. Pokud chcete odstranit uživatelská data během tohoto 7denního období, můžete použít operaci [Odstranit uživatele.](https://docs.microsoft.com/graph/api/user-delete) Operace DELETE je vyžadována pro každého klienta Azure AD B2C, kde se mohou nasytit data.

Každému uživateli v Azure AD B2C je přiřazeno ID objektu. ID objektu poskytuje jednoznačný identifikátor, který můžete použít k odstranění uživatelských dat v Azure AD B2C. V závislosti na vaší architektuře může být ID objektu užitečným identifikátorem korelace mezi jinými službami, jako jsou finanční databáze, marketingové databáze a databáze řízení vztahů se zákazníky.

Nejpřesnější způsob, jak získat ID objektu pro uživatele je získat jako součást cesty ověřování s Azure AD B2C. Pokud obdržíte platný požadavek na data od uživatele pomocí jiných metod, může být pro vyhledání uživatele a zapamatování si ID přidruženého objektu proces offline, například hledání agentem podpory služeb zákazníkům.

Následující příklad ukazuje možný tok odstranění dat:

1. Uživatel se přihlásí a vybere **možnost Odstranit moje data**.
2. Aplikace nabízí možnost odstranit data v rámci administrační části aplikace.
3. Aplikace vynutí ověřování na Azure AD B2C. Azure AD B2C poskytuje token s ID objektu uživatele zpět do aplikace.
4. Token je přijat aplikací a ID objektu se používá k odstranění uživatelských dat prostřednictvím volání rozhraní Microsoft Graph API. Rozhraní Microsoft Graph API odstraní uživatelská data a vrátí stavový kód 200 OK.
5. Aplikace orchestruje odstranění uživatelských dat v jiných organizačních systémech podle potřeby pomocí ID objektu nebo jiných identifikátorů.
6. Aplikace potvrzuje odstranění dat a poskytuje další kroky uživateli.

## <a name="export-customer-data"></a>Export dat odběratele

Proces exportu zákaznických dat z Azure AD B2C je podobný procesu odstranění.

Uživatelská data Azure AD B2C jsou omezená na:

- **Data uložená ve službě Azure Active Directory**: Data můžete načíst v cestě uživatele ověřování Azure AD B2C pomocí ID objektu nebo libovolného přihlašovacího jména, jako je e-mailová adresa nebo uživatelské jméno.
- **Sestava událostí auditu specifických pro uživatele**: Data můžete indexovat pomocí ID objektu.

V následujícím příkladu toku dat exportu lze kroky popsané jako prováděné aplikací provést také proces back-endu nebo uživatel s rolí správce v adresáři:

1. Uživatel se přihlásí k aplikaci. Azure AD B2C vynucuje ověřování pomocí Azure Multi-Factor Authentication v případě potřeby.
2. Aplikace používá pověření uživatele k volání operace rozhraní MICROSOFT Graph API k načtení atributů uživatele. Rozhraní Microsoft Graph API poskytuje data atributů ve formátu JSON. V závislosti na schématu můžete nastavit obsah tokenu ID tak, aby zahrnoval všechny osobní údaje o uživateli.
3. Aplikace načte aktivitu auditu uživatele. Rozhraní Microsoft Graph API poskytuje data událostí do aplikace.
4. Aplikace agreguje data a zpřístupňuje je uživateli.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak spravovat přístup uživatelů k vaší aplikaci, naleznete v [tématu Správa přístupu uživatelů](manage-user-access.md).
