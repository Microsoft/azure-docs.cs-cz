---
title: K vyřešení upravené výchozí pravidla – Azure AD Connect | Dokumentace Microsoftu
description: Další informace o opravách upravené výchozí pravidla, které jsou součástí služby Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067636"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Oprava upravené výchozí pravidla ve službě Azure AD Connect

Azure Active Directory (Azure AD) Connect používá výchozí pravidla pro synchronizaci.  Tato pravidla bohužel nemůžete použít univerzálně pro všechny organizace. Na základě vašich požadavků, může být potřeba je upravit. Tento článek popisuje dva příklady nejběžnější přizpůsobení a vysvětluje správný způsob, jak dosáhnout tyto úpravy.

>[!NOTE] 
> Úprava existující výchozí pravidla pro dosažení potřebná přizpůsobení se nepodporuje. Pokud tak učiníte, zabraňuje aktualizace těchto pravidel na nejnovější verzi v budoucích vydáních. Vám neposkytne opravy chyb, které potřebujete, a nové funkce. Tento dokument vysvětluje, jak stejného výsledku dosáhnout beze změny stávajících výchozích pravidel. 

## <a name="how-to-identify-modified-default-rules"></a>Zjištění upravené výchozí pravidla
Počínaje verzí 1.3.7.0 služby Azure AD Connect, je snadné identifikovat upravené výchozí pravidlo. Přejděte na **aplikace na ploše**a vyberte **Editor pravidel synchronizace**.

![Azure AD Connect se zvýrazní Editor pravidel synchronizace](media/how-to-connect-fix-default-rules/default1.png)

V editoru se zobrazí všechny změny výchozích pravidel s ikonou upozornění před název.

![Ikona upozornění](media/how-to-connect-fix-default-rules/default2.png)

 Zakázané pravidlo se stejným názvem vedle objevuje se taky (Toto je standardní výchozí pravidlo).

![Editor pravidel synchronizace zobrazující standardní výchozí pravidlo a upravené výchozí pravidlo](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Společné úpravy
Tady jsou běžné vlastní nastavení pro výchozí pravidla:

- Změna toku atributů
- Změnit filtr oborů
- Podmínka spojení změn

Před změnou všechna pravidla:

- Zakážete Plánovač synchronizace. Plánovač se ve výchozím nastavení spouští každých 30 minut. Ujistěte se, že se spouští, když provedete změny a řešení potíží s nová pravidla. Dočasně zakázat plánovač, spusťte prostředí PowerShell a spusťte `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Příkazy prostředí PowerShell pro zakázání Plánovač synchronizace](media/how-to-connect-fix-default-rules/default3.png)

- Odstranění objektů v cílovém adresáři může způsobit změnu v hodnotě filtr oborů. Před provedením jakýchkoli změn v rozsahu objekty buďte opatrní. Doporučujeme provést změny na testovacím serveru před prováděním změn na aktivní server.
- Spustit ve verzi preview na jeden objekt, jak je uvedeno v [ověřit pravidlo synchronizace](#validate-sync-rule) oddílu přidané žádné nové pravidlo.
- Spusťte úplnou synchronizaci po přidání nové pravidlo nebo úprava jakékoli vlastní synchronizační pravidlo. Tato synchronizace použije nová pravidla pro všechny objekty.

## <a name="change-attribute-flow"></a>Změna toku atributů
Existují tři různé scénáře pro změnu toku atributů:
- Přidání nového atributu.
- Hodnota atributu stávající přepsání.
- Zvolí existující atribut synchronizovat.

Můžete provést tyto beze změny standardní výchozí pravidla.

### <a name="add-a-new-attribute"></a>Přidat nový atribut
Pokud zjistíte, že není atribut odesílaných ze zdrojového adresáře do cílového adresáře, použijte [synchronizace Azure AD Connect: Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md) na tento problém vyřešit.

Pokud rozšíření není vhodná, zkuste přidat dvě nová pravidla synchronizace, je popsáno v následujících částech.


#### <a name="add-an-inbound-sync-rule"></a>Přidat pravidlo příchozí synchronizace
Pravidlo příchozí synchronizace znamená, že je zdroj pro atribut prostor konektoru a cílové úložiště metaverse. Například mít nový atribut směrovat z místní služby Active Directory do Azure Active Directory, vytvořit nové pravidlo příchozí synchronizace. Spusťte **Editor pravidel synchronizace**vyberte **příchozí** směr, a vyberte **přidat nové pravidlo**. 

 ! Editor](media/how-to-connect-fix-default-rules/default3a.png) pravidla synchronizace

Postupujte podle vlastní zásady vytváření názvů pro název pravidla. Tady používáme **vlastní ve ze služby AD - uživatel**. To znamená, že pravidlo je vlastní pravidlo a je příchozí pravidlo v prostoru konektoru Active Directory do úložiště metaverse.   

 ![Vytvoření pravidla synchronizace příchozích dat](media/how-to-connect-fix-default-rules/default3b.png)

Zadejte vlastní popis pravidla, tak, aby budoucí údržby pravidla je snadné. Popis může být například založen na co je úkol pravidla a proč je potřeba.

Proveďte požadovaná nastavení **připojený systém**, **připojený systémový typ objektu**, a **typ objekt úložiště Metaverse** pole.

Zadejte hodnotu priority od 0 do 99 (nižší číslo, tím vyšší je priorita). Pro **značky**, **povolení synchronizace hesel**, a **zakázané** pole, použijte výchozí možnosti.

Zachovat **Scoping filtr** prázdný. To znamená, že se pravidlo vztahuje na všechny objekty připojené mezi systémem připojení Active Directory a úložiště metaverse.

Zachovat **připojení pravidla** prázdný. To znamená, že toto pravidlo používá definovaným v pravidle standardní výchozí podmínku spojení. To je další důvod není můžete zakázat nebo odstranit standardní výchozí pravidlo. Pokud neexistuje žádná podmínka spojení, tok nebude atribut. 

Přidejte příslušné transformace pro atribut. Můžete přiřadit konstantu, aby konstantní hodnota tok, který cílový atribut. Můžete použít přímé mapování mezi atribut zdroje nebo cíle. Nebo můžete použít výraz pro atribut. Tady jsou různé [výraz funkce](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) můžete použít.

#### <a name="add-an-outbound-sync-rule"></a>Přidat pravidlo odchozí synchronizace
Odkaz atribut do cílového adresáře, je potřeba vytvořit odchozí pravidlo. To znamená, že je zdroj úložiště metaverse, a cíl je připojený systém. Pokud chcete vytvořit odchozí pravidlo, spusťte **Editor pravidel synchronizace**, změnit **směr** k **odchozí**a vyberte **přidat nové pravidlo**. 

![Editor pravidel synchronizace](media/how-to-connect-fix-default-rules/default3c.png)

Jak se příchozí pravidlo, můžete použít vlastní zásady vytváření názvů pro název pravidla. Vyberte **připojený systém** tenanta Azure AD, a vyberte připojený systém objektu, ke kterému chcete nastavit hodnotu atributu. Nastavte prioritu od 0 do 99. 

![Vytvořit pravidlo odchozí synchronizace](media/how-to-connect-fix-default-rules/default3d.png)

Zachovat **Scoping filtr** a **připojení pravidla** prázdný. Vyplňte transformace jako konstanta, přímé nebo výraz. 

Nyní víte, jak vytvořit nový atribut pro objekt tok uživatele ze služby Active Directory do Azure Active Directory. Tyto kroky můžete použít k mapování jakéhokoliv atributu z libovolného objektu na zdroj a cíl. Další informace najdete v tématu [vytvoříte vlastní synchronizační pravidla](how-to-connect-create-custom-sync-rule.md) a [Příprava na zřízení uživatelů](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Hodnota existující atribut přepsání
Můžete chtít přepsat hodnotu atributu, který je již namapován. Například pokud chcete vždy nastavena hodnota null atributu ve službě Azure AD, jednoduše vytvořte pouze příchozí pravidlo. Ujistěte se, konstantní hodnoty, `AuthoritativeNull`, tok do cílového atributu. 

>[!NOTE] 
> Použití `AuthoritativeNull` místo `Null` v tomto případě. Toto je vzhledem k tomu nenulová hodnota nahradí hodnotu null, i v případě, že má nižší prioritou (vyšší číselnou hodnotu v pravidle). `AuthoritativeNull`, na druhé straně není nahrazena s nenulovou hodnotou další pravidla. 

### <a name="dont-sync-existing-attribute"></a>Existující atribut se nesynchronizují
Pokud chcete vyloučit atribut synchronizaci, použijte atribut filtrování funkce, které jsou k dispozici ve službě Azure AD Connect. Spuštění **Azure AD Connect** z ikony na ploše a pak vyberte **přizpůsobit možnosti synchronizace**.

![Dodatečné úlohy možnosti služby Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Ujistěte se, že **aplikace Azure AD a filtrování atributů** vybraný a vyberte možnost **Další**.

![Volitelné funkce Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Vymažte atributy, které chcete vyloučit z synchronizace.

![Atributy služby Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Změnit filtr oborů
Synchronizace služby Azure AD se postará o většinu objektů. Můžete omezit rozsah objektů a snížit počet objektů, které chcete exportovat, aniž byste měnili standardní výchozí pravidla pro synchronizaci. 

Omezit rozsah objektů, které synchronizujete pomocí jedné z následujících metod:

- atribut cloudFiltered
- Organizační jednotka filtrování

Pokud omezíte rozsah uživatelů synchronizuje, synchronizaci hodnot hash hesel zastaví se i pro uživatele filtrovat navýšením kapacity. Pokud objekty se synchronizují již, po snížení oboru, odstraní se z cílového adresáře objekty filtrované na více instancí. Z tohoto důvodu zajistěte oboru velmi opatrně.

>[!IMPORTANT] 
> Zvýšení rozsahu objektů nakonfigurovaný službou Azure AD Connect se nedoporučuje. To je těžké pro tým podpory společnosti Microsoft o přizpůsobení. Pokud zvýšíte rozsah objektů, upravte stávající pravidlo, naklonujte a původní pravidlo zakázat. 

### <a name="cloudfiltered-attribute"></a>atribut cloudFiltered
Tento atribut nelze nastavit ve službě Active Directory. Hodnota tohoto atributu nastavte tak, že přidáte nové příchozí pravidlo. Pak můžete použít **transformace** a **výraz** tento atribut nastavit v úložišti metaverse. Následující příklad ukazuje, že nechcete synchronizovat všechny uživatele, jejichž oddělení název začíná řetězcem **HRD** (nerozlišuje velikost písmen):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Nejprve můžeme převést oddělení ze zdroje (Active Directory) na malá písmena. Následně pomocí `Left` funkce, nám trvalo pouze první tři znaky a ve srovnání s `hrd`. Pokud je shoda, je hodnota nastavena na `True`, jinak `NULL`. V nastavení na hodnotu null, jiné pravidlo s nižší prioritou (vyšší číselnou hodnotu) do ní můžete zapisovat pomocí různých podmínku. Spuštění ve verzi preview na jeden objekt se ověřit pravidlo synchronizace, jak je uvedeno v [ověřit synchronizační pravidlo](#validate-sync-rule) oddílu.

![Vytvoření možností pravidla synchronizace příchozích dat](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtrování organizačních jednotek
Můžete vytvořit jeden nebo více organizačních jednotek (OU) a přesunout objekty, které nechcete synchronizovat pro tyto organizační jednotky. Potom nakonfigurujte OU filtrování ve službě Azure AD Connect. Spuštění **Azure AD Connect** z ikony na ploše a vyberte následující možnosti. Můžete také nakonfigurovat organizační jednotku filtrování v době instalace služby Azure AD Connect. 

![Další úkoly v Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

Postupujte podle pokynů průvodce a zrušte organizační jednotky nechcete synchronizovat.

![Azure AD Connect domény a organizační jednotky možnosti filtrování](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Podmínka spojení změn
Použijte výchozí spojení podmínky nakonfigurované pomocí služby Azure AD Connect. Změna podmínky spojení výchozí ztížíte podporu Microsoftu a pochopit vlastní nastavení a podporu produktu.

## <a name="validate-sync-rule"></a>Ověření pravidla synchronizace
Nově přidané synchronizační pravidlo můžete ověřit pomocí funkce ve verzi preview, bez nutnosti spuštění cyklu úplnou synchronizaci. Ve službě Azure AD Connect, vyberte **synchronizační služba**.

![Azure AD Connect se zvýrazněnou službou synchronizace](media/how-to-connect-fix-default-rules/default10.png)

Vyberte **vyhledávání Metaverse**. Vyberte objekt oboru jako **osoba**vyberte **přidat klauzuli**a zmiňovat kritériím hledání. V dalším kroku vyberte **hledání**a dvakrát klikněte na objekt ve výsledcích hledání. Ujistěte se, že vaše data ve službě Azure AD Connect je aktuální pro tento objekt spuštěním import a synchronizace v doménové struktuře před provedením tohoto kroku.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Na **vlastnosti objektu úložiště Metaverse**vyberte **konektory**, vyberte objekt v odpovídající konektor (doménová struktura) a vyberte **vlastnosti...** .

![Vlastnosti objektu úložiště Metaverse](media/how-to-connect-fix-default-rules/default12.png)

Vyberte **náhledu...**

![Vlastnosti objektu prostoru konektoru](media/how-to-connect-fix-default-rules/default13a.png)

V okně verze Preview vyberte **generovat ve verzi Preview** a **toku atributu importu** v levém podokně.

![Náhled](media/how-to-connect-fix-default-rules/default14.png)
 
Tady, Všimněte si, nově přidané pravidlo spustí na objekt a nastavil `cloudFiltered` atribut na hodnotu true.

![Náhled](media/how-to-connect-fix-default-rules/default15a.png)
 
Pro porovnání upravené pravidlo s výchozí pravidlo, exportujte z pravidel samostatně jako textové soubory. Tato pravidla jsou exportovány jako soubor skriptu Powershellu. Můžete porovnat je pomocí jakékoli nástroj pro porovnání souborů (například nástroje windiff) zobrazíte změny. 
 
Všimněte si, že v upravené pravidlo `msExchMailboxGuid` atribut se změní na **výraz** typu, místo **přímé**. Také, hodnota se změní na **NULL** a **ExecuteOnce** možnost. Můžete ignorovat rozdíly identifikovaný a priority. 

![výstup nástroje WinDiff nástroje](media/how-to-connect-fix-default-rules/default17.png)
 
Chcete-li vyřešit vaše pravidla, chcete-li změnit výchozí nastavení, odstranit pravidlo upravené a povolit výchozí pravidlo. Ujistěte se, abyste nepřišli přizpůsobení, které se snažíte dosáhnout. Jakmile budete připraveni, spusťte **úplnou synchronizaci**.

## <a name="next-steps"></a>Další postup
- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)



