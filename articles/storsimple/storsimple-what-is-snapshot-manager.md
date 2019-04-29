---
title: Co je StorSimple Snapshot Manager? | Dokumenty Microsoft
description: Popisuje Snapshot Manageru zařízení StorSimple, jeho architektuře a jeho funkcí.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: v-sharos
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f7436bb63f52c9c2b697c8e7031922ce89d786b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789594"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Úvod do StorSimple Snapshot Manageru

## <a name="overview"></a>Přehled
StorSimple Snapshot Manageru je modul snap-in konzoly Microsoft Management Console (MMC), který zjednodušuje ochranu dat a správy zálohování v prostředí Microsoft Azure StorSimple. Pomocí StorSimple Snapshot Manager můžete spravovat Microsoft Azure StorSimple data v datovém centru i v cloudu jako jedné integrované řešení úložiště, tedy zjednodušení procesů zálohování a snížení nákladů.

Tento přehled zavádí StorSimple Snapshot Manageru, popisuje jeho funkcí a vysvětluje jejich rolí v Microsoft Azure StorSimple. 

Přehled celého systému Microsoft Azure StorSimple, včetně zařízení StorSimple, služby StorSimple Manager, StorSimple Snapshot Manageru a StorSimple Adapter pro SharePoint, naleznete v tématu [StorSimple řady 8000: hybridní cloud řešení úložiště](storsimple-overview.md). 

> [!NOTE]
> * StorSimple Snapshot Manageru nejde použít pro správu Microsoft Azure StorSimple virtuální pole (označované také jako StorSimple místní virtuální zařízení).
> * Pokud budete chtít nainstalovat StorSimple Update 2 na zařízení StorSimple, je potřeba stáhnout nejnovější verzi StorSimple Snapshot Manageru a nainstalujte ho **před instalací StorSimple Update 2**. Nejnovější verzi StorSimple Snapshot Manageru je zpětně kompatibilní a funguje ve všech vydaných verzích Microsoft Azure StorSimple. Pokud používáte předchozí verzi StorSimple Snapshot Manageru, je potřeba aktualizovat (není potřeba odinstalovat předchozí verzi před instalací nové verze).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Architektura a účel StorSimple Snapshot Manageru
StorSimple Snapshot Manageru poskytuje centrální konzolu, můžete použít k vytvoření konzistentní vzhledem k aplikacím, v okamžiku záložní kopie místní a Cloudová data. Například můžete použít konzolu pro:

* Konfigurace zálohování a odstraňte svazky.
* Konfigurace skupin svazků, aby zálohovaných dat je konzistentních s aplikací.
* Správa zásad zálohování tak, aby se data zálohují na předem určeného plánu.
* Vytvořit místní a cloudové snímky, které můžou být uložená v cloudu a používat pro zotavení po havárii.

StorSimple Snapshot Manager načte seznam aplikací, které jsou zaregistrované u poskytovatele služby VSS na hostiteli. K vytvoření zálohy konzistentní s aplikací, pak kontroluje svazků aplikace použít a navrhne skupin svazků konfigurace. StorSimple Snapshot Manageru používá tyto skupiny svazek k vygenerování záložní kopie, které jsou konzistentní s aplikací. (Konzistence aplikace existuje, když všechny související soubory a databáze jsou synchronizovány a představují o skutečném stavu aplikace v konkrétním bodě v čase.) 

StorSimple Snapshot Manageru zálohy podobu přírůstkových snímků, které zaznamenávají pouze změny od posledního zálohování. V důsledku toho zálohy vyžadují méně úložiště a může být vytvořen a obnovit rychle. StorSimple Snapshot Manageru používá Windows svazku stínové kopie Service (VSS) k zajištění, že zachytávat snímky konzistentní s aplikací data. (Další informace, přejděte na integrace s oddílem Windows služby Stínová kopie svazku.) Pomocí StorSimple Snapshot Manageru můžete vytvořit plány zálohování nebo provést okamžité zálohování podle potřeby. Pokud chcete data obnovit ze zálohy, umožňuje StorSimple Snapshot Manageru vyberte z katalogu místních nebo cloudových snímků. Azure StorSimple obnoví pouze data, která je potřeba, protože je to potřeba, což zabrání zpoždění v dostupnost dat během operace obnovení).

![Architektura StorSimple Snapshot Manageru](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architektura StorSimple Snapshot Manageru** 

## <a name="support-for-multiple-volume-types"></a>Podpora pro více typů svazků
StorSimple Snapshot Manager můžete použít ke konfiguraci a zálohovat následující typy svazky: 

* **Základní svazky** – základní svazek je jednoho oddílu na základním disku. 
* **Jednoduché svazky** – jednoduchý svazek je dynamický svazek, který obsahuje místa na disku z jednoho dynamického disku. Jednoduchý svazek se skládá z jedné oblasti na disku nebo více oblastí, které jsou propojeny na stejném disku. (Pouze na dynamické disky můžete vytvořit jednoduché svazky.) Jednoduché svazky nejsou odolné proti chybám.
* **Dynamické svazky** – dynamický svazek je svazek vytvořený na dynamický disk. Dynamické disky používají ke sledování informací o svazcích, které jsou obsaženy na dynamických discích v počítači databáze. 
* **Dynamické svazky s zrcadlení** – dynamické svazky s zrcadlení je postavená na architektuře RAID 1. Pomocí diskového pole RAID 1 stejná data zapisují na dvou nebo více disků, vytváření zrcadlených sady. Žádost o čtení může být zpracována pak žádný disk obsahující požadovaná data.
* **Sdílené svazky clusteru** – sdílené svazky clusteru (CSV), několik uzlů v převzetí služeb při selhání clusteru můžou současně čtení nebo zápisu ke stejnému disku. Převzetí služeb při selhání z jednoho uzlu do jiného uzlu může dojít, rychle a bez nutnosti změny vlastnictví disku nebo připojením, odpojení a odstranění svazku. 

> [!IMPORTANT]
> Nekombinujte sdílených svazků clusteru a bez-sdílených svazků clusteru ve stejné snímku. Smíšené používání sdílených svazků clusteru a bez CSV snímku není podporována. 
> 
> 

StorSimple Snapshot Manageru můžete obnovit celý svazek skupiny nebo naklonujte jednotlivé svazky a obnovení jednotlivých souborů.

* [Svazky a skupin svazků](#volumes-and-volume-groups) 
* [Typy zálohování a zásady zálohování](#backup-types-and-backup-policies) 

Další informace o funkcích Snapshot Manageru zařízení StorSimple a jejich použití naleznete v tématu [uživatelské rozhraní StorSimple Snapshot Manageru](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Svazky a skupin svazků
Pomocí StorSimple Snapshot Manageru budete vytvářet svazky a nakonfigurujete je do skupin svazků. 

StorSimple Snapshot Manageru k vytvoření záložní kopie, které jsou konzistentní s aplikací používá skupin svazků. Konzistentnost aplikací existuje, když všechny související soubory a databáze jsou synchronizovány a představují true stavu aplikace v konkrétním bodě v čase. Skupin svazků (což se také označují jako *skupiny konzistence*) představují základ pro zálohování nebo obnovení úlohy.

Svazek skupiny nejsou stejná jako kontejnery svazků. Kontejner svazků obsahuje jeden nebo více svazků, které sdílejí účtu cloudového úložiště a další atributy, jako je například šifrování a šířky pásma. Jeden svazek kontejner může obsahovat až 256 dynamicky zřizované svazky StorSimple. Další informace o kontejnery svazků, přejděte na [Spravovat kontejnery svazků](storsimple-manage-volume-containers.md). Svazek skupiny jsou kolekce svazků, které nakonfigurujete pro usnadnění operace zálohování. Pokud vyberete dva svazky, které patří do jiného svazku kontejnerů, je umístit do jednoho svazku skupiny a vytvořte zásadu zálohování pro tuto skupinu svazku, všechny svazky zálohovat v kontejneru svazku, pomocí účtu odpovídající úložiště.

> [!NOTE]
> Všechny svazky ve skupině svazku musí pocházet z jednoho poskytovatele cloudových služeb.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integrace s Windows služby Stínová kopie svazku
StorSimple Snapshot Manageru používá k zaznamenání dat konzistentních s aplikací Windows Stínová kopie svazku Service (VSS). Stínové kopie svazku zajišťuje konzistenci aplikace tím, že komunikuje s aplikací podporujících službu VSS pro koordinaci vytváření přírůstkových snímků. Stínové kopie svazku zajišťuje, že aplikace jsou dočasně není neaktivní nebo klidný, když snímky. 

StorSimple Snapshot Manageru implementace služby Stínová kopie svazku funguje s SQL serverem a obecné svazky systému souborů NTFS. Probíhá takto: 

1. Žadatel, což je obvykle Správa dat a řešení ochrany (jako je StorSimple Snapshot Manageru) nebo zálohovací aplikaci, vyvolá VSS a vyzve ho k získání informací z zapisovače softwaru v cílové aplikaci.
2. VSS kontaktuje komponenty zápisu načíst popis data. Zapisovač, který vrací popis dat, které mají být zazálohovány. 
3. Signály stínové kopie svazku zapisovač, který má příprava aplikace pro zálohování. Zapisovač připraví data pro zálohování pomocí otevřených transakcí, aktualizuje protokoly transakcí a tak dále a upozorní službu VSS.
4. VSS nastaví zapisovač, který má dočasně zastavte úložišť dat vaší aplikace a ujistěte se, že žádná data se zapisují do svazku při vytvoření stínové kopie. Tento krok zajistí konzistenci dat a přijímá více než 60 sekund.
5. VSS nastaví poskytovatele za účelem vytvoření stínové kopie. Poskytovateli, což může být software nebo hardware založené, Správa svazků, které jsou aktuálně spuštěné a vytvoření stínové kopie je na vyžádání. Zprostředkovatel vytvoří stínovou kopii a stínové kopie svazku oznámí, až se dokončí.
6. Kontaktuje VSS writer aplikaci oznámilo, že vstupně-výstupních operací můžete pokračovat a také potvrďte, že vstupně-výstupní operace byla úspěšně pozastavena během vytváření stínové kopie. 
7. Pokud kopii byla úspěšná, vrátí VSS kopírování umístění žadateli. 
8. Pokud data byla zapsána, zatímco stínové kopie byla vytvořena, bude zálohování nekonzistentní. Stínové kopie svazku odstraní stínových kopií a oznámí žadatel. Žadatel můžete automaticky opakujte proces zálohování nebo upozornit na správce a zkuste to znovu později.

Viz následující obrázek.

![Proces VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Proces služby Stínová kopie svazku pro Windows** 

## <a name="backup-types-and-backup-policies"></a>Typy zálohování a zásady zálohování
Pomocí StorSimple Snapshot Manageru, můžete zálohovat data a uložte ho místně i v cloudu. StorSimple Snapshot Manageru můžete zálohovat data okamžitě nebo můžete použít zásady zálohování k vytvoření plánu automaticky umožňoval vytvářet zálohy. Zásady zálohování také umožňují určit, kolik snímků se zachovají. 

### <a name="backup-types"></a>Typy zálohování
StorSimple Snapshot Manager můžete použít k vytvoření následujících typů zálohy:

* **Místní snímky** – místní snímky jsou kopie v okamžiku objem dat, které jsou uložené na zařízení StorSimple. Tento typ zálohování obvykle, můžete vytvořit a rychle obnovit. Můžete použít místní snímek, stejně jako místní záložní kopie.
* **Cloudové snímky** – cloudové snímky jsou kopie v okamžiku objem dat, které jsou uložené v cloudu. Snímek v cloudu je ekvivalentní ke snímku replikovat i na jiné vzdálené úložiště systému. Cloudové snímky jsou zvláště užitečné ve scénářích zotavení po havárii.

### <a name="on-demand-and-scheduled-backups"></a>Zálohování na vyžádání a plánované
Pomocí StorSimple Snapshot Manageru zahájíte jednorázovou zálohu okamžitě vytvořit nebo zásady zálohování můžete použít k naplánování opakování operace zálohování.

Zásada zálohování je sada automatizovaných pravidel, které můžete použít k naplánování pravidelného zálohování. Zásady zálohování umožňuje definovat četnost a parametry pro pořizování snímků skupinu určitý svazek. Zásady můžete použít k určení počátku a ukončení platnosti data, časy, frekvencí a požadavky na uchovávání dat pro obě místní a cloudové snímky. Zásada se použije ihned poté, co můžete definovat. 

StorSimple Snapshot Manager můžete použít ke konfiguraci nebo změnit konfiguraci zásad zálohování, kdykoli je to zapotřebí. 

Můžete nakonfigurovat pro každé zásadě zálohování, který vytvoříte následující informace:

* **Název** – jedinečný název vybrané zásady zálohování.
* **Typ** – typ zásady zálohování; místní snímek nebo cloudový snímek.
* **Skupiny svazků** – skupiny svazků, ke kterému je přiřazena vybraných zásadách zálohování.
* **Uchování** – počet záložních kopií, pokud chcete zachovat. Pokud zaškrtnete **všechny** pole, všechny záložní kopie uchovávají až do dosažení maximální počet záložních kopií každého svazku, v tomto okamžiku zásady selže a generovat chybovou zprávu. Alternativně můžete zadat počet záloh uchovávat (1 až 64).
* **Datum** – datum vytvoření zásady zálohování.

Informace o konfiguraci zásad zálohování, přejděte na [pomocí StorSimple Snapshot Manageru k vytvoření a Správa zásad zálohování](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Správa a monitorování úlohy zálohování
StorSimple Snapshot Manager můžete použít ke sledování a správě nadcházející plánované a dokončení úlohy zálohování. Kromě toho StorSimple Snapshot Manageru poskytuje katalog až 64 dokončené zálohy. Můžete vyhledat a obnovit svazky nebo jednotlivé soubory katalogu. 

Informace o monitorování úlohy zálohování, přejděte na [pomocí StorSimple Snapshot Manageru k zobrazení a Správa úloh zálohování](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Další postup
* Další informace o [pomocí StorSimple Snapshot Manager ke správě vašeho řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Stáhněte si [StorSimple Snapshot Manageru](https://www.microsoft.com/download/details.aspx?id=44220).

