---
title: Správa údajů uživatele ve službě Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak odstranit nebo export dat uživatele v Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 202ee9da94fc93e7301c29b62dc61ad443685807
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449819"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Správa údajů uživatele ve službě Azure Active Directory B2C

 Tento článek popisuje, jak můžete spravovat uživatelská data v Azure Active Directory (Azure AD) B2C s použitím operací, které jsou poskytovány [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog). Správa uživatelských dat zahrnuje odstranění nebo export dat z protokolů auditu.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Odstranit data uživatelů

Data uživatele uložená v adresáři Azure AD B2C a v protokolech auditování. Všechna data auditu uživatele se uchovávají po dobu 30 dnů v Azure AD B2C. Pokud chcete odstranit data uživatele během tohoto 30denního období, můžete použít [odstranění uživatele](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser) operace. Operace odstranění se vyžaduje pro jednotlivé tenanty Azure AD B2C kde se může nacházet data. 

Každého uživatele v Azure AD B2C je přiřazen identifikátor objektu. ID objektu poskytuje jednoznačný identifikátor můžete použít k odstranění údajů uživatele ve službě Azure AD B2C. V závislosti na vaší architektuře ID objektu může být užitečné korelační identifikátor napříč dalšími službami, jako jsou finanční, marketing a zákaznických relace správy databází. 

Co nejvíce zpřesnili způsob, jak získat ID objektu uživatele je získat ji jako součást cesty ověřování pomocí Azure AD B2C. Pokud se zobrazí platný požadavek na data z uživatele pomocí jiných metod offline proces, jako je hledání podle zákaznickou podporu služby agenta, může být potřeba vyhledejte uživatele a poznamenejte si ID přidruženého objektu. 

Následující příklad ukazuje tok možné odstranění dat:

1. Uživatel se přihlásí a vybere **odstranit data**.
2. Aplikace nabízí možnost odstranit data v rámci správy část aplikace.
3. Aplikace vynutí ověření přes Azure AD B2C. Azure AD B2C poskytuje token s ID objektu uživatele zpět do aplikace. 
4. Token, který přijme aplikaci a na objekt, který ID slouží k odstranění dat uživatele prostřednictvím volání do Azure AD Graph API. Azure AD Graph API odstraní uživatelská data a vrátí stavový kód 200 OK.
5. Aplikace orchestruje odstranění dat uživatele v jiné organizační systémy podle potřeby s použitím ID objektu nebo další identifikátory.
6. Aplikace potvrdí odstranění dat a poskytuje další kroky pro uživatele.

## <a name="export-customer-data"></a>Export zákaznických údajů

Proces exportu zákaznická data z Azure AD B2C je podobný procesu odstranění.

Data uživatele Azure AD B2C je omezený na:

- **Data uložená v Azure Active Directory**: data v cestě uživatele ověřování Azure AD B2C můžete načíst pomocí ID objektu nebo jakékoli přihlašovací jméno, například uživatelské jméno nebo e-mailovou adresu. 
- **Události sestavy auditování uživatelská**: můžete indexovat data s použitím ID objektu.

V následujícím příkladu toku dat exportu kroky, které jsou popsány jako prováděného aplikace lze také provést pomocí back-endový proces nebo uživatel s rolí správce v adresáři:

1. Uživatel se přihlásí do aplikace. Azure AD B2C vynucuje ověřování pomocí Azure Multi-Factor Authentication v případě potřeby.
2. Aplikace používá přihlašovací údaje uživatele k volání operace Azure AD Graph API k načtení atributy uživatele. Azure AD Graph API poskytuje atribut data ve formátu JSON. V závislosti na schématu můžete nastavit obsah tokenu ID zahrnout všechny osobní údaje o uživateli.
3. Aplikace načte auditování aktivit uživatelů. Azure AD Graph API poskytuje data události do aplikace.
4. Aplikace agreguje data a zpřístupňuje je pro uživatele.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak spravovat přístup uživatelů k aplikaci, najdete v článku [spravovat přístup uživatelů](manage-user-access.md).




