---
title: Co je StorSimple Snapshot Manager? | Dokumentace Microsoftu
description: Popisuje Snapshot Manager zařízení StorSimple, jeho architektura a jejich funkce.
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
ms.openlocfilehash: 38c197c7bc57110b29b1d8cb789d5b7310823da2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876828"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Úvod k Snapshot Manager zařízení StorSimple

## <a name="overview"></a>Přehled
Snapshot Manager zařízení StorSimple je modul snap-in konzoly Microsoft Management Console (MMC), který zjednodušuje ochrany dat a správu záloh v prostředí Microsoft Azure StorSimple. S StorSimple Snapshot Manager můžete spravovat Microsoft Azure StorSimple data v datovém centru i v cloudu jako řešení jedné integrované úložiště, proto kvůli zjednodušení zálohování procesů a snížení nákladů.

Tento přehled zavádí Snapshot Manager zařízení StorSimple, popisuje její funkce a vysvětluje, jeho role v Microsoft Azure StorSimple. 

Přehled celého systému Microsoft Azure StorSimple, včetně zařízení StorSimple, služby StorSimple Manager, Snapshot Manager zařízení StorSimple a StorSimple adaptéru pro službu SharePoint, naleznete v části [řady StorSimple 8000: hybridní cloud řešení úložiště](storsimple-overview.md). 

> [!NOTE]
> * StorSimple Snapshot Manager nelze použít pro správu Microsoft Azure StorSimple virtuální pole (také označované jako StorSimple místní virtuální zařízení).
> * Pokud plánujete nainstalovat StorSimple Update 2 na zařízení StorSimple, je nutné stáhnout nejnovější verzi služby StorSimple Snapshot Manager a nainstalujte ji **před instalací StorSimple Update 2**. Nejnovější verzi služby StorSimple Snapshot Manager je zpětně kompatibilní a spolupracuje s všechny vydané verze Microsoft Azure StorSimple. Pokud používáte předchozí verze služby StorSimple Snapshot Manager, musíte aktualizovat (není nutné odinstalovat předchozí verzi před instalací nové verze).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Účel Snapshot Manager zařízení StorSimple a architektura
StorSimple Snapshot Manager poskytuje konzolu centrální správy, která můžete použít k vytvoření konzistentní, v okamžiku záložní kopie místní a Cloudová data. Například můžete použít konzolu pro:

* Konfigurace, zálohovat a odstraňte svazky.
* Konfigurace skupin svazku zajistit zálohovaných dat je konzistentní s aplikací.
* Spravovat zásady zálohování tak, aby se data zálohují na předem určeného plánu.
* Vytvořte místních a cloudových snímků, které můžou být uložené v cloudu a použít pro obnovení po havárii.

StorSimple Snapshot Manager načte seznam aplikací, které jsou registrované u poskytovatele služby Stínová kopie svazku na hostiteli. K vytvoření zálohování konzistentní s aplikací, poté zkontroluje svazky používané aplikace a navrhuje svazku skupiny ke konfiguraci. StorSimple Snapshot Manager používá tyto skupiny svazek k vygenerování záložní kopie, které jsou konzistentní s aplikací. (Konzistence aplikací existuje, když všechny související soubory a databáze jsou synchronizovány a reprezentovat true stav aplikace v určitém bodě v čase.) 

Zálohování StorSimple Snapshot Manager mít formu přírůstkové snímky, které zaznamenat pouze změny od poslední zálohy. V důsledku toho zálohy vyžadují menší úložiště umožňuje vytvořit a a rychle obnovit. StorSimple Snapshot Manager používá Windows svazku stínové kopie Service (VSS) k zajištění, že zaznamenat snímky konzistentní s aplikací data. (Další informace, přejděte na integraci s části služby Stínová kopie svazku systému Windows.) S StorSimple Snapshot Manager můžete vytvořit plánů zálohování nebo provést okamžitou zálohování podle potřeby. Pokud potřebujete obnovit data ze zálohy, umožňuje Snapshot Manager zařízení StorSimple, vyberte z katalogu místní nebo cloudové snímky. Azure StorSimple obnoví pouze data, která je potřeba, protože je to potřeba, což zabraňuje zpoždění v dostupnost dat během operace obnovení).

![Architektura Snapshot Manager zařízení StorSimple](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architektura Snapshot Manager zařízení StorSimple** 

## <a name="support-for-multiple-volume-types"></a>Podpora pro více typů svazku
StorSimple Snapshot Manager můžete použít ke konfiguraci a zálohovat následující typy svazky: 

* **Základní svazky** – vytvoření základního svazku je jednoho oddílu na základním disku. 
* **Jednoduché svazky** – jednoduchý svazek je dynamický svazek, který obsahuje místa na disku z jeden dynamický disk. Jednoduchý svazek se skládá z jedné oblasti na disku nebo více oblastí, které jsou propojeny společně na stejném disku. (Jenom na dynamické disky můžete vytvořit jednoduché svazky.) Jednoduché svazky nejsou odolné proti chybám.
* **Dynamické svazky** – dynamický svazek je svazek vytvořený na dynamický disk. Dynamické disky používají databáze ke sledování informací o svazcích, které jsou obsaženy na dynamické disky v počítači. 
* **Dynamické svazky s zrcadlení** – dynamické svazky s zrcadlení jsou postaveny na architektuře RAID 1. S RAID 1 identické data zapsána na dva nebo víc disku, který vytvořil sadu zrcadlených. Požadavek čtení může následně ošetřit každý disk, který obsahuje požadovaná data.
* **Sdílených svazcích clusteru** – sdílených svazcích clusteru (CSV), více uzlů v převzetí služeb při selhání clusteru můžou současně číst nebo zapisovat na stejný disk. Převzetí služeb při selhání z jednoho uzlu do jiného uzlu může dojít, rychle a bez nutnosti změny vlastnictví disku nebo připojení, odpojení a odebrání svazku. 

> [!IMPORTANT]
> Nemíchat sdílené svazky clusteru a bez CSV ve stejném snímku. Kombinování sdílené svazky clusteru a bez CSV snímku se nepodporuje. 
> 
> 

StorSimple Snapshot Manager můžete obnovit celý svazek skupiny nebo klonovat jednotlivé svazky a obnovit jednotlivé soubory.

* [Svazky a svazek skupiny](#volumes-and-volume-groups) 
* [Typy zálohování a zásady zálohování](#backup-types-and-backup-policies) 

Další informace o funkcích Snapshot Manager zařízení StorSimple a jejich použití najdete v tématu [StorSimple Snapshot Manager uživatelské rozhraní](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Svazky a svazek skupiny
S StorSimple Snapshot Manager, můžete vytvářet svazky a nakonfigurujete je do skupin svazku. 

StorSimple Snapshot Manager používá k vytvoření záložních kopií, které jsou konzistentní s aplikací skupiny svazku. Konzistence aplikací existuje, když všechny související soubory a databáze jsou synchronizovány a reprezentovat true stav aplikace v určitém bodě v čase. Svazek skupiny (která se také označují jako *konzistence skupin*) představují základ pro zálohování nebo obnovení úlohy.

Svazek skupiny nejsou stejná jako kontejnery svazků. Kontejner svazků obsahuje jeden nebo více svazků, které sdílejí účet cloudového úložiště a další atributy, jako je například šifrování a šířky pásma. Kontejner jednom svazku může obsahovat až 256 dynamicky zřizované svazky zařízení StorSimple. Další informace o kontejnery svazků, přejděte na [spravovat vaše kontejnery svazků](storsimple-manage-volume-containers.md). Svazek skupiny jsou kolekce položek svazky, které nakonfigurujete pro usnadnění operace zálohování. Pokud vyberete dva svazky, které patří na jiný svazek kontejnery, umístěte je v jednom svazku skupiny a pak vytvořit zásady zálohování pro tuto skupinu svazku, každý svazek zálohovat v kontejneru vhodný svazek, pomocí účtu odpovídající úložiště.

> [!NOTE]
> Všechny svazky ve skupině svazku musí pocházet ze zprostředkovatele služeb jeden cloud.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integrace s Windows služby Stínová kopie svazku
StorSimple Snapshot Manager používá k zaznamenání dat o konzistentní s aplikací Windows svazku stínové kopie Service (VSS). Služby Stínová kopie svazku zajišťuje konzistence aplikací navázat komunikaci s aplikací používající stínovou kopii svazku pro koordinaci vytváření přírůstkové snímků. Služby Stínová kopie svazku zajišťuje, aby aplikace byly dočasně neaktivní nebo tichém, při pořizování snímků. 

StorSimple Snapshot Manager implementace služby Stínová kopie svazku funguje pro SQL Server a obecné svazky systému souborů NTFS. Proces je následující: 

1. Žadatel, který je obvykle Správa dat a řešení ochrany (například StorSimple Snapshot Manager) nebo aplikace pro zálohování, vyvolá služby Stínová kopie svazku a požádá ho získat informace o softwaru zapisovače v cílové aplikaci.
2. VSS kontaktuje komponentu zapisovače načíst popis data. Zapisovač vrátí popis data, která mají být zálohovány. 
3. Signály služby Stínová kopie svazku zapisovač, který má příprava aplikací pro zálohování. Zapisovač připraví data pro zálohování provedením otevřených transakcí na aktualizace protokoly transakcí a tak dále a poté oznámí VSS
4. VSS dá pokyn zapisovač, který má dočasně zastavte úložišť dat aplikace a ujistěte se, že žádná data se zapisují do svazku při vytvoření stínové kopie. Tento krok zajišťuje konzistenci dat a trvá víc než 60 sekund.
5. VSS dá pokyn poskytovatele za účelem vytvoření stínové kopie. Zprostředkovatele, které může být softwaru nebo hardwaru – na základě, spravovat svazky, které jsou aktuálně spuštěné a vytvoření stínové kopie je na vyžádání. Zprostředkovatel vytvoří stínovou kopii a upozorní služby Stínová kopie svazku, až se dokončí.
6. VSS kontaktuje zapisovač upozornit aplikaci, která můžete obnovit vstupně-výstupních operací a také potvrďte, že vstupně-výstupní operace byla pozastavena úspěšně během vytváření stínových kopií. 
7. Pokud bude pro kopii byl úspěšný, vrátí služby Stínová kopie svazku žadatel o kopírování umístění. 
8. Data byla zapsána při vytvoření stínové kopie, zálohování bude být nekonzistentní. Služby Stínová kopie svazku odstraní stínové kopie a upozorní žadatel. Žadatel můžete automaticky opakovat proces zálohování nebo upozornění správcům opakujte později.

Viz následující obrázek.

![Proces služby Stínová kopie svazku](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Proces systému Windows služby Stínová kopie svazku** 

## <a name="backup-types-and-backup-policies"></a>Typy zálohování a zásady zálohování
S StorSimple Snapshot Manager můžete zálohovat data a uložte ho místně a v cloudu. StorSimple Snapshot Manager můžete použít k zálohování dat okamžitě, nebo můžete použít zásady zálohování k vytvoření plánu automaticky umožňoval vytvářet zálohy pro. Zásady zálohování taky umožňují určit, kolik snímky budou zachována. 

### <a name="backup-types"></a>Typy zálohování
StorSimple Snapshot Manager můžete použít k vytvoření následujících typů zálohy:

* **Místní snímky** – místní snímky jsou v daném okamžiku kopie svazku data, která jsou uložená v zařízení StorSimple. Tento typ zálohy obvykle, můžete vytvořit a rychle obnovit. Stejně jako místní záložní kopii můžete použít místní snímek.
* **Cloudových snímků** – cloudových snímků jsou v daném okamžiku kopie svazku data, která jsou uložená v cloudu. Cloudový snímek je stejná jako snímek replikují na jiný, odlehlého úložiště systému. Cloudové snímky jsou obzvláště užitečná ve scénářích zotavení po havárii.

### <a name="on-demand-and-scheduled-backups"></a>Zálohování na vyžádání a naplánované
S StorSimple Snapshot Manager můžete zahájit jednorázové zálohu, kterou chcete vytvořit okamžitě, nebo zásady zálohování můžete naplánovat opakování operace zálohování.

Zásady zálohování je sada automatizovaných pravidel, které můžete použít při plánování pravidelných záloh. Zásady zálohování můžete zadat frekvenci a parametry pořizování snímků skupinu konkrétní svazku. Zásady můžete použít k určení počátku a ukončení platnosti data, časy, četnosti a požadavky na uchování, jak při místním a cloudových snímků. Zásada je použita ihned po jeho definujete. 

StorSimple Snapshot Manager můžete nakonfigurovat nebo znovu nastavit zásady zálohování, kdykoli je to nezbytné. 

Můžete nakonfigurovat následující informace pro každé zásadě zálohování, kterou vytvoříte:

* **Název** – jedinečný název vybrané zásady zálohování.
* **Typ** – typ zásady zálohování; místní snímek nebo cloudový snímek.
* **Svazek skupiny** – svazek skupiny, ke kterému je přiřazena vybrané zásady zálohování.
* **Uchování** – počet záložních kopií, které chcete zachovat. Pokud zaškrtnete **všechny** pole všechny záložní kopie uchovávají až do dosažení maximální počet záložních kopií každého svazku, v tomto okamžiku zásady se nezdaří a generovat chybovou zprávu. Alternativně můžete zadat počet záloh zachování (mezi 1 a 64).
* **Datum** – datum vytvoření zásady zálohování.

Informace o konfiguraci zásady zálohování, přejděte na [použití StorSimple Snapshot Manager vytvořit a spravovat zásady zálohování](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Úloha zálohování monitorování a Správa
StorSimple Snapshot Manager můžete použít ke sledování a správě nadcházející, plánované a dokončené úlohy zálohování. Kromě toho StorSimple Snapshot Manager nabízí katalog až 64 dokončené záloh. Katalog můžete použít k vyhledání a obnovit svazky nebo jednotlivé soubory. 

Informace o monitorování úlohy zálohování, přejděte na [použití StorSimple Snapshot Manager můžete zobrazit a spravovat úlohy zálohování](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Další kroky
* Další informace o [použití ke správě vašeho řešení StorSimple Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Stáhněte si [Snapshot Manager zařízení StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

