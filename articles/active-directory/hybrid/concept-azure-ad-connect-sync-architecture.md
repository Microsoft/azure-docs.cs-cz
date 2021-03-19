---
title: 'Azure AD Connect synchronizace: Princip architektury – Azure'
description: Toto téma popisuje architekturu Azure AD Connect Sync a vysvětluje použité výrazy.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27055ce84bbb073045b69b942fd13f4fde4e3b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563858"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect synchronizace: Principy architektury
Toto téma popisuje základní architekturu pro Azure AD Connect synchronizaci. V mnoha aspektech se podobá jejím předchůdcům MIIS 2003, ILM 2007 a FIM 2010. Azure AD Connect synchronizace je vývoj těchto technologií. Pokud jste obeznámeni s některou z těchto dřívějších technologií, bude vám také docházet k obsahu tohoto tématu. Pokud s synchronizací začínáte, toto téma je za vás. Není však nutné znát podrobnosti tohoto tématu, aby bylo možné provést přizpůsobení Azure AD Connect synchronizaci (tzv. synchronizační modul v tomto tématu).

## <a name="architecture"></a>Architektura
Synchronizační modul vytvoří integrované zobrazení objektů uložených v několika propojených zdrojích dat a spravuje informace o identitě v těchto zdrojích dat. Toto integrované zobrazení je určeno informacemi o identitě získanými z připojených zdrojů dat a sadou pravidel, která určují, jak tyto informace zpracovat.

### <a name="connected-data-sources-and-connectors"></a>Připojené zdroje dat a konektory
Synchronizační modul zpracovává informace o identitě z různých úložišť dat, jako je například služba Active Directory nebo databáze SQL Server. Každé úložiště dat, které organizuje jeho data ve formátu podobném databázi a poskytuje standardní metody přístupu k datům, je potenciálním kandidátem zdroje dat pro synchronizační modul. Úložiště dat, která jsou synchronizovaná pomocí synchronizačního modulu, se nazývají **připojené zdroje dat** nebo **připojené adresáře** (CD).

Synchronizační modul zapouzdřuje interakci s připojeným zdrojem dat v rámci modulu s názvem **konektor**. Každý typ připojeného zdroje dat má určitý konektor. Konektor přeloží požadovanou operaci do formátu, který je v připojeném zdroji dat srozumitelný.

Konektory umožňují volání rozhraní API k výměně informací o identitě (čtení i zápis) s připojeným zdrojem dat. Je také možné přidat vlastní konektor pomocí rozšiřitelné architektury pro připojení. Následující obrázek znázorňuje, jak konektor připojuje propojený zdroj dat k synchronizačnímu modulu.

![Diagram znázorňuje připojený zdroj dat a synchronizační modul přidružený k řádku s názvem Connector.](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Data mohou být v obou směrech předávána, ale nemohou současně procházet oběma směry. Jinými slovy konektor lze nakonfigurovat tak, aby povoloval data, která se mají přesměrovat z připojeného zdroje dat do synchronizačního modulu, nebo z synchronizačního modulu do připojeného zdroje dat, ale pro jeden objekt a atribut můžou být v jednom okamžiku jenom jedna z těchto operací. Směr se může lišit pro různé objekty a pro různé atributy.

Chcete-li konfigurovat konektor, zadejte typy objektů, které chcete synchronizovat. Určení typů objektů definuje rozsah objektů, které jsou zahrnuty v procesu synchronizace. Dalším krokem je výběr atributů, které se mají synchronizovat, což je známý jako seznam zahrnutí atributů. Tato nastavení je možné kdykoli změnit v reakci na změny vašich obchodních pravidel. Když použijete Průvodce instalací Azure AD Connect, nakonfigurují se tato nastavení.

Aby bylo možné exportovat objekty do připojeného zdroje dat, musí seznam zahrnutí atributů obsahovat alespoň minimální atributy potřebné k vytvoření konkrétního typu objektu v připojeném zdroji dat. Například atribut **sAMAccountName** musí být obsažen v seznamu zahrnutí atributů pro export objektu uživatele do služby Active Directory, protože všechny uživatelské objekty ve službě Active Directory musí mít definován atribut **sAMAccountName** . Znovu provede Průvodce instalací tuto konfiguraci.

Pokud připojený zdroj dat používá ke uspořádání objektů strukturální komponenty, jako jsou oddíly nebo kontejnery, můžete omezit oblasti v připojeném zdroji dat, které se používají pro dané řešení.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Interní struktura oboru názvů synchronizačního modulu
Celý obor názvů synchronizačního modulu se skládá ze dvou oborů názvů, které ukládají informace o identitě. Dva obory názvů:

* Místo konektoru (CS)
* Úložiště metaverse (MV)

**Místo konektoru** je pracovní oblast, která obsahuje reprezentace určených objektů z připojeného zdroje dat a atributů zadaných v seznamu zahrnutí atributů. Synchronizační modul používá prostor konektoru k určení toho, co se změnilo v připojeném zdroji dat a přípravu příchozích změn. Synchronizační modul používá také prostor konektoru pro přípravu odchozích změn pro export do připojeného zdroje dat. Synchronizační modul udržuje odlišné místo konektoru jako pracovní oblast pro každý konektor.

Pomocí pracovní oblasti zůstává synchronizační modul nezávislý na připojených zdrojích dat a nemá vliv na jejich dostupnost a přístupnost. V důsledku toho můžete informace o identitě zpracovávat kdykoli pomocí dat v pracovní oblasti. Synchronizační modul může vyžádat pouze změny provedené v připojeném zdroji dat od ukončení poslední relace komunikace nebo nabízet pouze změny informací o identitě, které připojený zdroj dat ještě nepřijal, což snižuje síťový provoz mezi synchronizačním modulem a připojeným zdrojem dat.

Kromě toho synchronizační modul ukládá informace o stavu všech objektů, které se v prostoru konektoru rozpravují. Když jsou přijata nová data, synchronizační modul vždy vyhodnocuje, zda byla data již synchronizována.

**Metaverse** je oblast úložiště, která obsahuje agregované informace o identitě z více propojených zdrojů dat a poskytuje jedno globální integrované zobrazení všech kombinovaných objektů. Objekty Metaverse se vytvářejí na základě informací o identitě načtených z připojených zdrojů dat a sady pravidel, které vám umožní přizpůsobit proces synchronizace.

Následující obrázek znázorňuje obor názvů konektoru a obor názvů Metaverse v rámci synchronizačního modulu.

![Diagram znázorňuje připojený zdroj dat a synchronizační modul, který je rozdělený na prostor konektoru a obory názvů Metaverse, který je přidružený k řádku s názvem Connector.](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Synchronizovat objekty identity modulu
Objekty v modulu synchronizace představují reprezentace buď objektů v připojeném zdroji dat, nebo integrovaného zobrazení, které modul synchronizace obsahuje. Každý objekt synchronizačního modulu musí mít globálně jedinečný identifikátor (GUID). Identifikátory GUID poskytují integritu dat a expresní vztahy mezi objekty.

### <a name="connector-space-objects"></a>Objekty prostoru konektoru
Když synchronizační modul komunikuje s připojeným zdrojem dat, přečte informace o identitě v připojeném zdroji dat a použije tyto informace k vytvoření reprezentace objektu identity v prostoru konektoru. Tyto objekty nemůžete vytvořit ani odstranit jednotlivě. Můžete však ručně odstranit všechny objekty v prostoru konektoru.

Všechny objekty v prostoru konektoru mají dva atributy:

* Globálně jedinečný identifikátor (GUID)
* Rozlišující název (označovaný také jako DN)

Pokud připojený zdroj dat přiřadí objektu jedinečný atribut, objekty v prostoru konektoru mohou mít také atribut ukotvení. Atribut Anchor jednoznačně identifikuje objekt v připojeném zdroji dat. Synchronizační modul používá kotvu k vyhledání odpovídající reprezentace tohoto objektu v připojeném zdroji dat. Synchronizační modul předpokládá, že kotva objektu se nikdy nemění po dobu života objektu.

Mnohé z konektorů používají známý jedinečný identifikátor k automatickému vygenerování kotvy pro každý objekt při importu. Například konektor služby Active Directory používá pro kotvu atribut **objectGUID** . Pro připojené zdroje dat, které neposkytují jasně definovaný jedinečný identifikátor, můžete určit generování kotvy jako součást konfigurace konektoru.

V takovém případě je kotva sestavena z jednoho nebo více jedinečných atributů typu objektu, ani z těch, které se nemění a které jednoznačně identifikují objekt v prostoru konektoru (například číslo zaměstnance nebo ID uživatele).

Objekt prostoru konektoru může být jeden z následujících:

* Pracovní objekt
* Zástupný symbol

### <a name="staging-objects"></a>Pracovní objekty
Pracovní objekt představuje instanci určených typů objektů z připojeného zdroje dat. Kromě identifikátoru GUID a rozlišujícího názvu má pracovní objekt vždycky hodnotu, která označuje typ objektu.

Pracovní objekty, které byly importovány, mají vždy hodnotu pro atribut ukotvení. Pracovní objekty, které byly nově zřízeny synchronizačním modulem a v procesu vytváření v připojeném zdroji dat, nemají hodnotu pro atribut ukotvení.

Pracovní objekty také přenášejí aktuální hodnoty obchodních atributů a provozní informace, které synchronizační modul potřebuje k provedení procesu synchronizace. Provozní informace obsahují příznaky indikující typ aktualizací, které jsou připravené na pracovním objektu. Pokud pracovní objekt obdržel nové informace o identitě z připojeného zdroje dat, který ještě nebyl zpracován, objekt je označen jako **čeká na Import**. Pokud má pracovní objekt nové informace o identitě, která ještě nebyla exportována do připojeného zdroje dat, je označena jako **nevyřízená export**.

Pracovní objekt může být objekt pro import nebo objekt exportu. Synchronizační modul vytvoří objekt import s použitím informací o objektu přijatých z připojeného zdroje dat. Když synchronizační modul obdrží informace o existenci nového objektu, který odpovídá jednomu z typů objektů vybraných v konektoru, vytvoří objekt import v prostoru konektoru jako reprezentace objektu v připojeném zdroji dat.

Následující ilustrace znázorňuje objekt importu, který představuje objekt v připojeném zdroji dat.

![Diagram znázorňuje objekt importu předaný z připojeného zdroje dat do oboru názvů připojovacího prostoru v synchronizačním modulu.](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

Synchronizační modul vytvoří objekt exportu pomocí informací o objektu v úložišti Metaverse. Exportovat objekty jsou exportovány do připojeného zdroje dat během další komunikační relace. Z perspektivy synchronizačního modulu neexistují objekty exportu v připojeném zdroji dat. Proto atribut Anchor pro objekt exportu není k dispozici. Po přijetí objektu z synchronizačního modulu vytvoří připojený zdroj dat jedinečnou hodnotu pro atribut ukotvení objektu.

Následující obrázek ukazuje, jak je objekt exportu vytvořen pomocí informací o identitě v úložišti Metaverse.

![Diagram znázorňuje objekt exportu z úložiště metaverse do oboru názvů služby Connector a následně do připojeného zdroje dat.](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

Synchronizační modul potvrdí Export objektu opětovným importem objektu z připojeného zdroje dat. Exportovat objekty se stanou importovat objekty, když je synchronizační modul obdrží během příštího importu z připojeného zdroje dat.

### <a name="placeholders"></a>Zástupné symboly
Synchronizační modul používá k ukládání objektů plochý obor názvů. Některé připojené zdroje dat, jako je Active Directory, ale používají hierarchický obor názvů. Aby bylo možné transformovat informace z hierarchického oboru názvů do plochého oboru názvů, synchronizační modul používá k zachování hierarchie zástupné symboly.

Každý zástupný symbol představuje komponentu (například organizační jednotku) hierarchického názvu objektu, která nebyla importována do synchronizačního modulu, ale je nutná k vytvoření hierarchického názvu. Doplňují mezery vytvořené odkazy v připojeném zdroji dat k objektům, které nejsou pracovní objekty v prostoru konektoru.

Synchronizační modul používá také zástupné symboly k ukládání odkazovaných objektů, které ještě nebyly naimportovány. Pokud je třeba synchronizace nakonfigurovaná tak, aby zahrnovala atribut správce pro objekt *Abbie Spencer* a přijatá hodnota je objekt, který ještě není importovaný, například *CN = Novák Sperry, CN = Users, DC = Fabrikam, DC = com*, informace o Správci se ukládají jako zástupné symboly v prostoru konektoru. Pokud je objekt správce později importován, zástupný objekt je přepsán příznakem přípravy, který reprezentuje správce.

### <a name="metaverse-objects"></a>Objekty Metaverse
Objekt Metaverse obsahuje agregované zobrazení, které synchronizační modul obsahuje pracovní objekty v prostoru konektoru. Synchronizační modul vytváří objekty Metaverse pomocí informací v části Import objektů. Několik objektů prostoru konektoru lze propojit s jedním objektem Metaverse, ale objekt prostoru konektoru nelze propojit s více než jedním objektem Metaverse.

Objekty úložiště metaverse nelze vytvořit nebo odstranit ručně. Synchronizační modul automaticky odstraní objekty Metaverse, které nemají odkaz na žádný objekt prostoru konektoru v prostoru konektoru.

Pro mapování objektů v rámci připojeného zdroje dat na odpovídající typ objektu v úložišti Metaverse poskytuje synchronizační modul rozšiřitelné schéma s předdefinovanou sadou typů objektů a přidružených atributů. Můžete vytvořit nové typy objektů a atributy pro objekty Metaverse. Atributy můžou být jednoduché nebo vícehodnotové a typy atributů můžou být řetězce, odkazy, čísla a logické hodnoty.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Vztahy mezi přípravnými objekty a objekty úložiště metaverse
V rámci oboru názvů synchronizačního modulu je tok dat povolen vztahem propojení mezi pracovními objekty a objekty úložiště metaverse. Pracovní objekt propojený s objektem Metaverse se nazývá **připojený objekt** (nebo **objekt konektoru**). Pracovní objekt, který není propojen s objektem Metaverse, se označuje jako **odpojený objekt** (nebo **objekt odpojení**). Přidané a odpojování podmínek se nedoporučuje Zaměňujte s konektory zodpovědnými za import a export dat z připojeného adresáře.

Zástupné symboly nejsou nikdy propojeny s objektem Metaverse

Připojený objekt obsahuje pracovní objekt a jeho propojený vztah k jednomu objektu úložiště metaverse. Připojené objekty slouží k synchronizaci hodnot atributů mezi objektem prostoru konektoru a objektem Metaverse.

Když se pracovní objekt změní na připojený objekt během synchronizace, atributy můžou přesměrovat mezi pracovní objekt a objekt úložiště metaverse. Tok atributů je obousměrný a je nakonfigurovaný pomocí pravidel pro import atributů a exportovat pravidla atributů.

Jeden objekt prostoru konektoru může být propojený jenom s jedním objektem úložiště metaverse. Každý objekt úložiště metaverse je však možné propojit s více objekty prostoru konektoru ve stejném nebo v různých prostorech konektoru, jak je znázorněno na následujícím obrázku.

![Diagram znázorňuje dva připojené datové objekty přidružené konektory k synchronizačnímu modulu, který má připojené objekty a odpojený objekt.](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Propojený vztah mezi pracovním objektem a objektem úložiště metaverse je trvalý a lze jej odebrat pouze pomocí pravidel, která zadáte.

Odpojený objekt je pracovní objekt, který není propojený s žádným objektem Metaverse. Hodnoty atributu odpojeného objektu nejsou v úložišti Metaverse dále zpracovány. Synchronizační modul neaktualizuje hodnoty atributu odpovídajícího objektu v připojeném zdroji dat.

Pomocí odpojených objektů můžete ukládat informace o identitě v modulu synchronizace a zpracovat je později. Udržování pracovního objektu jako odpojeného objektu v prostoru konektoru má mnoho výhod. Vzhledem k tomu, že systém již připravil požadované informace o tomto objektu, není nutné znovu vytvořit reprezentaci tohoto objektu při dalším importu z připojeného zdroje dat. V takovém případě má modul synchronizace vždycky úplný snímek připojeného zdroje dat, i když neexistuje žádné aktuální připojení k připojenému zdroji dat. Odpojení objektů se dá převést na připojené objekty a naopak, v závislosti na zadaných pravidlech.

Objekt importu se vytvoří jako odpojený objekt. Objekt exportu musí být připojený objekt. Systémová logika vynutila toto pravidlo a odstraní všechny objekty exportu, které nejsou připojeným objektem.

## <a name="sync-engine-identity-management-process"></a>Proces správy identit modulu synchronizace
Proces správy identit řídí způsob, jakým se aktualizují informace o identitě mezi různými připojenými zdroji dat. Správa identit probíhá ve třech procesech:

* Import
* Synchronizace
* Export

Synchronizační modul během procesu importu vyhodnocuje příchozí informace o identitě z připojeného zdroje dat. Pokud jsou zjištěny změny, vytvoří buď nové pracovní objekty, nebo aktualizuje stávající pracovní objekty v prostoru konektoru pro synchronizaci.

Synchronizační modul během procesu synchronizace aktualizuje úložiště metaverse tak, aby odrážel změny, ke kterým došlo v prostoru konektoru, a aktualizuje prostor konektoru tak, aby odrážel změny, ke kterým došlo v úložišti Metaverse.

Synchronizační modul během procesu exportu vysune změny, které jsou připravené na pracovních objektech a které jsou označené jako nevyřízené exporty.

Následující obrázek ukazuje, kde se jednotlivé procesy vyskytují jako informace o identitě z jednoho připojeného zdroje dat do jiného.

![Diagram znázorňuje tok informací o identitě z připojených dat do prostoru konektoru (import) do úložiště dat (synchronizaci) do úložiště s připojenými daty (export) do úložiště metaverse.](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Importovat proces
Synchronizační modul během procesu importu vyhodnocuje aktualizace informací o identitě. Synchronizační modul porovnává informace o identitě získané z připojeného zdroje dat s informacemi o přípravných objektech a určí, zda pracovní objekt vyžaduje aktualizace. Pokud je potřeba aktualizovat pracovní objekt novými daty, pracovní objekt je označen jako čeká na import.

Pomocí pracovních objektů v prostoru konektoru před synchronizací může synchronizační modul zpracovat pouze informace o identitě, které se změnily. Tento proces přináší následující výhody:

* **Efektivní synchronizace**. Množství dat zpracovaných během synchronizace je minimalizováno.
* **Účinná opakovaná synchronizace**. Můžete změnit způsob, jakým synchronizační modul zpracovává informace o identitě bez opětovného připojení synchronizačního modulu ke zdroji dat.
* **Možnost synchronizace náhledu** Můžete zobrazit náhled synchronizace a ověřit, jestli jsou vaše předpoklady týkající se procesu správy identit správné.

Pro každý objekt zadaný v konektoru se synchronizační modul nejprve pokusí najít reprezentace objektu v prostoru konektoru konektoru. Synchronizační modul prověřuje všechny pracovní objekty v prostoru konektoru a pokusí se najít odpovídající pracovní objekt, který má odpovídající atribut ukotvení. Pokud žádný existující pracovní objekt nemá odpovídající atribut ukotvení, modul synchronizace se pokusí najít odpovídající pracovní objekt se stejným rozlišujícím názvem.

Když synchronizační modul najde pracovní objekt, který odpovídá rozlišujícímu názvu, ale nikoli kotvě, dojde k následujícímu zvláštnímu chování:

* Pokud objekt umístěný v prostoru konektoru nemá žádnou kotvu, pak synchronizační modul tento objekt z prostoru konektoru odstraní a označí objekt Metaverse, ke kterému je tento objekt propojený, jako **zkuste znovu zřídit při příštím spuštění synchronizace**. Pak vytvoří nový objekt importu.
* Pokud objekt umístěný v prostoru konektoru má kotvu, pak synchronizační modul předpokládá, že se tento objekt buď přejmenoval, nebo odstranil v připojeném adresáři. Přiřadí dočasný, nový rozlišující název objektu prostoru konektoru tak, aby mohl připravit příchozí objekt. Starý objekt pak bude **přechodný** a čeká, až konektor importuje přejmenování nebo odstranění za účelem vyřešení situace.

Pokud synchronizační modul vyhledá pracovní objekt, který odpovídá objektu zadanému v konektoru, určuje, jaký typ změn použít. Například synchronizační stroj může přejmenovat nebo odstranit objekt v připojeném zdroji dat nebo může aktualizovat pouze hodnoty atributu objektu.

Pracovní objekty s aktualizovanými daty jsou označeny jako nedokončené importy. K dispozici jsou různé typy nedokončených importů. V závislosti na výsledku procesu importu má pracovní objekt v prostoru konektoru jeden z následujících nedokončených typů importu:

* **Žádné**. Žádné změny atributů pracovního objektu nejsou k dispozici. Synchronizační modul neoznačí tento typ jako nedokončený import.
* **Přidat**. Pracovní objekt je novým objektem importu v prostoru konektoru. Synchronizační modul označí tento typ jako nedokončený import pro další zpracování v úložišti Metaverse.
* **Aktualizace**. Synchronizační modul vyhledá odpovídající pracovní objekt v prostoru konektoru a označí tento typ jako nedokončený import, aby bylo možné zpracovat aktualizace atributů v úložišti Metaverse. Aktualizace obsahují přejmenování objektů.
* **Delete**. Synchronizační modul najde odpovídající pracovní objekt v prostoru konektoru a označí tento typ jako nedokončený import, aby bylo možné připojený objekt odstranit.
* **Odstranit/přidat**. Synchronizační modul vyhledá odpovídající pracovní objekt v prostoru konektoru, ale typy objektů se neshodují. V tomto případě je upravena úprava přidávání a přidávání. Modifikace Delete-Add indikuje modulu synchronizace, že je nutné provést úplnou resynchronizaci tohoto objektu, protože se u tohoto objektu při změně typu objektu vztahují různé sady pravidel.

Nastavením stavu čekání na Import pracovního objektu je možné výrazně snížit množství dat zpracovaných během synchronizace, protože Tím umožníte, aby systém zpracovával pouze ty objekty, které mají aktualizovaná data.

### <a name="synchronization-process"></a>Proces synchronizace
Synchronizace se skládá ze dvou souvisejících procesů:

* Příchozí synchronizace, když se obsah úložiště metaverse aktualizuje pomocí dat v prostoru konektoru.
* Odchozí synchronizace, když se obsah prostoru konektoru aktualizuje pomocí dat v úložišti Metaverse.

Pomocí informací v prostoru konektoru vytvoří proces příchozí synchronizace v úložišti Metaverse integrované zobrazení dat uložených v připojených zdrojích dat. Všechny pracovní objekty nebo pouze ty, které mají nedokončené informace o importu, se agreguje v závislosti na konfiguraci pravidel.

Proces odchozí synchronizace aktualizuje objekty exportu při změně objektů úložiště metaverse.

Příchozí synchronizace vytvoří integrované zobrazení v úložišti Metaverse informací o identitě, která je přijímána z připojených zdrojů dat. Synchronizační modul může kdykoli zpracovat informace o identitě pomocí nejnovějších informací o identitě, které má z připojeného zdroje dat.

**Příchozí synchronizace**

Příchozí synchronizace zahrnuje následující procesy:

* **Zřízení** (označované také jako **projekce** , pokud je důležité odlišit tento proces od odchozího zřizování synchronizace). Synchronizační modul vytvoří nový objekt Metaverse na základě pracovního objektu a propojí je. Zřízení je operace na úrovni objektu.
* **Připojte** se. Synchronizační modul propojí pracovní objekt s existujícím objektem úložiště metaverse. Spojení je operace na úrovni objektu.
* **Importujte tok atributů**. Synchronizační modul aktualizuje hodnoty atributů, které se nazývají tok atributů objektu v úložišti Metaverse. Tok atributu import je operace na úrovni atributu, která vyžaduje propojení mezi pracovním objektem a objektem úložiště metaverse.

Zřizuje se jediný proces, který vytváří objekty v úložišti Metaverse. Zřizování má vliv pouze na Import objektů, které jsou odpojeny objekty. Během zřizování vytvoří synchronizační modul objekt úložiště metaverse, který odpovídá typu objektu importovaného objektu, a vytvoří propojení mezi oběma objekty, čímž vytvoří připojený objekt.

Proces připojení také vytvoří propojení mezi importovanými objekty a objektem úložiště metaverse. Rozdíl mezi připojením a zřizováním je, že proces připojení vyžaduje, aby byl objekt importu propojen s existujícím objektem úložiště metaverse, kde proces zřizování vytvoří nový objekt úložiště metaverse.

Synchronizační modul se pokusí připojit objekt import do objektu Metaverse pomocí kritérií, která jsou zadána v konfiguraci synchronizačního pravidla.

Během procesu zřizování a připojení synchronizační modul propojí odpojený objekt s objektem metaverse a připojí ho. Po dokončení těchto operací na úrovni objektu může synchronizační modul aktualizovat hodnoty atributu přidruženého objektu úložiště metaverse. Tento proces se nazývá import toku atributů.

Tok atributu import probíhá u všech objektů importu, které obsahují nová data a jsou propojeny s objektem Metaverse.

**Odchozí synchronizace**

Aktualizace odchozí synchronizace vyexportují objekty, když se změní objekt Metaverse, ale neodstraní se. Cílem odchozí synchronizace je vyhodnotit, jestli změny objektů Metaverse vyžadují aktualizace pracovních objektů v prostorech konektoru. V některých případech mohou změny vyžadovat, aby byly pracovní objekty ve všech prostorech konektoru aktualizovány. Změněné přípravné objekty jsou příznakem čekání na export, takže se exportují objekty. Tyto objekty exportu jsou později během procesu exportu přesunuty do připojeného zdroje dat.

Odchozí synchronizace má tři procesy:

* **Zřizování**
* **Zrušení zřízení**
* **Exportovat tok atributů**

Zřizování a rušení zřizování jsou operace na úrovni objektu. Zrušení zřízení závisí na zřizování, protože ho může iniciovat jenom zřizování. Zrušení zřízení se aktivuje, když zřizování odebere propojení mezi objektem metaverse a objektem exportu.

Zřizování se vždycky aktivuje, když se změny aplikují na objekty v úložišti Metaverse. Při provádění změn v objektech úložiště metaverse může synchronizační modul provádět v rámci procesu zřizování některé z následujících úloh:

* Vytvoří připojené objekty, kde je objekt úložiště metaverse propojený s nově vytvořeným objektem exportu.
* Přejmenování připojeného objektu.
* Odpojení propojení mezi objektem úložiště metaverse a pracovními objekty a vytvořením odpojeného objektu.

Pokud zřizování vyžaduje, aby modul pro synchronizaci vytvořil nový objekt konektoru, pracovní objekt, na který je objekt úložiště metaverse propojený, je vždy objekt exportu, protože objekt ještě neexistuje v připojeném zdroji dat.

Pokud zřizování vyžaduje, aby synchronizační modul zrušil připojení k připojenému objektu a vytvořil odpojený objekt, aktivuje se zrušení zřízení. Proces zrušení zřízení odstraní objekt.

Při zrušení zřízení neodstraní objekt exportu fyzický objekt. Objekt je označen jako **Odstraněný**, což znamená, že operace odstranění je v objektu připravená.

Tok atributů exportu se taky objevuje během procesu odchozí synchronizace, podobně jako při příchozí synchronizaci probíhá import toku atributů. Tok atributu export probíhá pouze mezi úložišti metaverse a objekty, které jsou připojeny.

### <a name="export-process"></a>Exportovat proces
V průběhu procesu exportu ověří modul synchronizace všechny objekty exportu, které jsou označeny jako nevyřízené při exportu v prostoru konektoru, a poté odešle aktualizace připojeného zdroje dat.

Synchronizační modul může určit úspěšnost exportu, ale nemůže dostatečně určit, zda je proces správy identit dokončen. Objekty v připojeném zdroji dat mohou být vždy změněny jinými procesy. Vzhledem k tomu, že synchronizační modul nemá trvalé připojení k připojenému zdroji dat, není dostačující vytvářet předpoklady o vlastnostech objektu v připojeném zdroji dat na základě úspěšného oznámení o exportu.

Například proces v připojeném zdroji dat může změnit atributy objektu zpátky na původní hodnoty (to znamená, že připojený zdroj dat může přepsat hodnoty ihned po vložení dat modulem synchronizace a úspěšně použít v připojeném zdroji dat).

Synchronizační modul ukládá informace o stavu exportu a importu o všech pracovních objektech. Pokud se od posledního exportu změnily hodnoty atributů, které jsou zadány v seznamu pro zahrnutí atributů, je uložení stavu importu a exportu umožní, aby synchronizační modul správně reagoval. Synchronizační modul používá proces importu k potvrzení hodnot atributů, které byly exportovány do připojeného zdroje dat. Porovnání importovaných a exportovaných informací, jak je znázorněno na následujícím obrázku, umožňuje modulu synchronizace určit, zda byl export úspěšný, nebo zda je nutné jej opakovat.

![Diagram znázorňuje synchronizaci objektu mezi spojovacím prostorem a připojenými daty přes konektor.](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Například pokud synchronizační modul exportuje atribut C, který má hodnotu 5, na připojený zdroj dat, ukládá C = 5 do své paměťové stavy exportu. Každý další export tohoto objektu vede k pokusu o export C = 5 do připojeného zdroje dat, protože synchronizační modul předpokládá, že se tato hodnota pro objekt netrvalě nepoužila (to znamená, pokud se v poslední době neimportovala jiná hodnota z připojeného zdroje dat). Exportovaná paměť je vymazána při přijetí C = 5 během operace importu objektu.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md) .

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

