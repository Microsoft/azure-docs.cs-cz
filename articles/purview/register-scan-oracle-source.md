---
title: Registrace zdrojových a instalačních kontrol Oracle (Preview) ve službě Azure dosah
description: Tento článek popisuje, jak zaregistrovat zdroj Oracle ve službě Azure dosah a nastavit kontrolu.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 76aadd667691e12c61e0e5e13c13ca0241a9f0ce
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045497"
---
# <a name="register-and-scan-oracle-source-preview"></a>Registrace a kontrola zdrojového kódu Oracle (Preview)

Tento článek popisuje, jak zaregistrovat databázi Oracle v dosah a nastavit kontrolu.

## <a name="supported-capabilities"></a>Podporované funkce

Zdroj Oracle podporuje **úplnou kontrolu** pro extrakci metadat z databáze Oracle a načítá mezi datovými assety **čáru** .

## <a name="prerequisites"></a>Požadavky

1.  Nastavte si nejnovější [prostředí Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717)na místním hostiteli.
    Další informace najdete v tématu [Vytvoření a konfigurace prostředí Integration runtime](../data-factory/create-self-hosted-integration-runtime.md)v místním prostředí.

2.  Ujistěte se, že je na virtuálním počítači, na kterém je nainstalovaný modul runtime integrace v místním prostředí, nainstalovaný [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) .

3.  Ujistěte \" \" se, že je na počítači prostředí Integration runtime v místním prostředí nainstalovaná Visual C++ Redistributable 2012 Update 4. Pokud \' ho ještě nemáte nainstalovanou, Stáhněte si ho odsud. [](https://www.microsoft.com/download/details.aspx?id=30679)

4.  Z [tohoto místa](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) budete muset ručně stáhnout ovladač Oracle JDBC z virtuálního počítače, ve kterém běží místní prostředí Integration runtime.

    > [!Note] 
    > Ovladač by měl být přístupný pro všechny účty na virtuálním počítači. Neinstalujte ji do uživatelského účtu.

5.  Podporované verze databáze Oracle jsou 6i na 19c.

6.  Oprávnění uživatele: aby se zajistilo úspěšné prohledávání poprvé, vyžaduje se plné oprávnění typu sys admin.

    Pro následné kontroly je vyžadováno oprávnění jen pro čtení k systémovým tabulkám. Uživatel by měl mít oprávnění k vytvoření relace a také přiřazení role katalog vybrat \_ roli \_ . Případně může uživatel mít udělené oprávnění vybrat pro každou systémovou tabulku, ze které tento konektor dotazuje metadata:
       > udělit uživateli relaci Create \[ \] ; \
        udělit uživateli možnost vybrat na všech uživatelích \_ \[ \] ; \
        udělit uživateli možnost vybrat u \_ objektu \[ DBA \] ; \
        udělit uživateli možnost vybrat na \_ kartě DBA \_ \[ \] ; \
        Udělte uživateli možnost vybrat \_ externí \_ umístění v \[ DBA \] . \
        udělit uživateli možnost vybrat v \_ adresářích DBA \[ \] ; \
        udělení výběru mviews DBA \_ \[ uživateli \] ; \
        Udělte uživateli možnost vybrat \_ clu \_ sloupce DBA \[ \] . \
        Udělte uživateli možnost vybrat \_ na \_ sloupcích karty \[ DBA \] . \
        udělit uživateli možnost vybrat \_ na \_ komentáři sloupce DBA \[ \] ; \
        udělit \_ uživateli omezení SELECT v DBA \[ \] ; \
        udělit uživateli vybrané sloupce pro nevýhody pro správce databáze \_ \_ \[ \] . \
        udělení výběru na \_ indexy DBA \[ uživateli \] ; \
        udělit uživateli možnost vybrat v DBA navýšení \_ \_ sloupců \[ \] ; \
        udělit pro uživatele metody Select na základě DBA \_ \[ \] ; \
        udělení výběru na \_ synonyma DBA \[ uživateli \] ; \
        udělení výběru pro \_ uživatele v zobrazeních DBA \[ \] ; \
        udělit uživateli možnost vybrat ve \_ zdroji \[ DBA \] ; \
        Udělte \_ aktivační události pro výběr v DBA \[ uživateli \] ; \
        udělení pro uživatele argumenty pro výběr v DBA \_ \[ \] ; \
        udělení výběru pro \_ sekvence v DBA \[ uživateli \] ; \
        Udělte uživateli možnost vybrat \_ závislosti v \[ DBA \] . \
        udělit uživateli možnost vybrat v \_ \$ instanci \[ v \]
        udělit uživateli možnost vybrat v \_ \$ databázi \[ v \]
    
## <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Jediným podporovaným ověřováním pro zdroj Oracle je **základní ověřování**.

## <a name="register-an-oracle-source"></a>Registrace zdroje Oracle

Pokud chcete zaregistrovat nový zdroj Oracle v katalogu dat, udělejte toto:

1.  Přejděte k účtu dosah.
2.  V levém navigačním panelu vyberte **zdroje** .
3.  Vybrat **registraci**
4.  V nabídce registrovat zdroje vyberte **Oracle**. Vyberte **Pokračovat**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="registrovat zdroje" border="true":::

Na obrazovce **Registrovat zdroje (Oracle)** udělejte toto:

1.  Zadejte **název** , který bude zdroj dat uveden v katalogu.

2.  Zadejte název **hostitele** , který se připojí ke zdroji Oracle. Tato možnost může být:
    - Název hostitele, který JDBC používá pro připojení k databázovému serveru. Například MyDatabaseServer.com nebo
    - IP adresa. Například 192.169.1.2 nebo
    - Jeho plně kvalifikovaný připojovací řetězec JDBC. Například \
        JDBC: Oracle: tenká: @ (DESCRIPTION = ( \_ Vyrovnávání zatížení = zapnuto) (adresa = (protokol = TCP) (hostitel = oracleserver1) (port = 1521)) (adresa = (protokol = TCP) (hostitel = oracleserver2) (port = 1521)) (adresa = (protokol = TCP) (hostitel = 1521)) (připojení \_ dat = (název služby = \_ ORCL)))

3.  Zadejte **číslo portu** používaného službou JDBC pro připojení k databázovému serveru 1521 (standardně standardně pro Oracle).

4.  Zadejte **název služby Oracle** , který používá JDBC pro připojení k databázovému serveru.

5.  Vyberte kolekci nebo vytvořte novou (volitelné).

6.  Dokončete registraci zdroje dat.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="Možnosti registrace zdrojů" border="true":::

## <a name="creating-and-running-a-scan"></a>Vytvoření a spuštění kontroly

Pokud chcete vytvořit a spustit novou kontrolu, udělejte toto:

1.  V centru pro správu klikněte na modul runtime integrace. Ujistěte se, že je nastavený modul Integration runtime v místním prostředí. Pokud není nastavený, použijte postup uvedený [tady](./manage-integration-runtimes.md) k vytvoření prostředí Integration runtime v místním prostředí.

2.  Přejděte do **zdrojů**.

3.  Vyberte registrovaný zdroj Oracle.

4.  Vyberte **+ Nová kontrola**.

5.  Zadejte následující podrobnosti:

    a.  **Name (název**): název kontroly

    b.  **Připojit prostřednictvím prostředí Integration runtime**: vyberte nakonfigurovaný místní prostředí Integration runtime.

    c.  **Přihlašovací údaje**: Vyberte přihlašovací údaje pro připojení ke zdroji dat. Nezapomeňte:
    - Při vytváření přihlašovacích údajů vyberte základní ověřování.        
    - Zadejte uživatelské jméno používané službou JDBC k připojení k databázovému serveru ve vstupním poli uživatelské jméno.        
    - Uložte heslo uživatele používaného JDBC pro připojení k databázovému serveru v tajném klíči.

    d.  **Schema (schéma**): podmnožina schémat pro import vyjádřená jako seznam oddělený středníkem. Například Schema1; schema2. Všechna schémata uživatelů jsou importována, pokud je tento seznam prázdný. Ve výchozím nastavení jsou všechna systémová schémata (například SysAdmin) a objekty ignorovány. Když je seznam prázdný, naimportují se všechna dostupná schémata.
        Přijatelné vzory názvů schémat pomocí syntaxe výrazů jako v jazyce SQL zahrnují použití%, například. A%; B % C%; Trojrozměrné
       -   Začněte s nebo        
       -   konec s B nebo        
       -   obsahuje C nebo        
       -   rovná se D

    Použití Nea speciálních znaků není přijatelné.

6.  **Umístění ovladače**: zadejte cestu k umístění ovladače JDBC ve vašem virtuálním počítači, kde je spuštěný modul runtime integrace na místním hostiteli. Mělo by se jednat o cestu k platnému umístění složky JAR.

7.  **Maximální dostupná paměť**: maximální velikost paměti (v GB), která je dostupná na virtuálním počítači zákazníka, který se bude používat při skenování procesů. To závisí na velikosti zdroje SAP S/4HANA, který se má prohledat.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Kontrola Oracle" border="true":::

8.  Klikněte na **pokračovat**.

9.  Vyberte **aktivační událost kontroly**. Můžete nastavit plán nebo jednu kontrolu spouštět jednou.

10.  Zkontrolujte prověřování a klikněte na **Uložit a spustit**.

## <a name="viewing-your-scans-and-scan-runs"></a>Zobrazení kontrol a spuštění kontroly

1. Přejděte do centra pro správu. V části **zdroje a skenování** vyberte **zdroje dat** .

2. Vyberte požadovaný zdroj dat. Zobrazí se seznam existujících kontrol pro daný zdroj dat.

3. Vyberte kontrolu, jejichž výsledky chcete zobrazit.

4. Na této stránce se zobrazí všechna předchozí kontrola spuštěná spolu se metrikami a stavy pro každé spuštění skenování. Zobrazí se také informace o tom, zda byla vaše kontrola naplánovaná nebo ruční, kolik prostředků bylo použito klasifikace, kolik prostředků bylo zjištěno, čas zahájení a ukončení kontroly a celková doba kontroly.

## <a name="manage-your-scans"></a>Správa kontrol

Pokud chcete kontrolu spravovat nebo odstranit, postupujte takto:

1. Přejděte do centra pro správu. V části **zdroje a skenování** vyberte **zdroje dat** a potom vyberte požadovaný zdroj dat.

2. Vyberte kontrolu, kterou chcete spravovat. Kontrolu můžete upravit výběrem možnosti **Upravit**.

3. Kontrolu můžete odstranit výběrem možnosti **Odstranit**.

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)