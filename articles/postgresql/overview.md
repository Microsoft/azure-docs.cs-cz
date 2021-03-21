---
title: Co je Azure Database for PostgreSQL
description: Poskytuje přehled služby Azure Database for PostgreSQL relačních databází v kontextu flexibilního serveru.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: c3ea7930f41fe89538a817da032e993e534db9cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92491321"
---
# <a name="what-is-azure-database-for-postgresql"></a>Co je Azure Database for PostgreSQL?

Azure Database for PostgreSQL je služba relačních databází v cloudu Microsoftu založená na [PostgreSQL Community Edition](https://www.postgresql.org/) (k dispozici v rámci licenčního stroje GPLv2). Azure Database for PostgreSQL nabízí:

- Integrovaná vysoká dostupnost
- Ochrana dat pomocí automatických záloh a obnovení k bodu v čase po dobu až 35 dnů.
- Automatizovaná údržba základního hardwaru, operačního systému a databázového stroje, aby byla služba zabezpečená a aktuálnost.
- Předvídatelný výkon s využitím celkových průběžných plateb.
- Elastické škálování během několika sekund.
- Zabezpečení na podnikové úrovni a špičkové dodržování předpisů pro ochranu citlivých dat v klidovém a pohybovém provozu.
- Monitorování a automatizace, které zjednodušují správu a monitorování pro rozsáhlá nasazení.
- Špičkové prostředí podpory v oboru

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Azure Database for PostgreSQL":::

Tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Díky tomu se můžete soustředit na rychlý vývoj aplikací a urychlení doby uvedení na trh, ale nemusíte přiřazovat drahý čas a prostředky pro správu virtuálních počítačů a infrastruktury. Kromě toho můžete pokračovat ve vývoji aplikací pomocí libovolných opensourcových nástrojů a platforem a dodávat je tak rychle a efektivně, jak váš podnik vyžaduje – a to vše, aniž byste se museli učit nové dovednosti.

## <a name="deployment-models"></a>Modely nasazení

Azure Database for PostgreSQL, kterou používá PostgreSQL Community Edition, je k dispozici ve třech režimech nasazení:

- Jeden server
- Flexibilní server (Preview)
- Hyperscale (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – Jeden server

Azure Database for PostgreSQL jeden server je plně spravovaná databázová služba s minimálními požadavky pro přizpůsobení databáze. Platforma s jednou serverem je navržená tak, aby zpracovávala většinu funkcí správy databáze, jako jsou třeba opravy, zálohování, vysoká dostupnost, zabezpečení s minimální konfigurací a řízením uživatele. Architektura je optimalizovaná pro integrovanou vysokou dostupnost s 99,99% dostupností v jedné zóně dostupnosti. Podporuje komunitní verze PostgreSQL 9,5, 9, 6, 10 a 11. Tato služba je všeobecně dostupná pro celou řadu [oblastí Azure](https://azure.microsoft.com/global-infrastructure/services/).

Nasazení typu Jeden server má tři cenové úrovně: Basic, Pro obecné účely a Optimalizováno pro paměť. Každá úroveň nabízí různé schopnosti prostředků pro podporu vašich úloh databáze. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Podrobnosti najdete v tématu [Cenové úrovně](./concepts-pricing-tiers.md).

Pro cloudové nativní aplikace navržené pro zpracování automatizovaných oprav bez nutnosti podrobného řízení plánu oprav a vlastních nastavení konfigurace PostgreSQL jsou samostatné servery vhodné.

Podrobný přehled režimu nasazení na jeden server najdete v tématu [Přehled jediného serveru](./overview-single-server.md).

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL – Flexibilní server (Preview)

Azure Database for PostgreSQL flexibilní Server je plně spravovaná databázová služba navržená tak, aby poskytovala přesnější kontrolu a flexibilitu nad funkcemi správy databáze a nastaveními konfigurace. Obecně platí, že služba poskytuje větší flexibilitu a přizpůsobení podle požadavků uživatelů. Flexibilní architektura serveru umožňuje uživatelům, aby se mohli rozhodnout o vysoké dostupnosti v rámci jedné zóny dostupnosti a napříč několika zónami dostupnosti. Flexibilní Server poskytuje lepší ovládací prvky pro optimalizaci nákladů a možnost zastavit/spustit server a provést rozstupnou výpočetní vrstvu, která je ideální pro úlohy, které nepotřebují nepřetržitě plný výpočetní výkon. Služba aktuálně podporuje komunitní verze PostgreSQL 11 a 12 s plány na nové přidávání novějších verzí. Služba je momentálně ve verzi Public Preview, která je dnes k dispozici v nejrůznějších oblastech Azure.

Flexibilní servery jsou vhodné pro

- Vývoj aplikací, který vyžaduje lepší kontrolu a přizpůsobení.
- Optimalizace nákladů řídí schopnost zastavit nebo spustit server.
- Redundantní vysoká dostupnost zóny
- Spravovaná okna údržby
  
Podrobný přehled režimu nasazení flexibilního serveru najdete v tématu [flexibilní přehled serveru](./flexible-server/overview.md).

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – Citus (škálování)

Možnost Hyperscale (Citus) horizontálně škáluje dotazy napříč více počítači pomocí shardování. Její dotazovací modul paralelizuje příchozí dotazy SQL na více serverech, aby byla u velkých datových sad zajištěná rychlejší odezva. Slouží aplikacím, které vyžadují větší rozsah a výkon, tedy obecně úlohám s přibližně 100 GB dat.

Možnost nasazení Hyperscale (Citus) podporuje:

- Horizontální škálování do více počítačů pomocí shardování
- Paralelizaci dotazů na více serverech za účelem rychlejší odezvy u velkých datových sad
- Vynikající podpora aplikací s více klienty, provozní analýzy v reálném čase a úlohy s vysokou transakční propustností
  
Aplikace vytvořené pro PostgreSQL můžou spouštět distribuované dotazy na úrovni Citus (s standardními [knihovnami připojení](./concepts-connection-libraries.md) a minimálními změnami).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o třech režimech nasazení pro Azure Database for PostgreSQL a vyberte si správné možnosti podle svých potřeb.

- [Jeden server](./overview-single-server.md)
- [Flexibilní Server](./flexible-server/overview.md)
- Hyperscale (Citus)