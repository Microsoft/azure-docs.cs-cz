---
title: Metody a procesy pro zavádění služby Azure Data Catalog | Dokumentace Microsoftu
description: Tento článek představuje přístup a proces pro organizace uvažující o přijmutí služby Azure Data Catalog, včetně definování vize, identifikace klíčových případů obchodního použití a výběr pilotního projektu.
services: data-catalog
documentationcenter: ''
author: steelanddata
manager: NA
editor: ''
tags: ''
ms.assetid: 0c771e7a-6fcd-417f-9247-897177719567
ms.service: data-catalog
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: e7c104fa9b136df8ac4b94ee1e444100e2a2f969
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="approach-and-process-for-adopting-azure-data-catalog"></a>Metody a procesy pro zavádění služby Azure Data Catalog
Tento článek vám pomůže přijmout službu **Azure Data Catalog** ve vaší organizaci. Chcete-li úspěšně přijmout službu **Azure Data Catalog**, budete se muset zaměřit na tři klíčové položky: definovat svou vizi, identifikovat klíčové případy obchodního použití v rámci vaší organizace a zvolit pilotní projekt.

## <a name="introducing-the-azure-data-catalog"></a>Úvod do služby Azure Data Catalog.
Očekávání uživatelů, jak by měli být schopni najít odborné informace o datových prostředcích, se v rámci světa práce změnila. Dnes, s rozšířeným používáním nástrojů sociálních médiích na pracovišti, jako je například Yammer, uživatelé očekávají, že rychle získají pomoc a rady ohledně široké škály témat. **Azure Data Catalog** pomáhá podnikům a týmům konsolidovat informace o podnikových datových prostředcích v centrálním úložišti. Spotřebitelé dat mohou tyto datové prostředky objevit a získat znalosti, kterými přispěli odborníci na danou oblast.

Tento článek představuje přístup k zahájení používání služby **Azure Data Catalog**. Článek popisuje typické plánu přijmutí služby Data Catalog pro fiktivní společnost nazývanou Adventure Works.

**Co je Azure Data Catalog?**

**Azure Data Catalog** je plně spravovaná služba ve službě Azure a katalog celopodnikových informací (metadata), který umožňuje samoobslužné zjišťování zdrojů dat. Pomocí katalogu Data Catalog zaregistrujete, zjišťujete, opatříte poznámkami a připojíte se k datovým prostředkům. Katalog Data Catalog je určen ke správě různorodých prostředků informací, aby je bylo možné snadno najít, porozumět jim a připojit se k nim. Tím se zkracuje doba získávání informací z dostupných dat a zvyšuje hodnota organizací. Další informace naleznete v tématu [Microsoft Azure Data Catalog](https://azure.microsoft.com/services/data-catalog/).

## <a name="azure-data-catalog-adoption-plan"></a>Plán přijetí služby Azure Data Catalog
Plán přijetí služby **Azure Data Catalog** popisuje, jak se výhody používání služby sdělují zúčastněným stranám a uživatelům a jaký druh školení uživatelům poskytujete. Jedním z klíčových faktorů úspěchu přijetí katalogu Data Catalog je, jak efektivně komunikujete hodnotu této služby uživatelům a zúčastněným stranám. Primární cílovou skupinou v plánu počátečního přijetí jsou uživatelé služby. Bez ohledu na to, kolik podpory získáte od zúčastněných stran, pokud uživatelé nebo zákazníci vaši nabídku služby Data Catalog nezačlení do svého využití, přijetí nebude úspěšné. Proto tento článek předpokládá, že máte podporu zúčastněných stran a soustředí se na vytváření plánu pro uživatelské přijetí služby Data Catalog.
Plán efektivního přijetí úspěšně angažuje uživatele do toho, co je možné s katalogem Data Catalog dosáhnout a poskytne jim informace a pokyny k dosažení příslušných cílů. Uživatelé musí porozumět hodnotě, kterou katalog Data Catalog poskytuje, aby jim pomohl úspěšně vykonávat svou práci. Když uživatelé uvidí, jak jim může služba Data Catalog pomoci dosáhnout více výsledků s daty, jasně pochopí hodnotu jejího přijetí. Změna je tvrdá, takže efektivní plánu musí vzít v úvahu problémy změny.

Plán přijetí vám umožňuje komunikovat, co je důležité pro uživatele, aby uspěli a dosáhli svých cílů. Typický plán vysvětluje, jak služba Data Catalog usnadní životy uživatelů a obsahuje následující části:

* **Vize projektu** – pomůže vám výstižně projednat plán přijetí s uživateli a zúčastněnými stranami. Je to vaše hodnocení hierarchie.
* **Pilotní tým a vlivné osoby** – zkušenosti od pilotního týmu a vlivných osob pomohou upřesnit, jak týmům a uživatelům představit službu Data Catalog. Vlivné osoby mohou párově školit ostatní uživatele. To zároveň pomáhá identifikovat blokující a pomocné prvky jeho přijetí.
* **Plánování komunikace a žhavých novinek** – díky němu uživatelé pochopí, jak jim může katalog Data Catalog pomoct a mohou podporovat organické přijetí v rámci týmů a nakonec i v celé organizaci.
* **Plán školení** – zevrubné školení zpravidla vede k úspěchu přijetí a uspokojivým výsledkům.

Zde jsou tipy k definování plánu přijetí služby **Azure Data Catalog**.

## <a name="define-your-data-catalog-project-vision"></a>Definování vize projektu katalogu Data Catalog
Prvním krokem k definování plánu přijetí služby **Azure Data Catalog** je napsat zamýšlený popis toho, co chcete dosáhnout. Doporučujeme zachovat poměrně rozsáhlé, ale dostatečně stručné vize projektu, aby definovala konkrétní krátkodobé i dlouhodobé cíle.

Zde uvádíme tipy, které vám mohou pomoct definovat vizi:

* **Identifikace klíčového faktoru nasazení** – zvažte potřeby správy specifických zdrojů dat z podnikání, které lze řešit pomocí katalogu Data Catalog. Pomáhá uvést hlavní výhody používání katalogu Data Catalog. Kupříkladu mohou existovat běžné zdroje dat,  o kterých se všichni noví zaměstnanci musí dozvědět a užívat je, nebo důležité a komplexní zdroje dat, které důkladně zná a chápe pouze několik klíčových osob. **Azure Data Catalog** může pomoci zajistit, aby tyto zdroje dat byly snadno vyhledatelné a pochopitelné, takže je možné tyto dobře známé problémové body řešit přímo a včas během přijetí služby.
* **Být zřetelný a jasný** – jasné porozumění vizi pomůže dostat všechny zaměstnance na stejnou úroveň ohledně hodnoty, kterou služba Data Catalog přináší společnosti, a jak tato vize podporuje firemní cíle.
* **Inspirovat zaměstnance, aby chtěli používat katalogu Data Catalog** – vaše vize a komunikační plán by měly inspirovat zaměstnance, aby pochopili, že Data Catalog jim může přinést výhody při hledání a připojení ke zdrojům dat, díky čemuž dosáhnou s daty lepších výsledků.
* **Specifikovat cíle a časový horizont** – tím se zajistí, že plán přijetí bude mít konkrétní, dosažitelné výsledky. Časový horizont udržuje všechny uživatele soustředěné a umožňuje měření úspěchu pomocí kontrolních bodů.

Zde je příklad sdělení vize pro plán přijetí katalogu Data Catalog fiktivní společnosti Adventure Works:

**Azure Data Catalog** zmocní tým finančního oddělení společnosti Adventure Works ke spolupráci na klíčových zdrojích dat, takže každý člen týmu snadno nalezne a použije data, která potřebuje, a své znalosti pak může sdílet s celým týmem.

Jakmile máte zřetelné sdělení vize, měli byste identifikovat vhodný pilotní projekt pro službu Data Catalog. Obecně platí, že pro službu Data Catalog bude existovat několik scénářů, takže v další části najdete několik užitečných tipů k identifikaci relevantních případů použití.

## <a name="identify-data-catalog-business-use-cases"></a>Identifikování případů obchodního použití služby Data Catalog
K identifikaci případů použití, které jsou relevantní pro katalog Data Catalog, se spojte s odborníky z různých organizačních jednotek, s nimiž identifikujete příslušné případy použití a obchodní problémy k vyřešení. Zkontrolujte stávající problémy, které mají uživatelé s identifikací a pochopením datových prostředků. Například dozvědí se týmy další informace o datových zdrojích až po dotázání se několika uživatelů v organizaci, který mají příslušné zdroje dat?

Nejlepší je vybrat případy použití, které představují snadno dostupný výsledek: případy, které jsou důležité a mají vysokou pravděpodobnost úspěchu, pokud budou vyřešení pomocí katalogu Data Catalog.

Zde jsou některé tipy k identifikaci případů použití:

* **Definování cílů týmu** – jak tým dosahuje svých cílů? Ještě se nezaměřujte na katalog Data Catalog, protože v této fázi chcete být objektivní. Mějte na paměti, že se že jedná o obchodní výsledky, nikoli o technologii.
* **Definování obchodního problému** – jaké jsou problémy, týkající se hledání a získávání informací o datových prostředcích, jimž tým čelí? Například informace o důležitých zdrojích dat lze nalézt v sešitech aplikace Excel v síťové složce a tým může trávit spoustu času vyhledáním příslušných sešitů.
* **Pochopení kultury týmu související se změnou** – mnoho problémů s přijetím se týká odporu vůči změně místo zavádění nového nástroje. Jak týmy reagují na změnu je důležité při identifikaci případů použití, protože existující proces může být používán, neboť "takhle jsme to dělali vždycky" nebo "pokud to není rozbité, proč to opravovat?". Přijetí nového nástroje nebo procesu je vždy jednodušší, když postižení zaměstnanci chápou hodnotu, která se má změnou realizovat a chápou závažnost problémů, které mají být vyřešeny.
* **Udržování zaměření souvisejícího s datovými prostředky** – když hovoříte o obchodních problémech, kterým tým čelí, musíte se "probít plevelem" a zaměřit na to, co je relevantní pro více efektivní využívání datových prostředků organizace.

Zde jsou některé případy použití příklad týkající se katalogu Data Catalog:

### <a name="example-use-cases"></a>Příklad případů použití
* **Registrace centrálních zdrojů dat vysoké hodnoty** – oddělení IT spravuje zdroje dat používané v rámci celé organizace. IT může začít s katalogem Data Catalog pomocí registrace a zadávání poznámek k běžným zdrojům dat organizace.
* **Registrace zdrojů dat na základě týmů** – různé týmy mají užitečné zdroje obchodních dat. Se službou **Azure Data Catalog** začněte tak, že identifikujete a zaregistrujete klíčové zdroje dat používané mnoha různými týmy a do poznámek služby **Azure Data Catalog** zachytíte kmenové znalosti týmu.
* **Samoobslužné služby business intelligence** – týmy tráví spoustu času kombinováním dat z více zdrojů. Zaregistrujte a opatřete poznámkami zdroje dat v centrálním umístění, čímž eliminujete ruční proces zjišťování zdrojů dat.

Další informace o scénářích katalogu Data Catalog najdete v dokumentu [Běžné scénáře služby Azure Data Catalog](data-catalog-common-scenarios.md).

Po identifikování některých případů použití pro katalog Data Catalog by se měly objevit běžné scénáře. Další oddíl popisuje, jak identifikovat první pilotní projekt založený na případu použití.

## <a name="choose-a-data-catalog-pilot-project"></a>Volba pilotního projektu katalogu Data Catalog
Klíčovým faktorem úspěchu je zjednodušení a začít malým projektem. Dobře definovaný pilotní projekt s omezeným rozsahem vám pomůže pokračovat v projektu, aniž zabřednete do projektu, který je příliš složitý nebo který má příliš mnoho účastníků. Je však také důležité zahrnout směs uživatelů, od inovátorů po skeptiky. Uživatelé, kteří si osvojí řešení, vám pomohou vylepšit budoucí komunikaci a plán žhavých novinek. Skeptici vám pomohou identifikovat a vyřešit problémy se zaseknutím. Jakmile se ze skeptiků stanou vítězové, můžete použít jejich zpětnou vazbu k identifikaci faktorů úspěchu.

Pilotní plán by měl postupně zavádět obchodní cíle, které chcete dosáhnout pomocí služby Data Catalog. Po poučení se z počátečního pilotního projektu můžete rozšířit uživatelskou základnu. Počáteční uzavřený pilotní projekt je dobrý k zavedení měřitelného úspěchu, ale konečným cílem je organický nebo virální růst. S organickým růstem katalogu Data Catalog mají uživatelé pod kontrolou své vlastní využití dat a mohou ovlivnit a vyzvat jiné uživatele k přijetí a přispívání do katalogu.

### <a name="target-the-right-team"></a>Zaměření se na správný tým
Když zvolíte pilotní projekt, vyberte tým s nejvíce přitažlivými scénáři, které řeší stávající podnikový problém. Například obchodní analytička Jana vytváří sestavy z databáze serveru SQL Server. Problém je, že o zdroji dat se dozvěděla až po rozhovoru s několika kolegy. Nakonec po plýtvání časem při hledání, jaký zdroj dat použít, se dozvěděla o sešitu aplikace Excel, který obsahuje popis každého zdroje dat. I když sešit aplikace Excel adekvátně popisuje tabulky, které Jana potřebuje, našla by tyto zdroje dat rychle, pokud by byly zaregistrovány a opatřeny poznámkami ve službě **Azure Data Catalog**.

### <a name="identify-data-heroes"></a>Identifikace hrdinů dat
První pilotní projekt by měl zahrnovat několik jednotlivců, kteří produkují data a využívají data, takže tým bude mít vyrovnané zastoupení.

**Producenti dat** jsou osoby s odbornými znalostmi o zdrojích dat. Například David v jiném týmu hodně pracuje klíčovými zdroji dat společnosti Adventure Works. Před adopcí služby **Azure Data Catalog** vytvořil David sešit aplikace Excel k zaznamenání informací o zdrojích dat společnosti Adventure Works.

**Spotřebitelé dat** jsou zaměstnanci s odbornými znalostmi ohledně použití dat k řešení obchodních problémů. Například Nancy je obchodní analytik a používá zdroje dat SQL Server společnosti Adventure Works k analýze dat.

Jeden z obchodních problémů, který řeší služba **Azure Data Catalog**, je připojení **Producentů dat** ke **Spotřebitelům dat**. Toho dosahuje tím, že slouží jako centrální úložiště pro informace o zdrojích dat organizace. Pomocí katalogu Data Catalog David zaregistruje datové zdroje společnosti Adventure Works a serveru SQL Server. Pomocí crowdsourcingu může každý uživatel, který objeví tento zdroj dat, sdílet své názory na data, a kromě toho používat data, která objevil. Například Nancy zjistí zdroje dat prohledáním katalogu a podělí se o své specializované znalosti o datech.  Nyní se mohou ostatní uživatelé v organizaci těžit ze sdílené znalostní báze vyhledáním v katalogu dat.

* Další informace o registraci zdrojů dat naleznete v tématu [Registrování zdrojů dat](data-catalog-get-started.md).
* Další informace o zjišťování zdrojů dat naleznete v tématu [Prohledávání zdrojů dat](data-catalog-get-started.md).

### <a name="start-small-and-focused"></a>Začněte v malém rozsahu a zaměřeně
U většiny podnikových pilotních projektů byste měli katalog zaplnit zdroji dat vysoké hodnoty, aby obchodní uživatelé mohli rychle vidět hodnotu katalogu Data Catalog. IT je vhodné oddělení na zahájení identifikace běžných zdrojů dat, které byly zajímavé pro váš pilotní tým. Pro podporované zdroje dat, jako SQL Server, doporučujeme používat nástroj registrace zdroje dat služby **Azure Data Catalog**. Pomocí nástroje registrace zdroje dat můžete zaregistrovat širokou škálu zdrojů dat včetně databází SQL Server a Oracle a sestav služby SQL Server Reporting Services. Úplný seznam aktuálních zdrojů dat naleznete v části [Podporované zdroje dat služby Azure Data Catalog](data-catalog-dsr.md).

Jakmile identifikujete a zaregistrujete klíčové zdroje dat, je možné také importovat popisy zdrojů dat uložené v jiných umístěních. Rozhraní API služby Data Catalog umožňuje vývojářům načíst popisy a poznámky z jiného umístění, jako například sešit aplikace Excel, které David vytvořil a udržuje.

Další část popisuje příklad projektu ze společnosti Adventure Works.

### <a name="an-example-project"></a>Příklad projektu
V tomto příkladu obchodní analytička Nancy vytváří sestavy pro svůj tým pomocí dat z databáze serveru SQL Server. Problém je, že o zdroji dat se dozvěděla až po rozhovoru s několika kolegy. Tyto zdroje dat by bývala nalezla rychleji, pokud by byly zaregistrovány a opatřeny poznámkami v centrálním umístění, jako je například **Azure Data Catalog**.

Pro ilustraci, jak snadno může Nancy a její tým najít hodnotná data, použijete nástroj pro registraci zdroje dat k vyplnění katalogu informacemi (metadata) o zdrojích dat. Tímto způsobem jsou informace o databázi k dispozici týmu a podniku a nikoli pouze několika jednotlivcům. Jakmile se zdroje dat zaregistrují ve službě Data Catalog, Nancy a její tým je mohou snadno použít. Výsledkem je komplexnější a relevantní katalog dat pro její tým i celý podnik. Čím více týmů přijme katalog Data Catalog, tím snadnější bude vyhledání a používání zdrojů obchodních dat, což vytvoří datově orientovanější kulturu, která s vašimi daty dosáhne lepších výsledků.

Další informace o nástroji pro registraci zdroje dat naleznete v tématu [Začínáme s Azure Data Catalog](data-catalog-get-started.md).

Jako součást pilotního projektu používá Nancyin tým také zdroje dat popsané v excelovém sešitu, který udržuje David a jeho kolegové. Vzhledem k tomu, že také jiné týmy v podniku používají sešity aplikace Excel k popisu zdrojů dat, se IT tým rozhodne vytvořit nástroj pro migraci sešitu aplikace Excel do katalogu Data Catalog. S použitím rozhraní API REST katalogu Data Catalog pro import existujících poznámek může tým pilotního projektu získat kompletní datový katalog, který se skládá z metadat extrahovaných ze zdroje dat pomocí registračního nástroje zdroje dat, s úplnými informacemi dříve zdokumentovanými producenty a spotřebiteli dat bez nutnosti jejich opětovného ručního zadání. S růstem podnikového katalogu dat může organizace používat nástroj registrace zdroje dat pro běžné zdroje dat, a rozhraní API katalogu Data Catalog pro vlastní zdroje a neobvyklé scénáře.

> [!NOTE]
> Napsali jsme ukázkový nástroj, který používá rozhraní API služby **Azure Data Catalog** pro migraci sešitu aplikace Excel do katalogu Data Catalog. Pokud chcete získat další informace o rozhraní API katalogu Data Catalog a ukázkový nástroj, [stáhněte si vzorek kódu sešitu Ad Hoc](https://azure.microsoft.com/documentation/samples/data-catalog-dotnet-excel-register-data-assets/) a prohlédněte si dokumentaci rozhraní [REST API služby Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267593.aspx).
>
>

Jakmile se realizuje pilotní projekt, je čas ke spuštění plánu přijetí katalogu Data Catalog.

### <a name="execute"></a>Spuštění
V tomto okamžiku jste našli případy použití pro katalog Data Catalog a identifikovali jste svůj první projekt. Kromě toho jste zaregistrovali klíčové zdroje dat společnosti Adventure Works a přidali informace z existujícího sešitu aplikace Excel pomocí nástroje, který sestavilo oddělení IT. Nyní je čas pracovat s pilotním týmem na spuštění procesu přijetí katalogu Data Catalog.

Zde jsou některé tipy, jak začít:

* **Vytvořte vzrušující očekávání** – podnikoví uživatelé budou plní očekávání, pokud budou věřit, že jim služba **Azure Data Catalog** usnadní život. Pokuste se uskutečnit konverzace ohledně řešení a výhod, které poskytuje, nikoli o technologii.
* **Usnadněte změnu** – začněte v malém rozsahu a komunikujte plán firemním uživatelům. K dosažení úspěchu je třeba zapojit uživatele od začátku, aby ovlivňovali výsledek a vyvíjeli si smysl pro vlastnictví řešení.
* **Pochvalte inovátory** – inovátoři jsou podnikoví uživatelé, kteří jsou vášniví v tom, co dělají a nadšení evangelizovat výhody služby **Azure Data Catalog** svým spolupracovníkům.
* **Cílové školení** – podnikoví uživatelé nemusí vědět vše o katalogu Data Catalog, takže naplánujte školení tak, abyste řešili konkrétní týmové cíle. Zaměřte se na to, co uživatelé dělají a jak se některé úkony mohou při začleňování služby **Azure Data Catalog** do každodenní rutiny změnit.
* **Buďte ochotni selhat** – pokud pilotní projekt nedosahuje požadovaných výsledků, znovu vyhodnoťte a identifikujte oblasti, které je třeba změnit. Opravte problémy v pilotním projektu, než rozšíříte jeho působnost.

Než pilotní tým přejde na používání katalogu Data Catalog, naplánujte zahajovací schůzku a prodiskutujte očekávání pro pilotní projekt a poskytněte počáteční školení.

### <a name="set-expectations"></a>Nastavte očekávání
Nastavení očekávání a cílů pomáhá podnikovým uživatelům zaměřit se na konkrétní výsledky. Pokud chcete udržet dynamiku projektu, přiřazujte pravidelné úkoly (například denně nebo týdně podle rozsahu a doby trvání pilotního projektu). Jednou z nejcennějších možností katalogu Data Catalog jsou crowdsourcingové datové prostředky, takže firemní uživatelé mohou mít prospěch ze znalostí podnikových dat. Skvělé přiřazení úkolu je pro každého člena pilotního týmu zaregistrovat nebo opatřit poznámkami alespoň jeden zdroj dat, který použili. Viz [Registrace zdroje dat](data-catalog-get-started.md) a [Postup přidání poznámek ke zdrojům dat](data-catalog-get-started.md).

V pravidelných intervalech se setkávejte s týmem, abyste posoudili některé poznámky. Dobré poznámky o zdrojích dat jsou jádrem úspěšného přijetí katalogu Data Catalog, protože poskytují smysluplný přehled zdrojů dat v centrálním umístění. Bez dobrých poznámek zůstanou znalosti o zdrojích dat roztroušené po celém podniku. Viz [Postup přidání poznámek ke zdrojům dat](data-catalog-get-started.md).

A rozhodující zkouškou projektu je, zda uživatelé dokáží vyhledat a porozumět zdrojům dat, které potřebují používat. Pilotní uživatelé by měli pravidelně zkoušet katalog, aby bylo zajištěno, že zdroje dat, které používají pro každodenní práci, jsou relevantní. Pokud požadovaný zdroj dat chybí nebo není správně opatřen poznámkami, má tato skutečnost sloužit jako připomenutí k zaregistrování dalších zdrojů dat nebo poskytnutí dalších poznámek. Tento návyk přidá nejen hodnotu úsilí pilotního projektu, ale zároveň vytvoří efektivní návyky, které se po dokončení pilotního projektu přenesou do dalších týmů.

### <a name="provide-training"></a>Poskytněte školení
Školení by mělo pomoct uživatelům začít a mělo by být přizpůsobeno specifickým cílům a úrovni zkušeností členů pilotního týmu. Chcete-li začít se školením, můžete postupovat podle kroků v článku [Začínáme s Azure Data Catalog](data-catalog-get-started.md). Navíc si můžete stáhnout [prezentaci školení pilotního projektu Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/blob/master/Azure%20Data%20Catalog%20Training.pptx?raw=true). Tato prezentace v aplikaci PowerPoint vám pomůže začít členům pilotního týmu představovat Data Catalog.

## <a name="conclusion"></a>Závěr
Když pilotní tým funguje poměrně bezproblémově a vy jste dosáhli svých počátečních cílů, měli byste rozšířit přijetí katalogu Data Catalog do více týmů. Použijte a zpřesněte zkušenosti z pilotního projektu, abyste rozšířili Data Catalog po celé organizaci.

První uživatelé, kteří se účastnili pilotního projektu, mohou být užiteční při rozšiřování povědomí o výhodách přijetí katalogu Data Catalog. Mohou s ostatními týmy sdílet zkušenosti, jak katalog pomohl jejich týmům vyřešit obchodní problémy, snadněji zjistit zdroje dat a sdílet uplatnitelné informace o zdrojích dat, které používají. Například inovátoři z pilotního týmu společnosti Adventure Works mohou ostatním ukázat, jak je snadné nalézt informace o datových prostředcích společnosti Adventure Works, které se dříve těžko hledaly a chápaly.

Tento článek byl o seznámení se službou **Azure Data Catalog** ve vaší organizaci. Věříme, že jste byli schopni spustit pilotní projekt katalogu Data Catalog a rozšířit katalog po celé organizaci.

## <a name="more-information-about-azure-data-catalog"></a>Další informace o Azure Data Catalog
* [Produktová stránka Azure Data Catalog](https://azure.microsoft.com/services/data-catalog/)
* [Dokumentace k Azure Data Catalog](https://azure.microsoft.com/documentation/services/data-catalog/)
* [Běžné scénáře služby Azure Data Catalog](data-catalog-common-scenarios.md)
* [Registrace zdrojů dat](data-catalog-get-started.md)
* [Prohledávání zdrojů dat](data-catalog-get-started.md)
* [Přidání poznámek ke zdrojům dat](data-catalog-get-started.md)
* [Metadata crowdsourcingu](data-catalog-get-started.md)
