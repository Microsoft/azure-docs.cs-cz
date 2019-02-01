---
title: 'Synchronizace Azure AD Connect: Pochopení architektury | Dokumentace Microsoftu'
description: Toto téma popisuje architekturu služby synchronizace Azure AD Connect a vysvětluje termíny používané.
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
ms.openlocfilehash: 1b2f82b4feea94c5c0ed732ff84856c96f46afc0
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55498068"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Synchronizace Azure AD Connect: Principy architektury
Toto téma popisuje základní architekturu pro synchronizaci Azure AD Connect. V mnoha aspektech se podobně jako jeho předchůdci MIIS 2003, ILM 2007 a FIM 2010. Synchronizace Azure AD Connect navazuje na těchto technologií. Pokud jste se seznámili s některým z těchto starší technologie, obsah tohoto tématu bude známými i. Pokud jste ještě synchronizace, toto téma je za vás. Je však není potřeba znát podrobnosti o tomto tématu k dosažení úspěchu při vytváření vlastního nastavení synchronizace Azure AD Connect (označované jako synchronizačního modulu v tomto tématu).

## <a name="architecture"></a>Architektura
Synchronizační modul vytváří integrované zobrazení objektů, které jsou uložené ve více připojených zdrojů dat a spravuje informace o identitě v těchto zdrojích dat. Toto integrované zobrazení se určuje podle informací o identitě z připojených zdrojů dat a sadu pravidel, které určují, jak zpracovat tyto informace načíst.

### <a name="connected-data-sources-and-connectors"></a>Připojené zdroje dat a konektory
Synchronizační modul zpracovává informace o identitě z různých datových úložišť, jako je Active Directory nebo do databáze SQL serveru. Každé úložiště dat, který slouží k uspořádání svá data ve formátu podobném databáze, který poskytuje metody standardní přístup k datům je potenciální Release candidate zdroje dat pro synchronizační modul. Úložiště dat, které jsou synchronizovány pomocí synchronizační modul se nazývají **připojené zdroje dat** nebo **připojení adresáře** (CD).

Synchronizační modul zapouzdřuje interakci s připojeného zdroje dat v rámci modul s názvem **konektor**. Každý typ zdroje dat má ke konkrétním konektorům. Konektor přeloží požadované operace do formátu srozumitelného připojeného zdroje dat.

Konektory provádět s připojeného zdroje dat volání rozhraní API k výměně informací o identitě (čtení a zápis). Je také možné přidat vlastní konektor pomocí rozhraní rozšiřitelného připojení. Následující obrázek znázorňuje, jak konektor připojí připojeného zdroje dat synchronizačního modulu.

![Arch1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Můžete v některém směru toku dat, ale jeho nelze současně probíhaly v obou směrech. Jinými slovy konektor můžete nakonfigurovat tak, aby data, které jsou předávány z připojeného zdroje dat pro synchronizační modul nebo synchronizačního modulu do zdroje dat, ale pouze jeden z těchto operací může dojít kdykoliv pro jeden objekt a atribut. Směr se může lišit pro různé objekty a pro jiné atributy.

Konfigurace konektoru, určit typy objektů, které se mají synchronizovat. Určení typů objektů definuje rozsah objektů, které jsou součástí procesu synchronizace. Dalším krokem je vybrat atributy pro synchronizaci, což se označuje jako seznam povolených položek atributů. Tato nastavení můžete změnit kdykoli v reakci na změny na obchodní pravidla. Při použití Průvodce instalací Azure AD Connect, tato nastavení se nakonfigurují automaticky.

Exportovat objekty do připojeného zdroje dat, seznam povolených položek atribut musí obsahovat alespoň minimální atributy vyžadované k vytvoření připojeného zdroje dat určitého typu objektu. Například **sAMAccountName** atribut musí obsahovat atribut seznam povolených položek pro export objekt uživatele do služby Active Directory, protože všechny objekty uživatelů ve službě Active Directory musí mít **sAMAccountName** definován atribut. Průvodce instalací nemá znovu, tato konfigurace za vás.

Pokud je připojený zdroj dat používá strukturálních součásti, jako je například oddíly nebo kontejnery pro uspořádání objektů, můžete omezit oblasti v připojených zdrojů dat, které se používají pro dané řešení.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Vnitřní struktura oboru názvů modul synchronizace
Obor názvů pro modul celý synchronizaci se skládá z dva obory názvů, které ukládají informace o identitě. Dané dva obory názvů jsou:

* V prostoru konektoru (CS)
* Úložiště metaverse (MV)

**Prostoru konektoru** je pracovní oblast, která obsahuje reprezentace určené objekty z připojeného zdroje dat a atributy určené v seznamu povolených položek atributů. Synchronizační modul používá v prostoru konektoru, můžete zjistit, co se změnilo v připojených zdrojů dat a příchozí změny. Synchronizační modul používá také v prostoru konektoru pro odchozí změny pro export do zdroje dat. Synchronizační modul udržuje prostoru jedinečných konektoru jako pracovní oblast pro jednotlivé konektory.

S použitím pracovní oblast, synchronizační modul zůstane v nezávisle na připojené zdroje dat a nemá vliv jejich dostupnost a usnadnění přístupu. V důsledku toho může zpracovat informace o identitě v čase s využitím dat v pracovní oblasti. Synchronizační modul může vyžádat pouze změny provedené v připojených zdrojů dat od poslední komunikace relace byla ukončena nebo push pouze ke změnám informace o identitě, který dosud nepřijal připojených zdrojů dat, což snižuje sítě provoz mezi synchronizačního modulu a připojené datové zdroje.

Kromě toho synchronizační modul uchovává stavové informace o všech objektech, zpracování v prostoru konektoru. Po přijetí nových dat synchronizační modul vždy vyhodnotí, zda data již byly synchronizovány.

**Metaverse** je úložiště, který obsahuje informace o agregovaných identitě z různých zdrojů dat, poskytuje jednotný pohled globální, a integrované všechny kombinované objektů. Podle informací o identitě, který je načten z připojené zdroje dat a sadu pravidel, která umožňují přizpůsobit proces synchronizace jsou vytvořeny objekty úložiště Metaverse.

Následující obrázek znázorňuje konektor obor názvů a názvů metaverse v rámci synchronizačního modulu.

![Arch2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Objekty identity modul synchronizace
Objekty v synchronizační modul jsou reprezentace buď objektů v připojených zdrojů dat nebo těchto objektů má integrované zobrazení, které modul synchronizace. Každý objekt synchronizační modul musí mít globálně jedinečný identifikátor (GUID). Identifikátory GUID zajištění integrity dat a výslovně vztahy mezi objekty.

### <a name="connector-space-objects"></a>Objekty prostoru konektoru
Pokud synchronizační modul komunikuje s připojeného zdroje dat, čte informace o identitě v připojených zdrojů dat a použije ji k vytvoření reprezentace objektu identity v prostoru konektoru. Nejde vytvořit nebo odstranit tyto objekty jednotlivě. Můžete však ručně odstranit všechny objekty v prostoru konektoru.

Všechny objekty v prostoru konektoru mít dva atributy:

* Globálně jedinečný identifikátor (GUID)
* Rozlišující název (DN)

Pokud připojeného zdroje dat objektu přiřadí jedinečný atribut, pak objekty v prostoru konektoru můžete taky nechat atributem ukotvení. Atribut ukotvení jednoznačně identifikuje objektů v připojených zdrojů dat. Synchronizační modul používá k vyhledání odpovídající reprezentaci tohoto objektu v připojených zdrojů dat ukotvení. Synchronizační modul předpokládá, že anchor objektu se nikdy nemění během životního cyklu objektu.

Řada konektory známé jedinečný identifikátor slouží k vytvoření ukotvení automaticky pro každý objekt po importu. Například používá konektor služby Active Directory **objectGUID** atribut ukotvení. Připojených zdrojů dat, které neposkytují jasně definované jedinečný identifikátor můžete určit generaci ukotvení jako součást konfigurace konektoru.

V tom případě ukotvení je sestaven z jednoho nebo více jedinečných atributy objektu typu, ani změny a který jednoznačně identifikuje objekt v prostoru konektoru (například číslo zaměstnance nebo ID uživatele).

Objekt prostoru konektoru může být jedna z následujících akcí:

* Pracovní objekt
* Zástupný text

### <a name="staging-objects"></a>Pracovní objekty
Pracovní objekt představuje instanci objektu určené typy z připojených zdrojů dat. Kromě rozlišující název a identifikátor GUID pracovní objekt má vždy hodnotu určující typ objektu.

Pracovní objekty, které se naimportovaly vždy mít hodnotu pro atribut ukotvení. Pracovní objekty, které byly nově zajišťovaný modulem synchronizace a se právě vytváří v připojených zdrojů dat nemají hodnotu pro atribut ukotvení.

Pracovní objekty také mít aktuální hodnoty atributů obchodní a provozní informace potřebné pro synchronizační modul provést proces synchronizace. Provozní informace zahrnují příznaky, které označují typ aktualizace, které jsou připravené na pracovní objekt. Pokud pracovní objekt obdržel nové informace o identitě z připojených zdrojů dat, která ještě nebyla zpracována, je objekt označený jako **čekajícího na import**. Pokud má pracovní objekt nové informace o identitě, který ještě nebyl byly vyexportovány do připojeného zdroje dat, je označena příznakem **čekající na export**.

Pracovní objekt může být objekt importu nebo exportu objektu. Synchronizační modul vytvoří objekt importu pomocí z připojeného zdroje dat byly přijaty informace o objektu. Když synchronizační modul obdrží informace o existenci nový objekt, který odpovídá jednomu z typů objektů vybraných v konektoru, vytvoří jako reprezentace objektu v připojených zdrojů dat objektu importu v prostoru konektoru.

Import objektu, který představuje objekt v připojených zdrojů dat na následujícím obrázku.

![Arch3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

Synchronizační modul vytváří objekt exportu s použitím informací o objektu v úložišti metaverse. Exportovat objekty jsou exportovány do zdroje dat během příští relaci komunikace. Z pohledu synchronizační modul exportovat objekty neexistují v připojených zdrojů dat ještě. Proto atribut ukotvení objektu export není k dispozici. Po přijetí objekt z synchronizační modul, připojeného zdroje dat vytvoří jedinečnou hodnotu pro atribut ukotvení objektu.

Následující obrázek znázorňuje, jak je vytvořen objekt exportu s použitím informací o identitě v úložišti metaverse.

![Arch4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

Synchronizační modul potvrdí export objektu o opakovaném importování z připojeného zdroje dat objektu. Při exportu objektů jsou import objektů při synchronizační modul je obdrží při dalším importu z tohoto připojeného zdroje dat.

### <a name="placeholders"></a>Zástupné symboly
Synchronizační modul používá k ukládání objektů s plochým oborem názvů. Ale některé zdroje dat jako je Active Directory pomocí hierarchického oboru názvů. Získat informace z hierarchického oboru názvů s plochým oborem názvů, synchronizační modul používá zástupné symboly pro zachování v hierarchii.

Každý zástupný text představuje součást (například s organizační jednotkou) objektu hierarchického oboru názvů, který ještě nebyl importován synchronizační modul, ale je potřeba vytvořit hierarchického oboru názvů. Probírají mezery vytvořené odkazy v připojených zdrojů dat na objekty, které nejsou pracovní objekty v prostoru konektoru.

Zástupné symboly synchronizační modul také používá k ukládání odkazované objekty, které dosud nebyl importován. Například, pokud sync je nakonfigurovaná pro atribut manager pro *Abbie Spencer* objektu a přijatá hodnota je objekt, který nebyl importované dosud, například *CN Lee Sperry, CN = = Users, DC = fabrikam, DC = com* , správce informace jsou uloženy jako zástupné symboly v prostoru konektoru. Pokud je objekt správce později importovat, zástupný objekt přepíše pracovní objekt představující správce.

### <a name="metaverse-objects"></a>Objekty úložiště Metaverse
Objekt úložiště metaverse obsahuje agregované zobrazení tohoto synchronizačního modulu má pracovní objektů v prostoru konektoru. Synchronizační modul vytváří objekty úložiště metaverse podle informací uvedených v import objektů. Několik objektů prostor konektoru nesmí být propojení objektu úložiště metaverse jednu, ale objekt prostoru konektoru, nelze ho propojit s více než jeden objekt úložiště metaverse.

Objekty úložiště Metaverse nelze ručně vytvořené ani odstranit. Synchronizační modul automaticky odstraní metaverse objekty, které nemají odkaz na libovolný objekt prostoru konektoru v prostoru konektoru.

K mapování objektů v rámci připojeného zdroje dat na odpovídající typ objektu v úložišti metaverse, synchronizační modul poskytuje rozšiřitelný schématu s předdefinovanou sadu typů objektů a přidružených atributů. Můžete vytvořit nové typy objektů a atributů pro objekty úložiště metaverse. Atributy mohou být jednoho nebo více hodnot a typy atributů může být řetězce, odkazy, čísla a logické hodnoty.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Vztahy mezi pracovním a objekty úložiště metaverse
V rámci oboru názvů modul Synchronizace tok dat zajišťuje vztah mezi pracovní a objekty úložiště metaverse. Je volána pracovní objekt, který je propojený s objektu úložiště metaverse **připojený k objektu** (nebo **objekt konektoru**). Je volána pracovní objekt, který není přidružený k objektu úložiště metaverse **odebrán objekt** (nebo **odpojovače objekt**). Podmínky připojený a jíž se dává přednost před nelze zaměňovat s využitím konektorů za import a export dat z připojený adresář.

Zástupné symboly jsou nikdy připojeného k objektu úložiště metaverse

Objekt připojené k doméně se skládá z pracovní objekt a jeho propojené vztah k objektu jednoho úložiště metaverse. Připojených objektů se používají k synchronizaci hodnoty atributů mezi objekt prostoru konektoru a objektu úložiště metaverse.

Když pracovní objekt se stane připojené k doméně objekt během synchronizace, může tok atributů mezi pracovní objekt a objektu úložiště metaverse. Tok atributů je obousměrný a se konfiguruje pomocí pravidel atribut import a export atribut.

Objekt prostoru jeden konektor lze propojit pouze jeden objekt úložiště metaverse. Ale každého objektu úložiště metaverse nesmí být propojení více místa na objekty konektoru ve stejných nebo v různých konektor mezery, jak je znázorněno na následujícím obrázku.

![Arch5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Propojené vztah mezi pracovní objekt a objektu úložiště metaverse je trvalé a lze odebrat pouze pomocí pravidel, která jste zadali.

Objekt rozděleným je pracovní objekt, který není propojený s libovolného objektu úložiště metaverse. Atribut, který hodnoty objektu rozděleným nejsou zpracovány žádné další v rámci úložiště metaverse. Atribut hodnoty odpovídajícího objektu v připojených zdrojů dat nejsou aktualizovány modulem synchronizace.

Pomocí rozděleným objektů můžete ukládat informace o identitě v synchronizační modul a později ji zpracovat. Zachovat pracovní objekt jako objekt rozděleným v prostoru konektoru má mnoho výhod. Protože systém už připravil požadované informace o tomto objektu, není nutné vytvořit reprezentaci tohoto objektu znovu při další importu z připojených zdrojů dat. Tímto způsobem synchronizační modul má úplný snímek zdroje dat vždy i v případě, že neexistuje žádná aktivní připojení ke zdroji dat. Rozděleným objekty mohou být převedeny do připojených objektů a naopak, v závislosti na zadaných pravidel.

Import objektu se vytvoří jako objekt rozděleným. Export objektu musí být připojený k objektu. Logika systému vynutí toto pravidlo a odstraní každý export objekt, který není připojený k objektu.

## <a name="sync-engine-identity-management-process"></a>Procesu synchronizace modul identity management
Proces správy identit řídí způsob, jakým aktualizuje informace o identitě mezi různé připojené datové zdroje. Správa identit probíhá tři procesy:

* Import
* Synchronizace
* Export

Během procesu importu synchronizační stroj vyhodnotí příchozí informace o identitách z připojeného zdroje dat. Když se zjistí změny, se vytvoří nový pracovní objekty nebo existující pracovní objekty v prostoru konektoru pro synchronizaci aktualizací.

Během procesu synchronizace synchronizační modul aktualizuje metaverse tak, aby odrážely změny, ke kterým došlo v prostoru konektoru a aktualizuje v prostoru konektoru tak, aby odrážely změny, ke kterým došlo v úložišti metaverse.

Během procesu exportu synchronizační modul přenáší změny, které jsou připravené na pracovní objekty a, které jsou označeny jako čekající na export.

Následující obrázek ukazuje, kde každý z procesů vyskytuje se jako toky identity informace z jednoho zdroje dat do jiného.

![Arch6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Proces importu
Během procesu importu synchronizační stroj vyhodnotí aktualizace informací o identitě. Synchronizační modul porovná informace o identitě přijatých z připojeného zdroje dat s informací o identitě o pracovní objektu a určuje, zda pracovní objekt vyžaduje aktualizace. Pokud je potřeba aktualizovat pracovní objekt s novými daty, pracovní objekt označen jako čekajícího na import.

Podle pracovní objekty v prostoru konektoru před synchronizací, synchronizační modul může zpracovat pouze informací o identitě, které se změnily. Tento postup nabízí následující výhody:

* **Efektivní synchronizace**. Minimalizovat objem dat zpracovaných během synchronizace.
* **Efektivní resynchronizace**. Můžete změnit způsob, jak synchronizační modul zpracuje informace o identitě bez opětovné připojení ke zdroji dat synchronizačního modulu.
* **Možnost vyzkoušet si synchronizace**. Můžete zobrazit náhled synchronizace ověřte správnost předpokladů o procesu správy identit.

Pro každý objekt určený v konektoru jak reprezentaci objektu v prostoru konektoru Connector poprvé pokusí synchronizačního modulu. Synchronizační modul zkontroluje všechny pracovní objekty v prostoru konektoru a pokusí se najít odpovídající pracovní objekt, který má odpovídající atribut ukotvení. Žádné existující pracovní objekt nemá odpovídající atribut ukotvení, synchronizační modul se pokusí najít odpovídající pracovní objekt s rozlišujícím názvem.

Když se synchronizační modul najde pracovní objekt, který odpovídá rozlišující název, ale ne ukotvení, dojde k následující zvláštní chování:

* Pokud není dostupná žádná kotva nemá objekt umístěný v prostoru konektoru, pak synchronizační modul odebere tento objekt prostoru konektoru a označí objekt úložiště metaverse spojený s jako **opakujte zřizování při další synchronizaci spustit**. Potom vytvoří nový objekt importu.
* Pokud má objekt nachází v prostoru konektoru kotva, synchronizační modul se předpokládá, že tento objekt obsahuje buď bylo přejmenováno nebo odstraněno v připojeném adresáři. Tak, aby ho můžete připravit, příchozí objektu přiřadí dočasné, nové rozlišující název pro objekt prostoru konektoru. Původní objekt se pak stane **přechodné**, čeká konektor pro import přejmenování nebo odstranění k vyřešení situace.

Pokud synchronizační modul vyhledává pracovní objekt, který odpovídá objekt určený v konektoru, určuje, jaký druh změn k použití. Synchronizační modul může přejmenování nebo odstranění objektu v připojených zdrojů dat nebo může aktualizovat pouze hodnoty atributů objektu.

Pracovní objekty s aktualizovanými daty jsou označené jako nedokončené importu. Různé typy čekající importy jsou k dispozici. V závislosti na výsledek procesu importu pracovní objekt v prostoru konektoru má jednu z následujících čekající import typů:

* **Žádný**. Žádné změny atributy pracovní objekt jsou k dispozici. Synchronizační modul neoznačí jako čekajícího na import tohoto typu.
* **Přidat**. Pracovní objekt je objekt importu v prostoru konektoru. Synchronizační modul označí tento typ jako čekajícího na import pro další zpracování v úložišti metaverse.
* **Aktualizace**. Synchronizační modul najde odpovídající pracovní objekt v prostoru konektoru a označí tento typ jako čekajícího na import tak, aby aktualizace atributů lze zpracovat v úložišti metaverse. Aktualizace zahrnují přejmenování objektu.
* **Odstranit**. Synchronizační modul najde odpovídající pracovní objekt v prostoru konektoru a označí tento typ jako čekajícího na import tak, aby připojený k objektu je odstranit.
* **Odstranit nebo přidat**. Synchronizační modul najde odpovídající pracovní objekt v prostoru konektoru, ale nejsou shodné typy objektů. V tomto případě na odstranění přidat připravené změny. A delete přidat úpravu označuje synchronizačního modulu, že dokončení opětovné synchronizace tohoto objektu musí dojít, protože různé sady pravidel použít pro tento objekt, když typ objektu změny.

Nastavením stavu čekajícího importu pracovní objekt je možné výrazně snížit objem dat zpracovaných během synchronizace, protože tak učiníte, umožníte systém zpracovávat pouze ty objekty, které mají aktualizovaná data.

### <a name="synchronization-process"></a>Procesu synchronizace
Synchronizace se skládá ze dvou souvisejících procesů:

* Synchronizace příchozích dat, když se aktualizuje obsah úložišti metaverse pomocí dat v prostoru konektoru.
* Odchozí synchronizace, když se aktualizuje obsah v prostoru konektoru s využitím dat v úložišti metaverse.

S použitím informací o připravené v prostoru konektoru, vytvoří proces synchronizace příchozích dat v úložišti metaverse integrované zobrazení dat, která je uložena v připojené zdroje dat. Všechny pracovní objekty nebo pouze ty s informacemi o čekajícího importu se agregují, v závislosti na konfiguraci pravidla.

Proces aktualizace odchozí synchronizace exportovat objekty při změně objektů úložiště metaverse.

Synchronizace příchozích dat vytvoří integrované zobrazení v úložišti metaverse přijatá z připojených zdrojů informací o identitě. Synchronizační modul může zpracovat informace o identitě v každém okamžiku pomocí nejnovějších informací o identitě, kterou má z připojených zdrojů dat.

**Synchronizace příchozích dat**

Synchronizace příchozích dat zahrnuje následující procesy:

* **Zřízení** (také nazývané **projekce** Pokud je důležité rozlišovat tento proces od odchozí synchronizace zřizování). Synchronizační modul vytvoří nový objekt úložiště metaverse podle pracovní objekt a propojí je. Zřízení je operace na úrovni objektů.
* **Připojte se k**. Synchronizační modul propojí pracovní objekt do existujícího objektu úložiště metaverse. Spojení je operace na úrovni objektů.
* **Importovat tok atributů**. Hodnoty atributů, volá tok atributů objektu v úložišti metaverse aktualizuje modul synchronizace. Import toku atributů je úroveň atributu operace, která vyžaduje odkaz mezi pracovní objekt a objektu úložiště metaverse.

Zřízení je jediný proces, který vytvoří objekty v úložišti metaverse. Zřízení ovlivňuje pouze import objektů, které jsou odpojené objekty. Během zřizování vytvoří modul synchronizace objektu úložiště metaverse, který odpovídá objektu typu importovaného objektu a vytvoří propojení mezi oba objekty, tedy vytvoření připojeného k objektu.

Proces připojení k také vytvoří propojení mezi import objektů a objektu úložiště metaverse. Rozdíl mezi spojení a zřizování je, že proces připojení vyžaduje, aby objekt importu jsou propojeny s existující objekt úložiště metaverse, kde proces zřízení vytvoří nový objekt úložiště metaverse.

Synchronizační stroj se pokusí připojit objekt importu do objektu úložiště metaverse pomocí kritéria, která je určená v konfiguraci synchronizačního pravidla.

Během procesu zřizování a zapojte se synchronizační modul odkazuje objekt rozděleným na objektu úložiště metaverse, díky kterým jsou připojené k. Po dokončení těchto operací na úrovni objektů, můžete aktualizovat synchronizační modul hodnoty atributů objektu přidruženého úložiště metaverse. Tento proces se nazývá toku atributu importu.

Import toku atributů probíhá na všechny objekty importu, které obsahují nová data a jsou propojeny s objektu úložiště metaverse.

**Odchozí synchronizace**

Aktualizace odchozí synchronizace exportovat objekty, když objektu úložiště metaverse změnit, ale není odstraněn. Cílem odchozí synchronizace je k vyhodnocení, zda změny do úložiště metaverse objektů vyžadují aktualizaci pracovní objekty v prostor konektoru. V některých případech se může vyžadovat změny této pracovní aktualizovat objekty ve všech prostor konektoru. Pracovní objekty, které se mění se označí jako čekající na export, díky kterým exportovat objekty. Tyto exportu objektů se později vynuceně instalují do zdroje dat během procesu exportu.

Odchozí synchronizace má tři procesy:

* **Zřizování**
* **Zrušení zřízení**
* **Exportovat tok atributů**

Zřizování a zrušení zřizování jsou operace na úrovni objektů. Zrušení zřízení závisí na zřizování, protože pouze zřizování můžete spustit ho. Zrušení zřízení se aktivuje při zřizování odebere propojení mezi objektu úložiště metaverse a export objektu.

Zřizování se vždy aktivuje v případě změny se použijí k objektům v úložišti metaverse. Při změně objektů v metaverse, synchronizačního modulu můžete provádět následující úlohy jako součást procesu zřizování:

* Vytvoření připojených objektů, kde je objekt úložiště metaverse propojeny s nově vytvořený export objektu.
* Přejmenování objektu připojené k doméně.
* Odpojování od propojení mezi objektu úložiště metaverse a přípravu objektů, vytváření samostatném objektu.

Pokud zřizování vyžaduje synchronizační modul, chcete-li vytvořit nový objekt konektoru, pracovní objekt, se kterým je spojen objektu úložiště metaverse je vždy export objektu, protože objekt v připojených zdrojů dat ještě neexistuje.

Zrušení zřízení se aktivuje, pokud zřizování vyžaduje synchronizační modul pro odpojování od objektu připojené k doméně, vytváření samostatném objektu. Proces zrušení zřízení odstraní objekt.

Během rušení zřízení se odstraněním objektu export nedojde k odstranění fyzicky objektu. Objekt je označena příznakem **odstranit**, což znamená, že je u objektu připravené operaci odstranění zopakovat.

Toku atributů exportu také vyvolá se během procesu odchozí synchronizace, podobným způsobem, že během synchronizace příchozích dat dojde k toku atributu importu. Toku atributů exportu nastávají jenom mezi objekty úložiště metaverse a export, které jsou připojené.

### <a name="export-process"></a>Proces exportu
Během procesu exportu synchronizační modul prozkoumá všechny exportovat objekty, které jsou označeny jako čekající na export v prostoru konektoru a potom pošle aktualizace do zdroje dat.

Synchronizační modul může zjistit úspěch export, ale nelze dostatečně určit, dokončení procesu správy identit. Objekty v připojených zdrojů dat můžete kdykoli změnit jinými procesy. Vzhledem k tomu, že synchronizační modul nemá trvalé připojení ke zdroji dat, není dostatečná k vytvářet předpoklady o vlastnosti objektu ve zdroji dat založen pouze na oznámení o úspěšném exportu.

Například může proces v připojených zdrojů dat změnit zpět na původní hodnoty atributů objektu (to znamená, připojeného zdroje dat by mohl přepsat hodnoty ihned po data jsou vynuceně instalují modulem synchronizace a úspěšně použila v zdroj dat).

Ukládá modul Synchronizace exportovat a importovat informace o každé pracovní objekt stavu. Pokud od poslední export změní hodnoty atributů, které jsou uvedeny v seznamu povolených položek atributů, skladování import a export stav umožňuje synchronizační modul reagovat odpovídajícím způsobem. Synchronizační modul používá pro potvrzení hodnoty atributů, které byly vyexportovány do připojeného zdroje dat proces importu. Porovnání mezi službou importované a exportované informace, jak je znázorněno na následujícím obrázku, umožňuje synchronizační modul k určení, zda export proběhl úspěšně, nebo jestli je potřeba opakovat.

![Arch7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Například pokud synchronizační modul exportuje atribut C, který má hodnotu 5, do zdroje dat, uloží C = 5 v jeho export stavu paměti. Každý další export na tento objekt výsledky při pokusu o export do zdroje dat C = 5 znovu vzhledem k tomu, že synchronizační modul se předpokládá, že tato hodnota nebyla trvale použita na objekt (to znamená, pokud jiná hodnota naimportovaná nedávno z připojený zdroj dat). Export paměti se vymaže při přijetí C = 5 během operace importu v objektu.

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](how-to-connect-sync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

