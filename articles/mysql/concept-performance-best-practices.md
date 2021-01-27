---
title: Osvědčené postupy pro výkon – Azure Database for MySQL
description: Tento článek popisuje osvědčené postupy pro monitorování a ladění výkonu Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 30176e2df850e6d2794ab9c1542bcb6a89d8f89f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880402"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Osvědčené postupy pro zajištění optimálního výkonu Azure Database for MySQL a jednoho serveru

Přečtěte si o osvědčených postupech pro získání nejlepšího výkonu při práci s Azure Database for MySQLm na jednom serveru. Jakmile na platformu přidáváme nové možnosti, budeme v této části dále upřesňovat osvědčené postupy popsané v této části.

## <a name="physical-proximity"></a>Fyzická blízkost

 Ujistěte se, že nasazujete aplikaci a databázi ve stejné oblasti. Rychlá kontrolu před spuštěním jakéhokoli srovnávacího testu výkonu je určení latence sítě mezi klientem a databází pomocí jednoduchého dotazu SELECT 1. 

## <a name="accelerated-networking"></a>Akcelerované síťové služby

Použijte akcelerované síťové služby pro aplikační server, pokud používáte virtuální počítač Azure, Azure Kubernetes nebo App Services. Akcelerované síťové služby umožňují virtuálnímu počítači pomocí rozhraní SR-IOV (single-root I/O Virtualization), což výrazně zlepšuje výkon sítě. Tato cesta s vysokým výkonem obchází hostitele z DataPath, snižuje latenci, kolísání a využití CPU a používá se u nejnáročnějších síťových úloh na podporovaných typech virtuálních počítačů.

## <a name="connection-efficiency"></a>Efektivita připojení

Vytvoření nového připojení je vždycky náročná a časově náročná úloha. Když aplikace požaduje připojení k databázi, upřednostní přidělení stávajících nečinných připojení k databázi místo vytvoření nové.  Tady je několik možností pro osvědčené postupy připojení:

- **ProxySQL**: použijte [ProxySQL](https://proxysql.com/) , které poskytuje integrované sdružování připojení a [Vyrovnávání zatížení](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) , jak je potřeba na vyžádání, podle požadavků a změn v kódu aplikace.

- **Heimdall data proxy serveru**: Alternativně můžete také využít proxy data Heimdall, což je specifické pro proxy řešení neutrální od dodavatele. Podporuje ukládání dotazů do mezipaměti a rozdělení čtení/zápisu s detekcí prodlevy replikace. Můžete se také podívat na to, jak [urychlit výkon MySQL pomocí proxy Heimdall](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349).  

- **Trvalé nebo Long-Lived připojení**: Pokud má vaše aplikace krátké transakce nebo dotazy obvykle s dobou spuštění < 5-10 MS, pak nahraďte krátká připojení pomocí trvalých připojení. Nahrazení krátkých připojení pomocí trvalých připojení vyžaduje pouze drobné změny kódu, ale má zásadní vliv na zlepšení výkonu v řadě typických aplikačních scénářů. Nezapomeňte nastavit časový limit nebo ukončení připojení po dokončení transakce.

- **Replika**: Pokud používáte repliku, použijte [ProxySQL](https://proxysql.com/) k vyrovnávání zatížení mezi primárním serverem a čitelným serverem sekundární repliky. Přečtěte si, [jak nastavit ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847).

## <a name="data-import-configurations"></a>Konfigurace importu dat

- Před zahájením operace importu dat můžete instanci dočasně škálovat na vyšší velikost SKU a pak ji po úspěšném dokončení importu škálovat dolů.
- Data můžete importovat s minimálními výpadky pomocí [Azure Database Migration Service (DMS)](https://datamigration.microsoft.com/) pro online nebo offline migrace. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Doporučení Azure Database for MySQL paměti

Osvědčeným postupem z hlediska výkonu Azure Database for MySQL je přidělit dostatek paměti RAM, abyste pracovní sadu měli skoro kompletně v paměti. 

- Ověřte, jestli je procento využití paměti používané při dosažení [limitů](./concepts-pricing-tiers.md) [za použití metriky pro server MySQL](./concepts-monitoring.md). 
- Nastavte výstrahy na těchto číslech, abyste měli jistotu, že když servery dosáhnou omezení, můžete s jejich opravou provádět akce s výzvou. V závislosti na definovaných omezeních ověřte, zda vertikální navýšení kapacity databáze – buď na vyšší výpočetní velikost, nebo na lepší cenovou úroveň, což vede k výraznému nárůstu výkonu. 
- Horizontální navýšení kapacity až po operaci škálování se už neprojeví. Informace o monitorování metrik instance databáze najdete v tématu [metriky databáze MySQL](./concepts-monitoring.md#metrics).

## <a name="next-steps"></a>Další kroky

- [Osvědčené postupy při operacích serveru pomocí Azure Database for MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Osvědčené postupy monitorování Azure Database for MySQL](concept-monitoring-best-practices.md)<br/>
- [Začínáme s Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>