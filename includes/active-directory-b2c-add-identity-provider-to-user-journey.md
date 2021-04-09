---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98674220"
---
## <a name="add-a-user-journey"></a>Přidat cestu uživatele 

V tuto chvíli se poskytovatel identity nastavil, ale ještě není dostupný na žádném z přihlašovacích stránek. Pokud nemáte vlastní cestu k uživateli, vytvořte duplikát existující cesty uživatele šablony, jinak pokračujte dalším krokem. 

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
1. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
1. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
1. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
1. Přejmenujte ID cesty pro uživatele. Například, `Id="CustomSignUpSignIn"`.

## <a name="add-the-identity-provider-to-a-user-journey"></a>Přidání zprostředkovatele identity k cestě uživatele 

Teď, když máte cestu uživatele, přidejte nového zprostředkovatele identity k cestě uživatele. Nejprve přidáte tlačítko pro přihlášení a pak propojíte tlačítko s akcí. Akce je technický profil, který jste vytvořili dříve.

1. Najděte element kroku orchestrace, který obsahuje `Type="CombinedSignInAndSignUp"` nebo `Type="ClaimsProviderSelection"` v cestě uživatele. Obvykle je to první krok orchestrace. Element **ClaimsProviderSelections** obsahuje seznam zprostředkovatelů identity, pomocí kterých se uživatel může přihlásit. Pořadí prvků řídí pořadí přihlašovacích tlačítek prezentovaných uživateli. Přidejte element XML **claimsproviderselection.** . Nastavte hodnotu **TargetClaimsExchangeId** na popisný název.

1. V dalším kroku orchestrace přidejte element **ClaimsExchange** . Nastavte **ID** na hodnotu cílového ID Exchange deklarací identity. Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve.

Následující kód XML znázorňuje první dva kroky orchestrace na cestě uživatele s poskytovatelem identity: