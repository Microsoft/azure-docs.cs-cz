---
title: Azure Database for PostgreSQL jeden server
description: Poskytuje přehled o Azure Database for PostgreSQL jednom serveru.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 098d6e3712a928392547fcd2cba68b68dcca5ef3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476140"
---
# <a name="azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL jeden server

[Azure Database for PostgreSQL](./overview.md) , kterou používá PostgreSQL Community Edition, je k dispozici ve třech režimech nasazení:

- Jeden server
- Flexibilní server (Preview)
- Hyperscale (Citus)

V tomto článku budeme poskytovat přehled a Úvod do základních konceptů modelu nasazení na jeden server. Další informace o flexibilním režimu nasazení serveru najdete v tématu Přehled [flexibilního přehledu serveru](./flexible-server/overview.md) a Citus ().

## <a name="overview"></a>Přehled

Jeden server je plně spravovaná databázová služba s minimálními požadavky pro přizpůsobení databáze. Platforma s jednou serverem je navržená tak, aby zpracovávala většinu funkcí správy databáze, jako jsou třeba opravy, zálohování, vysoká dostupnost, zabezpečení s minimální konfigurací a řízením uživatele. Architektura je optimalizovaná tak, aby poskytovala 99,99% dostupnost v jedné zóně dostupnosti. Podporuje komunitní verze PostgreSQL 9,5, 9,6, 10 a 11. Tato služba je všeobecně dostupná pro celou řadu [oblastí Azure](https://azure.microsoft.com/global-infrastructure/services/).

Pro cloudové nativní aplikace navržené pro zpracování automatizovaných oprav bez nutnosti podrobného řízení plánu oprav a vlastních nastavení konfigurace PostgreSQL jsou samostatné servery vhodné.

## <a name="high-availability"></a>Vysoká dostupnost

Model nasazení s jedním serverem je optimalizovaný pro integrovanou vysokou dostupnost a pružnost na nižších nákladech. Architektura odděluje výpočetní výkon a úložiště. Databázový stroj běží na speciálním výpočetním kontejneru, zatímco datové soubory se nacházejí v úložišti Azure. Úložiště udržuje tři místně redundantní synchronní kopie databázových souborů, které zajišťují odolnost proti datům.

Při výpadku plánovaných nebo neplánovaných událostí pro převzetí služeb při selhání udržuje služba vysokou dostupnost serverů pomocí následujícího automatizované procedury:

1. Zřizuje se nový výpočetní kontejner.
2. Úložiště s datovými soubory je namapováno na nový kontejner.
3. Databázový stroj PostgreSQL se do nového kontejneru COMPUTE přepne do online režimu.
4. Služba brány zajišťuje transparentní převzetí služeb při selhání, které nevyžadují žádné změny na straně aplikace.
   
 :::image type="content" source="./media/overview/overview-azure-postgres-single-server.png" alt-text="Azure Database for PostgreSQL jeden server":::

Typický časový rozsah pro převzetí služeb při selhání je od 60-120 sekund. Cloudový nativní návrh jedné serverové služby umožňuje IT podpoře 99,99% dostupnosti, což eliminuje náklady na pasivní aktivní pohotovostní režim.

99,99 špičková smlouva o úrovni služeb (SLA) v oboru Azure, která využívá globální síť datacenter spravovaných Microsoftem, pomáhá udržet vaše aplikace s 24/7.

## <a name="automated-patching"></a>Automatizované opravy

Služba provádí automatizované opravy základního hardwaru, operačního systému a databázového stroje. Opravy zahrnují zabezpečení a aktualizace softwaru. V případě stroje PostgreSQL jsou upgrady podverze automaticky a zahrnuty v rámci cyklu oprav. Pro opravy není nutná žádná akce uživatele nebo nastavení konfigurace. Frekvence oprav je spravovaná služba na základě závažnosti datové části. Obecně platí, že služba dodržuje plán měsíčního vydání jako součást průběžné integrace a vydání. Uživatelé se můžou přihlásit k odběru [oznámení o plánované údržbě]() , aby obdrželi oznámení o nadcházející údržbě 72 hodin před událostí.

## <a name="automatic-backups"></a>Automatické zálohování

Jedna serverová služba automaticky vytvoří zálohy serveru a uloží je do místně nakonfigurovaného místně redundantního (LRS) nebo geograficky redundantního úložiště. Zálohy můžete použít k obnovení serveru k jakémukoli časovému okamžiku v rámci doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Doba uchovávání může být volitelně nakonfigurovaná na 35 dní. Všechny zálohy se šifrují s využitím 256bitového šifrování AES. Podrobnosti najdete v tématu [zálohování](./concepts-backup.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Úprava výkonu a škálování během několika sekund

Jedna serverová služba je k dispozici ve třech úrovních SKU: základní, Pro obecné účely a paměťově optimalizovaná. Úroveň Basic je vhodná pro vývoj s nízkými náklady a pro úlohy s nízkou úrovní souběžnosti. Pro obecné účely a paměť optimalizované pro produkční úlohy, které vyžadují vysokou souběžnost, škálování a předvídatelný výkon, jsou lépe vhodné. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Škálování úložiště je online a podporuje automatické zvětšování úložiště. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které využíváte. Podrobnosti najdete v tématu [Cenové úrovně]().

## <a name="enterprise-grade-security-compliance-and-governance"></a>Zabezpečení, dodržování předpisů a řízení bezpečnosti na podnikové úrovni

Služba Single server používá šifrovací modul ověřený 140-2 Standard FIPS pro šifrování úložiště neaktivních dat. Data, včetně záloh a dočasných souborů vytvořených při spouštění dotazů, jsou zašifrovaná. Služba používá algoritmus AES 256-bit, který je součástí šifrování úložiště Azure, a klíče můžou být spravované systémem (výchozí) nebo [spravované zákazníky](). Služba šifruje přenášená data pomocí protokolu TLS (Transport Layer Security), který je ve výchozím nastavení vynutil. Služba podporuje TLS verze 1,2, 1,1 a 1,0 s možností vyhovět [minimální verzi TLS]().

Služba umožňuje privátní přístup k serverům pomocí privátního propojení a poskytuje funkci rozšířené ochrany před internetovými útoky. Rozšířená ochrana před internetovými útoky detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Kromě nativního ověřování podporuje jediná serverová služba Azure Active Directory ověřování. Ověřování Azure AD je mechanismus připojení k serverům PostgreSQL pomocí identit definovaných a spravovaných ve službě Azure AD. Pomocí ověřování Azure AD můžete spravovat identity uživatelů databáze a další služby Azure v centrálním umístění, které zjednodušuje a centralizovat řízení přístupu.

[Protokolování auditu]() (ve verzi Preview) je k dispozici ke sledování všech aktivit na úrovni databáze.

Jediná serverová služba je stížnost se všemi špičkovými certifikacemi, jako je FedRAMP, HIPAA a PCI DSS. Informace o zabezpečení platformy Azure najdete v [Centru zabezpečení Azure]().

Další informace o funkcích zabezpečení Azure Database for PostgreSQL najdete v [přehledu zabezpečení]().

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Jedna serverová služba je vybavená integrovanými funkcemi pro monitorování výkonu a upozorňování. Všechny metriky Azure mají četnost jednosměrných minut a každá metrika poskytuje historii 30 dnů. Výstrahy můžete nakonfigurovat pro metriky. Služba umožňuje konfigurovat protokoly pomalých dotazů a dodává se s odlišnou funkcí [úložiště dotazů](./concepts-query-store.md) . Úložiště dotazů zjednodušuje řešení potíží s výkonem tím, že vám pomůže rychle najít nejdelší běžící a většinu dotazů náročných na prostředky. Pomocí těchto nástrojů můžete rychle optimalizovat vaše úlohy a nakonfigurovat server tak, aby co nejlépe vyzpůsobil výkon. Podrobnosti najdete v tématu [monitorování](./concepts-monitoring.md) .

## <a name="migration"></a>Migrace

Služba spouští komunitní verze PostgreSQL. To umožňuje úplnou kompatibilitu aplikací a vyžaduje minimální náklady na Refaktoring pro migraci existující aplikace vyvíjené na PostgreSQL stroji na jedinou serverovou službu. Migraci na jeden server lze provést pomocí jedné z následujících možností:

- **Vypsat a obnovit** – pro offline migrace, kde můžou uživatelé získat nějaké výpadky, vypsat a obnovit pomocí nástrojů komunity, jako je Pg_dump, a Pg_restore můžou poskytnout nejrychlejší způsob migrace. Podrobnosti najdete v tématu [migrace pomocí výpisu paměti a obnovení](./howto-migrate-using-dump-and-restore.md) .
- **Azure Database Migration Service** – pro bezproblémové a zjednodušené migrace na jediný server s minimálními výpadky je možné využít Azure Database Migration Service. Viz [DMS prostřednictvím portálu](../dms/tutorial-postgresql-azure-postgresql-online-portal.md) a [DMS prostřednictvím](../dms/tutorial-postgresql-azure-postgresql-online.md)rozhraní příkazového řádku.

## <a name="contacts"></a>Kontakty

Pokud máte jakékoli dotazy nebo návrhy, které byste mohli potřebovat při práci s Azure Database for PostgreSQL, pošlete e-mail týmu Azure Database for PostgreSQL ([ @Ask Azure DB pro PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Tato e-mailová adresa není alias technické podpory.

Můžete také využít tyto komunikační kanály:

- Pro podporu Azure [vytvořte lístek podpory na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Další kroky

Teď, když jste si přečetli Úvod do Azure Database for PostgreSQL režimu nasazení na jeden server, jste připraveni:
- Vytvořte svůj první server.
