---
title: Průvodce výkonem a škálovatelností aktivity kopírování v Azure Data Factory | Microsoft Docs
description: Přečtěte si o klíčových faktorech, které ovlivňují výkon přesunu dat v Azure Data Factory při použití aktivity kopírování.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: jingwang
ms.openlocfilehash: 05ecfdc4f082aaa44fe54e6b807a1c5faf84eb8d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996448"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Průvodce škálovatelností a výkonem aktivity kopírování
> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuální verze](copy-activity-performance.md)

Bez ohledu na to, jestli chcete provést rozsáhlou migraci dat ze služby Data Lake nebo Enterprise Data Warehouse (podnikového) do Azure nebo chcete ingestovat data ve velkém měřítku z různých zdrojů do Azure pro analýzu velkých objemů dat, je důležité dosáhnout optimálního výkonu a škálovatelnost.  Azure Data Factory poskytuje výkonné, odolné a nákladově efektivní mechanismy pro ingestování dat ve velkém měřítku, díky čemuž je vhodné, aby technici pro data dokázali vytvářet vysoce výkonné a škálovatelné kanály příjmu dat.

Po přečtení tohoto článku, budou moci odpovědět na následující otázky:

- Jakou úroveň výkonu a škálovatelnosti je možné dosáhnout použitím aktivity kopírování ADF pro scénáře migrace dat a přijímání dat?

- Jak mám provést optimalizaci výkonu aktivity kopírování ADF?
- Jaké knoflíky optimalizace výkonu ADF mohu využít k optimalizaci výkonu pro jednu spuštění aktivity kopírování?
- Jaké další faktory mimo ADF jsou při optimalizaci výkonu kopírování vhodné?

> [!NOTE]
> Pokud nejste obeznámeni s aktivitou kopírování obecně, přečtěte si článek [Přehled aktivity kopírování](copy-activity-overview.md) .

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopírování výkonu a škálovatelnosti dosažitelné pomocí ADF

ADF nabízí architekturu bez serveru, která umožňuje paralelismus na různých úrovních, což vývojářům umožňuje vytvářet kanály pro plné využití šířky pásma sítě a vstupně-výstupních operací úložiště a šířky pásma pro maximalizaci propustnosti přesunu dat pro vaše prostředí.  To znamená, že propustnost, kterou můžete dosáhnout, lze odhadnout měřením minimální propustnosti nabízených zdrojovým úložištěm dat, cílovým úložištěm dat a šířky pásma sítě mezi zdrojovým a cílovým serverem.  V následující tabulce je vypočítána doba kopírování na základě velikosti dat a limitu šířky pásma pro vaše prostředí. 

| Velikost dat/ <br/> šířka pásma | 50 Mb/s    | 100 Mb/s  | 500 Mb/s  | 1 Gb/s   | 5 Gb/s   | 10 Gb/s  | 50 GB/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min.    | 1,4 min.   | 0,3 min.   | 0,1 min.  | 0,03 min. | 0,01 min. | 0,0 min.   |
| **10 GB**                   | 27,3 min.   | 13,7 min.  | 2,7 min.   | 1,3 min.  | 0,3 min.  | 0,1 min.  | 0,03 min.  |
| **100 GB**                  | 4,6 hod.    | 2,3 hod.   | 0,5 hod.   | 0,2 hod.  | 0,05 hod. | 0,02 hod. | 0,0 hod.   |
| **1 TB**                    | 46,6 hod.   | 23,3 hod.  | 4,7 hod.   | 2,3 hod.  | 0,5 hod.  | 0,2 hod.  | 0,05 hod.  |
| **10 TB**                   | 19,4 dní  | 9,7 dní  | 1,9 dní  | 0,9 dní | 0,2 dní | 0,1 dní | 0,02 dní |
| **100 TB**                  | 194,2 dní | 97,1 dní | 19,4 dní | 9,7 dní | 1,9 dní | 1 dny   | 0,2 dní  |
| **1 PB**                    | 64,7. mo    | 32,4. mo   | 6,5. mo    | 3,2. mo   | 0,6. mo   | 0,3. mo   | 0,06. mo   |
| **10 PB**                   | 647,3. mo   | 323,6. mo  | 64,7. mo   | 31,6. mo  | 6,5. mo   | 3,2. mo   | 0,6. mo    |

Kopie ADF je škálovatelná na různých úrovních:

![Jak kopíruje ADF škálování](media/copy-activity-performance/adf-copy-scalability.png)

- Tok řízení ADF může spustit více aktivit kopírování paralelně, například pomocí [pro každou smyčku](control-flow-for-each-activity.md).
- Jediná aktivita kopírování může využít výhod škálovatelných výpočetních prostředků: při použití Azure Integration Runtime můžete pro každou aktivitu kopírování v rámci serveru zadat [až 256 DIUs](#data-integration-units) . Při použití Integration Runtime v místním prostředí můžete ručně navýšení kapacity počítače nebo horizontální navýšení kapacity na více počítačů ([až 4 uzly](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) a jedna aktivita kopírování bude rozdělit svou sadu souborů napříč všemi uzly.
- Jedna aktivita kopírování čte z a zapisuje do úložiště dat pomocí paralelního více [](#parallel-copy)vláken.

## <a name="performance-tuning-steps"></a>Postup optimalizace výkonu

Provedením těchto kroků vyoptimalizujete výkon služby Azure Data Factory s aktivitou kopírování.

1. **Vyberte testovací datovou sadu a vytvořte směrný plán.** Během fáze vývoje otestujte svůj kanál pomocí aktivity kopírování v reprezentativní ukázce dat. Datová sada, kterou zvolíte, by měla představovat vaše typické vzorce dat (struktura složek, vzorek souboru, schéma dat atd.) a je dostatečně velká pro vyhodnocení výkonu kopírování, například trvá 10 minut nebo i déle, než se aktivita kopírování dokončí. Shromažďování podrobností o spuštění a charakteristik výkonu po [monitorování aktivity kopírování](copy-activity-overview.md#monitoring)

2. **Jak maximalizovat výkon jedné aktivity kopírování**:

   Aby bylo možné začít používat, doporučujeme nejprve maximalizovat výkon pomocí jedné aktivity kopírování.

   **Pokud je aktivita kopírování prováděna na Azure Integration Runtime:**

   Začněte s výchozími hodnotami pro [jednotky integrace dat (diú)](#data-integration-units) a nastavení [paralelního kopírování](#parallel-copy) .  Proveďte testovací běh a poznamenejte si dosažený výkon a také skutečné hodnoty používané pro DIUs a paralelní kopie.  Informace o tom, jak shromažďovat výsledky spuštění a nastavení výkonu, najdete v tématu [monitorování aktivit kopírování](copy-activity-overview.md#monitoring) .

   Nyní proveďte další spuštění testů výkonu, pokaždé, když se zdvojnásobí hodnota nastavení DIÚ.  Případně, pokud si myslíte, že výkon dosažený pomocí výchozího nastavení je daleko pod očekáváním, můžete nastavení DIÚ v následném testovacím běhu zvýšit.

   Aktivita kopírování by se měla při zvětšování nastavení DIÚ skoro dokonale lineárně škálovat.  Pokud při zdvojnásobení nastavení DIÚ nevidíte propustnost, může docházet k dvěma akcím:

   - Konkrétní vzor kopírování, který používáte, nepřináší přidávání dalších DIUs.  I když jste zadali větší hodnotu DIÚ, skutečná DIÚ použitá zůstala stejná a proto získáte stejnou propustnost jako předtím.  Pokud se jedná o tento případ, maximalizujte agregovanou propustnost spuštěním několika kopií současně odkazujících na krok 3.
   - Přidáním dalších DIUs (zvýšení výkonu) a tím se zvýší rychlost extrakce, přenosu a načítání dat, buď zdrojové úložiště dat, síť v nástroji, nebo cílové úložiště dat dosáhlo svého kritického bodu a případně omezení.  Pokud se jedná o tento případ, zkuste kontaktovat správce úložiště dat nebo správce vaší sítě, aby se zvýšil nejvyšší limit, nebo můžete také omezit nastavení DIÚ, dokud nedojde k překročení omezení.

   **Pokud je aktivita kopírování prováděna v Integration Runtime v místním prostředí:**

   Pro hostování prostředí Integration runtime doporučujeme použít vyhrazený počítač oddělený od serveru, který je hostitelem úložiště dat.

   Začněte s výchozími hodnotami pro nastavení [paralelního kopírování](#parallel-copy) a použijte jeden uzel pro místní prostředí IR.  Proveďte test výkonnosti a poznamenejte si dosažený výkon.

   Pokud chcete dosáhnout vyšší propustnosti, můžete buď horizontální navýšení nebo horizontální navýšení kapacity v místním prostředí IR:

   - Pokud procesor a dostupná paměť v uzlu IR v místním prostředí nejsou plně využívány, ale spuštění souběžných úloh dosáhlo limitu, měli byste škálovat kapacitu tak, že zvýšíte počet souběžných úloh, které lze spustit na uzlu.  Pokyny najdete [tady](create-self-hosted-integration-runtime.md#scale-up) .
   - Pokud je na druhé straně procesor vysoký v místním prostředí IR nebo je dostupná paměť nízká, můžete přidat nový uzel, který vám umožní lépe škálovat zatížení napříč více uzly.  Pokyny najdete [tady](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

   Při horizontálním navýšení kapacity nebo horizontálním navýšení kapacity prostředí IR v místním prostředí můžete spustit test testu, abyste zjistili, jestli se vám stále zobrazuje větší propustnost.  Pokud propustnost přestane zlepšit, asi nejspíš buď zdrojové úložiště dat, síť v síti, nebo cílové úložiště dat dosáhlo svého kritického bodu a zahajuje se omezení. Pokud se jedná o tento případ, zkuste kontaktovat správce úložiště dat nebo správce vaší sítě, aby se zvýšil nejvyšší limit, nebo můžete přejít zpátky na předchozí nastavení škálování pro prostředí IR s vlastním hostováním. 

3. **Jak maximalizovat agregovanou propustnost spuštěním více kopií současně:**

   Teď, když jste dosáhli maximálního výkonu jedné aktivity kopírování, pokud jste ještě nedosáhli horní meze propustnosti vašeho prostředí – síť, zdrojové úložiště dat a cílové úložiště dat – můžete spouštět více aktivit kopírování paralelně pomocí ADF. konstrukce toků řízení, například [pro každou smyčku](control-flow-for-each-activity.md).

4. **Tipy pro ladění výkonu a funkce optimalizace.** V některých případech se při spuštění aktivity kopírování v Azure Data Factory zobrazí zpráva "Tipy pro ladění výkonu" na stránce [monitorování aktivity kopírování](copy-activity-overview.md#monitor-visually), jak je znázorněno v následujícím příkladu. Zpráva vás upozorní na kritické body, které se identifikovaly pro daný běh kopírování. Také vám pomůže s tím, co se dá změnit, aby se zvýšila propustnost kopírování. Tipy pro ladění výkonu aktuálně poskytují návrhy jako:

   - Při kopírování dat do Azure SQL Data Warehouse použít základ
   - Zvyšte Azure Cosmos DB jednotky žádostí nebo Azure SQL Database DTU (jednotky propustnosti databáze), když je prostředek na straně úložiště dat kritický.
   - Odeberte nepotřebnou fázi kopírování.

   Pravidla optimalizace výkonu se postupně rozšiřují.

   **Příklad: Kopírování do Azure SQL Database s využitím tipů pro ladění výkonu**

   V této ukázce se při spuštění kopírování Azure Data Factory oznámení, že Azure SQL Database jímka dosáhne vysokého využití DTU, což zpomaluje operace zápisu. Návrhem je zvýšení Azure SQL Database úrovně o více DTU. 

   ![Sledování kopírování pomocí tipů pro ladění výkonu](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   Kromě toho jsou zde některé funkce optimalizace výkonu, o kterých byste měli vědět:

   - [Paralelní kopírování](#parallel-copy)
   - [Jednotky integrace dat](#data-integration-units)
   - [Kopírování dvoufázové instalace](#staged-copy)
   - [Škálovatelnost prostředí Integration runtime v místním prostředí](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **Rozšiřte konfiguraci na celou datovou sadu.** Až budete spokojeni s výsledky a výkonem spuštění, můžete rozšířit definici a kanál tak, aby pokryly celou datovou sadu.

## <a name="copy-performance-optimization-features"></a>Kopírovat funkce optimalizace výkonu

Azure Data Factory poskytuje následující funkce optimalizace výkonu:

- [Paralelní kopírování](#parallel-copy)
- [Jednotky integrace dat](#data-integration-units)
- [Kopírování dvoufázové instalace](#staged-copy)

### <a name="data-integration-units"></a>Jednotky pro integraci dat

Jednotka Integration data je míra, která představuje napájení (kombinace procesoru, paměti a přidělení síťových prostředků) jedné jednotky v Azure Data Factory. Jednotka pro integraci dat platí jenom pro [prostředí Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime), ale ne pro místní [prostředí Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

Bude se vám účtovat počet **využitých jednotek \* doby trvání \* kopírování DIUs a cena za diú za hodinu**. [Tady se můžete](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)podívat na aktuální ceny. Pro každý typ předplatného se můžou použít místní měna a samostatná sleva.

Povolený DIUs k tomu, aby mohl provádět kopírování aktivit, je **mezi 2 a 256**. Pokud není zadaný nebo jste v uživatelském rozhraní zvolili "automaticky", Data Factory dynamicky použít optimální nastavení DIÚ na základě páru zdroj-jímka a datového vzoru. V následující tabulce jsou uvedeny výchozí DIUs používané v různých scénářích kopírování:

| Kopírování | Výchozí DIUs určené služby |
|:--- |:--- |
| Kopírovat data mezi úložišti souborů | Mezi 4 a 32 v závislosti na počtu a velikosti souborů |
| Kopírovat data do Azure SQL Database nebo Azure Cosmos DB |Mezi 4 a 16 v závislosti na úrovni jímky Azure SQL Database nebo Cosmos DB (počet DTU/ru) |
| Všechny ostatní scénáře kopírování | 4 |

Chcete-li přepsat toto výchozí nastavení, zadejte hodnotu **dataIntegrationUnits** vlastnost následujícím způsobem. *Skutečný počet DIUs* , že operace kopírování používá za běhu je rovna nebo menší než nakonfigurovaná hodnota, v závislosti na vašich dat vzor.

Při monitorování spuštění aktivit můžete zobrazit DIUs, která se používá pro každé spuštění kopírování v výstupu aktivity kopírování. Další informace najdete v tématu [monitorování aktivit kopírování](copy-activity-overview.md#monitoring).

> [!NOTE]
> Nastavení DIUs větší než 4 se v současné době týká jenom při kopírování více souborů z Azure Storage, Azure Data Lake Storage, Amazon S3, Google Cloud Storage, cloudového FTP FTP nebo cloudu SFTP do dalších cloudových úložišť dat.

**Příklad:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="parallel-copy"></a>Paralelní kopírování

Vlastnost **parallelCopies** můžete použít k označení paralelismu, které má aktivita kopírování použít. Tuto vlastnost si můžete představit jako maximální počet vláken v rámci aktivity kopírování, kterou si můžete přečíst ze zdroje nebo zapisovat do úložiště dat jímky paralelně.

Pro každou spuštění aktivity kopírování Azure Data Factory určuje počet paralelních kopií, které se mají použít ke kopírování dat ze zdrojového úložiště dat a do cílového úložiště dat. Výchozí počet paralelních kopií, které používá, závisí na typu zdroje a jímky, které používáte.

| Kopírování | Výchozí počet paralelních kopií určené služby |
| --- | --- |
| Kopírovat data mezi úložišti souborů |Závisí na velikosti souborů a na počtu DIUs používaných ke kopírování dat mezi dvěma úložišti dat cloudu nebo na fyzické konfiguraci počítače prostředí Integration runtime v místním prostředí. |
| Kopírování dat z libovolného zdrojového úložiště do Azure Table Storage |4 |
| Dalších scénářů kopírování |1 |

> [!TIP]
> Když kopírujete data mezi úložišti na základě souborů, výchozí chování obvykle poskytuje nejlepší propustnost. Výchozí chování je automaticky určováno na základě vzoru zdrojového souboru.

Pro řízení zatížení počítačů, které hostují vaše úložiště dat nebo pro optimalizaci výkonu kopírování, můžete přepsat výchozí hodnotu a zadat hodnotu pro vlastnost **parallelCopies** . Hodnota musí být celé číslo větší než nebo rovno 1. V době běhu používá aktivita kopírování hodnotu, která je menší nebo rovna hodnotě, kterou jste nastavili.

**Ukazuje na poznámku:**

- Při kopírování dat mezi úložišti založenými na souborech Určuje **parallelCopies** paralelismus na úrovni souboru. Blokování v rámci jednoho souboru probíhá automaticky a transparentně. Je navržená tak, aby používala nejvhodnější velikost bloku dat pro daný typ zdrojového úložiště dat, aby se data načetla paralelně a kolmo k **parallelCopies**. Skutečný počet paralelních kopie služba pro přesun dat se používá pro operaci kopírování v době běhu je delší než počet souborů, které máte. Pokud je chování kopírování **mergeFile**, aktivita kopírování nemůže využít paralelismus na úrovni souborů.
- Při kopírování dat z úložišť, která nejsou založená na souborech (s výjimkou [Oracle](connector-oracle.md#oracle-as-source), [Teradata](connector-teradata.md#teradata-as-source), [tabulka SAP](connector-sap-table.md#sap-table-as-source)a konektoru [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) jako zdroje s povoleným vytvářením oddílů dat), do úložišť, která jsou založená na souborech, služba pro přesun dat ignoruje vlastnost **parallelCopies** . I v případě, že je zadán paralelismu, není použita v tomto případě.
- Vlastnost **parallelCopies** je kolmá na **dataIntegrationUnits**. Předchozí se počítá přes všechny jednotky integrace Data.
- Když zadáte hodnotu vlastnosti **parallelCopies** , zvažte zvýšení zátěže ve zdrojovém a úložišti dat jímky. Zvažte také zvýšení zatížení v místním prostředí Integration runtime, pokud je aktivita kopírování oprávněná, například pro hybridní kopírování. Toto zvýšení zatížení nastane hlavně v případě, že máte více aktivit nebo souběžných spuštění stejných aktivit, které se spouštějí ve stejném úložišti dat. Pokud si všimnete, že úložiště dat nebo místní prostředí Integration runtime je zahlcené zatížením, snižte hodnotu **parallelCopies** k uvolnění zátěže.

**Příklad:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

### <a name="staged-copy"></a>Kopírování dvoufázové instalace

Při kopírování dat ze zdrojového úložiště dat do úložiště dat jímky, můžete zvolit použití Blob storage jako dočasné pracovní úložiště. Pracovní je zvláště užitečná v následujících případech:

- **Chcete ingestovat data z různých úložišť dat do SQL Data Warehouse prostřednictvím základny.** SQL Data Warehouse používá k načtení velkých objemů dat do SQL Data Warehouse PolyBase jako vhodný mechanismus vysokou propustnost. Zdrojová data musí být ve službě BLOB Storage nebo Azure Data Lake Store a musí splňovat další kritéria. Při načítání dat z úložiště dat než Blob storage nebo Azure Data Lake Store můžete aktivovat kopírování prostřednictvím Blob storage dočasné pracovní data. V takovém případě Azure Data Factory provádí požadované transformace dat, aby se zajistilo, že splňuje požadavky základny. Potom použije PolyBase k načtení dat do SQL Data Warehouse efektivně. Další informace najdete v tématu [použití PolyBase k načítání dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **V některých případech trvá i v průběhu provádění hybridního přesunu dat (tedy kopírování z místního úložiště dat do cloudového úložiště dat) prostřednictvím pomalého síťového připojení.** Za účelem zvýšení výkonu můžete pomocí připravené kopie komprimovat data v místním prostředí, aby při přesunu dat do pracovního úložiště dat v cloudu trvalo méně času. Pak můžete data v pracovním úložišti dekomprimovat ještě předtím, než se načtou do cílového úložiště dat.
- **Nechcete v bráně firewall otevírat jiné porty než port 80 a port 443 kvůli podnikovým zásadám IT.** Například při kopírování dat z do místního úložiště dat jímky Azure SQL Database nebo Azure SQL Data Warehouse jímky, budete muset aktivovat odchozí komunikaci TCP na portu 1433 pro bránu Windows firewall a váš podnikový firewall. V tomto scénáři může připravené kopírování využít výhod místního prostředí Integration runtime k prvnímu kopírování dat do pracovní instance úložiště objektů BLOB přes protokol HTTP nebo HTTPS na portu 443. Pak může data načíst do SQL Database nebo SQL Data Warehouse z přípravy úložiště objektů BLOB. V tomto toku není nutné povolit port 1433.

#### <a name="how-staged-copy-works"></a>Kopírování funguje jak dvoufázové instalace

Při aktivaci pracovní funkce data se zkopíruje ze zdrojového úložiště dat do přípravného úložiště objektů Blob (funkce přineste si vlastní). V dalším kroku data zkopírována z pracovní úložiště dat do úložiště dat jímky. Azure Data Factory pro vás automaticky spravuje tok se dvěma fázemi. Po dokončení přesunu dat Azure Data Factory taky vyčistit dočasná data z pracovního úložiště.

![Kopírování dvoufázové instalace](media/copy-activity-performance/staged-copy.png)

Když provedete přesun dat pomocí pracovního úložiště, můžete určit, jestli chcete data před přesunem dat ze zdrojového úložiště dat do dočasného nebo přípravného úložiště dat a pak je dekomprimovat, před přesunem dat z dočasného nebo přípravného dat. úložiště dat jímky.

V současné době nemůžete kopírovat data mezi dvěma datovými úložišti, která jsou propojena prostřednictvím jiného samoobslužného úřadu pro hostování, ani bez dvoufázové kopie. V takovém případě můžete nakonfigurovat dvě explicitně zřetězené aktivity kopírování ke kopírování ze zdroje do přípravy, a to z přípravy do jímky.

#### <a name="configuration"></a>Konfiguraci

Nakonfigurujte nastavení **enableStaging** v aktivitě kopírování, abyste určili, jestli chcete data připravit v úložišti objektů blob, než je načtete do cílového úložiště dat. Při nastavování **enableStaging** na `TRUE`zadejte další vlastnosti uvedené v následující tabulce. Je také potřeba vytvořit sdílenou službu Azure Storage nebo sdílený přístupový podpis s úložištěm pro přípravu, pokud ji ještě nemáte.

| Vlastnost | Popis | Výchozí hodnota | Požadováno |
| --- | --- | --- | --- |
| enableStaging |Určete, jestli chcete kopírovat data prostřednictvím jako dočasné pracovní úložiště. |False |Ne |
| linkedServiceName |Zadejte název [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) propojenou službu, která odkazuje na instanci úložiště, které můžete použít jako dočasné pracovní úložiště. <br/><br/> Úložiště se sdíleným přístupovým podpisem nelze použít k načtení dat do SQL Data Warehouse prostřednictvím základny. Můžete ji použít v jiných scénářích. |neuvedeno |Ano, pokud **enableStaging** nastavena na hodnotu TRUE |
| path |Zadejte cestu úložiště objektů Blob, který chcete s daty, dvoufázové instalace. Pokud cestu nezadáte, služba vytvoří kontejner, do kterého budou ukládat dočasná data. <br/><br/> Zadejte cestu, pouze v případě, že používáte úložiště pomocí sdíleného přístupového podpisu nebo vyžadujete dočasných dat v konkrétním umístění. |neuvedeno |Ne |
| Hodnotou EnableCompression |Určuje, zda mají být data před kopírováním do cíle komprimována. Toto nastavení omezuje objem dat přenášených. |False |Ne |

>[!NOTE]
> Pokud použijete připravené kopírování s povolenou kompresí, instanční objekt nebo ověřování MSI pro propojenou službu pracovního objektu BLOB se nepodporuje.

Tady je ukázková definice aktivity kopírování s vlastnostmi popsanými v předchozí tabulce:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

#### <a name="staged-copy-billing-impact"></a>Fázovaného kopírování fakturace dopad

Účtují se vám poplatky podle dvou kroků: doba kopírování a typ kopírování.

* Při použití přípravy během kopírování do cloudu, který kopíruje data z cloudového úložiště dat do jiného cloudového úložiště dat, se v obou fázích, které zmocňuje prostředí Azure Integration runtime, účtují [součet doby kopírování pro krok 1 a krok 2] x [cena za jednotku cloudového kopírování].
* Při použití přípravy během hybridní kopie, která kopíruje data z místního úložiště dat do cloudového úložiště dat, je jedna fáze, kterou má samoobslužný modul runtime integrace v místním prostředí, účtována za [doba trvání hybridního kopírování] × [cena za jednotku Hybrid Copy] + [doba kopírování v cloudu] x [Jednotková cena za kopii v cloudu].

## <a name="references"></a>Odkazy

Tady jsou odkazy na sledování výkonu a ladění pro některá z podporovaných úložišť dat:

* Azure Storage, což zahrnuje úložiště objektů BLOB a úložiště tabulek: [Azure Storage cíle škálovatelnosti](../storage/common/storage-scalability-targets.md) a [Kontrolní seznam pro výkon a škálovatelnost Azure Storage](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: Můžete [monitorovat výkon](../sql-database/sql-database-single-database-monitor.md) a kontrolovat procento transakční jednotky (DTU).
* Azure SQL Data Warehouse: Jeho schopnost se měří v jednotkách datového skladu (DWU). Viz [Správa výpočetního výkonu v Azure SQL Data Warehouse (přehled)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Úrovně výkonu v Azure Cosmos DB](../cosmos-db/performance-levels.md).
* Místní SQL Server: [Monitorujte a Optimalizujte výkon](https://msdn.microsoft.com/library/ms189081.aspx).
* Místní souborový server: [Ladění výkonu pro souborové servery](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Použití Azure Data Factory k migraci dat ze služby Data Lake nebo datového skladu do Azure](data-migration-guidance-overview.md)
- [Migrace dat ze služby Amazon S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md)
