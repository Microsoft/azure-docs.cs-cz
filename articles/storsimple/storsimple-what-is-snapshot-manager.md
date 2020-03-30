---
title: Co je StorSimple Snapshot Manager? | Dokumentace Microsoftu
description: Popisuje Správce snímků StorSimple, jeho architekturu a jeho funkce.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: twooley
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e58e2d929dd1e4db16ce495ad54045e9dc3a6fb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267414"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Úvod do Správce snímků StorSimple

## <a name="overview"></a>Přehled
StorSimple Snapshot Manager je modul snap-in konzoly MMC (Microsoft Management Console), který zjednodušuje ochranu dat a správu zálohování v prostředí Microsoft Azure StorSimple. Pomocí Správce snímků StorSimple můžete spravovat data Microsoft Azure StorSimple v datovém centru a v cloudu jako jediné integrované úložné řešení, čímž zjednodušíte procesy zálohování a snížíte náklady.

Tento přehled představuje Správce snímků StorSimple, popisuje jeho funkce a vysvětluje jeho roli v Microsoft Azure StorSimple. 

Přehled celého systému Microsoft Azure StorSimple, včetně zařízení StorSimple, služby StorSimple Manager, správce snímků StorSimple a adaptéru StorSimple pro sharepoint, najdete v [článku Řada StorSimple 8000: řešení hybridního cloudového úložiště](storsimple-overview.md). 

> [!NOTE]
> * Správce snímků StorSimple nelze použít ke správě virtuálních polí Microsoft Azure StorSimple (označovaných také jako místní virtuální zařízení StorSimple).
> * Pokud plánujete nainstalovat StorSimple Update 2 na zařízení StorSimple, nezapomeňte stáhnout nejnovější verzi Správce snímků StorSimple a nainstalovat jej **před instalací StorSimple Update 2**. Nejnovější verze Správce snímků StorSimple je zpětně kompatibilní a funguje se všemi vydanými verzemi Microsoft Azure StorSimple. Pokud používáte předchozí verzi Správce snímků StorSimple, budete ji muset aktualizovat (před instalací nové verze není nutné odinstalovat předchozí verzi).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Účel a architektura správce snímků StorSimple
Správce snímků StorSimple poskytuje centrální konzolu pro správu, kterou můžete použít k vytvoření konzistentních záložních kopií místních a cloudových dat v okamžiku. Konzolu můžete například použít k:

* Konfigurace, zálohování a odstranění svazků.
* Nakonfigurujte skupiny svazků, abyste zajistili, že zálohovaná data budou konzistentní s aplikací.
* Spravujte zásady zálohování tak, aby data byla zálohována podle předem stanoveného plánu.
* Vytvořte místní a cloudové snímky, které lze uložit v cloudu a použít pro zotavení po havárii.

Správce snímků StorSimple načte seznam aplikací registrovaných u zprostředkovatele VSS na hostiteli. Potom k vytvoření záloh konzistentní aplikace, zkontroluje svazky používané aplikací a navrhuje skupiny svazků ke konfiguraci. Správce snímků StorSimple používá tyto skupiny svazků ke generování záložních kopií, které jsou konzistentní s aplikací. (Konzistence aplikace existuje, když jsou synchronizovány všechny související soubory a databáze a představují skutečný stav aplikace v určitém okamžiku v čase.) 

Zálohy Správce snímků StorSimple mají podobu přírůstkových snímků, které zachycují pouze změny od poslední zálohy. V důsledku toho zálohy vyžadují méně úložného prostoru a lze je rychle vytvořit a obnovit. Správce snímků StorSimple používá službu Stínové kopie svazků systému Windows (VSS) k zajištění toho, aby snímky zachycovaly data konzistentní s aplikací. (Další informace naleznete v části Integrace se službou Stínové kopie svazků systému Windows.) Pomocí Správce snímků StorSimple můžete vytvořit plány zálohování nebo provést okamžité zálohování podle potřeby. Pokud potřebujete obnovit data ze zálohy, StorSimple Snapshot Manager umožňuje vybrat z katalogu místních nebo cloudových snímků. Azure StorSimple obnoví jenom data, která je potřeba podle potřeby, což zabraňuje zpoždění v dostupnosti dat během operací obnovení.)

![Architektura Správce snímků StorSimple](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architektura Správce snímků StorSimple** 

## <a name="support-for-multiple-volume-types"></a>Podpora pro více typů svazků
Správce snímků StorSimple můžete použít ke konfiguraci a zálohování následujících typů svazků: 

* **Základní svazky** – Základní svazek je jeden oddíl na základním disku. 
* **Jednoduché svazky** – Jednoduchý svazek je dynamický svazek, který obsahuje místo na disku z jednoho dynamického disku. Jednoduchý svazek se skládá z jedné oblasti na disku nebo z více oblastí, které jsou propojeny na stejném disku. (Jednoduché svazky lze vytvářet pouze na dynamických discích.) Jednoduché svazky nejsou odolné proti chybám.
* **Dynamické svazky** – Dynamický svazek je svazek vytvořený na dynamickém disku. Dynamické disky používají databázi ke sledování informací o svazcích, které jsou obsaženy na dynamických discích v počítači. 
* **Dynamické svazky se zrcadlením** – Dynamické svazky se zrcadlením jsou postaveny na architektuře RAID 1. S RAID 1, identické data jsou zapsány na dva nebo více disků, produkovat zrcadlené sady. Požadavek na čtení pak může být zpracován libovolným diskem, který obsahuje požadovaná data.
* **Svazky sdílené clusterem** – U svazků sdílených clusterem (CSV) může více uzlů v clusteru s podporou převzetí služeb při selhání současně číst nebo zapisovat na stejný disk. Převzetí služeb při selhání z jednoho uzlu do jiného uzlu může dojít rychle, bez nutnosti změny vlastnictví jednotky nebo připojení, demontáže a odebrání svazku. 

> [!IMPORTANT]
> Nekombinujte CSV a non-CSVs ve stejném snímku. Míchání CSV a non-CSVs ve snímku není podporována. 
> 
> 

Správce snímků StorSimple můžete použít k obnovení celých skupin svazků nebo klonování jednotlivých svazků a obnovení jednotlivých souborů.

* [Objemy a skupiny svazků](#volumes-and-volume-groups) 
* [Typy zálohování a zásady zálohování](#backup-types-and-backup-policies) 

Další informace o funkcích Správce snímků StorSimple a jejich použití naleznete v [tématu StorSimple Snapshot Manager user interface](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Objemy a skupiny svazků
Pomocí Správce snímků StorSimple vytvoříte svazky a pak je nakonfigurujete do skupin svazků. 

Správce snímků StorSimple používá skupiny svazků k vytvoření záložních kopií, které jsou konzistentní s aplikací. Konzistence aplikace existuje, když jsou synchronizovány všechny související soubory a databáze a představují skutečný stav aplikace v určitém okamžiku. Skupiny svazků (které jsou také označovány jako *skupiny konzistence*) tvoří základ úlohy zálohování nebo obnovení.

Skupiny svazků nejsou stejné jako kontejnery svazků. Kontejner svazku obsahuje jeden nebo více svazků, které sdílejí účet cloudového úložiště a další atributy, jako je šifrování a využití šířky pásma. Kontejner s jedním svazkem může obsahovat až 256 tence zřízených svazků StorSimple. Další informace o kontejnerech svazků naleznete v najdete [v informacích Správa kontejnerů svazků](storsimple-manage-volume-containers.md). Skupiny svazků jsou kolekce svazků, které nakonfigurujete pro usnadnění operací zálohování. Pokud vyberete dva svazky, které patří do různých kontejnerů svazků, umístěte je do jedné skupiny svazků a pak vytvoříte zásadu zálohování pro tuto skupinu svazků, bude každý svazek zálohován v příslušném kontejneru svazků pomocí příslušného účtu úložiště.

> [!NOTE]
> Všechny svazky ve skupině svazků musí pocházet od jednoho poskytovatele cloudových služeb.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integrace se službou Stínové kopie svazků systému Windows
Správce snímků StorSimple používá službu Stínové kopie svazků systému Windows (VSS) k zachycení dat konzistentních s aplikací. VSS usnadňuje konzistenci aplikací tím, že komunikuje s aplikacemi podporujícími VSS a koordinuje vytváření přírůstkových snímků. VSS zajišťuje, že aplikace jsou dočasně neaktivní nebo klidový, když jsou pořízeny snímky. 

Implementace Správce snímků StorSimple v systému VSS funguje se serverem SQL Server a obecnými svazky NTFS. Postup je následující: 

1. Žadatel, který je obvykle řešení pro správu dat a ochranu (například StorSimple Snapshot Manager) nebo záložní aplikace, vyvolá VSS a požádá jej ke shromažďování informací ze softwaru pro zapisovače v cílové aplikaci.
2. VSS kontaktuje komponentu zapisovače, aby načetl popis dat. Zapisovatel vrátí popis dat, která mají být zálohována. 
3. VSS signalizuje zapisovateli připravit aplikaci pro zálohování. Zapisovač připraví data pro zálohování dokončením otevřených transakcí, aktualizací protokolů transakcí a tak dále a pak upozorní VSS.
4. VSS pokyn zapisovač dočasně zastavit úložiště dat aplikace a ujistěte se, že žádná data zapsána do svazku, zatímco stínová kopie je vytvořen. Tento krok zajišťuje konzistenci dat a trvá déle než 60 sekund.
5. VSS instruuje zprostředkovatele k vytvoření stínové kopie. Zprostředkovatelé, kteří mohou být založeni na softwaru nebo hardwaru, spravují aktuálně spuštěné svazky a vytvářejí stínové kopie na vyžádání. Zprostředkovatel vytvoří stínovou kopii a upozorní VSS po dokončení.
6. VSS kontaktuje zapisovatele, aby aplikaci oznámil, že vstupně-v.o může pokračovat, a také potvrdil, že vstupně-up byl během vytváření stínové kopie úspěšně pozastaven. 
7. Pokud byla kopie úspěšná, vss vrátí umístění kopie žadateli. 
8. Pokud byla data zapsána v době vytvoření stínové kopie, bude záloha nekonzistentní. VSS odstraní stínovou kopii a upozorní ohláštele. Žadatel může proces zálohování opakovat automaticky nebo upozornit správce, aby jej později zopakoval.

Viz následující obrázek.

![Proces VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Proces stínové kopie svazků systému Windows** 

## <a name="backup-types-and-backup-policies"></a>Typy zálohování a zásady zálohování
Pomocí Správce snímků StorSimple můžete zálohovat data a ukládat je místně a v cloudu. Správce snímků StorSimple můžete použít k okamžitému zálohování dat nebo můžete použít zásadu zálohování k vytvoření plánu pro automatické zálohování. Zásady zálohování také umožňují určit, kolik snímků bude zachováno. 

### <a name="backup-types"></a>Typy zálohování
Správce snímků StorSimple můžete použít k vytvoření následujících typů záloh:

* **Místní snímky** – Místní snímky jsou kopie dat svazku v okamžiku, které jsou uloženy na zařízení StorSimple. Tento typ zálohy lze obvykle rychle vytvořit a obnovit. Můžete použít místní snímek jako místní záložní kopii.
* **Cloudové snímky** – snímky cloudu jsou kopie dat svazku v okamžiku, které jsou uloženy v cloudu. Snímek cloudu je ekvivalentní snímku replikovaného v jiném systému úložiště mimo pracoviště. Cloudové snímky jsou užitečné zejména ve scénářích zotavení po havárii.

### <a name="on-demand-and-scheduled-backups"></a>Zálohy na vyžádání a plánované zálohování
Pomocí Správce snímků StorSimple můžete zahájit jednorázovou zálohu, která bude vytvořena okamžitě, nebo můžete použít zásadu zálohování k naplánování opakovaných operací zálohování.

Zásady zálohování je sada automatizovaných pravidel, která můžete použít k plánování pravidelných záloh. Zásady zálohování umožňuje definovat frekvenci a parametry pro pořizování snímků určité skupiny svazků. Zásady můžete použít k určení data zahájení a vypršení platnosti, časy, frekvence a požadavky na uchovávání informací pro místní i cloudové snímky. Zásada je použita ihned po její definici. 

Správce snímků StorSimple můžete použít ke konfiguraci nebo překonfigurování zásad zálohování, kdykoli je to nutné. 

Pro každou vytvářenou zásadu zálohování nakonfigurujete následující informace:

* **Název** – jedinečný název vybrané zásady zálohování.
* **Typ** – typ zásadzálohování; buď místní snímek nebo snímek cloudu.
* **Skupina svazků** – skupina svazků, ke které je přiřazena vybraná zásada zálohování.
* **Uchovávání informací** – počet záložních kopií, které chcete zachovat. Pokud zaškrtnete **políčko Vše,** všechny záložní kopie zůstanou zachovány, dokud nebude dosaženo maximálního počtu záložních kopií na svazek, v tomto okamžiku se zásada nezdaří a vygeneruje chybovou zprávu. Případně můžete zadat počet záloh, které chcete zachovat (mezi 1 a 64).
* **Datum** – datum, kdy byla vytvořena zásada zálohování.

Informace o konfiguraci zásad zálohování naleznete v [seznamu Vytvoření a správa zásad zálohování pomocí správce snímků StorSimple](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Monitorování a správa úloh zálohování
Správce snímků StorSimple můžete použít ke sledování a správě nadcházejících, naplánovaných a dokončených úloh zálohování. Kromě toho Správce snímků StorSimple poskytuje katalog až 64 dokončených záloh. Katalog můžete použít k vyhledání a obnovení svazků nebo jednotlivých souborů. 

Informace o monitorování úloh zálohování naleznete v seznamu [Pomocí správce snímků StorSimple zobrazit a spravovat úlohy zálohování](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Další kroky
* Další informace o [použití Správce snímků StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Stáhnout [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

