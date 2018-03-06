---
title: "Konektor obecné SQL | Microsoft Docs"
description: "Tento článek popisuje postup při konfiguraci konektoru SQL obecné společnosti Microsoft."
services: active-directory
documentationcenter: 
author: billmath
manager: bhu
editor: 
ms.assetid: fd8ccef3-6605-47ba-9219-e0c74ffc0ec9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: billmath
ms.openlocfilehash: 66e3559c244a76101be7b7d944a48cd6dd99bd4c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2018
---
# <a name="generic-sql-connector-technical-reference"></a>Technické informace o obecné konektor SQL
Tento článek popisuje obecný konektor SQL. Se článek vztahuje následující produkty:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manageru 2010 R2 (FIM2010R2)
  * Musíte použít opravu hotfix 4.1.3671.0 nebo novější [KB3092178](https://support.microsoft.com/kb/3092178).

Pro MIM2016 a FIM2010R2, je k dispozici ke stažení z konektoru [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

Tento konektor v praxi, najdete v sekci [obecné konektor SQL podrobné](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) článku.

## <a name="overview-of-the-generic-sql-connector"></a>Přehled konektoru obecné SQL
Obecné konektor SQL umožňuje integrovat databázový systém, který nabízí připojení ODBC synchronizační služby.  

Z hlediska vysoké úrovně služba aktuální verzi konektor podporuje následující funkce:

| Funkce | Podpora |
| --- | --- |
| Připojeného zdroje dat |Konektor podporuje všechny ovladače ODBC 64-bit. Byl testován s následujícími službami: <li>Microsoft SQL Server & SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 a 11 g</li><li>MySQL 5.x</li> |
| Scénáře |<li>Správa životního cyklu objektu</li><li>Správa hesel</li> |
| Operace |<li>Úplný Import a rozdílový Import, Export</li><li>Pro Export: Přidání, aktualizaci, odstranit a nahradit</li><li>Nastavení hesla, změnit heslo</li> |
| Schéma |<li>Dynamické zjišťování objektů a atributů</li> |

### <a name="prerequisites"></a>Požadavky
Než použijete tento konektor, ujistěte se, že máte k dispozici následující na serveru synchronizace:

* 4.5.2 rozhraní Microsoft .NET Framework nebo novější
* 64bitová verze ovladače klienta rozhraní ODBC

### <a name="permissions-in-connected-data-source"></a>Oprávnění v připojeného zdroje dat
Vytvořit nebo proveďte jednu z podporovaných úlohy v konektoru obecné SQL, musíte mít:

* db_datareader
* db_datawriter

### <a name="ports-and-protocols"></a>Porty a protokoly
Porty požadované ovladače ODBC pro práci v dokumentaci od dodavatele databáze.

## <a name="create-a-new-connector"></a>Vytvořit nový konektor
Chcete-li vytvořit konektor obecné SQL, v **synchronizační služba** vyberte **agenta pro správu** a **vytvořit**. Vyberte **obecné SQL (Microsoft)** konektor.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Připojení
Konektor používá soubor název DSN rozhraní ODBC pro připojení k síti. Vytvoření souborového DSN pomocí **zdroje dat ODBC** najít v nabídce start pod **nástroje pro správu**. Nástroje pro správu, vytvořit **souborové DSN** tak můžete zajistit ke konektoru.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

Na obrazovce připojení je první, když vytvoříte nový konektor obecné SQL. Musíte nejprve zadejte následující informace:

* Cesta k souboru DSN
* Authentication
  * Uživatelské jméno
  * Heslo

Databáze by měly podporovat jednu z těchto metod ověřování:

* **Ověřování systému Windows**: ověřování databáze používá přihlašovací údaje Windows k ověření uživatele. Uživatelské jméno a heslo zadané se používá k ověření s databází. Tento účet potřebuje oprávnění k databázi.
* **Ověřování SQL**: ověřování databáze používá jeden uživatelské jméno a heslo definované na obrazovce připojení pro připojení k databázi. Pokud ukládáte uživatelské jméno nebo heslo v souboru názvu DSN, přihlašovací údaje na obrazovce připojení mají přednost před.
* **Azure SQL Database ověřování**: Další informace najdete v tématu [připojit k databázi pomocí pomocí Azure Active Directory ověřování SQL](../../sql-database/sql-database-aad-authentication.md).

**Rozlišující název je kotva**: Pokud vyberete tuto možnost, název domény se používá jako atribut ukotvení. Lze použít pro jednoduché implementace, ale také má následující omezení:

* Konektor podporuje pouze jeden objekt typu. Proto všechny atributy typu odkaz může odkazovat pouze na stejný typ objektu.

**Typ exportu: Objekt nahraďte**: během exportu, když jenom některých atributů změnily, celý objekt s všechny atributy se exportují a nahradí existující objekt.

### <a name="schema-1-detect-object-types"></a>Schéma 1 (zjistit typy objektů)
Na této stránce se chystáte nakonfigurovat, jak se bude tento konektor najít různé typy objektů v databázi.

Každý typ objektu je uvedené jako oddíl a nakonfigurovat další na **konfigurace oddílů a hierarchií**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Objekt metody detekce typu**: konektor služby podporuje tyto metody detekce typu objektu.

* **Pevná hodnota**: Zadejte seznam typů objektů se seznam oddělený čárkami. Například: `User,Group,Department`.  
  ![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
* **Tabulka/zobrazení nebo uložené procedury**: Zadejte název tabulky, zobrazení nebo uložené procedury a potom název sloupce, který poskytuje seznam typů objektů. Pokud používáte uložené procedury, zadejte také parametry pro něj ve formátu **[Name]: [směr]: [hodnota]**. Zadejte každý parametr na samostatném řádku (pomocí kláves Ctrl + Enter získat nový řádek).  
  ![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
* **Dotaz SQL**: Tato možnost umožňuje zadat dotaz SQL, který vrátí jeden sloupec s typy objektů, například `SELECT [Column Name] FROM TABLENAME`. Vrácený sloupec musí být typu řetězec (varchar).

### <a name="schema-2-detect-attribute-types"></a>Schéma 2 (rozpoznat atribut typy)
Na této stránce se chystáte nakonfigurovat, jak se bude atribut názvy a typy za detekovanou. Možnosti konfigurace jsou uvedené pro každý typ objektu, který je zjištěn na předchozí stránce.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Atribut metody detekce typu**: konektor služby podporuje tyto metody detekce typu atributu každý zjištěný objekt typu ve schématu 1 obrazovky.

* **Tabulka/zobrazení nebo uložené procedury**: Zadejte název tabulky, zobrazení nebo uloženou proceduru, který se má použít pro vyhledání názvů atribut. Pokud používáte uložené procedury, zadejte také parametry pro něj ve formátu **[Name]: [směr]: [hodnota]**. Zadejte každý parametr na samostatném řádku (pomocí kláves Ctrl + Enter získat nový řádek). Ke zjišťování názvy atributů s více hodnotami atributu, poskytovat seznam oddělený čárkami tabulek nebo zobrazení. Scénáře s více hodnotami nejsou podporované při nadřazenou a podřízenou tabulku mají stejné názvy sloupců.
* **Dotaz SQL**: Tato možnost umožňuje zadat dotaz SQL, který vrátí jeden sloupec s názvy atributů, například `SELECT [Column Name] FROM TABLENAME`. Vrácený sloupec musí být typu řetězec (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Schéma 3 (definovat ukotvení a rozlišující název)
Tato stránka umožňuje konfigurovat ukotvení a rozlišující název atributu pro každý zjištěný objekt typu. Můžete vybrat několik atributů, aby ukotvení jedinečný.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

* Nejsou uvedené atributy s více hodnotami a logická hodnota.
* Stejný atribut nelze použít pro rozlišující název a anchor, pokud **rozlišující název je kotva** je vybrané na stránce připojení.
* Pokud **rozlišující název je kotva** je vybrána na stránce připojení tato stránka vyžaduje pouze rozlišující název atributu. Tento atribut se taky použije jako atribut ukotvení.

  ![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Schéma 4 (definovat typ atributu, odkaz a směr)
Tato stránka umožňuje konfigurovat typ atributu, jako je například celé číslo, binární, nebo logická hodnota a směr pro každý atribut. Všechny atributy ze stránky **schématu 2** jsou uvedeny včetně více hodnot atributů.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

* **Datový typ**: slouží k mapování na těchto typech známého synchronizační modul typ atributu. Ve výchozím nastavení se pomocí stejného typu jako ve schématu SQL zjištěna, ale data a času a referenční informace nejsou snadno rozpoznat. Pro ty, budete muset zadat **data a času** nebo **odkaz**.
* **Směr**: můžete nastavit atribut směr na Import, Export nebo ImportExport. ImportExport je výchozí.

![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Poznámky:

* Pokud není typ atributu rozpoznat konektorem, používá datový typ String.
* **Vnořené tabulky** lze považovat za jeden sloupec databázových tabulek. Oracle ukládá řádků vnořené tabulky seřazeny. Ale pokud načítáte vnořené tabulky do proměnné PL/SQL, řádky jsou uvedeny po sobě jdoucích dolní indexy začínajícím hodnotou 1. Který poskytuje přístup jako pole na jednotlivé řádky.
* **VARRYS** nejsou podporovány v konektoru.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Schéma 5 (Definujte oddíly pro atributy typu odkaz)
Na této stránce můžete nakonfigurovat pro oddíl, který (typ objektu) atribut odkazuje na všechny atributy typu odkaz.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Pokud používáte **rozlišující název je kotva**, pak musíte použít stejný typ objektu jako ta, se odkazuje z. Jiný typ objektu nelze odkazovat.

>[!NOTE]
Počínaje března 2017 aktualizace je nyní k dispozici možnost pro "*" Pokud tato možnost je zvolená pak všechny typy možné člen bude importována.

![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/any-option.png)

>[!IMPORTANT]
 Od verze 2017 může "*" neboli **jakákoliv možnost** byl změněn na podporu import a export toku. Pokud chcete použít tuto možnost měli více hodnot tabulky či zobrazení atribut, který obsahuje typ objektu.

![](./media/active-directory-aadconnectsync-connector-genericsql/any-02.png)

 </br> Pokud "*" je vybraná, pak musí být zadaná také název sloupce s typem objektu.</br> ![](./media/active-directory-aadconnectsync-connector-genericsql/any-03.png)

Po importu zobrazí se něco podobného jako na následujícím obrázku:

  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/after-import.png)



### <a name="global-parameters"></a>Globální parametry
Globální parametry stránka slouží ke konfiguraci rozdílový Import, formát data a času a metoda heslo.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)



Obecné SQL konektor podporuje následující metody pro rozdílový Import:

* **Aktivační událost**: najdete v části [generování zobrazení rozdílů pomocí aktivační události](https://technet.microsoft.com/library/cc708665.aspx).
* **Vodoznak**: Obecný přístup, který lze použít s libovolnou databázi. Dotaz vodoznak je předem vyplněná podle dodavatele databáze. Sloupec vodoznaku musí být na každé tabulky či zobrazení použít. Tento sloupec musí sledovat vložení a aktualizace tabulky jako a jeho závislých (s více hodnotami nebo podřízené) tabulky. Hodiny mezi synchronizační službou a databázový server musí být synchronizovány. Pokud ne, může být některé položky v Rozdílový import vynechán.  
  Omezení:
  * Strategie vodoznak nemá podporu odstranit objekty.
* **Snímek**: (funguje pouze v systému Microsoft SQL Server) [generování zobrazení rozdílů pomocí snímků](https://technet.microsoft.com/library/cc720640.aspx)
* **Sledování změn**: (funguje pouze v systému Microsoft SQL Server) [o sledování změn](https://msdn.microsoft.com/library/bb933875.aspx)  
  Omezení:
  * Ukotvení & rozlišující název atributu musí být součástí primární klíč pro vybraný objekt v tabulce.
  * Během importu a exportu s sledování změn není podporováno dotazu SQL.

**Další parametry**: určete časové pásmo serveru databáze, která určuje, kde se nachází databáze serveru. Tato hodnota se používá pro podporu různých formátech atributů datum a čas.

Konektor vždy ukládá datum a datum a čas ve formátu UTC. Abyste mohli správně převést datum a čas, na časové pásmo databázového serveru a formát použitý je třeba zadat. Formát by měla být vyjádřena v rozhraní .net formátu.

Každý atribut datum čas je třeba zadat při exportu do konektoru ve formátu času UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Konfigurace hesla**: konektor poskytuje funkce Synchronizace hesla a podporuje nastavit a změnit heslo.

Konektor nabízí dvě metody pro podporu synchronizace hesel:

* **Uložené procedury**: Tato metoda vyžaduje dva uložené procedury pro podporu změnu & Nastavit heslo. Zadejte všechny parametry pro přidat a změňte operace hesla v **nastavit heslo SP** a **změnit heslo SP** parametry v uvedeném pořadí jako v následujícím příkladu.
  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
* **Heslo rozšíření**: Tato metoda vyžaduje heslo rozšíření knihovny DLL (je třeba zadat název knihovny DLL rozšíření, který implementuje [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) rozhraní). Sestavení rozšíření heslo musí být umístěny ve složce rozšíření tak, aby konektor můžete načíst knihovnu DLL za běhu.
  ![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Máte také povolit správu hesla na **nakonfigurovat rozšíření** stránky.
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Konfigurace oddílů a hierarchií
Na stránce oddílů a hierarchií vyberte všechny typy objektů. Pro každý typ objektu je vlastní oddíl.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Můžete také přepsat hodnot fronty definovaných na **připojení** nebo **globální parametry** stránky.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Konfigurace ukotvení
Tato stránka je jen pro čtení, protože již byl definován s ukotvením. Atribut vybrané ukotvení je připojen vždy s typem objektu zajistit, že zůstane jedinečný mezi typy objektů.

![ukotvení](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Konfigurace parametrů kroku spuštění
Tyto kroky jsou nakonfigurované na profilů spuštění pro konektor. Tyto konfigurace vykonávají samotnou práci import a export dat.

### <a name="full-and-delta-import"></a>Úplné a rozdílový Import
Obecné konektor SQL podporu úplné a rozdílový Import pomocí těchto metod:

* Table
* Zobrazení
* Uložená procedura
* Příkaz jazyka SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabulka/zobrazení**  
K importu více hodnot atributů pro objekt, je nutné zadat název tabulky či zobrazení v **název vícehodnotových tabulky nebo zobrazení** a podmínky pro příslušné připojení v **podmínka spojení** s nadřazenou tabulkou . Pokud existuje více než jedné více hodnot tabulky ve zdroji dat, můžete použít sjednocení pro jedno zobrazení.

>[!IMPORTANT]
Agent pro správu obecné SQL můžete pracovat pouze s více hodnotami jedna tabulka. Nevkládejte do název více hodnot tabulky nebo zobrazení více než jeden název tabulky. Je omezení obecné SQL.


Příklad: Chcete importovat objekt zaměstnanců a všech jeho více hodnot atributů. Existují dvě tabulky, s názvem zaměstnanec (hlavní tabulka) a oddělení (více hodnot).
Udělejte toto:

* Typ **zaměstnanec** v **tabulky nebo zobrazení/SP**.
* Typ oddělení v **název vícehodnotových tabulky nebo zobrazení**.
* Zadejte podmínku připojení mezi zaměstnanci & oddělení v **podmínku připojení**, například `Employee.DEPTID=Department.DepartmentID`.
  ![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Uložené procedury**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

* Pokud máte množství dat, se doporučuje implementovat stránkování se vaše uložené procedury.
* Pro vaše uloženou proceduru pro podporu stránkování potřebujete poskytovat Start a End indexem. Přejděte na téma: [efektivně stránkování prostřednictvím velké objemy dat](https://msdn.microsoft.com/library/bb445504.aspx).
* @StartIndex a @EndIndex v době provedení nahradí se hodnota velikosti stránky příslušné nakonfigurované na **krok konfigurace** stránky. Například když konektor načte první stránka a velikost stránky nastavená 500, v takové situaci @StartIndex by bylo 1 a @EndIndex 500. Tyto hodnoty zvýšit, když konektor načte následující stránky a změňte @StartIndex & @EndIndex hodnotu.
* Chcete-li provést parametrizované uloženou proceduru, zadat parametry v `[Name]:[Direction]:[Value]` formátu. Zadejte každý parametr na samostatném řádku (pomocí kláves Ctrl + Enter získat nový řádek).
* Obecné SQL konektor rovněž podporuje operace importu z odkazované servery v systému Microsoft SQL Server. Pokud mají být načtena informace z tabulky propojené serveru, by měl tabulky zadané ve formátu: `[ServerName].[Database].[Schema].[TableName]`
* Obecné SQL konektor podporuje pouze ty objekty, které mají podobnou strukturou (alias název i datový typ) mezi, která spustit kroky zjišťování informace a schéma. Pokud vybraný objekt ze schématu a zadaných informací v kroku spuštění se liší, je konektor služby SQL nelze pro tento typ scénáře podporován.

**Dotaz SQL**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

* Sady více výsledků dotazů není podporována.
* Dotaz SQL podporuje číslování stránek a zadejte počáteční a koncové indexem jako proměnnou pro podporu stránkování.

### <a name="delta-import"></a>Delta Import
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Rozdílový Import konfigurace vyžaduje určitou další konfiguraci ve srovnání s úplný Import.

* Pokud si zvolíte aktivační události nebo snímek přístup ke sledování rozdílové změny, zadejte Tabulka historie nebo snímek databáze v **název tabulky historie nebo snímek databáze** pole.
* Musíte také zadat podmínku připojení mezi Tabulka historie a nadřazené tabulky, například `Employee.ID=History.EmployeeID`
* Sledovat transakce pro nadřazenou tabulku z tabulky historie, je nutné zadat název sloupce, který obsahuje informace o operaci (přidat, aktualizace nebo odstranění).
* Pokud si zvolíte vodoznak sledovat rozdílové změny, zadejte název sloupce, který obsahuje informace o operaci v **název sloupce označit horních**.
* **Změnit atribut Type** je vyžadován pro daný typ změny sloupec. V tomto sloupci mapuje změnu, ke kterému dochází v primární tabulce nebo tabulky s více hodnotami typu změny v zobrazení rozdílu. Tento sloupec může obsahovat daný typ změny Modify_Attribute pro úroveň atributu změnit nebo přidat, upravit, nebo odstraňte změnit typ pro typ změny na úrovni objektů. Pokud je jiný než výchozí hodnota přidat, upravit, nebo odstraňte a potom můžete definovat tyto hodnoty pomocí této možnosti.

### <a name="export"></a>Export
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Obecné konektor SQL podporují Export pomocí čtyř podporované metody, jako třeba:

* Table
* Zobrazení
* Uložená procedura
* Příkaz jazyka SQL

**Tabulka/zobrazení**  
Pokud zvolíte možnost tabulky či zobrazení, konektor generuje příslušných dotazů udělat Export.

**Uložené procedury**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Pokud zvolíte možnost uloženou proceduru, Export vyžaduje tři různé uložené procedury k provádění operací vložení, aktualizaci nebo odstranění.

* **Přidejte název SP**: Tento SP spustí v případě, že jakýkoli objekt je teď dostupná i konektor pro vložení v příslušné tabulce.
* **Aktualizujte název SP**: Tento SP spustí v případě, že jakýkoli objekt je teď dostupná i konektor pro aktualizaci v příslušné tabulce.
* **Odstraňte název SP**: Tento SP spustí v případě, že jakýkoli objekt je teď dostupná i konektor pro odstranění v příslušné tabulce.
* Atribut vybrané ze schématu použít jako hodnotu parametru uložené procedury. Například `@EmployeeName: INPUT: EmployeeName` (EmployeeName je vybrána ve schématu connector a konektor nahradí odpovídající hodnota přitom export)
* Chcete-li spustit parametrizované uložené procedury, zadat parametry v `[Name]:[Direction]:[Value]` formátu. Zadejte každý parametr na samostatném řádku (pomocí kláves Ctrl + Enter získat nový řádek).

**Dotaz SQL**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Pokud zvolíte možnost dotazu SQL, vyžaduje Export tři různé dotazy k provádění operací vložení, aktualizaci nebo odstranění.

* **Vložte dotaz**: spuštění tohoto dotazu, pokud libovolného objektu je teď dostupná i konektor pro vložení v příslušné tabulce.
* **Aktualizace dotazu**: spuštění tohoto dotazu, pokud libovolného objektu je teď dostupná i konektor pro aktualizaci v příslušné tabulce.
* **Dotaz odstranit**: spuštění tohoto dotazu, pokud libovolného objektu je teď dostupná i konektor pro odstranění v příslušné tabulce.
* Atribut vybrané ze schématu použít jako hodnotu parametru do dotazu, například `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Řešení potíží
* Informace o tom, jak povolit protokolování pro řešení potíží s konektoru najdete v tématu [postup povolení trasování ETW pro konektory](http://go.microsoft.com/fwlink/?LinkId=335731).
