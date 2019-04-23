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
ms.openlocfilehash: 761f3e6e72319a2e63d6b66f2893130ec5a82ebf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60352880"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Oprava upravené výchozí pravidla ve službě Azure AD Connect

Azure AD Connect se dodává s výchozí pravidla pro synchronizaci.  Bohužel tato pravidla se nedá použít univerzálně u všech organizací a může nastat situace, na základě vašich požadavků, když je potřeba je upravit.

 Pokud byly upraveny. výchozí pravidla nebo hodlají začít upravovat, pak věnujte prosím chvilku ke čtení tohoto dokumentu.

Tento dokument 2 jsou příklady nejběžnější přizpůsobení provádí uživatelů a vysvětluje správný způsob, jak dosáhnout tyto úpravy.

>[!NOTE] 
> Úprava existující výchozí pravidla pro dosažení potřebná přizpůsobení není podporována – díky tomu nebudou moct tato pravidla aktualizace na nejnovější verzi v budoucích verzích. Nebudete tak potřebné opravy chyb a nové funkce.  Tento dokument vysvětluje, jak k dosažení stejného výsledku beze změny stávajících výchozích pravidel. 

## <a name="how-to-identify-modified-default-rules"></a>Jak identifikovat změny výchozích pravidel?
Počínaje verzí 1.3.7.0 **Azure AD Connect**, je teď snadno rozpoznat upravené výchozí pravidlo. Můžete přejít do aplikace na ploše a klikněte na **Editor pravidel synchronizace**.

![editor](media/how-to-connect-fix-default-rules/default1.png)

V editoru všechny změny výchozích pravidel zobrazí s ikonou před název jak je znázorněno níže:

![Ikona](media/how-to-connect-fix-default-rules/default2.png)

 Taky uvidíte zakázané pravidlo rule se stejným názvem vedle sebe, což je standardní výchozí pravidlo:

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Společné úpravy
Tady jsou běžné vlastní nastavení pro výchozí pravidla:

- [Změna toku atributů](#changing-attribute-flow)
- [Změna filtr oborů](#changing-scoping-filter)
- [Změna podmínka spojení](#changing-join-condition)

## <a name="before-changing-any-rules"></a>Před změnou všechna pravidla
- Zakážete Plánovač synchronizace.  Plánovač se ve výchozím nastavení spouští každých 30 minut. Ujistěte se, že se spouští při provádění změn a řešení potíží s nová pravidla. Pokud chcete dočasně zakázat plánovač, spusťte prostředí PowerShell a spusťte `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Výchozí pravidla](media/how-to-connect-fix-default-rules/default3.png)

- Změnu v hodnotě filtr oborů může mít za následek odstranění objektů v cílovém adresáři. Před provedením jakýchkoli změn v rozsahu objekty buďte opatrní. Doporučujeme provádět změny pracovní server před prováděním změn na aktivní server.
- Spusťte prosím ve verzi preview na jeden objekt jak je uvedeno v [ověřit pravidlo synchronizace](#validate-sync-rule) oddílu přidané žádné nové pravidlo.
- Po přidání nové pravidlo nebo úprava jakékoli vlastní synchronizační pravidlo spusťte úplnou synchronizaci. Tato synchronizace bude používat nová pravidla pro všechny objekty.

## <a name="changing-attribute-flow"></a>Změna toku atributů
Existují 3 různé scénáře pro tok atributů, Podívejme se na tom, jak dosáhnout beze změny standardní výchozí pravidla.
- Přidat nový atribut
- Hodnota atributu existující přepsání
- Existující atribut se nesynchronizují

### <a name="adding-new-attribute"></a>Přidání nového atributu:
Pokud zjistíte, že atribut není odesílaných ze zdrojového adresáře do cílového adresáře a potom můžete použít [synchronizace Azure AD Connect: Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md) tok nové atributy.

Mějte prosím na paměti, že by měl být nejprve použít [synchronizace Azure AD Connect: Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md), funkci pole, které poskytuje Azure AD Connect. Ale pokud ho neměli pracují pro vás, projděte si následující kroky pro tok atributu beze změny stávajících standardní výchozí pravidlo synchronizace, můžete to provést tak, že přidáte dvě nová pravidla synchronizace.


#### <a name="add-an-inbound-sync-rule"></a>Přidáte pravidlo příchozí synchronizace:
Pravidlo příchozí synchronizace znamená, že je zdroj pro atribut prostor konektoru a cíl je úložiště metaverse. Například tok nový atribut z místní služby Active Directory do Azure Active Directory, vytvořit nové pravidlo příchozí synchronizace spuštěním **Synchronization Rule Editor**, pak vyberte směr jako **příchozího** a klikněte na tlačítko **přidat nové pravidlo**. 

 ![Výchozí pravidla](media/how-to-connect-fix-default-rules/default3a.png)

Postupujte podle vlastní zásady vytváření názvů pro název pravidla, tady jsme použili **vlastní ve ze služby AD - uživatel**, to znamená, že pravidlo vlastního pravidla a je v prostoru konektoru AD příchozí pravidlo do úložiště Metaverse.   

 ![Výchozí pravidla](media/how-to-connect-fix-default-rules/default3b.png)

Tak, aby budoucí údržby pravidla je jednoduché, jako je co cílem tohoto pravidla je a proč je potřeba, poskytněte vlastní popis pravidla.
Vyberte připojený systém (doménová struktura) - zdrojového atributu. Vyberte typ objektu připojený systém a typ objekt úložiště Metaverse.

Zadejte číslo prioritu v rozsahu 0 – 99 (nižší číslo, vyšší prioritu). Zachovejte další pole, jako jsou "Štítku" povolení synchronizace hesel a zakázané jako výchozí.

Zachovat filtr Scoping' prázdná, to znamená, že pravidlo platit pro všechny objekty, které jsou připojené k mezi AD připojený systém a Metaverse.

Zajistit 'Připojte se k pravidla' prázdná, což znamená, že toto pravidlo bude parazitují na definovaným v pravidle standardní výchozí podmínku spojení. To je další důvod nechcete zakázat/odstranit standardní výchozí pravidlo, protože pokud neexistuje žádná podmínka spojení počítač pak nebude toku atributu. 

Přidat odpovídající transformace pro atribut, můžete přiřadit konstantní tok konstantní hodnoty do cílového atributu nebo přímé mapování mezi zdroj nebo cíl atributu nebo výraz pro atribut. Tady jsou různé [výraz funkce](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) můžete použít.

#### <a name="add-an-outbound-sync-rule"></a>Přidáte pravidlo odchozí synchronizace:
Zatím přidáním pouze pravidlo příchozí synchronizace jsme udělali poloviční práci, protože atribut není ještě propojené do cílového adresáře. Pokud chcete propojit ředitel cílový atribut je potřeba vytvořit odchozí pravidlo, což znamená, že je úložiště metaverse zdroj a cíl je připojený systém. Odchozí pravidlo vytvoříte spuštění **Synchronization Rule Editor**, změnit **směr** k **odchozí** a klikněte na tlačítko **přidat nové pravidlo**. 

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default3c.png)

Stejně jako v příchozí pravidlo, můžete použít vlastní zásady vytváření názvů pro **název** pravidlo. Vyberte **připojený systém** jako tenant Azure AD, vyberte objekt připojený systém, do které chcete nastavit hodnotu atributu. Nastavit prioritu mezi 0 - 99. 

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default3d.png)

Zachovat **Scoping filtr** prázdný, zachovat **připojení pravidla** prázdný, vyplňte transformace jako konstanta, přímé nebo výraz. 

V tomto příkladu jsme se ukázal, jak tok nový atribut pro objekt uživatele ze služby Active Directory do Azure Active Directory. Tyto kroky můžete použít k mapování jakéhokoliv atributu z libovolného objektu na zdroj a cíl.  Další informace najdete v části [vytvoříte vlastní synchronizační pravidla](how-to-connect-create-custom-sync-rule.md) a [Příprava na zřízení uživatelů](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="overriding-value-of-existing-attribute"></a>Hodnota atributu existující přepsání
Je možné, že chcete přepsat hodnotu atributu již namapovaný, například chcete vždy nastavena hodnota Null atributu ve službě Azure AD, můžete to provést tak, že jednoduše pouze příchozí pravidlo vytvoříte tak, jak je uvedeno v předchozím kroku a flow  **AuthoritativeNull** konstantní hodnota pro atribut target. Mějte prosím na paměti, že jsme použili AuthoritativeNulll místo Null v tomto případě. Je to proto nenulová hodnota nahradí hodnotu Null, i když má nižší prioritou (vyšší číselnou hodnotu v pravidle). AuthoritativeNull však budou považovány za hodnotu Null a nebude nahrazen s jinou hodnotu než null jinými pravidly. 

### <a name="dont-sync-existing-attribute"></a>Existující atribut se nesynchronizují
Pokud chcete vyloučit atribut synchronizaci, můžete použít atribut filtrování funkce, které jsou k dispozici ve službě Azure AD Connect. Spuštění **Azure AD Connect** z ikony na ploše a pak vyberte **přizpůsobit možnosti synchronizace**.

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default4.png)

 Ujistěte se, že **aplikace Azure AD a filtrování atributů** zaškrtnuto a klikněte na tlačítko **Další**.

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default5.png)

Zrušte zaškrtnutí políčka atributy, které chcete vyloučit z synchronizace.

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>Změna filtr oborů
Synchronizace služby Azure AD se postará o většinu objektů, můžete omezit rozsah objektů a snížit méně objektů export podporované způsobem beze změny standardní výchozí pravidla pro synchronizaci. Chcete-li rozšířit rozsah objektů, pak můžete **upravit** stávající pravidlo, naklonujte a původní pravidlo zakázat. Společnost Microsoft doporučuje, není-li rozšířit rozsah nakonfigurovaný službou Azure AD Connect. Rozšíření objektů bude znesnadňují pro tým podpory vlastního nastavení a podporu produktu.

Zde je, jak můžete omezit rozsah objektů, které synchronizují do Azure AD. Upozorňujeme, že je-li omezit rozsah **uživatelé** synchronizuje pak synchronizaci hodnot hash hesel se zastaví také pro uživatele filtrovat navýšením kapacity. Pokud objekty jsou již synchronizace, pak po omezení oboru, filtrovaný na více instancí objektů budou odstraněny z cílového adresáře, prosím práci na rozsahu velmi opatrně.
Tady jsou podporované jak omezit rozsah objektů, které se synchronizují.

- [atribut cloudFiltered](#cloudfiltered-attribute)
- [Filtrování podle organizačních jednotek](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>atribut cloudFiltered
Všimněte si, že to není atribut, který lze nastavit ve službě Active Directory. Je nutné nastavit hodnotu atributu jde tak, že přidáte nové příchozí pravidlo, jak je uvedeno v **přepsání hodnotu atributu existující** oddílu. Pak můžete použít **transformace** a používat **výraz** tento atribut nastavit v úložišti Metaverse. Tady je příklad, který není nutné synchronizovat všechny uživatele, jehož název oddělení začíná malá a velká písmena **HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Oddělení jsme nejprve mít převeden na malá písmena ze zdroje (Active Directory). Pak pomocí funkce Left, nám trvalo pouze první 3 znaky a porovnání s hrd. Pokud je shoda, nastavte hodnotu na True jinak NULL. Mějte prosím na paměti, že jsme se nastavení na hodnotu NULL, tak, aby ostatní pravidla s nižší prioritou (vyšší číselnou hodnotu) můžete do ní zapisovat různé podmínky. Spusťte prosím ve verzi preview na jeden objekt se ověřit pravidlo synchronizace, jak je uvedeno v [ověřit pravidlo synchronizace](#validate-sync-rule) oddílu.

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>Filtrování podle organizačních jednotek
Můžete vytvořit jeden nebo více organizačních jednotkách a přesunout objekty, které nechcete synchronizovat pro tyto organizační jednotky. Potom nakonfigurujte organizační jednotky ve službě Azure AD Connect filtrování spuštěním **Azure AD Connect** z ikony na ploše a vyberte požadované možnosti, jak je znázorněno níže. Můžete také nakonfigurovat organizační jednotku filtrování v době instalace služby Azure AD Connect. 

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default8.png)

Postupujte podle pokynů průvodce a pak zrušte výběr organizační jednotky nechcete synchronizovat.

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>Změna podmínka spojení
Společnost Microsoft doporučuje se, že používáte výchozí do programu ujednání nakonfigurované služby Azure AD Connect. Změna výchozí podmínky spojení bude znesnadňují pro tým podpory vlastního nastavení a podporu produktu.

## <a name="validate-sync-rule"></a>Ověření pravidla synchronizace
Nově přidané synchronizační pravidlo můžete ověřit pomocí funkce ve verzi preview bez spuštění cyklu úplnou synchronizaci. Spuštění **synchronizační služba** uživatelského rozhraní.

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default10.png)

Klikněte na **vyhledávání Metaverse**, vyberte objekt oboru jako **osoba**, **přidat klauzuli** a zmiňovat kritériím hledání. Klikněte na **hledání** tlačítko a dvakrát klikněte na objekt v **výsledky hledání** mějte na paměti, že jste před provedením tohoto kroku spusťte import a synchronizace v doménové struktuře, je to z toho data ve službě Azure AD Connect je aktuální pro tento objekt.

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default11.png)



Vyberte **konektory**, vyberte objekt v odpovídající connector(forest), klikněte na tlačítko **vlastnosti...** .

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default12.png)

Klikněte na **náhledu...**

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default13a.png)

Klikněte na **generovat ve verzi Preview** a **toku atributu importu** v levém podokně.

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default14.png)
 
Tady si všimnete, že nově přidané pravidlo spustí na objekt a má atribut cloudFiltered nastavena na hodnotu True.

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default15a.png)
 
Jak porovnat upravené pravidlo s výchozí pravidlo?
Můžete exportovat pravidla i samostatně jako textové soubory. Tato pravidla se exportují jako soubor skriptu powershellu. Můžete porovnat je využít libovolný nástroj porovnání souboru Pokud chcete zobrazit, jaký druh změn hotovi. Tady v tomto příkladu můžu používat nástroje windiff porovnat dva soubory.
 
Všimněte si, že v uživatelském upravit pravidla, atributu msExchMailboxGuid se změní na **výraz** zadejte místo **přímé** s hodnotou **NULL** a  **ExecuteOnce** možnost. Můžete ignorovat rozdíly identifikovaný a priority. 

![Výchozí pravidla](media/how-to-connect-fix-default-rules/default17.png)
 
Jak vyřešit upravené výchozí pravidlo?
Stanovit pravidla a nastavení výchozí můžete odstranit pravidlo upravené a povolit výchozí pravidlo, jak je znázorněno níže a spusťte **úplnou synchronizaci**. Před tím, který prosím provést opravné akce, jak je uvedeno výše, aby neztratili přizpůsobení se snažíte dosáhnout ## další kroky

## <a name="next-steps"></a>Další kroky
- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)

