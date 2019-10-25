---
title: Ukládání, Správa a uchovávání dat aplikací v cloudu pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je App Center, které vám umožní ukládat, spravovat a uchovávat data mobilní aplikace v cloudu.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795572"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Ukládání, synchronizace a dotazování dat mobilní aplikace z cloudu
Bez ohledu na to, jaký druh aplikace sestavíte, budete pravděpodobně generovat a zpracovávat data. Očekávání uživatelů jsou vysoká a chtějí, aby aplikace fungovala rychle a hladce, a to za všech okolností. Většina aplikací pracuje na více zařízeních, takže když přistupujete k aplikaci, budete k nim mít přístup z desktopového nebo mobilního zařízení. Tuto aplikaci může používat více uživatelů současně a sdílet data s očekáváním získání přístupu k datům v reálném čase.

Nad tím, že uživatelé aplikace nebudou mít vždycky připojení k Internetu. aplikace jsou navržené a očekává se, že budou pracovat s připojením k Internetu nebo bez něj. U všech těchto rostoucích složitosti je to velmi náročný úkol, který vývojářům umožňuje vybrat vhodné řešení pro ukládání a synchronizaci dat do cloudu a zajistit tak skvělé prostředí pro zákazníky v rámci své aplikace.

Společnost Microsoft poskytuje celou řadu služeb, které eliminují nutnost vystavovat servery, vybírat svou databázi nebo si dělat starosti se škálováním a zabezpečením, aby poskytovala co možná hodně možností. Tyto služby poskytují skvělé vývojové prostředí, které umožňuje ukládat data aplikací do cloudu pomocí rozhraní API SQL nebo NoSQL, synchronizovat data na všech zařízeních a povolit aplikaci pracovat se systémem nebo bez připojení k síti, aby bylo možné lépe sestavit škálovatelné a robustní. vyrovnání.

Pomocí následujících služeb můžete spravovat a ukládat data mobilní aplikace v cloudu.

## <a name="visual-studio-app-center"></a>Centrum aplikací Visual Studio
[App Center Data](/appcenter/data/) je služba pro správu dat, která umožňuje aplikacím spravovat, uchovávat a synchronizovat data aplikací v cloudu napříč různými zařízeními a platformami v online i offline scénáři. Tato služba je **postavená na službě Cosmos DB** , která se škáluje podle vaší uživatelské základny a počtu aplikací, a zajišťuje tak nízkou latenci, vysokou dostupnost a vysokou škálovatelnost pro všechna vaše data.

**Klíčové funkce**
- Umožňuje snadno **zřídit novou Cosmos DB databázi** nebo **se připojit k existující databázi Cosmos DB** z portálu App Center.
- **Podpora databáze NoSQL** , která umožňuje snadno ukládat, synchronizovat a dotazovat data aplikací.
- Tato služba je **postavená na Cosmos DB**, která poskytuje většinu klíčových funkcí nabízených v rámci Azure Cosmos DB a umožňuje **globálně škálovat** , aby splňovaly vaše obchodní potřeby.
- Možnosti **online a offline synchronizace** pro synchronizaci dat napříč zařízeními.
- Sady **SDK mobilních klientů** , které umožňují snadno spravovat data privátních aplikací.
- **Podpora platforem** – iOS, Android, Xamarin, reakce nativní.

**Odkazy**
- [Zaregistrujte se App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s App Centermi daty](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je globálně distribuovaná databázová služba Microsoftu, která vám pomůže sestavovat aplikace v globálním měřítku. Když kliknete na tlačítko, Cosmos DB vám umožní elasticky a nezávisle škálovat propustnost a úložiště napříč libovolným počtem oblastí Azure po celém světě. Můžete elasticky škálovat propustnost a úložiště a využít rychlý přístup k datům v řádu milisekund pomocí vašich oblíbených ploch rozhraní API, včetně SQL, MongoDB, Cassandra, tabulek nebo Gremlin. Cosmos DB jednoznačně poskytuje kompletní smlouvy o úrovni služeb (SLA) pro propustnost, latenci, dostupnost a konzistenci.

**Klíčové funkce**
- Podporuje **široké spektrum rozhraní API** , zahrnuje rozhraní API SQL (Core), rozhraní API Cassandra, rozhraní API MongoDB, rozhraní Gremlin api a rozhraní API pro tabulky.
- **Globální distribuce klíč** replikuje vaše data všude, kde jsou vaši uživatelé, takže vaši uživatelé můžou pracovat s replikou dat, která jsou nejblíže.
- **Žádná Správa schématu ani indexu** , protože databázový stroj je plně schématu – nezávislá.
- **Všudypřítomný regionální přítomnost** , protože Cosmos DB je dostupná ve všech oblastech Azure po celém světě, včetně 54 + oblastí ve veřejném cloudu.
- **Přesně definované, vícenásobné volby konzistence,** jako je například protokol replikace s více hlavními servery Cosmos DB, je pečlivě navržena tak, aby nabízela pět jasně definovaných voleb konzistence – silná, ohraničená neaktuálnost, relace, konzistentní předpona a konečné.
- **99,999% dostupnost** pro čtení i zápis.
- **Prostřednictvím kódu programu (nebo prostřednictvím portálu) se vyvolají místní převzetí služeb při selhání** účtu Cosmos, abyste měli jistotu, že je vaše aplikace navržená tak, aby vydržela regionální
- **Garantovaná nízká latence na 99. percentilu, po**celém světě.

**Odkazy**
- [Azure Portal](https://portal.azure.com) 
- [Nápovědě](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) je spravovaná služba relační databáze pro obecné účely, která umožňuje vytvořit vysoce dostupnou a vysoce výkonnou vrstvu úložiště dat pro aplikace a řešení v cloudu Azure.

**Klíčové funkce**
- **Modely elastické databáze a nástroje** – elastická databáze dává vývojářům možnost vytvářet fondy prostředků, které se mají použít ve skupině databází pro škálování, které se pak dají spravovat tak, že se skript odešle jako úloha a SQL Database provede. skript napříč databázemi.
- **Vysoce výkonné** aplikace s vysokou propustností můžou využít nejnovější verzi, která přináší o 25% vyšší výkon databáze Premium.
- **Zálohování, replikace a vysoká dostupnost** – integrovaná replikace a smlouva SLA založená na Microsoftu na úrovni databáze poskytují kontinuitu aplikací a ochranu před závažnými událostmi. Aktivní geografická replikace umožňuje nakonfigurovat převzetí služeb při selhání a samoobslužné obnovení, které poskytuje úplnou kontrolu nad "neobnovením" (obnovení dat z dostupných záloh dat až do 35 dnů).
- **Téměř nulová údržba** – automatický software je součástí služby a integrované systémové repliky pomůžou zajistit ochranu dat, dobu provozu databáze a stabilitu systému. Systémové repliky se automaticky přesouvají na nové počítače, které jsou při výpadku původních počítačů okamžitě dostupné.
- **Security** -SQL Database nabízí portfolio funkcí zabezpečení pro splnění zásad dodržování předpisů v organizaci nebo v oboru.
    - Auditování poskytuje vývojářům možnost provádět úlohy související s dodržováním předpisů a získat poznatky o aktivitách.
    - Vývojáři a IT můžou implementovat zásady na úrovni databáze, abyste omezili přístup k citlivým datům pomocí zabezpečení na úrovni řádků, dynamického maskování dat a transparentního šifrování dat pro Azure SQL Database.
    - Služba Key Cloud Auditors ověřuje SQL Database v rámci rozsahu klíčových certifikátů a schválení dodržování předpisů Azure, například HIPAA BAA, ISO/IEC 27001:2005, FedRAMP a vzorové klauzule EU).

**Odkazy**
- [Azure Portal](https://portal.azure.com) 
- [Nápovědě](/azure/sql-database/)
   