---
title: Řešení potíží s Azure dat sdílené složky ve verzi Preview
description: Zjistěte, jak řešit problémy s Azure Data sdílené složky ve verzi Preview
services: data-share
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: c02f72d6a327c4dcb94ac8844005613cfe316986
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838381"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>Řešení běžných potíží ve verzi Preview služby Azure Data sdílené složky

Tento článek ukazuje, jak řešení běžných potíží pro Azure Data sdílené složky ve verzi Preview. 

## <a name="azure-data-share-invitations"></a>Pozvánky sdílení dat v Azure 

V některých případech, pokud nový uživatel klikne **přijměte pozvánku** z byla odeslána e-mailové pozvánky, může se vám prázdný seznam pozvánky. 

![Žádné pozvánky](media/no-invites.png)

Výše uvedené chyby se o známý problém ve službě a je aktuálně zabýváme. Jako alternativní řešení, postupujte následujících kroků. 

1. Na webu Azure Portal, přejděte na **předplatná**
1. Vyberte předplatné, které používáte pro sdílenou složku Azure dat
1. Klikněte na **poskytovatelů prostředků**
1. Hledat Microsoft.DataShare
1. Klikněte na tlačítko **zaregistrovat**

Je potřeba mít [role Azure RBAC Přispěvatel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) k dokončení těchto kroků. 

Pokud stále nemůžete zobrazíte data sdílet pozvánku, obraťte se na svého poskytovatele dat a ujistěte se, že odeslali pozvánku k e-mailovou adresu pro přihlášení k Azure a *není* e-mailový alias. 

> [!IMPORTANT]
> Pokud jste už přijmout pozvánku sdílené složky služby Azure Data a služby před konfigurací úložiště byl ukončen, postupujte podle pokynů, najdete v [nakonfigurovat mapování datovou sadu](how-to-configure-mapping.md) postupy příručku a zjistěte, jak dokončit konfiguraci vašeho přijatá data sdílet a začít přijímat data. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Chyba při vytváření nebo přijímání nových sdílení dat

"Chyba: Operace vrátil neplatný stavový kód "chybného požadavku."

"Chyba: AuthorizationFailed"

"Chyba: přiřazení role do účtu úložiště"

![Chyba oprávnění](media/error-write-privilege.png)

Pokud se zobrazí některé z výše uvedených chyb při vytváření nové sdílené složky dat nebo zobrazuje Nová sdílená složka dat, protože jsou k dispozici dostatečná oprávnění k účtu úložiště. Je požadováno oprávnění *Microsoft.Authorization/role přiřazení a zápis*, která existuje v roli vlastník úložiště, nebo je možné přiřadit vlastní roli. I v případě, že jste vytvořili účet úložiště, neprovede automaticky jste vlastníkem účtu úložiště. Použijte následující postup udělit sami vlastníka účtu úložiště. Alternativně lze vytvořit vlastní roli s tímto oprávněním, který můžete přidat sobě a.  

1. Přejděte na účet úložiště na webu Azure portal
1. Vyberte **řízení přístupu (IAM)**
1. Klikněte na tlačítko **přidat**
1. Přidáte vás jako vlastníka.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak začít, sdílení dat, nadále [sdílet vaše data](share-your-data.md) kurzu.

