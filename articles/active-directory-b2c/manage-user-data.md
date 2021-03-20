---
title: Spravovat uživatelská data v Azure Active Directory B2C | Microsoft Docs
description: Naučte se odstraňovat nebo exportovat data uživatelů v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d9bdf7258296b82e65e03f6b8af8021b9a7be0f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94952467"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Správa uživatelských dat v Azure Active Directory B2C

 Tento článek popisuje, jak můžete spravovat data uživatelů v Azure Active Directory B2C (Azure AD B2C) pomocí operací, které jsou k dispozici v [rozhraní API pro Microsoft Graph](/graph/use-the-api). Správa uživatelských dat zahrnuje odstranění nebo export dat z protokolů auditu.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Odstranění uživatelských dat

Uživatelská data jsou uložená v adresáři Azure AD B2C a v protokolech auditu. Všechna data auditu uživatele se uchovávají 7 dní v Azure AD B2C. Pokud chcete odstranit uživatelská data během období 7 dní, můžete použít operaci [Odstranit uživatele](/graph/api/user-delete) . Pro každého klienta Azure AD B2C, kde se mohou nacházet data, se vyžaduje operace odstranění.

Každému uživateli v Azure AD B2C je přiřazeno ID objektu. ID objektu poskytuje jednoznačný identifikátor, který můžete použít k odstranění uživatelských dat v Azure AD B2C. V závislosti na vaší architektuře může být ID objektu užitečným identifikátorem korelace napříč ostatními službami, jako jsou finanční, marketingová a zákaznická Správa vztahů se zákazníky.

Nejpřesnější způsob, jak získat ID objektu pro uživatele, je získat ho jako součást cesty pro ověřování pomocí Azure AD B2C. Pokud obdržíte platnou žádost o data od uživatele pomocí jiných metod, může být nutné najít uživatele v režimu offline, například hledání pomocí agenta podpory služeb zákazníkům, a poznamenat si ID přidruženého objektu.

Následující příklad ukazuje možný tok odstranění dat:

1. Uživatel se přihlásí a vybere **Odstranit moje data**.
2. Aplikace nabízí možnost odstranit data v části pro správu aplikace.
3. Aplikace vynutí ověřování Azure AD B2C. Azure AD B2C poskytuje token s ID objektu uživatele zpátky do aplikace.
4. Token obdrží aplikace a ID objektu slouží k odstranění uživatelských dat prostřednictvím volání rozhraní Microsoft Graph API. Rozhraní Microsoft Graph API odstraní data uživatelů a vrátí stavový kód 200 OK.
5. Aplikace orchestruje odstranění uživatelských dat v jiných systémech organizace podle potřeby pomocí ID objektu nebo jiných identifikátorů.
6. Aplikace potvrdí odstranění dat a poskytne uživateli další kroky.

## <a name="export-customer-data"></a>Exportovat zákaznická data

Proces exportu zákaznických dat z Azure AD B2C je podobný procesu odstranění.

Azure AD B2C uživatelských dat je omezeno na:

- **Data uložená v Azure Active Directory**: data můžete načíst v cestě uživatele ověřování Azure AD B2C pomocí ID objektu nebo jakéhokoliv přihlašovacího jména, například e-mailové adresy nebo uživatelského jména.
- **Sestava událostí auditování specifických pro uživatele**: data můžete indexovat pomocí ID objektu.

V následujícím příkladu toku dat exportu může být postup, který je popsán v rámci aplikace, také proveden buď pomocí back-endu, nebo uživatelem s rolí správce v adresáři:

1. Uživatel se přihlásí k aplikaci. Azure AD B2C vynutil ověřování pomocí služby Azure AD Multi-Factor Authentication v případě potřeby.
2. Aplikace používá pověření uživatele k volání operace Microsoft Graph rozhraní API k načtení atributů uživatele. Rozhraní Microsoft Graph API poskytuje data atributů ve formátu JSON. V závislosti na schématu můžete nastavit obsah tokenu ID tak, aby zahrnoval všechna osobní údaje o uživateli.
3. Aplikace načte aktivitu auditování uživatele. Rozhraní Microsoft Graph API poskytuje aplikaci data události.
4. Aplikace agreguje data a zpřístupňuje je uživateli.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak spravovat, jak uživatelé přistupují k vaší aplikaci, najdete v tématu [Správa přístupu uživatelů](manage-user-access.md).