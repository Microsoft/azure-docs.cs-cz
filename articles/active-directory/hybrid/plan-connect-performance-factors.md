---
title: Faktory ovlivňující výkon nástroje Azure AD Connect
description: Tento dokument vysvětluje, jak různé faktory ovlivňují zřizovací modul Azure AD Connect. Tyto faktory pomohou organizacím naplánovat nasazení služby Azure AD Connect a zajistit, aby splňovalo jejich požadavky na synchronizaci.
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
ms.openlocfilehash: a5518d516848ba7c006827faa41ff76bbca35d0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897052"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Faktory ovlivňující výkon nástroje Azure AD Connect

Azure AD Connect synchronizuje službu Active Directory se službou Azure AD. Tento server je důležitou součástí přesunutí uživatelských identit do cloudu. Primární faktory, které ovlivňují výkon Azure AD Connect jsou:

| **Konstrukční faktor**| **Definice** |
|:-|-|
| Topologie| Distribuce koncových bodů a součástí Azure AD Connect musí spravovat v síti. |
| Škálování| Počet objektů, jako jsou uživatelé, skupiny a vou, které mají být spravovány Azure AD Connect. |
| Hardware| Hardware (fyzický nebo virtuální) pro Azure AD Connect a závislé kapacity výkonu každé hardwarové součásti, včetně konfigurace procesoru, paměti, sítě a pevného disku. |
| Konfigurace| Jak Azure AD Connect zpracovává adresáře a informace. |
| Načtení| Frekvence změn objektu. Zatížení se může lišit během hodiny, dne nebo týdne. V závislosti na součásti může být pravděpodobně navrženo špičkové zatížení nebo průměrné zatížení. |

Účelem tohoto dokumentu je popsat faktory ovlivňující výkon zřizovacího modulu Azure AD Connect. Velké nebo složité organizace (organizace zřizující více než 100 000 objektů) můžete použít doporučení k optimalizaci jejich implementace Azure AD Connect, pokud dojde k problémům s výkonem zde popsané. Ostatní součásti Azure AD Connect, jako je [například Azure AD Connect stavu](how-to-connect-health-agent-install.md) a agenti nejsou zahrnuty zde.

> [!IMPORTANT]
> Microsoft nepodporuje úpravy nebo provoz Azure AD Connect mimo akce, které jsou formálně zdokumentovány. Každá z těchto akcí může mít za následek nekonzistentní nebo nepodporovaný stav synchronizace Azure AD Connect. V důsledku toho společnost Microsoft nemůže poskytovat technickou podporu pro tato nasazení.

## <a name="azure-ad-connect-component-factors"></a>Faktory komponent Azure AD Connect

Následující diagram znázorňuje architekturu vysoké úrovně zřizovacího modulu, který se připojuje k jedné doménové struktuře, i když je podporováno více doménových struktur. Tato architektura ukazuje, jak různé součásti vzájemně spolupracují.

![AzureADConnentInterní](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Zřizovací modul se připojuje ke každé doménové struktuře služby Active Directory a k Azure AD. Proces čtení informací z každého adresáře se nazývá Import. Export odkazuje na aktualizaci adresářů z zřizovacího modulu. Synchronizace vyhodnocuje pravidla, jak objekty bude toku uvnitř zřizovacího modulu. Pro hlubší ponor můžete odkazovat na [Synchronizaci Azure AD Connect: Principy architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

Azure AD Connect používá následující pracovní oblasti, pravidla a procesy k povolení synchronizace ze služby Active Directory do služby Azure AD:

* **Mezerník (CS)** - Objekty z každého připojeného adresáře (CD), skutečné adresáře, jsou připraveny zde nejprve před jejich zpracováním zřizovacím strojem. Azure AD má vlastní CS a každá doménová struktura, ke které se připojíte, má vlastní CS.
* **Metaverse (MV)** - Objekty, které je třeba synchronizovat, jsou zde vytvářeny na základě pravidel synchronizace. Objekty musí existovat v MV před tím, než mohou naplnit objekty a atributy do jiných připojených adresářů. Je tu jen jeden MV.
* **Pravidla synchronizace** – rozhodnou, které objekty budou vytvořeny (promítnuty) nebo připojeny (spojeny) s objekty v MV. Pravidla synchronizace také rozhodnou, které hodnoty atributů budou zkopírovány nebo transformovány do adresářů a z adresářů.
* **Spustit profily** – sedne kroky procesu kopírování objektů a jejich hodnot atributů podle pravidel synchronizace mezi pracovními oblastmi a připojenými adresáři.

Existují různé profily spuštění pro optimalizaci výkonu zřizovacího modulu. Většina organizací bude používat výchozí plány a spustit profily pro normální operace, ale některé organizace může mít [změnit plán](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) nebo aktivovat jiné profily spuštění pro neobvyklé situace. K dispozici jsou následující profily spuštění:

### <a name="initial-sync-profile"></a>Počáteční profil synchronizace

Profil počáteční synchronizace je proces prvního čtení připojených adresářů, například doménové struktury služby Active Directory. Potom provádí analýzu všech položek v databázi synchronizačního modulu. Počáteční cyklus vytvoří nové objekty ve službě Azure AD a bude trvat déle, než dokončení, pokud vaše doménové struktury služby Active Directory jsou velké. Počáteční synchronizace zahrnuje následující kroky:

1. Úplný import na všechny konektory
2. Úplná synchronizace na všech konektorech
3. Export na všechny konektory

### <a name="delta-sync-profile"></a>Profil synchronizace delta

Chcete-li optimalizovat proces synchronizace, tento profil spuštění zpracovává pouze změny (vytváří, odstraňuje a aktualizuje) objektů v připojených adresářích od posledního procesu synchronizace. Ve výchozím nastavení se profil synchronizace delta spouští každých 30 minut. Organizace by se měly snažit zachovat čas potřebný k méně než 30 minut, aby se ujistil, Azure AD je aktuální. Chcete-li sledovat stav Azure AD Connect, použijte [agenta monitorování stavu](how-to-connect-health-sync.md) zobrazíte všechny problémy s procesem. Profil synchronizace delta obsahuje následující kroky:

1. Rozdílový import na všech konektorech
2. Synchronizace delta na všech konektorech
3. Export na všechny konektory

Typický scénář synchronizace rozdílové synchronizace organizace organizace je:

- Přibližně 1 % objektů je odstraněno.
- Vytvoří se ~1 % objektů
- Přibližně 5 % objektů je upraveno

Míra změn se může lišit v závislosti na tom, jak často vaše organizace aktualizuje uživatele ve službě Active Directory. Například vyšší míra změn může nastat s sezónností náboru a snižování pracovní síly.

### <a name="full-sync-profile"></a>Profil úplné synchronizace

Úplný cyklus synchronizace je vyžadován, pokud jste provedli některou z následujících změn konfigurace:



- Byl zvýšen rozsah objektů nebo atributů, které mají být importovány z připojených adresářů. Například když přidáte doménu nebo ou do oboru importu.
- Provedl a provedl změny pravidel synchronizace. Například při vytvoření nového pravidla k naplnění názvu uživatele ve službě Azure AD z extension_attribute3 ve službě Active Directory. Tato aktualizace vyžaduje, aby zřizovací modul znovu prozkoumat všechny stávající uživatele aktualizovat své názvy použít změny do budoucna.

Následující operace jsou zahrnuty v cyklu úplné synchronizace:

1. Úplný import na všechny konektory
2. Úplná/rozdílová synchronizace na všech konektorech
3. Export na všechny konektory

> [!NOTE]
> Pečlivé plánování je vyžadováno při hromadných aktualizacích mnoha objektů ve službě Active Directory nebo Azure AD. Hromadné aktualizace způsobí, že proces synchronizace delta trvat déle při importu, protože mnoho objektů se změnilo. K dlouhým importům může dojít i v případě, že hromadná aktualizace nemá vliv na proces synchronizace. Například přiřazení licencí mnoha uživatelům ve službě Azure AD způsobí dlouhý cyklus importu z Azure AD, ale nezpůsobí žádné změny atributů ve službě Active Directory.

### <a name="synchronization"></a>Synchronizace

Modul runtime procesu synchronizace má následující charakteristiky výkonu:

* Synchronizace je jednovláknová, což znamená, že zřizovací modul neprovádí žádné paralelní zpracování spuštěných profilů připojených adresářů, objektů nebo atributů.
* Doba importu lineárně roste s počtem synchronizovaných objektů. Například pokud 10 000 objektů trvat 10 minut importovat, pak 20 000 objektů bude trvat přibližně 20 minut na stejném serveru.
* Export je také lineární.
* Synchronizace bude exponenciálně růst na základě počtu objektů s odkazy na jiné objekty. Členství ve skupinách a vnořené skupiny mají hlavní dopad na výkon, protože jejich členové odkazují na objekty uživatelů nebo jiné skupiny. Tyto odkazy musí být nalezeny a odkazoval se na skutečné objekty v MV k dokončení cyklu synchronizace.

### <a name="filtering"></a>Filtrování

Velikost topologie služby Active Directory, kterou chcete importovat, je faktorem číslo jedna ovlivňujícím výkon a celkovou dobu, po kterou budou vnitřní součásti zřizovacího modulu trvat.

[Filtrování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) by měla být použita ke snížení objekty synchronizované. Zabrání nepotřebné objekty z zpracované a exportované do Azure AD. V pořadí podle preference jsou k dispozici následující techniky filtrování:



- **Filtrování založené na doméně** – tuto možnost použijte k výběru konkrétních domén pro synchronizaci se službou Azure AD. Při provádění změn místní infrastruktury po instalaci synchronizace Azure AD Connect je nutné přidat a odebrat domény z konfigurace synchronizačního modulu.
- **Filtrování organizační jednotky (OU)** – používá organizační jednotky k cílení na konkrétní objekty v doménách služby Active Directory pro zřizování do Azure AD. Filtrování ou je druhý doporučený mechanismus filtrování, protože k importu menší podmnožiny objektů ze služby Active Directory používá jednoduché dotazy oboru LDAP.
- **Filtrování atributů na objekt** – používá hodnoty atributů na objektech k rozhodnutí, zda je ve službě Azure AD zřízen konkrétní objekt ve službě Active Directory. Filtrování atributů je skvělé pro jemné doladění filtrů, když filtrování domény a ou nesplňuje specifické požadavky na filtrování. Filtrování atributů nezkracuje dobu importu, ale může zkrátit dobu synchronizace a exportu.
- **Filtrování založené na skupinách** – používá členství ve skupině k rozhodnutí, zda objekty by měly být zřízeny ve službě Azure AD. Filtrování založené na skupině je vhodné pouze pro testovací situace a není doporučeno pro produkční prostředí, z důvodu dodatečné režie potřebné ke kontrole členství ve skupině během cyklu synchronizace.

Mnoho [trvalých odpojovacích objektů](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) v cs služby Active Directory může způsobit delší dobu synchronizace, protože zřizovací modul musí přehodnotit každý objekt odpojovače pro možné připojení v cyklu synchronizace. Chcete-li tento problém překonat, zvažte jedno z následujících doporučení:



- Umístěte objekty odpojovače mimo rozsah pro import pomocí filtrování domény nebo ou.
- Project/join objekty MV a nastavte [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) atribut rovná True, aby se zabránilo zřizování těchto objektů v Azure AD CS.

> [!NOTE]
> Uživatelé mohou být zmateni nebo může dojít k problémům s oprávněními aplikací, pokud je filtrováno příliš mnoho objektů. Například v hybridní implementaci Exchange online se uživatelům s místními poštovními schránkami zobrazí v globálním seznamu adres více uživatelů než uživatelů s poštovními schránkami v Exchange online. V ostatních případech může uživatel chtít udělit přístup v cloudové aplikaci jinému uživateli, který není součástí oboru filtrované sady objektů.

### <a name="attribute-flows"></a>Toky atributů

Toky atributů je proces kopírování nebo transformace hodnot atributů objektů z jednoho připojeného adresáře do jiného připojeného adresáře. Jsou definovány jako součást pravidel synchronizace. Například při změně telefonního čísla uživatele ve službě Active Directory, telefonní číslo ve službě Azure AD se aktualizuje. Organizace mohou [upravit toky atributů](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) do sady různých požadavků. Doporučujeme zkopírovat existující toky atributů před jejich změnou.

Jednoduché přesměrování, jako je přetékání hodnoty atributu na jiný atribut nemá vliv na výkon materiálu. Příkladem přesměrování je tok mobilního čísla ve službě Active Directory na telefonní číslo kanceláře ve službě Azure AD.

Transformace hodnoty atributů může mít vliv na výkon procesu synchronizace. Transformace hodnot atributů zahrnuje úpravy, přeformátování, zřetězení nebo odčítání hodnot atributů.

Organizace můžete zabránit určité atributy toku do Azure AD, ale nebude mít vliv na výkon zřizovacího modulu.

> [!NOTE]
> Neodstraňujte nežádoucí toky atributů v pravidlech synchronizace. Doporučujeme je raději zakázat, protože odstraněná pravidla se znovu vytvoří během upgradů služby Azure AD Connect.

## <a name="azure-ad-connect-dependency-factors"></a>Faktory závislostí Azure AD Connect

Výkon Azure AD Connect závisí na výkonu připojených adresářů, které importuje a exportuje do. Například velikost služby Active Directory, kterou potřebuje k importu, nebo latence sítě ve službě Azure AD. Databáze SQL zřizovací modul používá také vliv na celkový výkon cyklu synchronizace.

### <a name="active-directory-factors"></a>Faktory služby Active Directory

Jak již bylo zmíněno dříve, počet objektů, které mají být importovány, významně ovlivňuje výkon. [Hardware a předpoklady pro Azure AD Connect](how-to-connect-install-prerequisites.md) popisují konkrétní úrovně hardwaru na základě velikosti vašeho nasazení. Azure AD Connect podporuje jenom konkrétní topologie, jak je uvedeno v [topologie pro Azure AD Connect](plan-connect-topologies.md). Neexistují žádné optimalizace výkonu a doporučení pro nepodporované topologie.

Ujistěte se, že váš server Azure AD Connect splňuje požadavky na hardware na základě velikosti služby Active Directory, kterou chcete importovat. Chybné nebo pomalé síťové připojení mezi serverem Azure AD Connect a řadiči domény služby Active Directory může zpomalit import.

### <a name="azure-ad-factors"></a>Faktory Azure AD

Azure AD používá omezení k ochraně cloudové služby před útoky odmítnutí služby (DoS). V současné době Azure AD má omezení limit 7 000 zápisů za 5 minut (84 000 za hodinu). Například následující operace mohou být omezeny:



- Export Azure AD Connect do Azure AD.
- Skripty nebo aplikace prostředí PowerShell, které aktualizují Azure AD přímo na pozadí, jako je například dynamická členství ve skupinách.
- Uživatelé aktualizují své vlastní záznamy identit, jako je například registrace pro vícefaktorové ověřování nebo samoobslužné resetování hesla.
- Operace v rámci grafického uživatelského rozhraní.

Naplánujte úlohy nasazení a údržby, abyste se ujistili, že váš cyklus synchronizace Azure AD Connect není ovlivněn omezeními omezení. Pokud například máte velkou náborovou vlnu, kde vytváříte tisíce identit uživatelů, může to způsobit aktualizace dynamických členství ve skupinách, přiřazení licencí a samoobslužných registrací resetování hesla. Je lepší šířit tyto zápisy na několik hodin nebo několik dní.

### <a name="sql-database-factors"></a>Faktory databáze SQL

Velikost zdrojové topologie služby Active Directory ovlivní výkon databáze SQL. Postupujte [podle hardwarových požadavků](how-to-connect-install-prerequisites.md) pro databázi serveru SQL a zvažte následující doporučení:



- Organizace s více než 100 000 uživateli mohou snížit latenci sítě pomocí společného přidělení databáze SQL a zřizovacího modulu na stejném serveru.
- Vzhledem k vysokým požadavkům na vstup a výstup disku (I/O) procesu synchronizace použijte jednotky SSD (SSD) pro databázi SQL zřizovacího modulu pro dosažení optimálních výsledků, pokud to není možné, zvažte konfigurace RAID 0 nebo RAID 1.
- Neprovázte úplnou synchronizaci preventivně; způsobuje zbytečné konve a pomalejší doby odezvy.

## <a name="conclusion"></a>Závěr

Chcete-li optimalizovat výkon implementace služby Azure AD Connect, zvažte následující doporučení:



- Použijte [doporučenou konfiguraci hardwaru](how-to-connect-install-prerequisites.md) na základě velikosti implementace pro server Azure AD Connect.
- Při upgradu Služby Azure AD Connect ve velkých nasazeních zvažte použití [metody migrace houpavosti](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration)a ujistěte se, že máte nejmenší prostoje a nejlepší spolehlivost. 
- Použijte SSD pro databázi SQL pro nejlepší výkon zápisu.
- Filtrujte obor služby Active Directory tak, aby zahrnoval pouze objekty, které je potřeba zřídit ve službě Azure AD pomocí filtrování domény, ou nebo atributů.
- Pokud požadujete změnit výchozí pravidla toku atributu, nejprve zkopírujte pravidlo, potom změňte kopii a zakažte původní pravidlo. Nezapomeňte znovu spustit úplnou synchronizaci.
- Naplánujte dostatečný čas pro počáteční úplný profil spuštění synchronizace.
- Snažte se dokončit cyklus synchronizace delta za 30 minut. Pokud se profil synchronizace delta nedokončí za 30 minut, upravte výchozí frekvenci synchronizace tak, aby zahrnovala úplný cyklus synchronizace delta.
- Monitorujte [stav synchronizace Azure AD Connect](how-to-connect-health-agent-install.md) ve službě Azure AD.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
