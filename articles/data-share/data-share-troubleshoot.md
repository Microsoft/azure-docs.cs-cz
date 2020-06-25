---
title: Řešení potíží se službou Azure Data Share
description: Naučte se řešit problémy s požadavky a chybami při vytváření nebo přijímání sdílených dat pomocí Azure Data Share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 510787b395388f0dc895b9646aee8208013f3b42
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85322032"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Řešení běžných problémů v Azure Data Share 

Tento článek popisuje, jak řešit běžné problémy s Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Pozvánky ke sdílení dat Azure 

V některých případech platí, že když nový uživatel klikne na **přijmout pozvánku** z e-mailové pozvánky, která se poslala, může se zobrazit prázdný seznam pozvánek. 

![Žádná pozvání](media/no-invites.png)

K tomu může dojít z následujících důvodů:

* **Služba Azure Data Share není zaregistrovaná jako poskytovatel prostředku žádného předplatného Azure v tenantovi Azure.** K tomuto problému dochází, pokud ve vašem tenantovi Azure není žádný prostředek pro sdílení dat. Když vytvoříte prostředek sdílené složky Azure, zaregistruje se v předplatném Azure automaticky poskytovatele prostředků. Službu sdílení dat můžete také ručně zaregistrovat podle těchto kroků. K provedení těchto kroků budete potřebovat roli Přispěvatel Azure.

    1. V Azure Portal přejděte na **odběry** .
    1. Vyberte předplatné, které chcete použít k vytvoření prostředku Azure Data Share
    1. Kliknout na **poskytovatelé prostředků**
    1. Hledání **Microsoft. Datashare**
    1. Klikněte na **zaregistrovat** . 

    K provedení těchto kroků budete potřebovat [roli RBAC přispěvatele Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) . 

* **Pozvánka se pošle na váš e-mailový alias místo na přihlašovací e-mail Azure.** Pokud jste zaregistrovali službu Azure data Shared nebo už jste vytvořili prostředek pro sdílení dat v tenantovi Azure, ale pořád nevidíte pozvánku, může to být tím, že poskytovatel zadal váš e-mailový alias jako příjemce namísto přihlašovací e-mailové adresy Azure. Obraťte se na poskytovatele dat a zajistěte, aby odeslal pozvánku na vaši e-mailovou adresu Azure, a ne na váš e-mailový alias.

* **Pozvánka již byla přijata.** Odkaz v e-mailu vás přesměruje na stránku pozvánky ke sdílení dat v Azure Portal, která obsahuje jenom seznam nevyřízených pozvánek. Pokud jste již pozvánku přijali, již se nezobrazí na stránce pozvání ke sdílení dat. Přejděte ke zdroji dat, který jste použili k přijetí pozvánky, abyste si zobrazili přijaté sdílené složky a nakonfigurovali cílové nastavení clusteru Azure Průzkumník dat.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Chyba při vytváření nebo přijímání nové sdílené složky

"Nepovedlo se přidat datové sady."

Nepovedlo se namapovat datové sady.

"Nepovedlo se udělit prostředku sdílení dat x přístup k y"

"Nemáte správná oprávnění k x"

"Nemohli jsme přidat oprávnění k zápisu pro účet Azure Data Share k jednomu nebo více vybraným prostředkům"

Pokud při vytváření nové sdílené složky nebo mapování datových sad dojde k některé z výše uvedených chyb, může to být způsobeno nedostatečnými oprávněními pro úložiště dat Azure. Další informace najdete v tématu [role a požadavky](concepts-roles-permissions.md) pro požadovaná oprávnění. 

Ke sdílení nebo příjmu dat z úložiště dat Azure, které obvykle existuje v roli přispěvatel, potřebujete oprávnění k zápisu. 

Pokud se jedná o první sdílení nebo příjem dat z úložiště dat Azure, potřebujete také *Microsoft. Authorization/role nebo* oprávnění k zápisu, které obvykle existují v roli vlastníka. I když jste vytvořili prostředek Azure Data Store, nevytvoří automaticky vlastníka tohoto prostředku. Pomocí správného oprávnění služba Azure Data Share automaticky uděluje přístup spravované identitě prostředku ke sdílení dat do úložiště dat. Tento proces může trvat několik minut, než se projeví. Pokud dojde k selhání kvůli této prodlevě, zkuste to znovu za několik minut.

Sdílení založené na SQL vyžaduje další oprávnění. Podrobnosti najdete v tématu řešení potíží se sdílením na základě SQL.

## <a name="troubleshooting-sql-based-sharing"></a>Řešení potíží se sdílením založeným na SQL

"Uživatel x neexistuje v SQL Database"

Pokud se tato chyba zobrazí při přidávání datové sady ze zdroje založeného na SQL, může to být tím, že jste nevytvořili uživatele pro spravovanou identitu Azure data Shared v SQL Database.  Pokud chcete tento problém vyřešit, spusťte následující skript:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Pokud se tato chyba zobrazí při mapování datové sady k cíli založenému na SQL, může to být tím, že jste nevytvořili uživatele pro spravovanou identitu Azure data Shared na svém SQL Server.  Pokud chcete tento problém vyřešit, spusťte následující skript:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Všimněte si, že *<share_acc_name>* je název vašeho prostředku pro sdílení dat.      

Ujistěte se, že jste postupovali podle všech požadavků uvedených v kurzu [sdílení dat](share-your-data.md) a [přijetí a přijetí dat](subscribe-to-data-share.md) .

## <a name="snapshot-failed"></a>Nepodařilo se vytvořit snímek
Snímek se kvůli nejrůznějším důvodům nezdařil. Podrobnou chybovou zprávu najdete tak, že kliknete na počáteční čas snímku a pak na stav každé datové sady. 

Pokud chybová zpráva souvisí s oprávněním, ověřte, že služba sdílení dat má požadovaná oprávnění. Podrobnosti najdete v tématu [role a požadavky](concepts-roles-permissions.md) . Pokud snímek vyberete poprvé, může trvat několik minut, než se prostředku sdílení dat udělí přístup k úložišti dat Azure. Počkejte pár minut a zkuste to znovu.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) . 

Pokud se chcete dozvědět, jak přijímat data, přejděte k kurzu [přijetí a přijetí dat](subscribe-to-data-share.md) .

