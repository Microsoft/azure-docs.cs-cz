---
title: Řešení potíží se službou Azure Data Share
description: Naučte se řešit problémy s požadavky a chybami při vytváření nebo přijímání sdílených dat pomocí Azure Data Share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 6ad612d56b25da9e092070198e321e7fca8ad96b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490569"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Řešení běžných problémů v Azure Data Share 

Tento článek popisuje, jak řešit běžné problémy s Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Pozvánky ke sdílení dat Azure 

V některých případech platí, že když nový uživatel klikne na **přijmout pozvánku** z e-mailové pozvánky, která se poslala, může se zobrazit prázdný seznam pozvánek. 

![Žádná pozvání](media/no-invites.png)

Výše uvedená chyba je známý problém se službou a v tuto chvíli se řeší. Alternativním řešením je postupovat podle následujících kroků. 

1. V Azure Portal přejděte na **odběry** .
1. Vyberte předplatné, které používáte pro sdílení dat Azure.
1. Kliknout na **poskytovatelé prostředků**
1. Hledání Microsoft. datashare
1. Klikněte na **zaregistrovat** .

K provedení těchto kroků budete potřebovat [roli RBAC přispěvatele Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) . 

Pokud stále nemůžete zobrazit pozvánku ke sdílení dat, kontaktujte poskytovatele dat a zajistěte, aby odeslali pozvánku na vaši e-mailovou adresu Azure, a *ne* váš e-mailový alias. 

> [!IMPORTANT]
> Pokud jste už přijali pozvánku ke sdílené složce Azure a před konfigurací úložiště jste službu ukončili, postupujte podle pokynů uvedených v průvodci [Konfigurovat datovou sadu](how-to-configure-mapping.md) s návodem, kde se dozvíte, jak dokončit konfiguraci přijatých sdílených dat. a začněte přijímat data. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Chyba při vytváření nebo přijímání nové sdílené složky dat

"Chyba: operace vrátila neplatný stavový kód důvodu chybného požadavku."

"Chyba: AuthorizationFailed"

"Chyba: přiřazení role k účtu úložiště"

![Chyba oprávnění](media/error-write-privilege.png)

Pokud se při vytváření nebo přijímání nové sdílené datové složky zobrazí některá z výše uvedených chyb, je to z důvodu nedostatečných oprávnění k účtu úložiště. Požadovaná oprávnění jsou *Microsoft. Authorization/přiřazení role/zápis*, která existuje v roli vlastníka úložiště nebo je možné ji přiřadit vlastní roli. I když vytvoříte účet úložiště, automaticky to z vás NEDĚLÁ vlastníka účtu úložiště. Pokud si chcete udělit oprávnění vlastníka účtu úložiště, postupujte podle těchto kroků. Alternativně lze vytvořit vlastní roli s tímto oprávněním, které můžete přidat do nástroje do aplikace.  

1. Na webu Azure Portal přejděte k účtu úložiště.
1. Výběr **řízení přístupu (IAM)**
1. Klikněte na **Přidat** .
1. Přidejte sami sebe jako vlastníka.

## <a name="troubleshooting-sql-based-sharing"></a>Řešení potíží s sdílením na základě SQL

"Chyba: datové sady x nebyly přidány, protože nemáte požadovaná oprávnění ke sdílení."

Pokud se tato chyba zobrazí při přidávání datové sady ze zdroje založeného na SQL, může to být tím, že jste nevytvořili uživatele pro soubor MSI Azure data Shared na vašem SQL Server.  Pokud chcete tento problém vyřešit, spusťte následující skript:

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Všimněte si, že *< share_acc_name >* je název vašeho účtu pro sdílení dat. Pokud jste ještě nevytvořili účet pro sdílení dat, můžete se k tomuto předběžnému požadavku vrátit později.         

Ujistěte se, že jste postupovali podle všech požadavků uvedených v kurzu [sdílení vašich dat](share-your-data.md) .

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .

