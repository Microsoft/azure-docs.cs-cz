---
title: Mezipaměť Azure pro monitorování Redis a časté otázky týkající se řešení potíží
description: Přečtěte si odpovědi na nejčastější dotazy, které vám pomůžou monitorovat a řešit potíže s Azure cache pro Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 2149d069ce9cb0b636e461d19963f413b162bc9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010858"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Mezipaměť Azure pro monitorování Redis a časté otázky týkající se řešení potíží
Tento článek obsahuje odpovědi na běžné dotazy týkající se sledování a řešení potíží s Azure cache pro Redis.

## <a name="common-questions-and-answers"></a>Časté otázky a odpovědi
Tato část obsahuje následující Nejčastější dotazy:

* [Návody monitorovat stav a výkon složky Moje mezipaměť?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Proč se zobrazuje časový limit?](#why-am-i-seeing-timeouts)
* [Proč byl můj klient odpojený od mezipaměti?](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Návody monitorovat stav a výkon složky Moje mezipaměť?
Mezipaměť Microsoft Azure pro instance Redis lze monitorovat v [Azure Portal](https://portal.azure.com). Můžete zobrazit metriky, připnout grafy metriky na úvodní panel, přizpůsobit datum a časový rozsah grafů monitorování, přidat a odebrat metriky z grafů a nastavit upozornění, když budou splněny určité podmínky. Další informace najdete v tématu [monitorování mezipaměti Azure pro Redis](cache-how-to-monitor.md).

**Nabídka prostředků** Azure cache for Redis obsahuje taky několik nástrojů pro monitorování a odstraňování potíží s mezipamětí.

* **Diagnostika a řešení problémů** poskytuje informace o běžných problémech a strategiích jejich řešení.
* **Stav prostředku** sleduje váš prostředek a oznamuje, zda je spuštěný podle očekávání. Další informace o službě Azure Resource Health najdete v článku [Přehled Azure Resource Health](../resource-health/resource-health-overview.md).
* **Nová žádost o podporu** poskytuje možnosti pro otevření žádosti o podporu pro vaši mezipaměť.

Tyto nástroje vám umožní monitorovat stav mezipaměti Azure pro instance Redis a pomáhat při správě aplikací pro ukládání do mezipaměti. Další informace najdete v části "Podpora nastavení řešení potíží s & v tématu [Konfigurace mezipaměti Azure pro Redis](cache-configure.md).

### <a name="why-am-i-seeing-timeouts"></a>Proč se zobrazuje časový limit?
V klientovi dojde k vypršení časového limitu, který používáte ke komunikaci s Redis. Když se do serveru Redis pošle příkaz, příkaz se zařadí do fronty a server Redis ho nakonec vybere a spustí. Klient však může během tohoto procesu vyprší časový limit a v případě, že dojde k výjimce, je vyvolána na volající straně. Další informace o řešení potíží s časovým limitem najdete v tématu [řešení potíží na straně klienta](cache-troubleshoot-client.md) a [výjimky stackexchange. Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Proč byl můj klient odpojený od mezipaměti?
Níže jsou uvedeny některé běžné důvody pro odpojení mezipaměti.

* Příčiny na straně klienta
  * Klientská aplikace se znovu nasadila.
  * Klientská aplikace provedla operaci škálování.
    * V případě Cloud Services nebo Web Apps to může být způsobeno automatickým škálováním.
  * Síťová vrstva na straně klienta se změnila.
  * V klientovi nebo v síťových uzlech mezi klientem a serverem došlo k přechodným chybám.
  * Bylo dosaženo limitu prahové hodnoty šířky pásma.
  * Dokončení operací vázaných na procesor trvalo příliš dlouho.
* Příčiny na straně serveru
  * V nabídce standardní mezipaměti služba Azure cache pro Redis zahájila převzetí služeb při selhání z primárního uzlu do uzlu repliky.
  * Azure použil opravu instance, ve které byla mezipaměť nasazená.
    * Může to být pro aktualizace serveru Redis nebo obecnou údržbu virtuálních počítačů.


## <a name="next-steps"></a>Další kroky

Další informace o monitorování a řešení potíží s mezipamětí Azure cache pro instance Redis najdete v tématu [monitorování mezipaměti Azure pro Redis](cache-how-to-monitor.md) a v různých průvodcích odstraňováním potíží.

Přečtěte si o dalších [nejčastějších dotazech k mezipaměti Azure pro Redis](cache-faq.md).
