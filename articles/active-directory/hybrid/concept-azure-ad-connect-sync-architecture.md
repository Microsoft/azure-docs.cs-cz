---
title: 'Synchronizace Azure AD Connect: Principy architektury – Azure'
description: Toto téma popisuje architekturu synchronizace Azure AD Connect a vysvětluje použité termíny.
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
ms.openlocfilehash: fac0f9143918d3f273812e53abfb88d6a56f7a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261616"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Synchronizace Azure AD Connect: Principy architektury
Toto téma popisuje základní architekturu pro synchronizaci Azure AD Connect. V mnoha ohledech je podobná svým předchůdcům MIIS 2003, ILM 2007 a FIM 2010. Synchronizace Azure AD Connect je vývoj těchto technologií. Pokud jste obeznámeni s některou z těchto dřívějších technologií, obsah tohoto tématu bude znát i vás. Pokud jste novým synchronizace, pak toto téma je pro vás. Není však nutné znát podrobnosti tohoto tématu, aby byly úspěšné při provádění přizpůsobení synchronizace Azure AD Connect (v tomto tématu se nazývá synchronizační modul).

## <a name="architecture"></a>Architektura
Synchronizační modul vytvoří integrované zobrazení objektů, které jsou uloženy ve více připojených zdrojích dat a spravuje informace o identitě v těchto zdrojích dat. Toto integrované zobrazení je určeno informacemi o identitě načtenými z připojených zdrojů dat a sadou pravidel, která určují, jak tyto informace zpracovat.

### <a name="connected-data-sources-and-connectors"></a>Připojené zdroje dat a konektory
Synchronizační modul zpracovává informace o identitě z různých úložišť dat, jako je například služba Active Directory nebo databáze serveru SQL Server. Každé úložiště dat, které uspořádá svá data ve formátu podobném databázi a které poskytuje standardní metody přístupu k datům, je potenciálním kandidátem zdroje dat pro synchronizační modul. Úložiště dat, která jsou synchronizována pomocí synchronizačního modulu, se nazývají **připojené zdroje dat** nebo připojené **adresáře** (CD).

Synchronizační modul zapouzdřuje interakci s připojeným zdrojem dat v rámci modulu nazývaného **konektor**. Každý typ připojeného zdroje dat má specifický konektor. Konektor převádí požadovanou operaci do formátu, který rozumí připojený zdroj dat.

Konektory provádět volání rozhraní API pro výměnu informací o identitě (čtení i zápis) s připojeným zdrojem dat. Je také možné přidat vlastní konektor pomocí rozšiřitelné připojení rámce. Následující obrázek znázorňuje, jak konektor spojuje připojený zdroj dat k synchronizačnímu modulu.

![Arch1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Data mohou proudit v obou směrech, ale nemohou tok v obou směrech současně. Jinými slovy konektor lze nakonfigurovat tak, aby data toku z připojeného zdroje dat pro synchronizaci motoru nebo z synchronizačního modulu do připojeného zdroje dat, ale pouze jeden z těchto operací může dojít v jednom okamžiku pro jeden objekt a atribut. Směr se může lišit pro různé objekty a pro různé atributy.

Chcete-li konfigurovat konektor, zadejte typy objektů, které chcete synchronizovat. Určení typů objektů definuje rozsah objektů, které jsou zahrnuty v procesu synchronizace. Dalším krokem je výběr atributů pro synchronizaci, které se označují jako seznam zahrnutí atributů. Tato nastavení lze kdykoli změnit v reakci na změny obchodních pravidel. Při použití Průvodce instalací služby Azure AD Connect, tato nastavení jsou nakonfigurované pro vás.

Chcete-li exportovat objekty do připojeného zdroje dat, musí seznam zahrnutí atributů obsahovat alespoň minimální atributy potřebné k vytvoření určitého typu objektu v připojeném zdroji dat. Například atribut **sAMAccountName** musí být zahrnut do seznamu zahrnutí atributů, aby bylo možné exportovat objekt uživatele do služby Active Directory, protože všechny uživatelské objekty ve službě Active Directory musí mít definovaný atribut **sAMAccountName.** Průvodce instalací opět provede tuto konfiguraci za vás.

Pokud připojený zdroj dat používá strukturální součásti, jako jsou oddíly nebo kontejnery k uspořádání objektů, můžete omezit oblasti v připojeném zdroji dat, které se používají pro dané řešení.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Vnitřní struktura oboru názvů synchronizačního modulu
Celý obor názvů synchronizačního modulu se skládá ze dvou oborů názvů, které ukládají informace o identitě. Dva obory názvů jsou:

* Prostor konektoru (CS)
* Metaverse (MV)

**Prostor spojnice** je pracovní oblast, která obsahuje reprezentace určených objektů z připojeného zdroje dat a atributy zadané v seznamu zahrnutí atributů. Synchronizační modul používá prostor konektoru k určení, co se změnilo v připojeném zdroji dat, a k fázi příchozích změn. Synchronizační modul také používá prostor konektoru k fázi odchozí změny pro export do připojeného zdroje dat. Synchronizační modul udržuje odlišný prostor konektoru jako pracovní oblast pro každý konektor.

Při použití pracovní oblasti synchronizační modul zůstane nezávislý na připojených zdrojích dat a není ovlivněn jejich dostupnost a usnadnění. V důsledku toho můžete kdykoli zpracovat informace o identitě pomocí dat v pracovní oblasti. Synchronizační modul může požadovat pouze změny provedené uvnitř připojeného zdroje dat od ukončení poslední relace komunikace nebo vysunutí pouze změn informací o identitě, které připojený zdroj dat dosud neobdržel, což snižuje síť mezi synchronizačním motorem a připojeným zdrojem dat.

Kromě toho synchronizační modul ukládá informace o stavu všech objektů, které fáze v prostoru konektoru. Při přijetí nových dat synchronizační modul vždy vyhodnotí, zda již byla data synchronizována.

**Metaverse** je oblast úložiště, která obsahuje agregované informace o identitě z více připojených zdrojů dat a poskytuje jeden globální, integrovaný pohled na všechny kombinované objekty. Metaverse objekty jsou vytvořeny na základě informací o identitě, která je načtena z připojených zdrojů dat a sadu pravidel, které umožňují přizpůsobit proces synchronizace.

Následující obrázek znázorňuje obor názvů oboru oboru konektoru a obor názvů metaverse v synchronizačním modulu.

![Arch2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Synchronizace objektů identity motoru
Objekty v synchronizačním modulu jsou reprezentace buď objektů v připojeném zdroji dat, nebo integrované zobrazení, které synchronizační modul má těchto objektů. Každý objekt synchronizačního modulu musí mít globálně jedinečný identifikátor (GUID). Identifikátory GUID poskytují integritu dat a vyjadřují vztahy mezi objekty.

### <a name="connector-space-objects"></a>Objekty prostoru spojnice
Když synchronizační modul komunikuje s připojeným zdrojem dat, přečte informace o identitě v připojeném zdroji dat a použije tyto informace k vytvoření reprezentace objektu identity v prostoru konektoru. Tyto objekty nelze vytvořit ani odstranit jednotlivě. Můžete však ručně odstranit všechny objekty v prostoru spojnice.

Všechny objekty v prostoru spojnice mají dva atributy:

* Globálně jedinečný identifikátor (GUID)
* Rozlišující název (také známý jako DN)

Pokud připojený zdroj dat přiřadí objektu jedinečný atribut, mohou mít objekty v prostoru spojnice také atribut kotvy. Atribut kotvy jednoznačně identifikuje objekt v připojeném zdroji dat. Synchronizační modul používá kotvu k vyhledání odpovídající reprezentace tohoto objektu v připojeném zdroji dat. Synchronizační modul předpokládá, že kotva objektu se nikdy nezmění po dobu životnosti objektu.

Mnoho konektorů používá známý jedinečný identifikátor k automatickému generování kotvy pro každý objekt při importu. Konektor služby Active Directory například používá atribut **objectGUID** pro kotvu. Pro připojené zdroje dat, které neposkytují jasně definovaný jedinečný identifikátor, můžete určit generování ukotvení jako součást konfigurace konektoru.

V takovém případě je kotva sestavena z jednoho nebo více jedinečných atributů typu objektu, z nichž ani jedna se nezmění a která jednoznačně identifikuje objekt v prostoru spojnice (například číslo zaměstnance nebo ID uživatele).

Objekt prostoru spojnice může být jeden z následujících:

* Pracovní objekt
* Zástupný symbol

### <a name="staging-objects"></a>Pracovní objekty
Pracovní objekt představuje instanci určených typů objektů z připojeného zdroje dat. Kromě identifikátoru GUID a rozlišujícího názvu má pracovní objekt vždy hodnotu, která označuje typ objektu.

Pracovní objekty, které byly importovány, mají vždy hodnotu atributu kotvy. Pracovní objekty, které byly nově zřízeny synchronizačním strojem a jsou právě vytvářeny v připojeném zdroji dat, nemají hodnotu pro atribut kotvy.

Pracovní objekty také nesou aktuální hodnoty obchodních atributů a provozní informace potřebné synchronizačním strojem k provedení procesu synchronizace. Provozní informace zahrnují příznaky, které označují typ aktualizací, které jsou připraveny na pracovní objekt. Pokud pracovní objekt obdržel nové informace o identitě z připojeného zdroje dat, který ještě nebyl zpracován, je objekt označen jako **čekající na import**. Pokud pracovní objekt obsahuje nové informace o identitě, které ještě nebyly exportovány do připojeného zdroje dat, je označen jako **čekající export**.

Pracovní objekt může být objekt importu nebo exportobjekt. Synchronizační modul vytvoří objekt importu pomocí informací o objektu přijatých z připojeného zdroje dat. Když synchronizační modul obdrží informace o existenci nového objektu, který odpovídá jednomu z typů objektů vybraných v spojnici, vytvoří objekt importu v prostoru spojnice jako reprezentaci objektu v připojeném zdroji dat.

Následující obrázek znázorňuje objekt importu, který představuje objekt v připojeném zdroji dat.

![Arch3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

Synchronizační modul vytvoří exportní objekt pomocí informací o objektu v metaverse. Exportobjektů se exportuje do připojeného zdroje dat během další relace komunikace. Z hlediska synchronizačního modulu exportobjektů v připojeném zdroji dat ještě neexistují. Atribut ukotvení pro exportní objekt proto není k dispozici. Po přijetí objektu ze synchronizačního modulu vytvoří připojený zdroj dat jedinečnou hodnotu pro atribut ukotvení objektu.

Následující obrázek znázorňuje, jak je exportní objekt vytvořen pomocí informací o identitě v metaverse.

![Arch4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

Synchronizační modul potvrdí export objektu opětovným importem objektu z připojeného zdroje dat. Export objektů se stane objekty importu, když je synchronizační modul přijme během dalšího importu z tohoto připojeného zdroje dat.

### <a name="placeholders"></a>Zástupné symboly
Synchronizační modul používá plochý obor názvů k ukládání objektů. Některé připojené zdroje dat, například služba Active Directory, však používají hierarchický obor názvů. Chcete-li transformovat informace z hierarchického oboru názvů do plochého oboru názvů, synchronizační modul používá zástupné symboly k zachování hierarchie.

Každý zástupný symbol představuje komponentu (například organizační jednotku) hierarchického názvu objektu, která nebyla importována do synchronizačního modulu, ale je vyžadována k vytvoření hierarchického názvu. Vyplní mezery vytvořené odkazy v připojeném zdroji dat na objekty, které nejsou pracovní objekty v prostoru spojnice.

Synchronizační modul také používá zástupné symboly k ukládání odkazovaných objektů, které ještě nebyly importovány. Pokud je například synchronizace nakonfigurována tak, aby zahrnovala atribut správce pro objekt *Abbie Spencer* a přijatá hodnota je objekt, který ještě nebyl importován, například *CN=Lee Sperry,CN=Users,DC=fabrikam,DC=com*, informace správce jsou uloženy jako zástupné symboly v prostoru konektoru. Pokud je objekt správce později importován, zástupný objekt je přepsán pracovním objektem, který představuje správce.

### <a name="metaverse-objects"></a>Metaverse objekty
Metaverse objekt obsahuje agregované zobrazení, které synchronizační modul má pracovní objekty v prostoru konektoru. Synchronizační modul vytvoří metaverse objekty pomocí informací v importu objektů. Několik objektů prostoru spojnice může být propojeno s jedním metaverse objektem, ale objekt prostoru spojnice nelze propojit s více než jedním metaverse objektu.

Metaverse objekty nelze ručně vytvořit nebo odstranit. Synchronizační modul automaticky odstraní metaverse objekty, které nemají odkaz na žádný objekt prostoru konektoru v prostoru konektoru.

Chcete-li mapovat objekty v rámci připojeného zdroje dat na odpovídající typ objektu v rámci metaverse, synchronizační modul poskytuje rozšiřitelné schéma s předdefinovanou sadou typů objektů a přidružených atributů. Můžete vytvořit nové typy objektů a atributy pro metaverse objekty. Atributy mohou být s jednou hodnotou nebo s více hodnotami a typy atributů mohou být řetězce, odkazy, čísla a logické hodnoty.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Vztahy mezi pracovními objekty a metaverse objekty
V rámci oboru názvů synchronizačního modulu je tok dat povolen vztahem propojení mezi pracovními objekty a metaverse objekty. Pracovní objekt, který je propojen s metaverse objektem se nazývá **spojený objekt** (nebo **objekt konektoru).** Pracovní objekt, který není propojen s metaverse objektem se nazývá **odpojený objekt** (nebo **odpojovací objekt**). Podmínky spojené a odpojené jsou upřednostňovány nezaměňovat s konektory odpovědné za import a export dat z připojeného adresáře.

Zástupné symboly nejsou nikdy propojeny s metaverse objektem

Spojený objekt se skládá z pracovního objektu a jeho propojeného vztahu s jedním metaverse objektem. Spojené objekty se používají k synchronizaci hodnot atributů mezi objektem prostoru spojnice a metaverse objektem.

Když se pracovní objekt během synchronizace stane spojeným objektem, mohou atributy natékat mezi pracovním objektem a metaverse objektem. Tok atributů je obousměrný a je konfigurován pomocí pravidel atributů importu a pravidel atributů exportu.

Jeden objekt prostoru spojnice lze propojit pouze s jedním objektem metaverse. Každý metaverse objekt však může být propojen s více objekty prostoru spojnice ve stejném nebo v různých prostorech spojnice, jak je znázorněno na následujícím obrázku.

![Arch5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Propojený vztah mezi pracovním objektem a metaverse objektem je trvalý a lze jej odebrat pouze pomocí zadaných pravidel.

Disjoined objekt je pracovní objekt, který není propojen s žádným metaverse objektu. Hodnoty atributů disjoined objektu nejsou zpracovány žádné další v rámci metaverse. Hodnoty atributů odpovídajícího objektu v připojeném zdroji dat nejsou synchronizačním motorem aktualizovány.

Pomocí odpojených objektů můžete ukládat informace o identitě v synchronizačním modulu a zpracovat je později. Udržování pracovní objekt jako disjoined objektu v prostoru spojnice má mnoho výhod. Vzhledem k tomu, že systém již zinscenoval požadované informace o tomto objektu, není nutné znovu vytvořit reprezentaci tohoto objektu během dalšího importu z připojeného zdroje dat. Tímto způsobem synchronizační modul má vždy úplný snímek připojeného zdroje dat, i když neexistuje žádné aktuální připojení k připojenému zdroji dat. Odpojené objekty lze převést na spojené objekty a naopak v závislosti na zadaných pravidlech.

Objekt importu je vytvořen jako disjoined objekt. Exportní objekt musí být spojený objekt. Systémová logika vynucuje toto pravidlo a odstraní každý exportní objekt, který není spojený objekt.

## <a name="sync-engine-identity-management-process"></a>Synchronizovat proces správy identit motoru
Proces správy identit řídí, jak jsou informace o identitě aktualizovány mezi různými připojenými zdroji dat. Správa identit probíhá ve třech procesech:

* Import
* Synchronizace
* Export

Během procesu importu synchronizační modul vyhodnotí příchozí informace o identitě z připojeného zdroje dat. Když jsou zjištěny změny, buď vytvoří nové pracovní objekty nebo aktualizuje existující pracovní objekty v prostoru konektoru pro synchronizaci.

Během procesu synchronizace synchronizační modul aktualizuje metaverse tak, aby odrážel změny, ke kterým došlo v prostoru konektoru, a aktualizuje prostor konektoru tak, aby odrážel změny, ke kterým došlo v metaverse.

Během procesu exportu synchronizační modul odešle změny, které jsou připraveny na pracovní objekty a které jsou označeny jako čekající export.

Následující obrázek znázorňuje, kde se jednotlivé procesy vyskytují, když informace o identitě proudí z jednoho připojeného zdroje dat do jiného.

![Arch6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Proces importu
Během procesu importu synchronizační modul vyhodnotí aktualizace informací o identitě. Synchronizační modul porovná informace o identitě přijaté z připojeného zdroje dat s informacemi o identitě o pracovníobjekt a určuje, zda pracovní objekt vyžaduje aktualizace. Pokud je nutné aktualizovat pracovní objekt s novými daty, pracovní objekt je označen jako čekající import.

Tím, že pracovní objekty v prostoru konektoru před synchronizací, synchronizační modul může zpracovat pouze informace o identitě, která se změnila. Tento proces poskytuje následující výhody:

* **Efektivní synchronizace**. Množství dat zpracovaných během synchronizace je minimalizováno.
* **Efektivní resynchronizace**. Můžete změnit způsob, jakým synchronizační modul zpracovává informace o identitě, aniž byste znovu připojili synchronizační modul ke zdroji dat.
* **Možnost zobrazit náhled synchronizace**. Můžete zobrazit náhled synchronizace a ověřit, zda jsou vaše předpoklady o procesu správy identit správné.

Pro každý objekt zadaný v konektoru synchronizační modul nejprve pokusí vyhledat reprezentaci objektu v prostoru konektoru konektoru. Synchronizační modul zkontroluje všechny pracovní objekty v prostoru spojnice a pokusí se najít odpovídající pracovní objekt, který má odpovídající atribut kotvy. Pokud žádný existující pracovní objekt nemá odpovídající atribut kotvy, synchronizační modul se pokusí najít odpovídající pracovní objekt se stejným rozlišujícím názvem.

Když synchronizační modul najde pracovní objekt, který odpovídá rozlišujícímu názvu, ale ne podle kotvy, dojde k následujícímu speciálnímu chování:

* Pokud objekt umístěný v prostoru spojnice nemá žádnou kotvu, synchronizační modul odebere tento objekt z prostoru spojnice a označí metaverse objekt, se který je propojen jako **opakování zřizování při dalším spuštění synchronizace**. Potom vytvoří nový objekt importu.
* Pokud objekt umístěný v prostoru spojnice má kotvu, pak synchronizační modul předpokládá, že tento objekt byl přejmenován nebo odstraněn v připojeném adresáři. Přiřadí dočasný, nový rozlišující název objektu prostoru spojnice tak, aby mohl zinscenovat příchozí objekt. Starý objekt se pak stane **přechodné**, čekání na konektor importovat přejmenování nebo odstranění k vyřešení situace.

Pokud synchronizační modul vyhledá pracovní objekt, který odpovídá objektu určenému v konektoru, určuje, jaký druh změn použít. Synchronizační modul může například přejmenovat nebo odstranit objekt v připojeném zdroji dat nebo může pouze aktualizovat hodnoty atributů objektu.

Pracovní objekty s aktualizovanými daty jsou označeny jako čekající na import. K dispozici jsou různé typy nevyřízených importů. V závislosti na výsledku procesu importu má pracovní objekt v prostoru spojnice jeden z následujících typů čekajících na import:

* **Žádné**. Nejsou k dispozici žádné změny žádného z atributů pracovního objektu. Synchronizační modul neoznačuje tento typ jako čekající import.
* **Přidat**. Pracovní objekt je nový objekt importu v prostoru spojnice. Synchronizační modul označí tento typ jako čekající import pro další zpracování v metaverse.
* **Aktualizovat**. Synchronizační modul najde odpovídající pracovní objekt v prostoru konektoru a označí tento typ jako čekající na import, aby aktualizace atributů mohly být zpracovány v metaverse. Aktualizace zahrnují přejmenování objektu.
* **Odstranit**. Synchronizační modul najde odpovídající pracovní objekt v prostoru konektoru a označí tento typ jako čekající na import, aby mohl být odstraněn spojený objekt.
* **Odstranit nebo přidat**. Synchronizační modul najde odpovídající pracovní objekt v prostoru konektoru, ale typy objektů se neshodují. V tomto případě je připravena změna odstranění přidání. Změna delete-add označuje synchronizačnímu modulu, že musí dojít k úplné resynchronizaci tohoto objektu, protože při změně typu objektu se na tento objekt vztahují různé sady pravidel.

Nastavením čekajícího stavu importu pracovního objektu je možné výrazně snížit množství dat zpracovaných během synchronizace, protože to umožňuje systému zpracovat pouze ty objekty, které mají aktualizovaná data.

### <a name="synchronization-process"></a>Proces synchronizace
Synchronizace se skládá ze dvou souvisejících procesů:

* Příchozí synchronizace, když je aktualizován obsah metaverse pomocí dat v prostoru konektoru.
* Odchozí synchronizace, když je aktualizován obsah prostoru konektoru pomocí dat v metaverse.

Pomocí informací připravených v prostoru konektoru vytvoří proces příchozí synchronizace v metaverse integrované zobrazení dat, která jsou uložena v připojených zdrojích dat. V závislosti na konfiguraci pravidel jsou agregovány všechny pracovní objekty nebo pouze objekty s čekajícími informacemi o importu.

Proces odchozí synchronizace aktualizuje export objektů při změně objektů metaverse.

Příchozí synchronizace vytvoří integrované zobrazení v metaverse informací o identitě, které jsou přijímány z připojených zdrojů dat. Synchronizační modul může kdykoli zpracovávat informace o identitě pomocí nejnovějších informací o identitě, které má z připojeného zdroje dat.

**Příchozí synchronizace**

Příchozí synchronizace zahrnuje následující procesy:

* **Provision** (také nazývá **projekce,** pokud je důležité odlišit tento proces od zřizování odchozí synchronizace). Synchronizační modul vytvoří nový metaverse objekt založený na pracovní objekt a propojí je. Provision je operace na úrovni objektu.
* **Připojte se**. Modul Synchronizace propojí pracovní objekt s existujícím objektem metaverse. Spojení je operace na úrovni objektu.
* **Importovat tok atributů**. Synchronizační modul aktualizuje hodnoty atributů objektu v metaverse, nazývané tok atributů. Import atribut tok je operace na úrovni atributu, která vyžaduje propojení mezi pracovní objekt a metaverse objektu.

Provision je jediný proces, který vytváří objekty v metaverse. Provision ovlivňuje pouze import objektů, které jsou odpojené objekty. Během zřizování synchronizační modul vytvoří metaverse objekt, který odpovídá typu objektu importu objektu a vytvoří propojení mezi oběma objekty, a tím vytvořit spojený objekt.

Proces spojení také vytvoří propojení mezi objekty importu a metaverse objektu. Rozdíl mezi spojení a ustanovení je, že proces spojení vyžaduje, aby objekt importu jsou propojeny s existující metaverse objektu, kde proces zřizování vytvoří nový metaverse objektu.

Synchronizační modul se pokusí spojit objekt importu k metaverse objektu pomocí kritérií, která jsou zadána v konfiguraci pravidla synchronizace.

Během zřizování a spojení procesů synchronizační modul propojí odpojený objekt metaverse objektu, takže jsou spojeny. Po dokončení těchto operací na úrovni objektu může modul synchronizace aktualizovat hodnoty atributů přidruženého metaverse objektu. Tento proces se nazývá tok atributu importu.

Import ovat atribut toku dochází u všech objektů importu, které nesou nová data a jsou propojeny s metaverse objektu.

**Odchozí synchronizace**

Odchozí synchronizace aktualizuje export objektů při změně metaverse objektu, ale není odstraněn. Cílem odchozí synchronizace je vyhodnotit, zda změny metaverse objekty vyžadují aktualizace pracovní objekty v prostoru spojnice. V některých případech změny mohou vyžadovat aktualizaci pracovních objektů ve všech prostorech spojnice. Pracovní objekty, které jsou změněny, jsou označeny jako čekající na export, což je vede k exportu objektů. Tyto exportní objekty jsou později během procesu exportu vytlačovány do připojeného zdroje dat.

Odchozí synchronizace má tři procesy:

* **Zřizování**
* **Zrušení zřízení**
* **Tok atributu exportu**

Zřizování a zrušení zřizování jsou operace na úrovni objektu. Zrušení zřízení závisí na zřizování, protože pouze zřizování můžete iniciovat. Zrušení zřízení se aktivuje, když zřizování odebere propojení mezi metaverse objektu a export objektu.

Zřizování se vždy aktivuje, když jsou změny použity na objekty v metaverse. Při provádění změn metaverse objekty, synchronizační modul můžete provádět některé z následujících úloh jako součást procesu zřizování:

* Vytvořte spojené objekty, kde je metaverse objekt propojen s nově vytvořeným objektem exportu.
* Přejmenujte spojený objekt.
* Odpojení propojení mezi metaverse objektu a pracovní objekty, vytvoření odpojeného objektu.

Pokud zřizování vyžaduje synchronizační modul k vytvoření nového objektu konektoru, pracovní objekt, ke kterému je objekt metaverse propojen, je vždy exportním objektem, protože objekt ještě neexistuje v připojeném zdroji dat.

Pokud zřizování vyžaduje synchronizační modul pro odpojení spojeného objektu, vytvoření odpojeného objektu se aktivuje zrušení zřízení. Proces zrušení zřízení odstraní objekt.

Při odstraňování zřízení odstranění exportu objektu fyzicky neodstraní objekt. Objekt je označen jako **odstraněn ,** což znamená, že operace odstranění je připravena na objekt.

Tok atributů exportu se vyskytuje také během procesu odchozí synchronizace, podobně jako tok atributu importu během příchozí synchronizace. Exportovat tok atributů dochází pouze mezi metaverse a export objektů, které jsou spojeny.

### <a name="export-process"></a>Proces exportu
Během procesu exportu synchronizační modul zkontroluje všechny exportní objekty, které jsou označeny jako čekající na export v prostoru konektoru, a potom odešle aktualizace do připojeného zdroje dat.

Synchronizační modul může určit úspěch exportu, ale nemůže dostatečně určit, že proces správy identit je dokončen. Objekty v připojeném zdroji dat lze vždy měnit jinými procesy. Vzhledem k tomu, že synchronizační modul nemá trvalé připojení k připojenému zdroji dat, nestačí provést předpoklady o vlastnostech objektu v připojeném zdroji dat pouze na základě úspěšného oznámení o exportu.

Proces v připojeném zdroji dat může například změnit atributy objektu zpět na původní hodnoty (to znamená, že připojený zdroj dat může přepsat hodnoty ihned po vytlačení dat synchronizačním motorem a úspěšně aplikovanév připojeného zdroje dat).

Synchronizační modul ukládá informace o stavu exportu a importu jednotlivých pracovních objektů. Pokud se hodnoty atributů, které jsou uvedeny v seznamu zahrnutí atributů, od posledního exportu změnily, umožňuje ukládání stavu importu a exportu odpovídajícím způsobem reagovat synchronizačním modulem. Synchronizační modul používá proces importu k potvrzení hodnot atributů, které byly exportovány do připojeného zdroje dat. Porovnání mezi importovanými a exportovanými informacemi, jak je znázorněno na následujícím obrázku, umožňuje synchronizačnímu modulu určit, zda byl export úspěšný nebo zda je třeba jej opakovat.

![Arch7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Například pokud synchronizační modul exportuje atribut C, který má hodnotu 5, do připojeného zdroje dat, uloží C = 5 v paměti stavu exportu. Každý další export na tento objekt má za následek pokus o export C = 5 do připojeného zdroje dat znovu, protože synchronizační modul předpokládá, že tato hodnota nebyla trvale použita na objekt (to znamená, pokud nebyla nedávno importována jiná hodnota z objektu připojeného zdroje dat). Paměť exportu je vymazána, když je během operace importu objektu přijata c=5.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [synchronizace Azure AD Connect.](how-to-connect-sync-whatis.md)

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

