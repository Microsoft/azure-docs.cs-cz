---
title: Faktory ovlivňující výkon nástroje Azure AD Connect
description: Tento dokument vysvětluje, jak různé faktory ovlivňují Azure AD Connect zřizovací modul. Tyto faktory pomůžou organizacím, aby naplánovaly nasazení Azure AD Connect a zajistili tak, že splní požadavky na synchronizaci.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15bcb0f7ca30c343072da396abeac8d08dee03a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087005"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Faktory ovlivňující výkon nástroje Azure AD Connect

Azure AD Connect synchronizuje vaši službu Active Directory s Azure AD. Tento server je důležitou součástí přesunutí identity uživatelů do cloudu. Hlavní faktory ovlivňující výkon Azure AD Connect jsou:

| **Faktor návrhu**| **Definice** |
|:-|-|
| Topologie| Distribuce koncových bodů a komponent Azure AD Connect musí spravovat v síti. |
| Měřítko| Počet objektů, jako jsou uživatelé, skupiny a organizační jednotky, budou spravovány Azure AD Connect. |
| Hardware| Hardware (fyzický nebo virtuální) pro Azure AD Connect a výkonnou kapacitu jednotlivých hardwarových součástí, včetně nastavení procesoru, paměti, sítě a pevného disku. |
| Konfigurace| Jak Azure AD Connect zpracovávat adresáře a informace. |
| Načítání| Frekvence změn objektu. Zatížení se může lišit v průběhu hodiny, dne nebo týdne. V závislosti na komponentě bude možná nutné navrhnout zatížení ve špičce nebo průměrné zatížení. |

Účelem tohoto dokumentu je popsat faktory ovlivňující výkon modulu Azure AD Connect zřizování. Velká nebo složitá organizace (organizace, které zřizování více než 100 000 objektů) můžou použít doporučení k optimalizaci Azure AD Connect implementace, pokud se tady setkávají problémy s výkonem, které jsou zde popsané. Ostatní komponenty Azure AD Connect, jako jsou [Azure AD Connect stav](how-to-connect-health-agent-install.md) a agenti, zde nejsou pokryté.

> [!IMPORTANT]
> Společnost Microsoft nepodporuje úpravu ani provozní Azure AD Connect mimo tyto akce, které jsou zdokumentovány formálně. Některé z těchto akcí můžou mít za následek nekonzistentní nebo nepodporovaný stav Azure AD Connect synchronizace. V důsledku toho společnost Microsoft nemůže poskytnout technickou podporu pro taková nasazení.

## <a name="azure-ad-connect-component-factors"></a>Azure AD Connect faktory komponenty

Následující diagram znázorňuje architekturu zřizování modulu zřizování, která se připojuje k jedné doménové struktuře, i když je podporováno více doménových struktur. Tato architektura ukazuje, jak různé komponenty vzájemně komunikují.

![Diagram znázorňuje způsob interakce připojených adresářů a Azure AD Connect modulu zřizování, včetně prostoru konektoru a komponent úložiště metaverse v SQL Database. ](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Zřizovací modul se připojuje ke každé doménové struktuře služby Active Directory a ke službě Azure AD. Proces čtení informací z jednotlivých adresářů se nazývá import. Export odkazuje na aktualizaci adresářů z modulu zřizování. Synchronizace vyhodnocuje pravidla způsobu, jakým objekty budou procházet uvnitř modulu zřizování. Pro hlubší podrobně se můžete podívat na [Azure AD Connect synchronizace: Princip architektury](./concept-azure-ad-connect-sync-architecture.md).

Azure AD Connect používá následující pracovní oblasti, pravidla a procesy, aby bylo možné synchronizovat ze služby Active Directory do Azure AD:

* **Místo konektoru (CS)** – objekty z každého připojeného adresáře (CD) jsou nejprve připravené, aby mohly být zpracovány modulem zřizování. Azure AD má vlastní CS a každá doménová struktura, ke které se připojujete, má vlastní CS.
* **Metaverse (MV)** – objekty, které je třeba synchronizovat, jsou zde vytvořeny na základě pravidel synchronizace. Objekty musí existovat v MV předtím, než mohou naplnit objekty a atributy do dalších připojených adresářů. Existuje jenom jedna MV.
* **Pravidla synchronizace** – rozhodují o tom, které objekty budou vytvořeny (provedené) nebo připojené (připojené) k objektům v MV. Pravidla synchronizace také rozhodují, které hodnoty atributu budou zkopírovány nebo transformovány do a z adresářů.
* **Spustit profily** – umožňuje seskupit kroky procesu kopírování objektů a jejich hodnot atributů podle pravidel synchronizace mezi pracovními oblastmi a připojenými adresáři.

Existují různé spuštěné profily pro optimalizaci výkonu zřizovacího stroje. Většina organizací bude používat výchozí plány a profily spuštění pro běžné operace, ale některé organizace budou muset [plán změnit](./how-to-connect-sync-feature-scheduler.md) nebo aktivovat jiné profily spuštění v rámci služby stravování pro neobvyklé situace. K dispozici jsou následující profily spuštění:

### <a name="initial-sync-profile"></a>Profil počáteční synchronizace

Profil počáteční synchronizace je proces čtení propojených adresářů, jako je například doménová struktura služby Active Directory, při prvním spuštění. Pak provede analýzu všech položek v databázi synchronizačního modulu. Počáteční cyklus vytvoří nové objekty ve službě Azure AD a v případě vysoké doménové struktury služby Active Directory bude trvat déle, než se dokončí. Počáteční synchronizace zahrnuje následující kroky:

1. Úplný import na všechny konektory
2. Úplná synchronizace na všech konektorech
3. Exportovat na všechny konektory

### <a name="delta-sync-profile"></a>Profil rozdílové synchronizace

Pro optimalizaci procesu synchronizace tento profil spuštění zpracovává pouze změny (vytváření, odstraňování a aktualizace) objektů v připojených adresářích od posledního procesu synchronizace. Ve výchozím nastavení se rozdílový profil synchronizace spouští každých 30 minut. Organizace by se měly snažit zachovat čas, který trvá do 30 minut, aby se zajistilo aktuálnost služby Azure AD. Chcete-li monitorovat stav Azure AD Connect, pomocí [agenta sledování stavu](how-to-connect-health-sync.md) Zobrazte případné problémy s procesem. Rozdílový profil synchronizace zahrnuje následující kroky:

1. Rozdílový import u všech konektorů
2. Rozdílová synchronizace u všech konektorů
3. Exportovat na všechny konektory

Typický scénář rozdílové synchronizace podnikových organizací je:

- bylo odstraněno 1% objektů.
- bylo vytvořeno 1% objektů.
- bylo upraveno 5% objektů.

Rychlost změny se může lišit v závislosti na tom, jak často vaše organizace aktualizuje uživatele ve službě Active Directory. Například vyšší míry změny se mohou vyskytnout s sezónnostem při přijímání a snižování pracovní síly.

### <a name="full-sync-profile"></a>Profil úplné synchronizace

Pokud jste provedli některou z následujících změn konfigurace, vyžaduje se úplný cyklus synchronizace:



- Zvýšila se rozsah objektů nebo atributů, které mají být importovány z připojených adresářů. Například když přidáte doménu nebo organizační jednotku do oboru pro import.
- Provedeny změny pravidel synchronizace. Například když vytvoříte nové pravidlo pro naplnění názvu uživatele ve službě Azure AD z extension_attribute3 ve službě Active Directory. Tato aktualizace vyžaduje, aby zřizovací modul znovu prozkoumal všechny existující uživatele a aktualizoval jejich tituly, aby se změny projevily.

V rámci úplného synchronizačního cyklu jsou zahrnuté tyto operace:

1. Úplný import na všechny konektory
2. Úplná/rozdílová synchronizace u všech konektorů
3. Exportovat na všechny konektory

> [!NOTE]
> Při hromadné aktualizaci mnoha objektů ve službě Active Directory nebo v Azure AD se vyžaduje pečlivé plánování. Hromadné aktualizace způsobí, že proces rozdílové synchronizace při importu trvá déle, protože se změnil velký počet objektů. Dlouhé importy mohou nastat i v případě, že Hromadná aktualizace nemá vliv na proces synchronizace. Například přiřazení licencí k mnoha uživatelům ve službě Azure AD způsobí dlouhý cyklus importu z Azure AD, ale nebude mít za následek žádné změny atributů ve službě Active Directory.

### <a name="synchronization"></a>Synchronizace

Runtime procesu synchronizace má následující charakteristiky výkonu:

* Synchronizace je jediná vlákna, což znamená, že zřizovací modul neprovede žádné paralelní zpracování profilů spuštění připojených adresářů, objektů nebo atributů.
* Čas importu roste lineárně s počtem synchronizovaných objektů. Například pokud 10 000 objektů pro import trvá 10 minut, pak na stejném serveru budou mít tyto objekty 20 000 přibližně 20 minut.
* Export je také lineární.
* Synchronizace se exponenciálně zvýší na základě počtu objektů s odkazy na jiné objekty. Členství ve skupinách a vnořené skupiny mají hlavní dopad na výkon, protože jejich členové odkazují na uživatelské objekty nebo jiné skupiny. Tyto odkazy musí být nalezeny a odkazovány na skutečné objekty v části MV k dokončení cyklu synchronizace.

### <a name="filtering"></a>Filtrování

Velikost topologie služby Active Directory, kterou chcete importovat, je číslo jeden faktor ovlivňující výkon a celkovou dobu, po kterou budou interní součásti modulu zřizování přebírat.

[Filtrování](./how-to-connect-sync-configure-filtering.md) by mělo být použito ke zmenšování objektů do synchronizovaných. Zabrání v zpracování a exportu zbytečných objektů do služby Azure AD. V upřednostňovaném pořadí jsou k dispozici následující techniky filtrování:



- **Filtrování založené na doméně** – pomocí této možnosti můžete vybrat konkrétní domény, které se budou synchronizovat s Azure AD. Pokud po instalaci Azure AD Connect synchronizaci provedete změny místní infrastruktury, musíte přidat a odebrat domény z konfigurace synchronizačního modulu.
- **Filtrování organizační jednotky (OU)** – pomocí organizačních jednotek cílí na konkrétní objekty v doménách služby Active Directory pro zřizování do Azure AD. Filtrování organizačních jednotek je druhým doporučeným mechanismem filtrování, protože používá jednoduché dotazy oboru LDAP k importu menší podmnožiny objektů ze služby Active Directory.
- **Filtrování atributů na objekt** – používá hodnoty atributu pro objekty k určení, jestli se konkrétní objekt ve službě Active Directory zřídí ve službě Azure AD. Filtrování atributů je skvělé pro jemné vyladění filtrů, pokud filtrování domén a organizačních jednotek nesplňuje konkrétní požadavky na filtrování. Filtrování atributů nezkracuje dobu importu, ale může zkrátit dobu synchronizace a exportu.
- **Filtrování na základě skupin** – používá členství ve skupině k rozhodnutí, jestli se mají objekty zřídit ve službě Azure AD. Filtrování na základě skupin se hodí jenom pro testovací situace a nedoporučuje se pro produkční prostředí, protože se navíc vyžaduje další režie nutná ke kontrole členství ve skupině během synchronizačního cyklu.

Mnoho trvalých [objektů odpojení](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) v rámci služby Active Directory cs může způsobit delší dobu synchronizace, protože zřizovací modul musí v rámci synchronizačního cyklu znovu vyhodnotit všechny objekty odpojení, aby bylo možné připojení. Chcete-li tento problém vyřešit, vezměte v úvahu jedno z následujících doporučení:



- Odpojte objekty odpojit od rozsahu pro import pomocí filtrování domén nebo organizačních jednotek.
- Propojte objekty s objekty do MV a nastavte atribut [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) na hodnotu true, aby se zabránilo zřizování těchto objektů v Azure AD CS.

> [!NOTE]
> Pokud je filtrováno příliš mnoho objektů, může dojít k problémům se zaměňováním nebo oprávněním k aplikacím. Například v hybridní implementaci Exchange Online uživatelé s místními poštovními schránkami uvidí více uživatelů v globálním seznamu adres než uživatelé s poštovními schránkami v Exchangi Online. V jiných případech může uživatel chtít udělit přístup v cloudové aplikaci jinému uživateli, který není součástí rozsahu filtrované sady objektů.

### <a name="attribute-flows"></a>Toky atributů

Toky atributů jsou procesy pro kopírování nebo transformaci hodnot atributů objektů z jednoho připojeného adresáře do jiného připojeného adresáře. Jsou definovány jako součást pravidel synchronizace. Pokud se například ve službě Active Directory změní telefonní číslo uživatele, bude aktualizováno telefonní číslo ve službě Azure AD. Organizace můžou [upravovat toky atributů](./how-to-connect-sync-change-the-configuration.md) tak, aby vyhovovaly různým požadavkům. Předtím, než je změníte, doporučujeme zkopírovat toky stávajících atributů.

Jednoduché přesměrování, jako je například přetečení hodnoty atributu na jiný atribut, nemá vliv na výkon. Příkladem přesměrování je přenos mobilního čísla ve službě Active Directory na telefonní číslo do kanceláře v Azure AD.

Transformace hodnot atributů může mít vliv na výkon procesu synchronizace. Transformace hodnot atributů zahrnuje úpravu, přeformátování, zřetězení nebo odečítání hodnot atributů.

Organizace můžou zabránit určitým atributům v toku do služby Azure AD, ale nebudou mít vliv na výkon modulu zřizování.

> [!NOTE]
> Neodstraňujte v pravidlech synchronizace nechtěné toky atributů. Doporučuje se je místo toho zakázat, protože Odstraněná pravidla se během Azure AD Connect upgradu znovu vytvoří.

## <a name="azure-ad-connect-dependency-factors"></a>Azure AD Connect faktory závislosti

Výkon Azure AD Connect je závislý na výkonu připojených adresářů, které importuje a exportuje do nástroje. Například velikost služby Active Directory, kterou potřebuje k importu, nebo latenci sítě pro službu Azure AD. Databáze SQL, kterou zřizovací stroj používá, má také dopad na celkový výkon cyklu synchronizace.

### <a name="active-directory-factors"></a>Faktory služby Active Directory

Jak už bylo zmíněno dříve, počet objektů, které se mají importovat, významně ovlivňuje výkon. [Hardware a požadavky pro Azure AD Connect](how-to-connect-install-prerequisites.md) osnova specifických hardwarových vrstev v závislosti na velikosti vašeho nasazení. Azure AD Connect podporují pouze konkrétní topologie, jak je uvedeno v [topologiích pro Azure AD Connect](plan-connect-topologies.md). Neexistují žádné optimalizace výkonu a doporučení pro nepodporované topologie.

Ujistěte se, že váš server Azure AD Connect splňuje požadavky na hardware podle velikosti služby Active Directory, kterou chcete naimportovat. Špatné nebo pomalé síťové připojení mezi Azure AD Connect serverem a řadiči domény služby Active Directory může zpomalit import.

### <a name="azure-ad-factors"></a>Faktory Azure AD

Azure AD používá omezování pro ochranu cloudové služby před útoky DoS (Denial-of-Service). Služba Azure AD v současné době omezuje omezení 7 000 zápisů na 5 minut (84 000 za hodinu). Například je možné omezit následující operace:



- Azure AD Connect exportovat do služby Azure AD.
- Skripty a aplikace PowerShellu aktualizují službu Azure AD přímo na pozadí, jako je například dynamické členství ve skupinách.
- Uživatelé aktualizují své vlastní záznamy identity, jako je například registrace pro MFA nebo SSPR (Samoobslužné resetování hesla).
- Operace v grafickém uživatelském rozhraní.

Naplánujte úlohy nasazení a údržby, abyste se ujistili, že váš cyklus Azure AD Connect synchronizace není ovlivněn omezeními. Pokud máte například velké náborové vlny, kde vytváříte tisíce uživatelských identit, může to způsobit aktualizace členství v dynamické skupině, přiřazení licencování a registraci samoobslužného resetování hesla. Je lepší rozdělit tyto zápisy za několik hodin nebo několik dní.

### <a name="sql-database-factors"></a>Faktory SQL Database

Velikost zdrojové topologie služby Active Directory bude mít vliv na výkon SQL Database. Řiďte se [požadavky na hardware](how-to-connect-install-prerequisites.md) pro databázi SQL serveru a vezměte v úvahu následující doporučení:



- Organizace s více než 100 000 uživateli můžou snížit latenci sítě tak, že společně vyhledávají SQL Database a zřizovací modul na stejném serveru.
- Vzhledem k požadavkům na vysoký počet vstupně-výstupních operací a výstupu (v/v) procesu synchronizace použijte jednotky SSD (Solid-State Drive) pro databázi SQL zřizovacího modulu, pokud to není možné, zvažte konfigurace RAID 0 nebo RAID 1.
- Neprovádějte úplnou synchronizaci bez přerušení. způsobuje zbytečné změny a pomalejší doby odezvy.

## <a name="conclusion"></a>Závěr

Pokud chcete optimalizovat výkon vaší Azure AD Connect implementace, vezměte v úvahu následující doporučení:



- Použijte [doporučenou konfiguraci hardwaru](how-to-connect-install-prerequisites.md) založenou na velikosti vaší implementace serveru Azure AD Connect.
- Při upgradu Azure AD Connect v rozsáhlých nasazeních zvažte použití [metody migrace](./how-to-upgrade-previous-version.md#swing-migration)za provozu, abyste se ujistili, že máte minimální výpadky a nejvyšší spolehlivost. 
- K dosažení nejlepšího výkonu použijte SSD pro SQL Database.
- Vyfiltrujte obor služby Active Directory tak, aby zahrnoval jenom objekty, které se musí zřídit ve službě Azure AD, a to pomocí filtrování domén, organizačních jednotek nebo atributů.
- Pokud potřebujete změnit výchozí pravidla toku atributů, nejdřív zkopírujte pravidlo a pak změňte kopii a zakažte původní pravidlo. Nezapomeňte znovu spustit úplnou synchronizaci.
- Naplánujte adekvátní čas pro počáteční profil úplného spuštění synchronizace.
- Snažte se dokončit cyklus rozdílového synchronizace během 30 minut. Pokud se rozdílový profil synchronizace nedokončí za 30 minut, upravte výchozí četnost synchronizací tak, aby zahrnovala kompletní rozdílový cyklus synchronizace.
- Monitorujte svůj [stav služby Azure AD Connect Sync](how-to-connect-health-agent-install.md) ve službě Azure AD.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).