---
title: Faktory ovlivňující výkon nástroje Azure AD Connect
description: Tento dokument popisuje, jak různé faktory ovlivňující zřizování modulu Azure AD Connect. Tyto faktory pomůže podnikům naplánovat jejich nasazení Azure AD Connect, abyste měli jistotu, že jsou že splněné požadavky na jejich synchronizaci.
services: active-directory
author: billmath
manager: mtillman
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.openlocfilehash: 7cf0e2b211f9d34f6d8f4fe89a230d8a2e97512a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069009"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Faktory ovlivňující výkon nástroje Azure AD Connect

Azure AD Connect synchronizuje služby Active Directory do služby Azure AD. Tento server je zásadní součástí sady řešení identity vašich uživatelů Přesun do cloudu. K základním faktorům, které ovlivňují výkon služby Azure AD Connect jsou:

| **Faktor návrhu**| **Definice** |
|:-|-|
| Topologie| Distribuce koncových bodů a komponent Azure AD Connect musí spravovat v síti. |
| Měřítko| Počet objektů, jako jsou uživatelé, skupiny a organizační jednotky, jej lze spravovat pomocí služby Azure AD Connect. |
| Hardware| Hardware (fyzický nebo virtuální) pro Azure AD Connect a výkonu závislých kapacitu jednotlivých hardwarových komponent, včetně využití procesoru, paměti, sítě a konfigurace pevného disku. |
| Konfigurace| Jak Azure AD Connect procesy adresáře a informace. |
| Načtení| Četnost změn objektů. Zatížení se mohou lišit během hodiny, dne nebo týdne. V závislosti na komponentě bude pravděpodobně nutné návrh pro zátěž ve špičce nebo průměrnou zátěž. |

Účelem tohoto dokumentu je popsat faktory ovlivňující výkon vliv na výkon modulu zřizování služby Azure AD Connect. Ostatní součásti služby Azure AD Connect, jako například [Azure AD Connect health](how-to-connect-health-agent-install.md) a agenti nejsou součástí tohoto.

> [!IMPORTANT]
> Microsoft nepodporuje úpravy ani provoz služby Azure AD Connect mimo formálně zdokumentované akce. Některé z těchto akcí můžou způsobit nekonzistentní nebo nepodporovaný stav synchronizace služby Azure AD Connect. Microsoft proto nemůže pro taková nasazení poskytovat technickou podporu.

## <a name="azure-ad-connect-component-factors"></a>Faktory komponenty služby Azure AD Connect

Následující diagram znázorňuje základní architektura zřizování modul propojíte s jednou doménovou strukturou, i když se podporují více doménových struktur. Tato architektura ukazuje, jak různé komponenty interagují navzájem.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Modul zřizování připojení pro každou doménovou strukturu služby Active Directory a do služby Azure AD. Čtení informací z každý adresář proces se nazývá importu. Export odkazuje na aktualizaci adresáře z modulu zřizování. Synchronizace je vyhodnocen jako způsob, jakým se objekty probíhá v zřizování stroji pravidel. Podrobněji analyzovat, můžete se podívat do [synchronizace Azure AD Connect: Principy architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

Azure AD Connect používá následující pracovní oblasti, pravidla a procesy tak, aby synchronizace ze služby Active Directory do služby Azure AD:

* **Prostor konektoru (CS)** – objekty z každý připojený adresář (CD), skutečné adresáře jsou připraveny tady nejprve předtím, než mohou být zpracovány zřizování modulu. Azure AD má svůj vlastní CS a každou doménovou strukturu, ke kterým se připojujete má svůj vlastní CS.
* **Úložiště Metaverse (MV)** – objekty, které je třeba synchronizovat, vytvoří se tady podle pravidla synchronizace. Objekty, musí existovat v MV naplní objekty a atributy do dalších připojených adresářů. Existuje pouze jeden MV.
* **Synchronizovat pravidla** -rozhodnou objektů, které bude vytvořena (plánované) nebo připojení k objektům v MV (připojené k doméně). Synchronizační pravidla i rozhodnout hodnot atributů, které budou zkopírovány nebo transformovat do a z adresáře.
* **Profily spuštění** – obsahuje ureitou kroky procesu kopírování objektů a jejich hodnoty atributů podle pravidel synchronizace mezi pracovních oblastí a připojené adresáře.

Existují různé profily spuštění za účelem optimalizace výkonu zřizování modulu. Většina organizací bude používat výchozí plány a profily pro běžné operace spuštění, ale některé organizace můžou mít k [změnit plán](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) nebo jiných profilů spuštění pro řešení pro běžné situace aktivační události. K dispozici jsou následující profily spuštění:

### <a name="initial-sync-profile"></a>Počáteční synchronizace profilu

Profil počáteční synchronizace je proces čtení připojené adresáře, jako jsou doménové struktury služby Active Directory poprvé. Pak provede analýzu na všechny položky v databázi modul synchronizace. Počáteční cyklus bude vytváření nových objektů ve službě Azure AD a budou trvat delší dobu pro dokončení, pokud vaší doménové struktury služby Active Directory jsou velké. Počáteční synchronizace zahrnuje následující kroky:

1. Úplný import na všechny konektory
2. Plná synchronizace v všechny konektory
3. Exportovat na všechny konektory

### <a name="delta-sync-profile"></a>Rozdílová synchronizace profilu

K optimalizaci procesu synchronizace tento profil spuštění pouze zpracování změny (vytvoří, odstraní a aktualizuje) objektů v připojených adresářích, od posledního procesu synchronizace. Profil synchronizace delta ve výchozím nastavení spouští každých 30 minut. Organizace se měli snažit Udržovat čas potřebný k pod 30 minut, aby se zajistilo, že Azure AD je aktuální. Chcete-li monitorovat stav služby Azure AD Connect, použijte [agenta monitorování stavu](how-to-connect-health-sync.md) zobrazíte všechny problémy s procesem. Rozdílová synchronizace profil obsahuje následující kroky:

1. Rozdílový import na všechny konektory
2. Rozdílová synchronizace na všechny konektory
3. Exportovat na všechny konektory

Typické podnikové organizace rozdílové synchronizace scénáře je:

- ~ 1 % objekty budou odstraněny.
- ~ 1 % objekty jsou vytvořeny.
- přibližně 5 % objektů jsou upraveny.

Vaše míra změny se mohou lišit v závislosti na tom, jak často organizaci aktualizací uživatelé ve službě Active Directory. Například vyšší míru změny mohou nastat u sezónnosti náboru a snižování pracovní síly.

### <a name="full-sync-profile"></a>Úplná synchronizace profilu

Cyklus úplné synchronizace je vyžadována, pokud jste provedli všechny následující změny konfigurace:



- Zvýšení rozsahu objektů nebo atributů nelze importovat z připojených adresáře. Například když přidáte doménu nebo organizační jednotku pro import oboru.
- Změny provedené v pravidlech synchronizace. Například při vytváření nového pravidla a naplnit pozice uživatele ve službě Azure AD z extension_attribute3 ve službě Active Directory. Tato aktualizace vyžaduje, aby modul zřizování přezkoumat všichni stávající uživatelé k aktualizaci jejich názvy na použití změny do budoucna.

Tyto operace jsou součástí cyklus úplné synchronizace:

1. Úplný import na všechny konektory
2. Úplná nebo rozdílová synchronizace na všechny konektory
3. Exportovat na všechny konektory

> [!NOTE]
> Pečlivé plánování je povinný, při hromadné aktualizace na mnoho objektů ve službě Active Directory nebo Azure AD. Hromadné aktualizace způsobí, že proces synchronizace delta trvá déle, když importujete, protože jste změnili velké množství objektů. Dlouhé importu může dojít i v případě, že hromadné aktualizace neovlivňuje procesu synchronizace. Například přiřazování licencí pro mnoho uživatelů ve službě Azure AD způsobí, že cyklus dlouhé importu ze služby Azure AD, ale nebude mít za následek změny atributů ve službě Active Directory.

### <a name="synchronization"></a>Synchronizace

Modul runtime proces synchronizace má následující vlastnosti výkonu:

* Synchronizace je jedinou vláken, což znamená, že modul zřizování neprovádí žádné paralelní zpracování profilů spuštění připojené adresářů, objekty nebo atributy.
* Import čas lineárně roste s počtem objektů synchronizuje. Například pokud 10 000 objektů trvat 10 minut, než k importu, pak 20 000 objektů bude trvat přibližně 20 minut na stejném serveru.
* Export je také lineární.
* Synchronizace se zvětší exponenciálně zvyšuje podle počtu objektů s odkazy na jiné objekty. Členství ve skupinách a vnořené skupiny mají dopad na výkon hlavní, protože jeho členy odkazovat na objekty uživatele nebo jiné skupiny. Tyto odkazy musí být nalezen a odkazuje na skutečných objektů v MV k dokončení synchronizační cyklus.

### <a name="filtering"></a>Filtrování

Velikost topologie služby Active Directory, kterou chcete importovat je číslo jediný faktor ovlivňující výkon a celkový čas, bude trvat vnitřní součástí zřizování modulu.

[Filtrování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) by měla sloužit zmenšit synchronizované objekty. To zabrání zbytečné objekty zpracovat a exportovat do služby Azure AD. V pořadí podle priority jsou k dispozici následující techniky filtrování:



- **Filtrování podle domén** – tuto možnost použijte, chcete-li vybrat konkrétní domény synchronizovat do služby Azure AD. Musíte přidat a odebrat domény z konfigurace modulu synchronizace, když provedete změny v místní infrastruktuře po instalaci synchronizace Azure AD Connect.
- **Organizační jednotka (OU) filtrování** -využívá organizačních jednotek pro cílení konkrétní objekty v doménách služby Active Directory pro zřizování do služby Azure AD. Filtrování podle organizačních jednotek je druhý doporučuje filtrování mechanismus, protože ho používá jednoduché dotazy na obor LDAP k importu menší podmnožinu objektů ze služby Active Directory.
- **Atribut filtrování podle objektu** -používá hodnoty atributu na objekty se rozhodnout, jestli konkrétní objekt ve službě Active Directory je zřízené ve službě Azure AD. Filtrování atributů se skvěle hodí pro optimalizaci exportovatelného svoje filtry. Pokud domény a filtrování podle organizačních jednotek nesplňuje požadavky na konkrétní filtrování. Filtrování atributů není zkrátit čas, který importovat, ale může snížit synchronizace a exportu časy.
- **Filtrování podle skupin** -využívá skupiny členství se rozhodnout, zda by mělo proběhnout zřízení objektů ve službě Azure AD. Filtrování podle skupin je vhodné pouze pro účely testování situacích a nedoporučuje se pro produkční prostředí, z důvodu dalších zásahy nutné zkontrolovat členství ve skupinách během cyklus synchronizace.

Mnoho trvalých [odpojovače](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) ve vaší Active Directory CS může způsobit delší doba synchronizace, protože modul zřizování musí přehodnotit každý objekt odpojovače možné připojení v cyklus synchronizace. Chcete-li tento problém vyřešili, zvažte jednu z následujících doporučení:



- Umístíte odpojovače mimo rozsah pro import pomocí domény nebo filtrování podle organizačních jednotek.
- Objekty, které chcete MV a nastavte projekt/spojení [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) atribut rovná na hodnotu True, aby se zabránilo zřizování z těchto objektů ve službě Azure AD CS.

> [!NOTE]
> Uživatelé můžou nepřehledné nebo problémy s oprávněními aplikace může dojít, když jsou filtrovány příliš mnoho objektů. Například v Exchangi online implementaci hybridní, uživatelům s místním poštovním schránkám se zobrazí další uživatelé do svého seznamu globálního adresáře než uživatelé s poštovními schránkami v Exchangi online. V jiných případech může uživatel chcete udělit přístup v cloudové aplikace na jiného uživatele, který není součástí rozsahu filtrovanou sadu objektů.

### <a name="attribute-flows"></a>Toky atributů

Toky atributů je proces kopírování nebo transformace hodnoty atributů objektů z jednoho připojení adresáře do jiného adresáře připojené. Že jste definovali jako součást pravidla synchronizace. Například pokud změníte telefonní číslo uživatele ve službě Active Directory, bude aktualizovat telefonní číslo ve službě Azure AD. Můžou organizace [upravovat toky atributů](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) do sady různých požadavků. Doporučuje se, že je zkopírovat existující toky atributů před změnou je.

Jednoduché přesměrování, jako je tok hodnotu atributu na jiný atribut nebude mít vliv na výkon materiálu. Příklad přesměrování se přenášejí mobilní číslo ve službě Active Directory na telefonní číslo office ve službě Azure AD.

Transformace hodnoty atributů může mít dopad na výkon v procesu synchronizace. Transformace hodnoty atributů obsahuje úpravy, přeformátování, zřetězení nebo odečtením hodnoty atributů.

Organizace může zabránit určité atributy, které jsou předávány do služby Azure AD, ale nebude mít vliv na výkon modulu zřizování.

> [!NOTE]
> Neodstraňovat toky nežádoucí atributů v pravidlech synchronizace. Doporučujeme spíše zakázat, protože odstraněné pravidla rekonstruovaly během upgradu služby Azure AD Connect.

## <a name="azure-ad-connect-dependency-factors"></a>Azure AD Connect závislost faktory

Výkon služby Azure AD Connect je závislé na výkonu připojených adresáře se importy a exporty do. Například velikost služby Active Directory je nutné importovat nebo latence sítě ke službě Azure AD. SQL database, které používá modul zřizování taky ovlivňuje celkový výkon cyklus synchronizace.

### <a name="active-directory-factors"></a>Faktory Active Directory

Jak už bylo zmíněno dříve, počet objektů, které se mají naimportovat výrazně ovlivňuje výkon. [Hardware a požadavky pro Azure AD Connect](how-to-connect-install-prerequisites.md) popisují konkrétní hardware úrovních podle velikosti vašeho nasazení. Azure AD Connect pouze podporují konkrétní topologií, jak je uvedeno v [topologie pro Azure AD Connect](plan-connect-topologies.md). Neexistují žádné optimalizace výkonu a doporučení pro nepodporované topologie.

Ujistěte se, že váš server Azure AD Connect splňuje hardwarové požadavky na základě velikosti vaší služby Active Directory, že který chcete importovat. Chybná nebo pomalé síťové připojení mezi serverem Azure AD Connect a řadiče domény služby Active Directory může zpomalit import.

### <a name="azure-ad-factors"></a>Azure AD faktory

Azure AD používá omezování pro ochranu před útoky s cílem odepření služby (DoS) cloudové služby. Azure AD momentálně má limit omezení 7000 zápisů za 5 minut (84,000 za hodinu). Například můžete omezit následující operace:



- Azure AD Connect pro export do služby Azure AD.
- Skripty prostředí PowerShell nebo aktualizace Azure AD přímo aplikací i na pozadí, jako je například dynamické členství ve skupinách.
- Uživatelé, aktualizuje se vlastní záznamy identity jako je registrace pro vícefaktorové ověřování a samoobslužné resetování HESLA (samoobslužné resetování hesla).
- Operace v rámci grafické uživatelské rozhraní.

Plánování nasazení a Správa úloh, abyste měli jistotu, že váš cyklus synchronizace Azure AD Connect není ovlivněn limitů omezování. Pokud máte velké náborovou wave kde vytvořit tisíce identity uživatelů, může to způsobit aktualizace pro dynamické členství ve skupinách, přiřazení, licencování a registrace resetování hesla pomocí samoobslužné služby. Je lepší šíření těchto zápisu přes několik hodin i několik dní.

### <a name="sql-database-factors"></a>Faktory databáze SQL

Velikost vaší topologie služby Active Directory zdroj ovlivní výkon databáze SQL. Postupujte podle [požadavky na hardware](how-to-connect-install-prerequisites.md) pro SQL server, databázi a zvažte následující doporučení:



- Organizace s víc než 100 000 uživatelů, můžete snížit latenci sítě tak, že společné umístění databáze SQL a zřizování stroj na stejném serveru.
- Z důvodu vysoké disku, vstup a výstup (vstupně-výstupních operací) požadavky procesu synchronizace pomocí Solid stav jednotky SSD (Solid-State Drive) pro SQL database zřizování modulu pro dosažení optimálních výsledků, pokud to není možné, zvažte konfigurace RAID 1 nebo RAID 0.
- Neprovádějte preventivně; úplná synchronizace způsobí, že zbytečné četnosti změn a pomalejší doby odezvy.

## <a name="conclusion"></a>Závěr

Chcete-li optimalizovat výkon vaší implementace služby Azure AD Connect, zvažte následující doporučení:



- Použití [doporučené konfigurace hardwaru](how-to-connect-install-prerequisites.md) na základě velikosti vaší implementace pro server Azure AD Connect.
- Při upgradu služby Azure AD Connect v nasazení ve velkém měřítku, zvažte použití [Postupná migrace metoda](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration), ujistěte se, že máte alespoň výpadky a nejlepší spolehlivost. 
- Pro databázi SQL pro nejlepší výkon zápisu používají SSD.
- Filtrovat služby Active Directory oboru zahrnout pouze objekty, které je třeba zřídit ve službě Azure AD, pomocí doménu, organizační jednotky nebo filtrování atributů.
- Pokud potřebujete změnit výchozí pravidla pro tok atributů, nejprve zkopírovat pravidlo, pak změňte kopie a původní pravidlo zakázat. Nezapomeňte znovu spustit úplnou synchronizaci.
- Naplánujte dostatečný čas pro profil spuštění počáteční úplné synchronizace.
- Přitom se snaží dokončit cyklus synchronizace delta za 30 minut. Pokud profil rozdílové synchronizace nedokončí za 30 minut, upravte výchozí frekvence synchronizace zahrnout cyklus úplné rozdílové synchronizace.
- Monitorování vaší [stavu synchronizace Azure AD Connect](how-to-connect-health-agent-install.md) ve službě Azure AD.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
