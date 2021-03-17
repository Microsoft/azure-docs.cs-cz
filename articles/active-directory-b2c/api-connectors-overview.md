---
title: O konektorech rozhraní API v Azure AD B2C
description: Použijte konektory rozhraní API pro Azure Active Directory (Azure AD) k přizpůsobení a rozšíření toků uživatelů registrace pomocí webových rozhraní API.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: d7a3301cb6ec10e75979d0e1fdfad52c7103a2aa
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611517"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>Použití konektorů rozhraní API k přizpůsobení a rozšiřování toků uživatelů registrace

> [!IMPORTANT]
> Konektory rozhraní API pro registraci jsou funkcí verze Public Preview služby Azure AD B2C. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="overview"></a>Přehled 
Jako vývojář nebo správce IT můžete použít konektory rozhraní API k integraci uživatelských toků přihlášení k webovým rozhraním API, abyste mohli přizpůsobit prostředí pro registraci a integrovat je s externími systémy. Například pomocí konektorů rozhraní API můžete:

- **Ověřte vstupní data uživatele**. Ověřte proti poškozeným nebo neplatným uživatelským datům. Můžete například ověřit uživatelem zadaná data pro stávající data v externím úložišti dat nebo seznamu povolených hodnot. Pokud je neplatná, můžete požádat uživatele, aby poskytl platná data, nebo zablokovat, aby uživatel pokračoval v procesu registrace.
- Proveďte **integraci s vlastním pracovním postupem schválení**. Připojte se k vlastnímu systému schvalování pro správu a omezení vytváření účtů.
- **Přepsat atributy uživatele**. Přeformátujte nebo přiřaďte hodnotu atributu shromážděnému od uživatele. Pokud například uživatel zadá křestní jméno do všech malých a velkých písmen, můžete název naformátovat pouze na první písmeno. 
- **Proveďte ověření identity**. Pomocí služby ověřování identity přidejte další úroveň zabezpečení pro rozhodování o vytváření účtů.
- **Spusťte vlastní obchodní logiku**. Můžete aktivovat navazující události v cloudových systémech a odesílat nabízená oznámení, aktualizovat podnikové databáze, spravovat oprávnění, auditovat databáze a provádět další vlastní akce.

Konektor API poskytuje Azure Active Directory s informacemi potřebnými pro volání koncového bodu rozhraní API, a to definováním adresy URL koncového bodu HTTP a ověření pro volání rozhraní API. Po nakonfigurování konektoru API ho můžete povolit pro konkrétní krok v toku uživatele. Když uživatel dosáhne tohoto kroku v procesu registrace, vyvolá se konektor API a materializuje jako požadavek HTTP POST do vašeho rozhraní API a pošle informace o uživateli (deklarace identity) jako páry klíč-hodnota v těle JSON. Reakce rozhraní API může ovlivnit spuštění toku uživatele. Odpověď rozhraní API může například zablokovat uživateli registraci, požádat uživatele o opětovné zadání informací, nebo přepsat a připojit atributy uživatele.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Kde můžete povolit konektor API v toku uživatele

Tok uživatele obsahuje dvě místa, kde můžete povolit konektor API:

- Po přihlášení pomocí zprostředkovatele identity
- Před vytvořením uživatele

> [!IMPORTANT]
> V obou těchto případech jsou konektory rozhraní API vyvolány během **registrace** uživatele, nikoli při přihlášení.

### <a name="after-signing-in-with-an-identity-provider"></a>Po přihlášení pomocí zprostředkovatele identity

Konektor API v tomto kroku v procesu registrace se vyvolá hned po ověření uživatele s poskytovatelem identity (jako Google, Facebook, & Azure AD). Tento krok předchází ***stránku kolekce atributů***, což je formulář prezentovaný uživateli ke shromáždění atributů uživatele. Tento krok se nevyvolá, pokud se uživatel registruje pomocí místního účtu. Následují příklady scénářů konektoru rozhraní API, které můžete povolit v tomto kroku:

- Použijte e-mailovou nebo federované identitu, kterou uživatel zadal k vyhledání deklarací v existujícím systému. Vrátí tyto deklarace ze stávajícího systému, předem vyplní stránku kolekce atributů a zpřístupní je k vrácení v tokenu.
- Implementujte seznam povolených nebo blokovaných webů na základě sociální identity.

### <a name="before-creating-the-user"></a>Před vytvořením uživatele

Konektor rozhraní API v tomto kroku v procesu registrace je vyvolán po stránce kolekce atributů, pokud je jeden zahrnutý. Tento krok je vždy vyvolán před vytvořením uživatelského účtu. Níže jsou uvedené příklady scénářů, které můžete v tomto okamžiku povolit během registrace:

- Ověřte vstupní data uživatele a požádejte uživatele, aby data znovu odeslal.
- Zablokuje registraci uživatele na základě dat zadaných uživatelem.
- Proveďte ověření identity.
- Dotaz na externí systémy pro stávající data o uživateli, který se vrátí v tokenu aplikace nebo ho uloží do Azure AD.


## <a name="next-steps"></a>Další kroky
- Naučte se [Přidat konektor API do toku uživatele](add-api-connector.md) .
- Začněte s našimi [ukázkami](code-samples.md#api-connectors).
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->