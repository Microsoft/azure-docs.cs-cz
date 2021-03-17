---
title: Výběr správného typu nasazení – Azure Database for MySQL
description: Tento článek popisuje, co je třeba zvážit před nasazením Azure Database for MySQL jako IaaS (infrastruktura jako služba) nebo platforma jako služba (PaaS).
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 125431e6630ccfdd9e0e5d6b2a4ec5fa9b9e58fd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736181"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Volba pravého serveru MySQL v Azure

S Azure můžou vaše úlohy serveru MySQL běžet v hostované infrastruktuře virtuálních počítačů jako služba (IaaS) nebo jako hostovaná platforma jako služba (PaaS). PaaS má několik možností nasazení a v rámci každé možnosti nasazení jsou dostupné úrovně služeb. Když si vyberete mezi IaaS a PaaS, musíte se rozhodnout, jestli chcete databázi spravovat, použít opravy a udělat zálohy, nebo jestli chcete tyto operace delegovat na Azure.

Při rozhodování Vezměte v úvahu tyto dvě možnosti:

- **Azure Database for MySQL**. Tato možnost je plně spravovaný databázový stroj MySQL založený na stabilní verzi MySQL Community Edition. Tato relační databáze jako služba (DBaaS), která je hostovaná na cloudové platformě Azure, spadá do kategorie odvětví PaaS.

  Se spravovanou instancí MySQL v Azure můžete používat integrované funkce viz automatizované opravy, vysoké dostupnosti, automatizované zálohování, elastické škálování, zabezpečení na podnikové úrovni, dodržování předpisů a zásad správného řízení, monitorování a upozorňování, které jinak vyžadují rozsáhlou konfiguraci, když je server MySQL buď v místním prostředí, nebo na virtuálním počítači Azure. Při použití MySQL jako služby můžete průběžné platby s možností horizontálního navýšení nebo horizontálního navýšení kapacity pro větší kontrolu bez přerušení.
  
  [Azure Database for MySQL](overview.md), kterou používá edice MySQL Community Edition, je k dispozici ve dvou režimech nasazení:

  - [Jeden server](single-server-overview.md) je plně spravovaná databázová služba s minimálními požadavky pro přizpůsobení databáze. Platforma s jednou serverem je navržená tak, aby zpracovávala většinu funkcí správy databáze, jako jsou třeba opravy, zálohování, vysoká dostupnost, zabezpečení s minimální konfigurací a řízením uživatele. Architektura je optimalizovaná tak, aby poskytovala 99,99% dostupnost v jedné zóně dostupnosti. Pro cloudové nativní aplikace navržené pro zpracování automatizovaných oprav bez nutnosti podrobného řízení plánu oprav a vlastních nastavení konfigurace MySQL se nejlépe hodí samostatné servery.

  - [Flexibilní Server (Preview)](flexible-server/overview.md) je plně spravovaná databázová služba navržená tak, aby poskytovala přesnější kontrolu a flexibilitu nad funkcemi správy databáze a nastaveními konfigurace. Obecně platí, že služba poskytuje větší flexibilitu a přizpůsobení konfigurace serveru v porovnání s nasazením jednoho serveru na základě požadavků uživatelů. Flexibilní architektura serveru umožňuje uživatelům, aby se mohli rozhodnout o vysoké dostupnosti v rámci jedné zóny dostupnosti a napříč několika zónami dostupnosti. Flexibilní servery také poskytují lepší ovládací prvky pro optimalizaci nákladů a možnost spouštět a zastavovat Server a rozzpůsobitelné skladové položky, které jsou ideální pro úlohy, které nepotřebují nepřetržitě plný výpočetní výkon.

  Flexibilní servery jsou vhodné pro:

  - Vývoj aplikací vyžaduje lepší kontrolu a přizpůsobení modulu MySQL.
  - Redundantní vysoká dostupnost zóny
  - Spravovaná okna údržby

- **MySQL na virtuálních počítačích Azure**. Tato možnost spadá do kategorie odvětví IaaS. Pomocí této služby můžete server MySQL spustit ve spravovaném virtuálním počítači na cloudové platformě Azure. Na virtuálním počítači můžete nainstalovat všechny poslední verze a edice MySQL.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Porovnání možností nasazení MySQL v Azure

Hlavní rozdíly mezi těmito možnostmi jsou uvedené v následující tabulce:

| Atribut          | Azure Database for MySQL<br/>Jeden server |Azure Database for MySQL<br/>Flexibilní Server  |MySQL na virtuálních počítačích Azure                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| Podpora verze MySQL | 5,6, 5,7 & 8,0| 5,7 & 8,0 | Libovolná verze|
| Škálování na výpočetní výkon | Podporováno (škálování od a do úrovně Basic není podporováno)| Podporováno | Podporováno|
| Velikost úložiště | 5 GiB až 16 TiB| 5 GiB až 16 TiB | 32 GiB 32 767 GiB|
| Škálování online úložiště | Podporováno| Podporováno| Nepodporováno|
| Automatické škálování úložiště | Podporováno| Nepodporováno ve verzi Preview| Nepodporováno|
| Škálování dalších IOPs | Nepodporuje se| Podporováno| Nepodporováno|
| Připojení k síti | – Veřejné koncové body s bránou firewall serveru.<br/> – Privátní přístup s podporou privátních odkazů.|– Veřejné koncové body s bránou firewall serveru.<br/> – Privátní přístup s Virtual Network Integration.| – Veřejné koncové body s bránou firewall serveru.<br/> – Privátní přístup s podporou privátních odkazů.|
| Smlouva o úrovni služeb (SLA) | 99,99% dostupnost smlouvy SLA |Žádná smlouva SLA ve verzi Preview| 99,99% pomocí Zóny dostupnosti|
| Opravy operačního systému| Automaticky  | Automaticky s vlastním ovládacím prvkem okna údržby | Spravováno koncovými uživateli |
| Aktualizace MySQL     | Automaticky  | Automaticky s vlastním ovládacím prvkem okna údržby | Spravováno koncovými uživateli |
| Vysoká dostupnost | Integrované HA v rámci jedné zóny dostupnosti| Integrované HA v rámci zón dostupnosti a napříč nimi | Vlastní spravované pomocí clusteringu, replikace atd.|
| Zónová redundance | Nepodporováno | Podporováno | Podporováno|
| Umístění zóny | Nepodporováno | Podporováno | Podporováno|
| Hybridní scénáře | Podporováno s [replikace vstupních dat](./concepts-data-in-replication.md)| Není k dispozici ve verzi Preview. | Spravováno koncovými uživateli |
| Čtení replik | Podporováno (až 5 replik)| Podporováno (až 10 replik)| Spravováno koncovými uživateli |
| Backup | Automatizovaná doba uchování s 7-35 dny | Automatizovaná doba uchování s 1-35 dny | Spravováno koncovými uživateli |
| Monitorování operací databáze | Podporováno | Podporováno | Spravováno koncovými uživateli |
| Zotavení po havárii | Podporováno s geograficky redundantním úložištěm zálohování a replikami čtení mezi oblastmi | Nepodporováno ve verzi Preview| Vlastní Správa s využitím technologií replikace |
| Query Performance Insights | Podporováno | Není k dispozici ve verzi Preview.| Spravováno koncovými uživateli |
| Ceny rezervovaných instancí | Podporováno | Není k dispozici ve verzi Preview. | Podporováno |
| Ověřování Azure AD | Podporováno | Není k dispozici ve verzi Preview. | Nepodporuje se|
| Šifrování dat v klidovém umístění | Podporováno u klíčů spravovaných zákazníkem | Podporováno se spravovanými klíči služby | Nepodporuje se|
| SSL/TLS | Ve výchozím nastavení povolená s podporou TLS v 1.2, 1,1 a 1,0 | Vynutilo TLS v 1.2 | Podporováno s TLS v 1.2, 1,1 a 1,0 |
| Správa loďstva | Podporováno v Azure CLI, PowerShellu, REST a Azure Resource Manager | Podporováno v Azure CLI, PowerShellu, REST a Azure Resource Manager  | Podporováno pro virtuální počítače s využitím Azure CLI, PowerShellu, REST a Azure Resource Manager |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Obchodní motivace pro výběr PaaS nebo IaaS

Existuje několik faktorů, které mohou mít vliv na rozhodnutí zvolit PaaS nebo IaaS k hostování databází MySQL.

### <a name="cost"></a>Náklady

Snížení nákladů je často zásadním aspektem, který určuje nejlepší řešení pro hostování databází. To platí bez ohledu na to, jestli se jedná o spuštění s malým hotovostem nebo týmem v zřízené společnosti, která funguje s přísnými rozpočtovými omezeními. Tato část popisuje základy fakturace a licencování v Azure, které se vztahují na Azure Database for MySQL a MySQL na virtuálních počítačích Azure.

#### <a name="billing"></a>Fakturace

Azure Database for MySQL je v tuto chvíli k dispozici jako služba na několika úrovních s různými cenami za prostředky. Všechny prostředky se účtují po hodinách za pevnou sazbu. Nejnovější informace o aktuálně podporovaných úrovních služby, velikostech výpočtů a částkách úložiště najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/mysql/). Úrovně služeb a výpočetní velikosti můžete dynamicky upravovat tak, aby odpovídaly různým požadavkům na propustnost vaší aplikace. Účtuje se vám odchozí přenos přes Internet za běžné [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

Pomocí Azure Database for MySQL Microsoft automaticky konfiguruje, opraví a upgraduje databázový software. Tyto automatizované akce omezují náklady na správu. Azure Database for MySQL taky obsahuje možnosti [automatického zálohování](./concepts-backup.md) . Tyto funkce vám pomůžou dosáhnout výrazné úspory nákladů, zejména pokud máte velký počet databází. Na rozdíl od databáze MySQL na virtuálních počítačích Azure můžete zvolit a spustit libovolnou verzi MySQL. Bez ohledu na to, jakou verzi MySQL používáte, platíte za zřízený virtuální počítač a náklady na úložiště spojené s daty, zálohováním, monitorováním dat a úložištěm protokolů a náklady na konkrétní použitý typ licence MySQL (pokud existuje).

Azure Database for MySQL poskytuje integrovanou vysokou dostupnost pro jakýkoliv typ přerušení na úrovni uzlu a zároveň zachovává záruku smlouvy SLA pro službu 99,99%. Pro vysokou dostupnost databáze v rámci virtuálních počítačů ale používáte možnosti vysoké dostupnosti, jako je například [replikace MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html) , která je k dispozici v databázi MySQL. Použití podporované možnosti vysoké dostupnosti neposkytuje další smlouvu SLA. Ale umožňuje dosáhnout větší než 99,99% dostupnost databáze při dalších nákladech a administrativní režii.

Další informace o cenách najdete v následujících článcích:

- [Ceny Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
- [Ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Správa

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
- Monitoruje váš server a poskytuje funkce pro dotazování na výkon – přehledy a doporučení týkající se výkonu.

Následující seznam popisuje požadavky na správu pro jednotlivé možnosti:

- Pomocí Azure Database for MySQL můžete dál spravovat databázi. Ale už nemusíte spravovat databázový stroj, operační systém ani hardware. Mezi položky, které můžete dál spravovat, patří:

  - Databáze
  - Přihlášení
  - Ladění indexu
  - Ladění dotazů
  - Auditování
  - Zabezpečení

  Kromě toho konfigurace vysoké dostupnosti v jiném datovém centru vyžaduje minimální konfiguraci nebo správu.

- S MySQL na virtuálních počítačích Azure máte plnou kontrolu nad operačním systémem a konfigurací instance serveru MySQL. Pomocí virtuálního počítače se rozhodnete, kdy aktualizovat nebo upgradovat operační systém a software databáze a jaké opravy se mají použít. Také se rozhodnete, kdy nainstalovat další software, jako je třeba antivirová aplikace. K dispozici jsou některé automatizované funkce, které výrazně zjednodušují opravy, zálohování a vysokou dostupnost. Můžete řídit velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Další informace najdete v tématu [velikosti virtuálních počítačů a cloudových služeb pro Azure](../virtual-machines/sizes.md).

### <a name="time-to-move-to-azure"></a>Čas přechodu do Azure

- Azure Database for MySQL je to pravé řešení pro cloudové aplikace, když je produktivita vývojářů a rychlá doba uvedení na trh pro nová řešení. Díky programovým funkcím, které jsou jako DBA, je služba vhodná pro cloudové architekty a vývojáře, protože snižuje nutnost správy základního operačního systému a databáze.

- Pokud se chcete vyhnout času a nákladům na získání nového místního hardwaru, MySQL na virtuálních počítačích Azure je správné řešení pro aplikace, které vyžadují detailní řízení a přizpůsobení modulu MySQL, který není podporovaný službou, nebo vyžadování přístupu k základnímu operačnímu systému. Toto řešení je také vhodné k migraci stávajících místních aplikací a databází do Azure beze změny, pokud je Azure Database for MySQL nekvalitním potřebám.

Vzhledem k tomu, že není potřeba měnit vrstvy prezentace, aplikace a dat, ušetříte čas a rozpočet při reorganizaci stávajícího řešení. Místo toho se můžete soustředit na migraci všech vašich řešení do Azure a řešit některé optimalizace výkonu, které může platforma Azure vyžadovat.

## <a name="next-steps"></a>Další kroky

- Viz [ceny Azure Database for MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
- Začněte [vytvořením prvního serveru](./quickstart-create-mysql-server-database-using-azure-portal.md).
