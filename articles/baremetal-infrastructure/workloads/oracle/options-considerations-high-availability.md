---
title: Možnosti nebo servery infrastruktury Oracle BareMetal
description: Seznamte se s možnostmi a důležitými informacemi pro servery infrastruktury Oracle BareMetal.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: e8a2dece11884e3a659f14b30bce51e7f0244924
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590234"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Možnosti pro servery infrastruktury Oracle BareMetal

V tomto článku budeme zvážit možnosti a doporučení, které vám pomohou získat nejvyšší úroveň ochrany a výkonu, na kterých běží Oracle na serverech infrastruktury BareMetal. 

## <a name="data-guard-protection-modes"></a>Režimy ochrany dat Guard

Ochrana dat nabízí ochranu nedostupnou výhradně prostřednictvím clusteru Oracle Real Applications (RAC), logické replikace (například GoldenGate) a replikace na základě úložiště. 

| Režim ochrany | Description |
| --- | --- |
| **Maximální výkon** | Výchozí režim ochrany. Poskytuje nejvyšší úroveň ochrany, aniž by to ovlivnilo výkon primární databáze. Data se považují za potvrzená, jakmile se zapíše do primární databáze znovu streamu. Pak je replikována do pohotovostní databáze asynchronně. Obvykle se databáze v pohotovostním režimu přijímá během několika sekund, ale není jí zaručena žádná záruka. Tento režim obvykle splňuje obchodní požadavky (společně s monitorováním prodlev), aniž by bylo potřeba síťové připojení s nízkou latencí mezi primárními a pohotovostními lokalitami.<br /><br />Poskytuje nejlepší provozní trvalost; nezaručuje však nulovou ztrátu dat.   |
| **Maximální dostupnost** | Poskytuje nejvyšší úroveň ochrany, aniž by to ovlivnilo dostupnost primární databáze. Data se nikdy nepovažují za potvrzená do primární databáze, dokud se nepotvrdí taky aspoň v jedné pohotovostní databázi. Pokud primární databáze nemůže zapsat změny provedené znovu do alespoň jedné pohotovostní databáze, vrátí se do režimu maximálního výkonu, nikoli jako nedostupné. <br /><br />Umožňuje službě pokračovat, pokud není pohotovostní web k dispozici. Pokud funguje jenom jedna lokalita, zachová se jenom jedna kopie dat, dokud bude druhá lokalita online a synchronizace se znovu nevytvoří. |
| **Maximální ochrana** | Poskytuje podobnou úroveň ochrany pro maximální dostupnost. Primární databáze se ukončí s přidanou funkcí, pokud nemůže zapisovat změny znovu alespoň do jedné pohotovostní databáze. Tím se zajistí, že dojde ke ztrátě dat, ale s náklady na více křehké dostupnosti. |

>[!IMPORTANT]
>Pokud potřebujete cíl bodu obnovení (RPO) s hodnotou nula, doporučujeme maximální dostupnost konfigurace. Operaci RPO lze zaručit i v případě, že dojde k více chybám. Například dokonce v případě výpadku sítě z primární databáze a následnou ztrátou primární databáze pokaždé, když je výpadek sítě stále v platnosti.

### <a name="data-guard-deployment-patterns"></a>Vzory nasazení ochrany dat

Oracle umožňuje nakonfigurovat více cílů pro generování znovu, což umožňuje více databází v pohotovostním režimu. Nejběžnější konfigurace je znázorněna na následujícím obrázku, v jedné pohotovostní databázi v jiné oblasti.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Diagram znázorňující výchozí nasazení ochrany dat od Oracle":::

Ochrana dat je nakonfigurovaná v režimu maximálního výkonu pro výchozí nasazení. Tato konfigurace poskytuje replikaci dat téměř v reálném čase pomocí asynchronního přenosu po opětovném provedení. Pohotovostní databázi není nutné spouštět v rámci nasazení řadiče pro certifikát RAC, ale doporučujeme, aby splňovala požadavky na výkon primární lokality.

Doporučujeme nasazení, jako je znázorněno na následujícím obrázku pro prostředí, která vyžadují striktní dobu provozu nebo RPO bodu obnovení s hodnotou nula. Konfigurace maximální dostupnosti se skládá z místního pohotovostní databáze, která aplikuje znovu v synchronním režimu a druhá pohotovostní databáze běžící ve vzdálené oblasti.

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="Diagram znázorňující nasazení ochrany dat s maximální dostupností":::

Místní pohotovostní databázi můžete vytvořit v případě, že bude výkon aplikace zhoršený spuštěním databáze a aplikačních serverů v samostatných oblastech. V této konfiguraci se místní pohotovostní databáze používá v případě, že je v primárním clusteru potřeba plánovaná nebo neplánovaná údržba. Tyto databáze můžete spustit pomocí synchronní replikace, protože jsou ve stejné oblasti, což zajistí, že mezi nimi nebudou ztracena žádná data.

### <a name="data-guard-configuration-considerations"></a>Požadavky na konfiguraci ochrany dat

Zprostředkovatel ochrany dat by měl být implementován, protože zjednodušuje implementaci konfigurace ochrany dat a zajišťuje, aby byly osvědčené postupy dodržovány. Poskytuje funkce monitorování výkonu a významně zjednodušuje postupy přepnutí, převzetí služeb při selhání a vytváření instancí.

Data Guard umožňuje spustit proces pozorovatele, který monitoruje všechny databáze v konfiguraci ochrany dat a zjišťuje dostupnost databáze. Pokud primární databáze dojde k selhání, může pozorovatel ochrany dat automaticky spustit převzetí služeb při selhání do pohotovostní databáze v konfiguraci. Můžete implementovat pozorovatele ochrany dat s několika pozorovateli na základě počtu fyzických lokalit (až tří). 

Tento pozorovatel by měl být umístěn v infrastruktuře, která bude podporovat aplikační vrstvu. Primární pozorovatel by měl vždy existovat na fyzické lokalitě, kde není umístěna primární databáze. Doporučujeme opatrnost při automatizaci operací s podporou převzetí služeb při selhání, které aktivoval pozorovatel data Guard. Nejdřív se ujistěte, že jsou vaše aplikace navržené a testované, aby poskytovaly přijatelnou službu, když se databáze spustí na samostatném místě.

Pokud je aplikace schopná pracovat jenom místně, převzetí služeb při selhání sekundární lokalitou musí být ruční. Prostředí, která vyžadují úrovně vysoké dostupnosti (99,99% nebo 99,999% doba provozu), by měla používat místní i vzdálený pohotovostní databázi, jak je znázorněno na předchozím obrázku. V těchto případech se parametr FastStartFailoverTarget nastaví jenom na místní pohotovostní databázi.

Pro všechny aplikace, které podporují přístup k aplikacím/databázi mezi weby, je FastStartFailoverTarget nastaveno na všechny pohotovostní databáze v konfiguraci ochrany dat.

### <a name="active-data-guard"></a>Active Data Guard

Oracle Active Data Guard (ADG) je nadmnožinou základních funkcí ochrany dat, které jsou součástí Oracle Database Enterprise Edition. Poskytuje přidané následující funkce, které se použijí v rámci nasazení Oracle Exadata:

- Jedinečné zjištění poškození a Automatická oprava.
- Rychlé převzetí služeb při selhání synchronizované replikou výroby – ruční nebo automatické.
- Přesměrování produkčních úloh do synchronizovaného úsporného režimu otevřete jen pro čtení.
- Postupné upgrady databáze a pohotovostní režim. První Oprava pomocí fyzického úsporného režimu.
- Přesměrování přírůstkových záloh do úsporného režimu.
- Nulová ochrana při obnovení dat v rámci libovolné vzdálenosti bez dopadu na výkon.

Dokument White Paper, který je k dispozici na stránce, [https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf) nabízí dobrý přehled o předchozích funkcích, jak je znázorněno na následujícím obrázku.

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Diagram znázorňující přehled aktivních funkcí ochrany dat společnosti Oracle":::

## <a name="backup-recommendations"></a>Doporučení pro zálohování

Nezapomeňte zálohovat databáze. Pomocí funkcí obnovení a obnovení obnovte databázi do stejného nebo jiného systému nebo obnovte soubory databáze.

Je důležité vytvořit strategii obnovení zálohování, která chrání databáze Oracle Databasech zařízení před ztrátou dat. Tato ztráta by mohla vést k fyzickému problému s diskem, který způsobuje selhání čtení nebo zápisu do souboru na disku, který je nutný ke spuštění databáze. Chyba uživatele může také způsobit ztrátu dat. Funkce zálohování poskytuje možnost **obnovení v čase (PITR) obnovení databáze, obnovení systému SCN (System Change Number) a nejnovějšího obnovení**. Zásadu zálohování můžete vytvořit v uživatelském rozhraní v prohlížeči nebo v rozhraní příkazového řádku.

K dispozici jsou následující možnosti zálohování:

- Zálohujte na svazek úložiště NFS (rychlá oblast obnovení –/U98).
- Použití Azure NetApp Files SnapCenter – Snapshot.

Postup, který je potřeba vzít v úvahu:

- Ruční nebo automatické zálohování.
- Automatické zálohování se zapisuje do svazků úložiště systému souborů NFS (například/U98).
- Zálohy běží mezi 12:00. až 6:00 v časovém pásmu databázového systému.
- Současná doba uchování: 7, 15, 30, 45 a 60 dnů.

- Obnovit databázi ze zálohy uložené v úložišti objektů:
  - Do posledního známého funkčního stavu s nejmenší možnou ztrátou dat.
  - Používá se zadané časové razítko.
  - Pomocí zadaného SCN
  - BackupReport – _používá SCN ze sestavy zálohování místo zadaného SCN_.

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="Diagram znázorňující databázi zákazníka, která se zálohuje do/U98 a není typu/u95 ().":::

### <a name="backup-policy"></a>Zásady zálohování

Zásady zálohování definují informace o zálohování. Když vytváříte zásady zálohování, definujete cíl pro zálohování databáze (umístění systému souborů NFS) a definujete okno obnovení.

Ve výchozím nastavení se používá základní kompresní algoritmus. Při použití SLABÝch, STŘEDNÍch nebo vysoce kompresních algoritmů pro zásady zálohování na disk nebo NFS jsou k dispozici licenční opatření.

### <a name="backup-levels"></a>Úrovně zálohování

Při zálohování zadejte úroveň zálohy.

- Úroveň 0 – úplná
- Úroveň 1 – přírůstkové
- Dlouhodobý/Archievelog – s výjimkou zásad uchovávání záloh použijte umístění, které není v pořádku (například/u95).

## <a name="next-steps"></a>Další kroky

Zjistěte, jak obnovit databázi Oracle, když dojde k selhání:

> [!div class="nextstepaction"]
> [Obnovení databáze Oracle v infrastruktuře Azure BareMetal](oracle-high-availability-recovery.md)
