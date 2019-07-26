---
title: Řešení potíží s Azure Data Share Preview
description: Přečtěte si, jak řešit problémy s Azure Data Share Preview
services: data-share
author: joannapea
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 386a5e34dccafa61859cd13c3e0ad88cd3a7ffac
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421461"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>Řešení běžných problémů v Azure Data Share Preview

Tento článek popisuje, jak řešit běžné problémy pro Azure Data Share Preview. 

## <a name="azure-data-share-invitations"></a>Pozvánky ke sdílení dat Azure 

V některých případech platí, že když nový uživatel klikne na **přijmout pozvánku** z e-mailové pozvánky, která se poslala, může se zobrazit prázdný seznam pozvánek. 

![Žádná pozvání](media/no-invites.png)

Výše uvedená chyba je známý problém se službou a v tuto chvíli se řeší. Alternativním řešením je postupovat podle následujících kroků. 

1. V Azure Portal přejděte na odběry  .
1. Vyberte předplatné, které používáte pro sdílení dat Azure.
1. Kliknout na **poskytovatelé prostředků**
1. Hledání Microsoft. datashare
1. Klikněte na **zaregistrovat** .

K provedení těchto kroků budete potřebovat [roli RBAC přispěvatele Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) . 

Pokud stále nemůžete zobrazit pozvánku ke sdílení dat, kontaktujte poskytovatele dat a zajistěte, aby odeslali pozvánku na vaši e-mailovou adresu Azure, a *ne* váš e-mailový alias. 

> [!IMPORTANT]
> Pokud jste už přijali pozvánku ke sdílené složce Azure a před konfigurací úložiště jste službu ukončili, postupujte podle pokynů uvedených v průvodci [Konfigurovat datovou sadu](how-to-configure-mapping.md) s návodem, kde se dozvíte, jak dokončit konfiguraci přijatých sdílených dat. a začněte přijímat data. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Chyba při vytváření nebo přijímání nové sdílené složky dat

Chyba Operace vrátila neplatný stavový kód důvodu chybného požadavku.

Chyba AuthorizationFailed"

"Chyba: přiřazení role k účtu úložiště"

![Chyba oprávnění](media/error-write-privilege.png)

Pokud se při vytváření nebo přijímání nové sdílené datové složky zobrazí některá z výše uvedených chyb, je to z důvodu nedostatečných oprávnění k účtu úložiště. Požadovaná oprávnění jsou *Microsoft. Authorization/přiřazení role/zápis*, která existuje v roli vlastníka úložiště nebo je možné ji přiřadit vlastní roli. I když vytvoříte účet úložiště, automaticky to z vás NEDĚLÁ vlastníka účtu úložiště. Pokud si chcete udělit oprávnění vlastníka účtu úložiště, postupujte podle těchto kroků. Alternativně lze vytvořit vlastní roli s tímto oprávněním, které můžete přidat do nástroje do aplikace.  

1. Na webu Azure Portal přejděte k účtu úložiště.
1. Výběr **řízení přístupu (IAM)**
1. Klikněte na **Přidat** .
1. Přidejte sami sebe jako vlastníka.

## <a name="next-steps"></a>Další postup

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .

