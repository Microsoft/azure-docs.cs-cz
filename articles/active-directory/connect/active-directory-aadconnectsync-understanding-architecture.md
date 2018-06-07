---
title: 'Synchronizace Azure AD Connect: Principy architekturu | Microsoft Docs'
description: Toto téma popisuje architekturu synchronizace Azure AD Connect a vysvětluje pojmy použít.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 079dfe772e6c189c0e81ea7af16d11a1c40f3ebe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593482"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Synchronizace Azure AD Connect: Principy architektura
Toto téma popisuje základní architekturu pro synchronizaci Azure AD Connect. V mnoha aspektům se podobně jako jeho předchůdci serveru MIIS 2003 ILM 2007 a FIM 2010. Synchronizace Azure AD Connect je vývoj těchto technologií. Pokud jste obeznámeni s žádným z těchto starších technologií, obsah tohoto tématu bude povědomé také. Pokud jste ještě synchronizace, pak toto téma je pro vás. Je ale není potřeba znát podrobnosti o něm být úspěšné při přizpůsobení synchronizace služby Azure AD Connect (nazývané synchronizační modul v tomto tématu).

## <a name="architecture"></a>Architektura
Synchronizační modul ucelený přehled objekty, které jsou uložené v několika připojených zdrojů dat vytváří a spravuje informace o identitě v těchto zdrojů dat. Toto integrované zobrazení je určen podle informací o identitě z připojených zdrojů dat a sada pravidel, které určují, jak zpracovávat tyto informace načíst.

### <a name="connected-data-sources-and-connectors"></a>Konektory a připojených zdrojů dat
Synchronizační modul zpracovává informace o identitě z různých datových úložišť, jako je například služby Active Directory nebo databázi serveru SQL Server. Každý dat úložiště, který slouží k uspořádání jeho data ve formátu databázového typu, který poskytuje metody standardní přístup k datům je potenciální kandidátem zdroje dat pro synchronizační modul. Úložiště dat, které jsou synchronizovány pomocí synchronizační modul se nazývají **připojení zdroje dat** nebo **připojení adresáře** (CD).

Synchronizační modul zapouzdří interakci s připojeného zdroje dat v rámci modul s názvem **konektor**. Každý typ připojeného zdroje dat má konkrétní konektor. Konektor překládá požadované operace do formátu, který připojeného zdroje dat rozumí.

Konektory provádět volání rozhraní API pro výměnu informací o identitě (čtení a zápisu) s připojeného zdroje dat. Je také možné přidat vlastní konektor pomocí rozhraní extensible připojení. Následující obrázek znázorňuje, jak konektor připojí připojeného zdroje dat synchronizačního modulu.

![Arch1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

V obou směrech můžete toku dat, ale jeho nelze současně toku v obou směrech. Jinými slovy konektor můžete nakonfigurovat tak, aby data, které jsou předávány z připojeného zdroje dat pro synchronizační modul nebo synchronizační modul do připojeného zdroje dat, ale jenom některé z těchto operací může nastat v jednom okamžiku pro jeden objekt a atribut. Směr se může lišit pro různé objekty a pro různé atributy.

Chcete-li nakonfigurovat konektor, zadáte typy objektů, které chcete synchronizovat. Určení typů objektů definuje rozsah objektů, které jsou součástí procesu synchronizace. Dalším krokem je vybrat atributy, které se synchronizuje, což se označuje jako seznam povolených atribut. Tato nastavení můžete změnit kdykoli v reakci na změny na obchodní pravidla. Pokud použijete Průvodce instalací služby Azure AD Connect, tato nastavení jsou konfigurována pro vás.

Pokud chcete exportovat objekty do připojeného zdroje dat, seznam zahrnutí atributů musí obsahovat alespoň minimální atributy vyžadované k vytvoření připojeného zdroje dat určitého typu objektu. Například **sAMAccountName** atribut musí být obsažen v seznamu atributů zahrnutí exportovat objekt uživatele do služby Active Directory, protože všechny uživatelské objekty ve službě Active Directory musí mít **sAMAccountName** definován atribut. Průvodce instalací znovu, nemá tato konfigurace za vás.

Pokud připojeného zdroje dat používá strukturální komponenty, například oddíly nebo kontejnery pro uspořádání objektů, můžete omezit oblastí v připojených zdrojů dat, které se používají pro danou řešení.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Vnitřní struktura oboru názvů synchronizačního modulu
Obor názvů celý synchronizační modul se skládá ze dvou obory názvů, které obsahují informace o identitě. Jsou dva obory názvů:

* Prostoru konektoru (CS)
* Úložiště metaverse (MV)

**Prostoru konektoru** je pracovní oblasti, která obsahuje reprezentace určené objektů z připojeného zdroje dat a atributy uvedený v seznamu povolených atribut. Synchronizační modul používá prostoru konektoru, chcete-li zjistit, co se změnilo v připojených zdrojů dat a příprava příchozí změny. Synchronizační modul se používá také prostoru konektoru k dvoufázové odchozí změny pro export do připojeného zdroje dat. Synchronizační modul udržuje prostoru odlišné konektoru jako pracovní oblast pro každý konektor.

Pomocí pracovní oblasti, synchronizační modul zůstává nezávisle připojených zdrojů dat a nemá vliv jejich dostupnost a usnadnění přístupu. V důsledku toho může zpracovat informace o identitě kdykoli na základě dat v pracovní oblasti. Synchronizační modul může požádat o pouze změny provedené v připojených zdrojů dat od posledního komunikace relace byla ukončena nebo nabízené se pouze změny informace identity, které neobdržel ještě připojeného zdroje dat, což snižuje síťový provoz mezi synchronizační modul a připojeného zdroje dat.

Kromě toho synchronizační modul ukládá informace o všech objektů, zpracuje v prostoru konektoru stavu. Když se nepřijmou nová data, synchronizační modul vždy vyhodnotí, zda data již byly synchronizovány.

**Úložiště metaverse** je oblast úložiště, který obsahuje informace o agregované identity z více připojených zdrojů dat, poskytuje jednotný pohled globální, integrované součet všech objektů. Úložiště Metaverse objekty jsou vytvořené na základě informací identity, které se načítají ze zdroje dat připojený a sada pravidel, které vám umožní přizpůsobit proces synchronizace.

Následující obrázek znázorňuje obor názvů prostor konektoru a úložiště metaverse obor názvů v rámci synchronizační modul.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Objekty identity modul synchronizace
Objekty v synchronizační modul jsou reprezentace buď objektů v připojených zdrojů dat nebo integrované zobrazení, které synchronizovat modul má těchto objektů. Každý objekt synchronizační modul musí mít globálně jedinečný identifikátor (GUID). Identifikátory GUID zajištění integrity dat a express relace mezi objekty.

### <a name="connector-space-objects"></a>Objekty prostoru konektoru
Pokud synchronizační modul komunikuje s připojeného zdroje dat, čte informace o identitě v připojených zdrojů dat a použije tyto informace k vytvořit reprezentaci objektu identity v prostoru konektoru. Nelze vytvořit nebo odstranit tyto objekty jednotlivě. Můžete však ručně odstranit všechny objekty v prostoru konektoru.

Všechny objekty v prostoru konektoru mají dva atributy:

* Globálně jedinečný identifikátor (GUID)
* Rozlišující název (DN)

Pokud připojeného zdroje dat přiřadí jedinečný atribut k objektu, pak objekty v prostoru konektoru může mít atributem ukotvení. Atribut kotvy jednoznačně identifikuje objektů v připojených zdrojů dat. Synchronizační modul používá ukotvení v připojených zdrojů dat najít odpovídající reprezentace tohoto objektu. Synchronizační modul se předpokládá, že ukotvení objektu se změní nikdy průběhu životnosti objektu.

Mnoho konektorů pomocí známých jedinečný identifikátor vygenerujte anchor automaticky pro každý objekt při jeho importu. Například používá konektor služby Active Directory **objectGUID** atribut pro element anchor. Pro připojené datových zdrojů, které neposkytují jasně definované jedinečný identifikátor můžete určit generaci ukotvení jako součást konfigurace konektoru.

V tom případě ukotvení vychází z jednoho nebo více jedinečné atributy objektu typ, ani mění a která jednoznačně identifikuje objekt v prostoru konektoru (například číslo zaměstnance nebo ID uživatele).

Objekt místa konektoru může být jedna z následujících akcí:

* Pracovní objekt
* Zástupný text

### <a name="staging-objects"></a>Pracovní objekty
Objekt pracovní představuje instanci typy určené objektů z připojeného zdroje dat. Kromě identifikátor GUID a rozlišující název pracovní objekt má vždy hodnotu, která určuje typ objektu.

Pracovní objekty, které byly naimportovány vždy mít hodnotu pro atribut ukotvení. Pracovní objekty, které byly nově zajištěny synchronizační modul a probíhá vytváření v připojených zdrojů dat nemají hodnotu pro atribut ukotvení.

Pracovní objekty také provádění aktuálních hodnot atributů obchodní a provozní informace potřebné pro synchronizační modul provést proces synchronizace. Provozní informace zahrnují příznaky, které označují typ aktualizací, které jsou připravené na pracovní objekt. Pokud objekt pracovní obdržel nové informace o identitě z připojeného zdroje dat, ještě nebyla zpracována, objekt je označena příznakem **čekajícího na import**. Pokud pracovní objekt má nové informace identity, který ještě nebyl byly vyexportovány do připojeného zdroje dat, je označení **čekající na vyřízení export**.

Pracovní objekt může být objekt import nebo export objektu. Synchronizační modul vytvoří objekt import pomocí přijaté z připojeného zdroje dat informace o objektu. Informace o existenci nový objekt, který odpovídá jednomu z vybraných v konektoru typů objektu přijetí synchronizační modul vytvoří jako reprezentace objektu v připojených zdrojů dat objekt importu v prostoru konektoru.

Následující obrázek znázorňuje, import objekt, který reprezentuje objekt v připojených zdrojů dat.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

Synchronizační modul vytvoří objekt export pomocí informací o objektu v úložišti metaverse. Export objektů jsou vyexportovány do připojeného zdroje dat během relace další komunikaci. Z pohledu synchronizační modul export objekty neexistují v připojených zdrojů dat ještě. Atribut kotvy pro objekt export proto není k dispozici. Po přijetí objekt z synchronizační modul, připojeného zdroje dat vytvoří jedinečnou hodnotu pro atribut kotvy objektu.

Následující obrázek znázorňuje, jak je vytvořen objekt export pomocí informací o identitě v úložišti metaverse.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

Synchronizační modul potvrdí podle opakovaném importování objekt z připojeného zdroje dat, export objektu. Export objektů po synchronizační modul je obdrží při další importu z tohoto připojeného zdroje dat, se import objektů.

### <a name="placeholders"></a>Zástupné symboly
Synchronizační modul používá plochý obor názvů pro uložení objektů. Ale některé připojených zdrojů dat jako je Active Directory pomocí hierarchického oboru názvů. Transformace informace z hierarchického oboru názvů na plochý obor názvů, synchronizační modul používá zástupné symboly pro zachování v hierarchii.

Každý zástupný symbol představuje komponentu hierarchické název objektu, který nebyl importován do synchronizační modul, ale je potřeba vytvořit hierarchické název (například organizační jednotku). Jejich vyplnění mezer vytvořené odkazy v připojených zdrojů dat na objekty, které nejsou pracovní objekty v prostoru konektoru.

Synchronizační modul se používá také zástupné symboly pro uložení odkazovaného objektů, které dosud nebyl importován. Například, pokud sync je nakonfigurovaná zahrnout atribut správce pro *Abbie Spencer* objektu a přijatá hodnota je objekt, který ještě nebyl dosud, například importované *CN Lee Sperry, CN = Users, DC = fabrikam, DC = = com*, správce informace jsou uloženy jako zástupné symboly v prostoru konektoru. Pokud je objekt správce později importovat, je objekt zástupný symbol přepsány pracovní objekt, který reprezentuje správce.

### <a name="metaverse-objects"></a>Objekty úložiště Metaverse
Objekt úložiště metaverse obsahuje souhrnné zobrazení této synchronizační modul se pracovní objekty v prostoru konektoru. Synchronizační modul vytvoří úložiště metaverse objekty podle informací uvedených v import objektů. Několik objekty místa konektoru může být propojený objekt jednoho úložiště metaverse, ale objekt prostoru konektoru, nelze ho propojit s více než jednoho objektu metaverse.

Úložiště Metaverse objekty nelze ručně vytvořit nebo odstranit. Synchronizační modul se automaticky odstraní metaverse objekty, které nemají žádný odkaz na libovolného objektu prostoru konektoru v prostoru konektoru.

Synchronizační modul k mapování objektů v rámci připojeného zdroje dat na odpovídající typ objektu v úložišti metaverse, poskytuje rozšiřitelné schéma s předdefinovanou sadu typy objektů a přidružených atributů. Můžete vytvořit nové typy objektů a atributy pro objekty úložiště metaverse. Atributy mohou být jednoho nebo více hodnot a typy atributů může být řetězce, odkazy, čísla a logické hodnoty.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Vztahy mezi pracovní objekty a objekty úložiště metaverse
V rámci oboru názvů synchronizační modul se aktivuje tok dat odkaz vztah mezi pracovní objekty a úložiště metaverse objekty. Je volána pracovní objekt, který bude propojen objektu úložiště metaverse **připojený k objektu** (nebo **objekt konektoru**). Je volána pracovní objekt, který není přidružený k objektu úložiště metaverse **odpojování objekt** (nebo **odpojovače objekt**). Podmínky připojený a odpojování jsou preference nelze zaměňovat s konektory zodpovědná za importu a exportu dat z připojeného adresáře.

Zástupné symboly nikdy propojené s objektu úložiště metaverse

Připojený k objektu obsahuje pracovní objekt a jeho propojené vztah k objektu jednoho úložiště metaverse. Připojených objektů slouží k synchronizaci hodnot atributů mezi objektem prostor konektoru a objektu úložiště metaverse.

Když pracovní objekt během synchronizace nebude připojený k objektu, atributy může obtékat mezi pracovní objekt a objekt úložiště metaverse. Tok atributů obousměrný a se konfiguruje pomocí pravidel atribut import a export atribut pravidel.

Objekt místo jeden konektor můžete propojit jenom jednoho objektu metaverse. Ale každého objektu úložiště metaverse lze propojit s více místa na objekty konektoru ve stejné nebo v různých konektor prostorech, jak je znázorněno na následujícím obrázku.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

Propojené vztah mezi pracovní objekt a objektu úložiště metaverse je trvalé a lze odebrat pouze pomocí pravidel, která jste zadali.

Objekt rozděleným je pracovní objekt, který není přidružený k žádné objektu úložiště metaverse. Atribut, který hodnoty objekt rozděleným nejsou zpracovány žádné další v úložišti metaverse. Hodnoty atributu odpovídající objektu v připojených zdrojů dat se neaktualizují synchronizační modul.

Pomocí rozděleným objektů můžete ukládat informace o identitě v synchronizační modul a později ji zpracovat. Zachovat pracovní objekt jako objekt rozděleným v prostoru konektoru má mnoho výhod. Vzhledem k tomu, že systém má již dvoufázové instalace požadované informace o tomto objektu, není nutné vytvořit reprezentaci tohoto objektu znovu při dalším importu z připojeného zdroje dat. Tímto způsobem, synchronizační modul má kompletní snímek připojeného zdroje dat, vždy i v případě, že není aktuální připojení ke zdroji dat připojené. Rozděleným objekty mohou být převedeny do připojených objektů a naopak a v závislosti na pravidla, které zadáte.

Import objektu je vytvořit jako objekt odpojené. Export objektu musí být připojený k objektu. Logika systému vynucuje toto pravidlo a odstraní každý export objekt, který není připojený k objektu.

## <a name="sync-engine-identity-management-process"></a>Procesu synchronizace modul identity management
Proces správy identit řídí, jak je aktualizovat informace o identitě mezi různé připojených zdrojů dat. Správa identit dojde v tři procesy:

* Import
* Synchronizace
* Export

Během procesu importu synchronizační modul vyhodnotí příchozí informací o identitě z připojeného zdroje dat. Při zjištění změny, je buď vytvoří nové pracovní objekty, nebo aktualizuje stávající pracovní objekty v prostoru konektoru pro synchronizaci.

Synchronizační modul během procesu synchronizace aktualizací úložiště metaverse, aby se projevily změny, ke kterým došlo v prostoru konektoru a aktualizuje prostoru konektoru, aby se projevily změny, ke kterým došlo v úložišti metaverse.

Během procesu exportu se synchronizační modul přenáší změny, které jsou připravené na pracovní objekty a které jsou označeny jako čekající na export.

Následující obrázek ukazuje, kde každý z procesů dojde k jako toky identity informace z jednoho zdroje dat připojené do jiné.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Proces importu
Během procesu importu vyhodnotí synchronizační modul aktualizace informací o identitě. Synchronizační modul porovná informace o identitě přijaté z připojeného zdroje dat s informací o identitě o pracovní objektu a určuje, zda objekt pracovní vyžaduje aktualizace. Pokud je nutné aktualizovat pracovní objekt nová data, je objekt pracovní označeny jako čekajícího na import.

Pomocí pracovní objekty v prostoru konektoru před synchronizací, synchronizační modul může zpracovat pouze informace identity, které se změnila. Tento proces poskytuje následující výhody:

* **Efektivní synchronizace**. Je minimální množství dat, které jsou zpracovány během synchronizace.
* **Efektivní Opakovaná synchronizace**. Jak synchronizační modul zpracovává informace o identitě bez opětovného připojení synchronizační modul ke zdroji dat, můžete změnit.
* **Možnost zobrazit náhled synchronizace**. Můžete zobrazit náhled synchronizace ověřte správnost předpokladů o proces správy identit.

Pro každý objekt zadaný v konektoru synchronizační modul poprvé pokusí vyhledat reprezentaci objektu v prostoru konektoru konektoru. Synchronizační modul prozkoumá všechny pracovní objekty v prostoru konektoru a pokusí se najít odpovídající pracovní objekt, který má odpovídající atribut kotvy. Pokud žádné existující pracovní objekt má odpovídající atribut kotvy, synchronizační modul se pokusí najít odpovídající pracovní objekt se stejným názvem rozlišující.

Když synchronizační modul vyhledá pracovní objekt, který odpovídá rozlišující název, ale není anchor, očekávejte toto chování speciální:

* Pokud objekt umístěný v prostoru konektoru, nemá žádná kotva, pak synchronizační modul odebere tohoto objektu prostoru konektoru a označí objektu úložiště metaverse je propojen jako **opakujte zřizování při další synchronizaci spustit**. Pak vytvoří nový objekt importu.
* Pokud objekt v prostoru konektoru anchor, synchronizační modul se předpokládá, že tento objekt buď přejmenován nebo odstraněn v adresáři připojené. Přiřadí dočasné, nové rozlišující název objektu prostoru konektoru tak, aby ho můžete Příprava příchozí objekt. Původní objekt se pak stane **přechodný**, čekání konektor pro import přejmenování nebo odstranění situaci vyřešit.

Pokud synchronizační modul vyhledá pracovní objekt, který odpovídá objekt určený v konektoru, určuje, jaký druh změny použít. Synchronizační modul může přejmenování nebo odstranění tohoto objektu v připojených zdrojů dat nebo může aktualizovat pouze hodnoty atributů objektu.

Pracovní objekty s aktualizovaná data jsou označené jako čekajícího na import. Různé typy čekající na vyřízení importy jsou k dispozici. V závislosti na výsledek procesu importu má pracovní objekt v prostoru konektoru jednu z těchto čekající na vyřízení typy importu:

* **Žádný**. Žádné změny k některému z atributy pracovní objektu jsou k dispozici. Synchronizační modul není příznak tento typ jako čekajícího na import.
* **Přidat**. Pracovní objekt je nový objekt importu v prostoru konektoru. Synchronizační modul příznaky tento typ jako čekajícího na import pro další zpracování v úložišti metaverse.
* **Aktualizace**. Synchronizační modul najde odpovídající pracovní objekt v prostoru konektoru a označí tento typ jako čekajícího na import, aby aktualizace atributy lze zpracovat v úložišti metaverse. Aktualizace zahrnují přejmenování objektu.
* **Odstranit**. Synchronizační modul najde odpovídající pracovní objekt v prostoru konektoru a označí tento typ jako čekajícího na import, aby připojený k objektu může být odstraněny.
* **Odstranit nebo přidat**. Synchronizační modul najde odpovídající pracovní objekt v prostoru konektoru, ale typy objektů se neshodují. V takovém případě odstranění přidat úpravy je připravený. A delete přidat úpravy označuje synchronizačního modulu, že dokončení opakované synchronizace tohoto objektu musí dojít, protože různé sady pravidel použít u tohoto objektu, když typ objektu změny.

Nastavit stav čekajícího importu pracovní objektu, je možné výrazně snížit objem dat zpracovaných během synchronizace, protože tak učiníte, umožníte systému zpracovat pouze ty objekty, které byly aktualizovány data.

### <a name="synchronization-process"></a>Procesu synchronizace
Synchronizace se skládá ze dvou souvisejících procesů:

* Příchozí synchronizace, když je obsah úložišti metaverse aktualizován na základě dat v prostoru konektoru.
* Odchozí synchronizace, když obsah prostoru konektoru se aktualizuje pomocí data v úložišti metaverse.

Pomocí informací o dvoufázové instalace v prostoru konektoru, vytvoří proces synchronizace příchozích dat v úložišti metaverse integrované zobrazení data, která je uložená v připojených datových zdrojů. Všechny pracovní objekty nebo pouze ty informace čekajícího importu nejsou agregovány, v závislosti na konfiguraci pravidla.

Proces aktualizace odchozí synchronizace exportovat objekty při změně objektů v metaverse.

Příchozí synchronizace vytvoří integrované zobrazení v úložišti metaverse identity informace, které při obdržení ze zdroje dat připojené. Synchronizační modul může zpracovat informace o identitě kdykoli z připojeného zdroje dat pomocí nejnovější informace o identitě, kterou má.

**Synchronizace příchozích dat**

Synchronizace příchozích dat zahrnuje následující postupy:

* **Zřízení** (také nazývané **projekce** Pokud je důležité k rozlišení tohoto procesu zřizování odchozí synchronizace). Synchronizační modul vytvoří nový objekt úložiště metaverse založeného na pracovní objektu a propojuje je. Zřizování je operace úrovni objektů.
* **Připojení k**. Synchronizační modul odkazuje objekt pracovní na existujícího objektu úložiště metaverse. Spojení je operace úrovni objektů.
* **Import toku atributů**. Synchronizační modul aktualizuje hodnoty atributu, která se nazývá toku atributů objektu v úložišti metaverse. Import toku atributů je úroveň atributu operaci, která vyžaduje odkaz mezi objektem pracovní a objektu úložiště metaverse.

Zřizování je pouze procesu, který vytváří objekty v úložišti metaverse. Zřízení ovlivňuje pouze importovat objekty, které jsou odpojené objekty. Při zřizování synchronizační modul vytvoří objekt úložiště metaverse, který odpovídá typu objektu importovaného objektu a vytvoří propojení mezi oba objekty, tedy vytvoření připojený k objektu.

Proces připojení také vytvoří propojení mezi import objektů a objektu úložiště metaverse. Rozdíl mezi spojení a zřizování je proces připojení vyžaduje, aby objekt import jsou propojen existujícího objektu úložiště metaverse, kde proces zřizování vytvoří nový objekt úložiště metaverse.

Synchronizační modul se pokusí připojit import objekt k objektu úložiště metaverse pomocí kritéria, která je zadaná v konfiguraci synchronizační pravidlo.

Během procesů zřídit a připojení k synchronizační modul propojí rozděleným objektu na objekt úložiště metaverse přitom připojený. Po dokončení těchto operací úrovni objektů můžete aktualizovat synchronizační modul hodnot atributů objektu úložiště metaverse přidružené. Tento proces se nazývá toku atributů importu.

Tok atributů importu dojde na všechny importovat objekty, které bude přenášet nová data a jsou propojeny s objektu úložiště metaverse.

**Odchozí synchronizace**

Při změně objektu úložiště metaverse, ale neodstraní, odchozí synchronizace aktualizací exportovat objekty. Cílem odchozí synchronizace je k vyhodnocení, zda změny provedené u objektů v metaverse vyžadovat aktualizace pro pracovní objekty v prostor konektoru. V některých případech může vyžadovat změny této pracovní aktualizovat objekty ve všech prostor konektoru. Pracovní objekty, které se změnily jsou označeny jako čekající na vyřízení exportu, přitom exportovat objekty. Tyto exportu objekty jsou později instalováni připojeného zdroje dat během procesu exportu.

Odchozí synchronizace má tři procesy:

* **Zřizování**
* **Zrušení zřízení**
* **Export toku atributů**

Zajišťování a rušení zajištění jsou operace úrovni objektů. Zrušení zřízení závisí na zřizování, protože pouze zřizování můžete spustit ho. Zrušení zřízení se aktivuje, když zřizování odebere propojení mezi objektu úložiště metaverse a export objektu.

Zřizování se aktivuje vždy když změny objektů v úložišti metaverse. Provedení změn do úložiště metaverse objekty, synchronizačního modulu můžete provádět následující úlohy jako součást procesu zřizování:

* Vytvoření připojených objektů, kde je objekt úložiště metaverse propojený na export nově vytvořený objekt.
* Přejmenování připojený k objektu.
* Odpojte propojení mezi objektu úložiště metaverse a pracovní objekty, vytvoření rozděleným objektu.

Pokud zřizování vyžaduje synchronizační modul vytvořit nový objekt konektoru, pracovní objekt, se kterou je propojena objektu úložiště metaverse je vždy export objektu, protože objekt v připojených zdrojů dat ještě neexistuje.

Pokud zřizování vyžaduje synchronizační modul odpojte připojený k objektu, vytváření objektu rozděleným zrušení zřízení se aktivuje. Proces zrušení zřízení odstraní objekt.

Během zrušení zřízení, odstraněním objekt export nedojde k odstranění fyzicky objektu. Objekt je označena příznakem **odstranit**, což znamená, že operace odstranění se připraví se na objekt.

Export toku atributů může také nastat během procesu odchozí synchronizace, podobným způsobem, že při synchronizace příchozích dat k importu toku atributů. Tok atributů export nastávají jenom mezi úložiště metaverse a export objektů, které jsou připojené.

### <a name="export-process"></a>Proces exportu
Během procesu exportu se synchronizační modul hledá všechny objekty export, které jsou označeny jako čekající na export v prostoru konektoru a poté odešle aktualizace do připojeného zdroje dat.

Synchronizační modul můžete určit úspěch exportu, ale nelze dostatečně zjistit, že proces správy identity je dokončeno. Objekty v připojených zdrojů dat můžete kdykoli změnit jinými procesy. Vzhledem k tomu, že synchronizační modul nemá trvalé připojení ke zdroji dat připojené, není dostatečná k připojeného zdroje dat podle pouze oznámení o úspěšném exportu předpoklady o vlastnosti objektu.

Například může změnu atributů objektu zpět na původní hodnoty zpracovat v připojených zdrojů dat (tedy připojeného zdroje dat může přepsat hodnoty ihned po data jsou instalováni pomocí synchronizační modul a úspěšně aplikováno v připojeného zdroje dat).

Synchronizační modul úložiště exportovat a importovat informace o jednotlivých pracovních objektů stavu. Pokud se hodnoty atributů, které jsou určené v seznamu atributů zahrnutí změnily od poslední export, úložiště import a export stav umožňuje synchronizační modul pro náležitě reagovat. Synchronizační modul používá pro potvrzení hodnot atributů, které byly vyexportovány do připojeného zdroje dat v importu. Porovnání mezi importované a exportované informace, jak je znázorněno na následujícím obrázku, umožňuje synchronizační modul k určení, jestli byla úspěšná exportu nebo jestli je třeba opakovat.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Například pokud synchronizační modul exportuje atribut C, který má hodnotu 5, do připojeného zdroje dat, uloží C = 5 v jeho export stavu paměti. Každý další export na tento objekt výsledky při pokusu o export C = 5 do připojeného zdroje dat znovu vzhledem k tomu, že synchronizační modul se předpokládá, že tato hodnota nebyla použita trvale pro objekt (Pokud na jinou hodnotu nedávno naimportované z připojeného zdroje dat). Export paměti není zaškrtnuto, když je obdržena C = 5 během operace importu v objektu.

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).

