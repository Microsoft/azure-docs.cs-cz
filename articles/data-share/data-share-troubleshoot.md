---
title: Řešení potíží se službou Azure Data Share
description: Zjistěte, jak řešit problémy s pozvánkami a chybami při vytváření nebo přijímání datových sdílených složek pomocí Služby Azure Data Share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964222"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Řešení běžných problémů ve službě Azure Data Share 

Tento článek ukazuje, jak řešit běžné problémy pro Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Pozvánky na sdílení dat Azure 

V některých případech, když nový uživatel klepne na tlačítko **Přijmout pozvánku** z e-mailové pozvánky, která byla odeslána, může být mu předložen prázdný seznam pozvánek. 

![Žádné pozvánky](media/no-invites.png)

To může být způsobeno následujícími důvody:

* **Služba Azure Data Share není registrovaná jako poskytovatel prostředků žádného předplatného Azure v tenantovi Azure.** Tento problém se zobrazí, pokud ve vašem tenantovi Azure není žádný prostředek sdílení dat. Když vytvoříte prostředek Azure Data Share, automaticky zaregistruje poskytovatele prostředků ve vašem předplatném Azure. Službu Sdílení dat můžete také ručně zaregistrovat následujícím postupem. K dokončení těchto kroků budete muset mít roli přispěvatele Azure.

    1. Na webu Azure Portal přejděte na **Předplatná**
    1. Vyberte předplatné, které chcete použít k vytvoření prostředku Azure Data Share.
    1. Klikněte na **Zprostředkovatelé prostředků**
    1. Hledání **microsoft.DataShare**
    1. Klikněte na **Registrovat.** 

    K dokončení těchto kroků budete muset mít [roli RBAC přispěvatele Azure.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 

* **Pozvánka se odesílá na váš e-mailový alias místo přihlašovacího e-mailu Azure.** Pokud jste zaregistrovali službu Azure Data Share nebo už jste vytvořili prostředek sdílení dat v tenantovi Azure, ale pořád nemůžete vidět pozvánku, možná proto, že poskytovatel zadal váš e-mailový alias jako příjemce místo vaší přihlašovací e-mailové adresy Azure. Obraťte se na svého poskytovatele dat a ujistěte se, že odeslal pozvánku na vaši přihlašovací e-mailovou adresu Azure a ne na váš e-mailový alias.

* **Pozvánka již byla přijata.** Odkaz v e-mailu vás přenese na stránku Pozvánka ke sdílení dat na webu Azure Portal, kde jsou uvedeny pouze nevyřízené pozvánky. Pokud jste pozvánku již přijali, nebude se již zobrazovat na stránce Pozvánka ke sdílení dat. Přejděte k prostředku sdílení dat, který jste použili k přijetí pozvánky k zobrazení přijatých sdílených složek a konfiguraci cílového nastavení clusteru Azure Data Explorer.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Chyba při vytváření nebo přijímání nové sdílené složky

"Přidání datových sad se nezdařilo"

"Mapování datových sad se nezdařilo"

"Nelze udělit prostředek sdílení dat x přístup k y"

"Nemáte správná oprávnění ke x"

"Nepodařilo se nám přidat oprávnění k zápisu pro účet Azure Data Share do jednoho nebo více vybraných prostředků"

Pokud se při vytváření nové sdílené složky nebo mapování datových sad zobrazí některá z výše uvedených chyb, může to být způsobeno nedostatečnými oprávněními k úložišti dat Azure. Viz [Role a požadavky](concepts-roles-permissions.md) pro požadovaná oprávnění. 

Potřebujete oprávnění k zápisu ke sdílení nebo přijímání dat z úložiště dat Azure, které obvykle existuje v roli přispěvatele. 

Pokud je to poprvé, kdy sdílíte nebo přijímáte data z úložiště dat Azure, potřebujete také *oprávnění Microsoft.Authorization/role assignments/write,* které obvykle existuje v roli Vlastník. I v případě, že jste vytvořili prostředek úložiště dat Azure, není automaticky vás vlastník prostředku. S řádným oprávněním služba Azure Data Share automaticky uděluje spravované identitě prostředku sdílení dat přístup k úložišti dat. Tento proces může trvat několik minut, než se projeví. Pokud dojde k selhání z důvodu tohoto zpoždění, zkuste to znovu za několik minut.

Sdílení založené na SQL vyžaduje další oprávnění. Podrobnosti najdete v tématu Poradce při potížích se sdílením na základě SQL.

## <a name="troubleshooting-sql-based-sharing"></a>Řešení potíží se sdílením založeným na SQL

"Uživatel x neexistuje v databázi SQL"

Pokud se tato chyba zobrazí při přidávání datové sady ze zdroje založeného na SQL, může to být proto, že jste nevytvořili uživatele pro spravovanou identitu Azure Data Share na serveru SQL Server.  Chcete-li tento problém vyřešit, spusťte následující skript:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Pokud se tato chyba zobrazí při mapování datové sady na cíl založený na SQL, může to být proto, že jste nevytvořili uživatele pro spravovanou identitu Azure Data Share na serveru SQL Server.  Chcete-li tento problém vyřešit, spusťte následující skript:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Všimněte si, že *<share_acc_name>* je název prostředku sdílení dat.      

Ujistěte se, že jste dodrželi všechny požadavky uvedené v [sekci Sdílení dat](share-your-data.md) a přijetí a příjem kurzu pro [data.](subscribe-to-data-share.md)

## <a name="snapshot-failed"></a>Snímek se nezdařil.
Snímek může selhat z různých důvodů. Podrobnou chybovou zprávu najdete kliknutím na čas zahájení snímku a potom na stav každé datové sady. 

Pokud se chybová zpráva týká oprávnění, ověřte, zda má služba Sdílení dat požadovaná oprávnění. Podrobnosti najdete [v tématu Role a požadavky.](concepts-roles-permissions.md) Pokud je to poprvé, kdy pořizovat snímek, může trvat několik minut pro prostředek sdílení dat, které mají být udělen přístup k úložišti dat Azure. Počkejte několik minut a akci opakujte.

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak začít sdílet data, [pokračujte ve sdílení dat](share-your-data.md) kurzu. 

Chcete-li se dozvědět, jak přijímat data, pokračujte v kurzu [přijmout a přijímat data.](subscribe-to-data-share.md)

