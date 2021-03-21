---
title: Co je StorSimple Snapshot Manager? | Dokumentace Microsoftu
description: Popisuje Snapshot Manager StorSimple, její architekturu a její funkce.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2214f085c30419cefb3f6f84139d5592873173f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017980"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Úvod do StorSimple Snapshot Manager

## <a name="overview"></a>Přehled
StorSimple Snapshot Manager je modul snap-in konzoly MMC (Microsoft Management Console), který zjednodušuje správu ochrany a zálohování dat ve Microsoft Azure StorSimplem prostředí. Pomocí StorSimple Snapshot Manager můžete spravovat Microsoft Azure StorSimple data v datovém centru a v cloudu jako jediné integrované řešení úložiště, což zjednodušuje procesy zálohování a snižuje náklady.

Tento přehled zavádí StorSimple Snapshot Manager, popisuje jeho funkce a vysvětluje jeho roli v Microsoft Azure StorSimple. 

Přehled celého Microsoft Azure StorSimple systému, včetně zařízení StorSimple, StorSimple Manager služby, StorSimple Snapshot Manager a adaptéru StorSimple pro SharePoint, najdete v tématu [StorSimple 8000 series: řešení hybridního cloudového úložiště](storsimple-overview.md). 

> [!NOTE]
> * Snapshot Manager StorSimple nemůžete použít ke správě Microsoft Azure StorSimple virtuálních polí (označují se také jako StorSimple místní virtuální zařízení).
> * Pokud plánujete nainstalovat StorSimple Update 2 na zařízení StorSimple, nezapomeňte si stáhnout nejnovější verzi StorSimple Snapshot Manager a nainstalovat ji **ještě před instalací StorSimple Update 2**. Nejnovější verze StorSimple Snapshot Manager je zpětně kompatibilní a funguje se všemi vydanými verzemi Microsoft Azure StorSimple. Pokud používáte předchozí verzi StorSimple Snapshot Manager, budete ji muset aktualizovat (před instalací nové verze ale nemusíte tuto předchozí verzi odinstalovat).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager účel a architektura
StorSimple Snapshot Manager poskytuje centrální konzolu pro správu, kterou můžete použít k vytvoření konzistentních záložních kopií místních a cloudových dat v určitém bodě v čase. Konzolu můžete například použít k těmto akcím:

* Nakonfigurujte, zálohujte a odstraňte svazky.
* Nakonfigurujte skupiny svazků, aby se zajistilo, že zálohovaná data jsou konzistentní vzhledem k aplikacím.
* Spravujte zásady zálohování tak, aby se data zálohovali podle předem určeného plánu.
* Vytvořte místní a cloudové snímky, které je možné uložit v cloudu a použít k zotavení po havárii.

StorSimple Snapshot Manager načte seznam aplikací zaregistrovaných u poskytovatele služby Stínová kopie svazku na hostiteli. Pokud pak chcete vytvořit zálohy konzistentní s aplikací, zkontroluje svazky používané aplikací a navrhne skupiny svazků, které se mají nakonfigurovat. StorSimple Snapshot Manager využívá tyto skupiny svazků k vytváření záložních kopií, které jsou konzistentní vzhledem k aplikacím. (Konzistence aplikace existuje, když se synchronizují všechny související soubory a databáze a představuje skutečný stav aplikace v určitém bodě v čase.) 

StorSimple Snapshot Manager zálohy mají podobu přírůstkových snímků, které zachycují pouze změny od poslední zálohy. V důsledku toho zálohování vyžaduje méně místa a je možné je rychle vytvořit a obnovit. StorSimple Snapshot Manager používá službu Windows služba Stínová kopie svazku (VSS) k zajištění toho, aby snímky zachytí data konzistentní vzhledem k aplikacím. (Další informace najdete v části integrace s Windows služba Stínová kopie svazku.) Pomocí Snapshot Manager StorSimple můžete vytvářet plány zálohování nebo podle potřeby provádět okamžité zálohování. Pokud potřebujete obnovit data ze zálohy, StorSimple Snapshot Manager umožňuje vybrat z katalogu místních nebo cloudových snímků. Azure StorSimple obnoví pouze data, která jsou potřebná v případě potřeby, což zabrání prodlevám v dostupnosti dat během operací obnovení.)

![Architektura Snapshot Manager StorSimple](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architektura Snapshot Manager StorSimple** 

## <a name="support-for-multiple-volume-types"></a>Podpora pro více typů svazků
Snapshot Manager StorSimple můžete použít ke konfiguraci a zálohování následujících typů svazků: 

* **Základní svazky** – základní svazek je jedním oddílem na základním disku. 
* **Jednoduché svazky** – jednoduchý svazek je dynamický svazek, který obsahuje místo na disku z jednoho dynamického disku. Jednoduchý svazek se skládá z jedné oblasti na disku nebo několika oblastí, které jsou propojeny na stejném disku. (Jednoduché svazky můžete vytvořit pouze na dynamických discích.) Jednoduché svazky nejsou odolné proti chybám.
* **Dynamické svazky** – dynamický svazek je svazek vytvořený na dynamickém disku. Dynamické disky používají databázi ke sledování informací o svazcích, které jsou obsaženy na dynamických discích v počítači. 
* **Dynamické svazky s zrcadlením** – dynamické svazky s zrcadlením jsou postavené na architektuře RAID 1. V případě RAID 1 se na dvou nebo více discích zapisuje identická data a vyprodukuje se zrcadlová sada. Žádost o čtení se pak dá zpracovat jakýmkoli diskem, který obsahuje požadovaná data.
* **Sdílené svazky clusteru** – se sdílenými svazky clusteru (CSV) může více uzlů v clusteru s podporou převzetí služeb při selhání současně číst nebo zapisovat na stejný disk. Převzetí služeb při selhání z jednoho uzlu na jiný uzel může probíhat rychle, aniž by se vyžadovala změna vlastnictví jednotky nebo připojení, odpojení a odebrání svazku. 

> [!IMPORTANT]
> Ve stejném snímku Nekombinujte CSV a non-CSV. Kombinování CSV a non-CSV ve snímku se nepodporuje. 
> 
> 

Pomocí StorSimple Snapshot Manager můžete obnovit celé skupiny svazků nebo klonovat jednotlivé svazky a obnovovat jednotlivé soubory.

* [Svazky a skupiny svazků](#volumes-and-volume-groups) 
* [Typy zálohování a zásady zálohování](#backup-types-and-backup-policies) 

Další informace o funkcích Snapshot Manager StorSimple a o tom, jak je používat, najdete v tématu [uživatelské rozhraní StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Svazky a skupiny svazků
Pomocí StorSimple Snapshot Manager vytvoříte svazky a pak je nakonfigurujete na skupiny svazků. 

StorSimple Snapshot Manager používá skupiny svazků k vytváření záložních kopií, které jsou konzistentní vzhledem k aplikacím. Konzistence aplikace existuje, když se synchronizují všechny související soubory a databáze a představuje skutečný stav aplikace v určitém bodě v čase. Skupiny svazků (které se také označují jako *skupiny konzistence*) tvoří základ úlohy zálohování nebo obnovení.

Skupiny svazků nejsou stejné jako kontejnery svazků. Kontejner svazků obsahuje jeden nebo víc svazků, které sdílejí účet cloudového úložiště a další atributy, jako je třeba šifrování a využití šířky pásma. Jeden kontejner svazků může obsahovat až 256 dynamicky zajištěných StorSimple svazků. Další informace o kontejnerech svazků najdete v [poznámkách ke správě kontejnerů svazků](./storsimple-8000-manage-volume-containers.md). Skupiny svazků jsou kolekce svazků, které nakonfigurujete pro usnadnění operací zálohování. Pokud vyberete dva svazky, které patří do různých kontejnerů svazků, umístěte je do jedné skupiny svazků a pak vytvoříte zásadu zálohování pro tuto skupinu svazků, bude každý svazek zálohovaný v příslušném kontejneru svazků pomocí příslušného účtu úložiště.

> [!NOTE]
> Všechny svazky ve skupině svazků musí pocházet od jednoho poskytovatele cloudové služby.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integrace s Windows služba Stínová kopie svazku
StorSimple Snapshot Manager používá ke zachycení dat konzistentních vzhledem k aplikacím službu Windows služba Stínová kopie svazku (VSS). Služba Stínová kopie svazku usnadňuje konzistenci aplikací tím, že komunikuje s aplikacemi pro službu Stínová kopie svazku a koordinuje vytváření přírůstkových snímků. Služba Stínová kopie svazku zajišťuje, aby byly aplikace dočasně neaktivní nebo quiescent při pořízení snímků. 

StorSimple Snapshot Manager implementace služby Stínová kopie svazku (VSS) funguje s SQL Server a obecnými svazky NTFS. Postup je následující: 

1. Žadatel, což je obvykle řešení pro správu a ochranu dat (například StorSimple Snapshot Manager) nebo zálohovací aplikace, vyvolá VSS a požádá ho, aby shromáždil informace ze softwaru pro zápis v cílové aplikaci.
2. Služba VSS kontaktuje součást zapisovače, aby načetla popis dat. Zapisovač vrátí popis zálohovaných dat. 
3. Služba VSS signalizuje zapisovači, aby aplikaci připravila k zálohování. Zapisovač připraví data pro zálohování tím, že dokončí otevřené transakce, aktualizují protokoly transakcí a tak dále a upozorní službu VSS.
4. Služba VSS dá zapisovači pokyn k dočasnému zastavení úložišť dat aplikace a zajistěte, aby do svazku během vytváření stínové kopie nebyla zapsána žádná data. Tento krok zajišťuje konzistenci dat a netrvá déle než 60 sekund.
5. Služba VSS instruuje poskytovatele, aby vytvořil stínovou kopii. Poskytovatelé, kteří můžou používat software nebo hardware, spravují aktuálně spuštěné svazky a vytvářejí na vyžádání stínové kopie. Zprostředkovatel vytvoří stínovou kopii a po dokončení zobrazí upozornění služby VSS.
6. Služba VSS kontaktuje zapisovače a upozorní aplikaci, že I/O může pokračovat, a také potvrdit, že se vstupně-výstupní operace pozastavila během vytváření stínové kopie. 
7. Pokud byla kopie úspěšná, služba VSS vrátí umístění kopie do žadatele. 
8. Pokud byla během vytváření stínové kopie zapsána data, bude záloha nekonzistentní. Služba VSS odstraní stínovou kopii a upozorní žadatele. Žadatel může buď znovu opakovat proces zálohování, nebo požádat správce, aby to opakoval později.

Podívejte se na následující obrázek.

![Proces VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Proces služba Stínová kopie svazku Windows** 

## <a name="backup-types-and-backup-policies"></a>Typy zálohování a zásady zálohování
StorSimple Snapshot Manager můžete zálohovat data a ukládat je místně a v cloudu. StorSimple Snapshot Manager můžete použít k okamžitému zálohování dat, nebo můžete použít zásady zálohování a vytvořit plán pro automatické přebírání záloh. Zásady zálohování také umožňují určit, kolik snímků bude zachováno. 

### <a name="backup-types"></a>Typy zálohování
StorSimple Snapshot Manager můžete použít k vytvoření následujících typů záloh:

* **Místní snímky** – místní snímky jsou kopie dat svazků, které jsou uložené na zařízení StorSimple. Tento typ zálohování je typicky možné vytvořit a rychle obnovit. Místní snímek můžete použít stejně jako místní záložní kopii.
* **Cloudové snímky** – cloudové snímky jsou kopie dat svazků, které jsou uložené v cloudu, v časovém bodě. Snímek v cloudu je ekvivalentní snímku replikovanému na jiném systému úložiště mimo lokalitu. Cloudové snímky jsou zvláště užitečné ve scénářích zotavení po havárii.

### <a name="on-demand-and-scheduled-backups"></a>Naplánovaných záloh na vyžádání
Pomocí StorSimple Snapshot Manager můžete iniciovat jednorázové zálohování, které se má okamžitě vytvořit, nebo můžete pomocí zásad zálohování naplánovat opakované operace zálohování.

Zásada zálohování je sada automatizovaných pravidel, která můžete použít k plánování pravidelného zálohování. Zásady zálohování umožňují definovat četnost a parametry pro pořizování snímků konkrétní skupiny svazků. Zásady můžete použít k zadání dat začátku a konce platnosti, časů, četnosti a požadavků na uchovávání pro místní i cloudové snímky. Zásady se aplikují hned po jejím definování. 

StorSimple Snapshot Manager můžete použít ke konfiguraci nebo překonfigurování zásad zálohování, kdykoli je to potřeba. 

Pro každou zásadu zálohování, kterou vytvoříte, nakonfigurujete následující informace:

* **Název** – jedinečný název vybrané zásady zálohování.
* **Typ** – typ zásady zálohování; buď místní snímek, nebo snímek v cloudu.
* **Skupina svazků** – skupina svazků, ke které je přiřazena vybraná zásada zálohování.
* **Uchování** – počet záložních kopií, které mají být zachovány. Pokud zaškrtnete políčko **vše** , všechny záložní kopie budou uchovány, dokud nedosáhnete maximálního počtu záložních kopií na svazek. v takovém případě zásada selže a vygeneruje chybovou zprávu. Případně můžete zadat počet záloh, které mají být zachovány (mezi 1 a 64).
* **Datum** – datum, kdy se vytvořila zásada zálohování.

Informace o konfiguraci zásad zálohování najdete v [StorSimple použití Snapshot Manager k vytváření a správě zásad zálohování](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Monitorování a správa úloh zálohování
Snapshot Manager StorSimple můžete použít ke sledování a správě nadcházejících, plánovaných a dokončených úloh zálohování. StorSimple Snapshot Manager navíc poskytuje katalog až 64 dokončených záloh. Katalog můžete použít k vyhledání a obnovení svazků nebo jednotlivých souborů. 

Informace o sledování úloh zálohování získáte tak, že přejdete na [použití StorSimple Snapshot Manager k zobrazení a správě úloh zálohování](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [použití Snapshot Manager StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Stáhněte [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).