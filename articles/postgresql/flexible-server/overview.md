---
title: Azure Database for PostgreSQL – flexibilní Server
description: Poskytuje přehled Azure Database for PostgreSQLho flexibilního serveru.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/22/2020
ms.openlocfilehash: 268eedf6f9d64d52539e20006322b6b1dd9964e8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91439969"
---
# <a name="azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL – flexibilní Server

[Azure Database for PostgreSQL](../overview.md) , kterou používá PostgreSQL Community Edition, je k dispozici ve třech režimech nasazení:

- [Jeden server](../overview-single-server.md)
- Flexibilní server (Preview)
- Hyperscale (Citus)

V tomto článku budeme poskytovat přehled a Úvod do základních konceptů flexibilního modelu nasazení serveru.

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

## <a name="overview"></a>Přehled

Azure Database for PostgreSQL – flexibilní Server je plně spravovaná databázová služba navržená tak, aby poskytovala přesnější kontrolu a flexibilitu nad funkcemi správy databáze a nastaveními konfigurace. Obecně platí, že služba poskytuje větší flexibilitu a přizpůsobení konfigurace serveru na základě požadavků uživatelů. Flexibilní architektura serveru umožňuje uživatelům společné umístění databázový stroj pomocí klientské vrstvy pro nižší latenci. Vyberte možnost Vysoká dostupnost v rámci jedné zóny dostupnosti a napříč několika zónami dostupnosti. Flexibilní servery také poskytují lepší ovládací prvky pro optimalizaci nákladů s možností zastavit nebo spustit server a zátěžovou výpočetní vrstvu, která je ideální pro úlohy, které nepotřebují nepřetržitě plný výpočetní výkon. Služba aktuálně podporuje komunitní verze PostgreSQL 11 a 12. Služba je momentálně ve verzi Preview, která je dnes k dispozici v nejrůznějších  [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/).

![Flexibilní Server – přehled](./media/overview/overview-flexible-server.png)


Flexibilní servery jsou vhodné pro

- Vývoj aplikací, který vyžaduje lepší kontrolu a přizpůsobení.
- Redundantní vysoká dostupnost zóny
- Spravovaná okna údržby
  
## <a name="high-availability"></a>Vysoká dostupnost

Model nasazení flexibilního serveru je navržený tak, aby podporoval vysokou dostupnost v rámci jedné zóny dostupnosti a napříč několika zónami dostupnosti. Architektura odděluje výpočetní výkon a úložiště. Databázový stroj běží na virtuálním počítači se systémem Linux, zatímco datové soubory jsou uloženy v úložišti Azure. Úložiště udržuje tři místně redundantní synchronní kopie databázových souborů, které zajišťují odolnost proti datům.

Při výpadku plánovaných nebo neplánovaných událostí pro převzetí služeb při selhání udržuje služba vysokou dostupnost serverů pomocí následujícího automatizované procedury:

1. Zřizuje se nový virtuální počítač pro výpočet Linux.
2. Úložiště s datovými soubory je namapované na nový virtuální počítač.
3. Databázový stroj PostgreSQL se na novém virtuálním počítači přepne do online režimu.

Následující obrázek ukazuje přechod pro virtuální počítač a selhání úložiště.

 :::image type="content" source="./media/overview/overview-azure-postgres-flex-virtualmachine.png" alt-text="Flexibilní selhání serveru – virtuální počítače a úložiště":::

Pokud je nakonfigurovaná vysoká dostupnost zóny, služba zřídí a udržuje aktivní pohotovostní server napříč zónou dostupnosti v rámci stejné oblasti Azure. Změny dat na zdrojovém serveru se synchronně replikují na pohotovostní server, aby se zajistila nulová ztráta dat. Pokud se po aktivaci plánované nebo neplánované události převzetí služeb při selhání spustí záložní server s vysokou dostupností, bude pohotovostní server okamžitě online a bude k dispozici pro zpracování příchozích transakcí. To umožňuje odolnost služby před selháním zóny dostupnosti v oblasti Azure, která podporuje více zón dostupnosti, jak je znázorněno na obrázku níže.

 :::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Flexibilní selhání serveru – virtuální počítače a úložiště":::

 Další podrobnosti najdete v [dokumentu vysoké dostupnosti](./concepts-high-availability.md) .

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatizované opravy pomocí spravovaného časového období údržby

Služba provádí automatizované opravy základního hardwaru, operačního systému a databázového stroje. Opravy zahrnují zabezpečení a aktualizace softwaru. V případě stroje PostgreSQL jsou jako součást plánované verze údržby zahrnuty také inovace podverze. Uživatelé můžou nakonfigurovat plán oprav tak, aby byl spravovaný systémem, nebo definovat vlastní plán. Během plánu údržby se oprava použije a server může být potřeba restartovat v rámci procesu opravy, aby se aktualizace dokončila. Díky vlastnímu plánu můžou uživatelé provádět předvídatelné cykly oprav a zvolit časové období údržby s minimálním dopadem na firmu. Obecně platí, že služba dodržuje plán měsíčního vydání jako součást průběžné integrace a vydání.

## <a name="automatic-backups"></a>Automatické zálohování

Flexibilní serverová služba automaticky vytvoří zálohy serveru a uloží je místně nakonfigurovaným uživatelem na zóně redundantní (ZRS). Zálohy můžete použít k obnovení serveru k jakémukoli časovému okamžiku v rámci doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Doba uchovávání může být volitelně nakonfigurovaná na 35 dní. Všechny zálohy se šifrují s využitím 256bitového šifrování AES. Další podrobnosti najdete v tématu [zálohování](./concepts-backup-restore.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Úprava výkonu a škálování během několika sekund

Flexibilní serverová služba je k dispozici ve třech výpočetních úrovních: Vícevrstvá, Pro obecné účelyová a paměťově optimalizovaná. Vícevrstvá vrstva je nejvhodnější pro vývoj s nízkými náklady a pro úlohy s nízkou úrovní souběžnosti, které nepotřebují nepřetržitě plný výpočet kapacity. Pro obecné účely a paměť optimalizované pro produkční úlohy, které vyžadují vysokou souběžnost, škálování a předvídatelný výkon, jsou lépe vhodné. Svou první aplikaci můžete vytvořit na malé databázi po dobu několika dolarů měsíčně a pak hladce upravit škálování tak, aby splňovalo požadavky vašeho řešení.

## <a name="stopstart-server-to-lower-tco"></a>Zastavit/spustit server a snížit celkové náklady na vlastnictví

Flexibilní serverová služba umožňuje zastavit a spustit server na vyžádání a snížit celkové náklady na vlastnictví. Fakturace na výpočetní úrovni se okamžitě zastaví, když se Server zastaví. To vám může způsobit výrazné úspory nákladů během vývoje, testování a předvídatelných produkčních úloh, které jsou časově závislé. Server zůstane v zastaveném stavu po dobu sedmi dnů, pokud se nerestartuje dřív.

## <a name="enterprise-grade-security"></a>Zabezpečení na podnikové úrovni

Flexibilní serverová služba používá šifrovací modul ověřený standardem FIPS 140-2 pro šifrování úložiště neaktivních dat. Data, včetně záloh a dočasných souborů vytvořených při spouštění dotazů, jsou zašifrovaná. Služba používá algoritmus AES 256-bit, který je součástí šifrování úložiště Azure, a klíče můžou být spravované systémem (výchozí). Služba šifruje přenášená data pomocí protokolu TLS (Transport Layer Security), který je ve výchozím nastavení vynutil. Služba vynutila a podporuje pouze TLS verze 1,2.

Flexibilní servery umožňují úplný privátní přístup k serverům pomocí Azure Virtual Network (integrace virtuální sítě). Servery ve službě Azure Virtual Network je možné oslovit a propojit jenom pomocí privátních IP adres. S integrací virtuální sítě se zamítl veřejný přístup a servery se nedají kontaktovat pomocí veřejných koncových bodů.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Flexibilní serverová služba je vybavená integrovanými funkcemi pro monitorování výkonu a upozorňování. Všechny metriky Azure mají četnost jednosměrných minut a každá metrika poskytuje historii 30 dnů. Výstrahy můžete nakonfigurovat pro metriky. Služba zpřístupňuje metriky hostitelského serveru pro monitorování využití prostředků a umožňuje konfigurovat protokoly pomalých dotazů. Pomocí těchto nástrojů můžete rychle optimalizovat vaše úlohy a nakonfigurovat server tak, aby co nejlépe vyzpůsobil výkon.

## <a name="migration"></a>Migrace

Služba spouští komunitní verze PostgreSQL. To umožňuje úplnou kompatibilitu aplikací a vyžaduje minimální náklady na Refaktoring pro migraci existující aplikace vyvinuté na PostgreSQL stroji na flexibilní Server. 

- **Vypsat a obnovit** – pro offline migrace, kde můžou uživatelé získat nějaké výpadky, vypsat a obnovit pomocí nástrojů komunity, jako je pg_dump, a pg_restore můžou poskytnout nejrychlejší způsob migrace. Podrobnosti najdete v tématu [migrace pomocí výpisu paměti a obnovení](https://docs.microsoft.com/azure/postgresql/howto-migrate-using-dump-and-restore) .
- **Azure Database Migration Service** – pro bezproblémové a zjednodušené migrace do flexibilního serveru s minimálními prostoji je možné využít Azure Database Migration Service. Viz [DMS prostřednictvím portálu](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online-portal) a [DMS prostřednictvím](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)rozhraní příkazového řádku. Z Azure Database for PostgreSQL můžete migrovat z jednoho serveru do flexibilního serveru. Podrobnosti najdete v tomto [článku o DMS](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) .

## <a name="next-steps"></a>Další kroky

Teď, když jste si přečetli Úvod do Azure Database for PostgreSQL režimu nasazení flexibilního serveru, jste připraveni vytvořit svůj první server: [vytvoření Azure Database for PostgreSQL flexibilního serveru pomocí Azure Portal](./quickstart-create-server-portal.md)


