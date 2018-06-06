---
title: Spravovat uživatelská data v Azure Active Directory B2C | Microsoft Docs
description: Zjistěte, jak odstranit nebo exportovat data uživatele v Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dacff48be3fbf16fc719f5a0395937b1f5acc979
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712534"
---
# <a name="manage-user-data-in-azure-ad-b2c"></a>Spravovat uživatelská data v Azure AD B2C

 Tento článek obsahuje informace o tom, jak můžete spravovat dat uživatele v Azure Active Directory (AD) B2C pomocí operace poskytované [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog). Správa uživatelských dat zahrnuje možnost odstranit data nebo exportovat data z protokolů auditu.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Odstranění dat uživatele

V adresáři Azure AD B2C a v protokolech auditu jsou uložená data uživatele. Všechna data auditu se zachovává kvůli uchování dat 30 dní v Azure AD B2C. Pokud chcete odstranit uživatelská data do tohoto 30 dní, můžete použít [odstranění uživatele](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser) operaci. Operace odstranění je vyžadován pro jednotlivé klienty Azure AD B2C, kde se může nacházet data. 

Každý uživatel v Azure AD B2C je přiřazen ID objektu. ID objektu poskytuje jednoznačným identifikátor můžete použít k odstranění dat uživatele v Azure AD B2C.  V závislosti na vaší architektury ID objektu může být užitečné korelační identifikátor prostřednictvím dalšími službami, například finanční, marketingu a zákazníků vztah správu databází.  

Nejpřesnější způsob, jak získat objekt ID pro uživatele je použít k získání ho jako součást cesty k ověřování s Azure AD B2C.  Pokud neobdrží platný požadavek na data z určitého uživatele pomocí jiných metod offline proces, jako je například vyhledávání podle Zákaznická podpora agenta, může být potřeba najít uživatele a poznamenejte si ID přidruženého objektu. 

Následující příklad ukazuje k odstranění toku dat:

1. Uživatel se přihlásí a vybere **odstranit svá data**.
2. Aplikace nabízí možnost odstranit data v rámci oddílu aplikace správu.
3. Aplikace vynutí ověření Azure AD B2C. Azure AD B2C poskytuje token s ID objektu uživatele zpět do aplikace. 
4. Token je přijatých aplikace a objekt, který ID se používá k odstranění dat uživatele prostřednictvím volání do Azure AD Graph API. Azure AD Graph API odstraní data uživatele a vrátí stavový kód 200 OK.
5. Aplikace orchestruje odstranění dat uživatele v ostatních systémech organizace podle potřeby pomocí ID objektu nebo další identifikátory.
6. Aplikace potvrdí odstranění dat a poskytuje další kroky pro uživatele.

## <a name="export-customer-data"></a>Exportovat data zákazníků

Proces exportu zákaznická data z Azure AD B2C je podobný procesu odstranění.

Data uživatele Azure AD B2C je omezený na:

- **Data uložená ve službě Azure Active Directory** – v Azure AD B2C ověřování uživatele cesty pomocí ID objektu nebo jakékoli přihlašovací jméno jako je například e-mailu nebo uživatelské jméno může načíst Data.  
- **Sestava událostí auditu uživatelská** – Data je indexovaný pomocí ID objektu.

V následujícím příkladu tok dat export postupu jako prováděného aplikace lze také provést procesem back-end nebo uživatel s rolí správce v adresáři:

1. Uživatel se přihlásí k aplikaci. Azure AD B2C vynucuje ověřování pomocí vícefaktorového ověřování v případě potřeby.
2. Aplikace používá přihlašovací údaje uživatele pro volání operace Azure AD Graph API k načtení atributů uživatele. Azure AD Graph API poskytuje atribut data ve formátu JSON. V závislosti na schématu může být nastaven obsah tokenu id zahrnout všechna osobní data o uživateli.
3. Aplikace načte auditu činnosti koncového uživatele. Azure AD Graph API poskytuje data událostí do aplikace.
4. aplikace agreguje data a zpřístupní ji pro uživatele.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak spravovat, jak mohou uživatelé přistupovat k vaší aplikaci v [spravovat přístup uživatelů](manage-user-access.md)




