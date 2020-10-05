---
title: Volba pravého serveru PostgreSQL v Azure
description: V této části najdete pokyny pro výběr správné možnosti PostgreSQL serveru pro vaše nasazení.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: f81a44af4a90ccda1875c0a9f2bfaa8dc0b73441
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90946978"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>Volba pravého serveru PostgreSQL v Azure

S Azure můžou vaše zatížení serveru PostgreSQL běžet v hostované infrastruktuře virtuálních počítačů jako služba (IaaS) nebo jako hostovaná platforma jako služba (PaaS). PaaS má několik možností nasazení, z nichž každá má několik úrovní služeb. Když si vyberete mezi IaaS a PaaS, musíte se rozhodnout, jestli chcete databázi spravovat, použít opravy a udělat zálohy, nebo jestli chcete tyto operace delegovat na Azure.

Při rozhodování zvažte následující tři možnosti v PaaS nebo na virtuálních počítačích Azure (IaaS).
- [Jeden server Azure Database for PostgreSQL](./overview-single-server.md)
- [Azure Database for PostgreSQL – flexibilní Server](./flexible-server/overview.md)
- [Azure Database for PostgreSQL – škálovatelné škálování (Citus)]()

Možnost **PostgreSQL na virtuálních počítačích Azure** spadá do kategorie odvětví IaaS. Pomocí této služby můžete spustit PostgreSQL Server v rámci plně spravovaného virtuálního počítače na cloudové platformě Azure. Na virtuálním počítači s IaaS můžete nainstalovat všechny poslední verze a edice PostgreSQL. V nejvýznamnějších rozdílech od Azure Database for PostgreSQL PostgreSQL na virtuálních počítačích Azure nabízí kontrolu nad databázovým strojem. Tento ovládací prvek ale přichází na zodpovědnost za správu virtuálních počítačů a mnoha úloh správy databází (DBA). Mezi tyto úlohy patří údržba a opravy databázových serverů, obnovení databáze a návrh vysoké dostupnosti.

Hlavní rozdíly mezi těmito možnostmi jsou uvedené v následující tabulce:

| **Atribut** | **Postgres na virtuálních počítačích Azure** | **PostgreSQL jako PaaS** |
| ----- | ----- | ----- |
| <B> Smlouva SLA k dostupnosti |-99,99% se skupinami dostupnosti <br> -99,95% s jedním virtuálním počítačům | – Jeden server – 99,99% <br> -Flexibilní Server – není k dispozici během verze Preview <br> -Citus) – 99,95% (Pokud je povolená vysoká dostupnost)|
| <B> Opravy operačního systému a PostgreSQL | – Spravované zákazníkem | – Samostatný server – automatický <br> -Flexibilní Server – automatický s volitelným oknem spravovaným zákazníkem <br> -Citus (škálovatelné) – automatické |
| <B> Vysoká dostupnost | – Uživatelé architekt, implementace, testování a údržba vysoké dostupnosti. Mezi funkce můžou patřit clustering, replikace atd. | – Jeden server: integrovaný <br> -Flexibilní Server: integrovaný <br> -Citus (škálovatelné): vytvořeno s pohotovostním režimem |
| <B> Redundance zóny | – Virtuální počítače Azure je možné nastavit tak, aby běžely v různých zónách dostupnosti. Pro místní řešení musí zákazníci vytvořit, spravovat a spravovat svoje vlastní sekundární datové centrum. | – Jeden server: ne <br> -Flexibilní Server: Ano <br> -Citus (škálování): ne |
| <B> Hybridní scénář | – Spravované zákazníkem |– Jeden server: čtení-replika <br> -Flexibilní Server: není k dispozici ve verzi Preview. <br> -Citus (škálování): ne |
| <B> Zálohování a obnovení | – Spravované zákazníkem | – Jeden server: integrovaná s konfigurací uživatele pro místní a geografickou <br> -Flexibilní Server: integrovaná s konfigurací uživatele v zóně – redundantní úložiště <br> -Citus (vestavěný): integrovaný |
| <B> Monitorování operací databáze | – Spravované zákazníkem | – Jeden server, flexibilní Server a Citus (): vše zákazníkům nabízí možnost nastavovat výstrahy pro databázovou operaci a působit při dosažení prahových hodnot. |
| <B> Rozšířená ochrana před internetovými útoky | -Zákazníci musí tuto ochranu vytvořit sami. |– Jeden server: Ano <br> -Flexibilní Server: není k dispozici ve verzi Preview. <br> -Citus (škálování): ne |
| <B> Zotavení po havárii | – Spravované zákazníkem | – Jeden server: geograficky redundantní zálohování a geografická čtení-replika <br> -Flexibilní Server: není k dispozici ve verzi Preview. <br> -Citus (škálování): ne |
| <B> Inteligentní výkon | – Spravované zákazníkem | – Jeden server: Ano <br> -Flexibilní Server: není k dispozici ve verzi Preview. <br> -Citus (škálování): ne |

## <a name="total-cost-of-ownership-tco"></a>Celkové náklady na vlastnictví (celkových nákladů na vlastnictví)

Celkové náklady na vlastnictví jsou často primárním aspektem, který určuje nejlepší řešení pro hostování databází. To platí bez ohledu na to, jestli se jedná o spuštění s malým hotovostem nebo týmem v zřízené společnosti, která funguje s přísnými rozpočtovými omezeními. Tato část popisuje základy fakturace a licencování v Azure, které se vztahují na Azure Database for PostgreSQL a PostgreSQL na virtuálních počítačích Azure.

## <a name="billing"></a>Fakturace

Azure Database for PostgreSQL je v tuto chvíli k dispozici jako služba na několika úrovních s různými cenami za prostředky. Všechny prostředky se účtují po hodinách za pevnou sazbu. Nejnovější informace o aktuálně podporovaných úrovních služby, velikostech výpočtů a částkách úložiště najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/server/) . můžete dynamicky upravovat úrovně služeb a výpočetní velikosti tak, aby odpovídaly různým požadavkům na propustnost vaší aplikace. Účtuje se vám odchozí přenos přes Internet za běžné [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

Pomocí Azure Database for PostgreSQL Microsoft automaticky konfiguruje, opraví a upgraduje databázový software. Tyto automatizované akce omezují náklady na správu. Azure Database for PostgreSQL taky nabízí [automatizované funkce pro propojení záloh]() . Tyto funkce vám pomůžou dosáhnout výrazné úspory nákladů, zejména pokud máte velký počet databází. Na rozdíl od PostgreSQL na virtuálních počítačích Azure můžete zvolit a spustit libovolnou PostgreSQL verzi. Musíte ale platit za zřízený virtuální počítač, náklady na úložiště spojené s daty, zálohování, monitorování dat a úložiště protokolů a náklady na konkrétní použitý typ licence PostgreSQL (pokud existuje).

Azure Database for PostgreSQL poskytuje integrovanou vysokou dostupnost pro jakýkoliv typ přerušení na úrovni uzlu a zároveň zachovává záruku smlouvy SLA pro službu 99,99%. Pro vysokou dostupnost databáze v rámci virtuálních počítačů ale používáte možnosti vysoké dostupnosti, jako je [replikace streamování](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION) , které jsou k dispozici v databázi PostgreSQL. Použití podporované možnosti vysoké dostupnosti neposkytuje další smlouvu SLA. Ale umožňuje dosáhnout větší než 99,99% dostupnost databáze při dalších nákladech a administrativní režii.

Další informace o cenách najdete v následujících článcích:
- [Ceny Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [Ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Správa

V mnoha firmách je rozhodování o přechodu na cloudovou službu velmi náročné na složitost správy, protože se jedná o náklady.

S IaaS Microsoft:

- Spravuje základní infrastrukturu.
- Poskytuje automatizované opravy pro základní hardware a operační systém.

S PaaS Microsoft:

- Spravuje základní infrastrukturu.
- Poskytuje automatizované opravy pro základní hardware, operační systém a databázový stroj.
- Spravuje vysokou dostupnost databáze.
- Automaticky provádí zálohování a replikuje všechna data pro zajištění zotavení po havárii.
- Zašifruje neaktivní data v klidovém a pohybovém stavu.
- Monitoruje váš server a poskytuje funkce pro dotazy na informace o výkonu a doporučení týkající se výkonu.

Pomocí Azure Database for PostgreSQL můžete dál spravovat databázi. Ale už nemusíte spravovat databázový stroj, operační systém ani hardware. Mezi položky, které můžete dál spravovat, patří:

- Databáze
- Přihlášení
- Ladění indexu
- Ladění dotazů
- Auditování
- Zabezpečení

Kromě toho konfigurace vysoké dostupnosti v jiném datovém centru vyžaduje minimální konfiguraci nebo správu.

- S PostgreSQL na virtuálních počítačích Azure máte plnou kontrolu nad operačním systémem a konfigurací instance serveru PostgreSQL. Pomocí virtuálního počítače se rozhodnete, kdy aktualizovat nebo upgradovat operační systém a software databáze a jaké opravy se mají použít. Také se rozhodnete, kdy nainstalovat další software, jako je třeba antivirová aplikace. K dispozici jsou některé automatizované funkce, které výrazně zjednodušují opravy, zálohování a vysokou dostupnost. Můžete řídit velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Další informace najdete v tématu [velikosti virtuálních počítačů a cloudových služeb pro Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Čas přechodu na službu Azure PostgreSQL (PaaS)

- Azure Database for PostgreSQL je to pravé řešení pro cloudové aplikace, když je produktivita vývojářů a rychlá doba uvedení na trh pro nová řešení. Díky programovým funkcím, které jsou jako DBA, je služba vhodná pro cloudové architekty a vývojáře, protože snižuje nutnost správy základního operačního systému a databáze.

- Pokud se chcete vyhnout času a nákladům na získání nového místního hardwaru, PostgreSQL na virtuálních počítačích Azure je to pravé řešení pro aplikace, které vyžadují detailní řízení a přizpůsobení PostgreSQL modulu, který není podporovaný službou, nebo vyžadování přístupu k základnímu operačnímu systému.

## <a name="next-steps"></a>Další kroky

- Informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/postgresql/server/)služby Azure Database for PostgreSQL.
- Začněte vytvářet první server.

