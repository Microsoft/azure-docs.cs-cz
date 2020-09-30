---
title: Online zálohování a obnovení dat na vyžádání v Azure Cosmos DB
description: Tento článek popisuje, jak funguje automatické zálohování a obnovení dat na vyžádání, jak nakonfigurovat interval zálohování a uchování v Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 310fee91ed98409e5a724d1be8de7bc9ccb5601b
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570926"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online zálohování a obnovení dat na vyžádání v Azure Cosmos DB

Azure Cosmos DB automaticky provede zálohování vašich dat v pravidelných intervalech. Automatické zálohování se provádí bez vlivu na výkon nebo dostupnost databázových operací. Všechny zálohy se ukládají samostatně ve službě úložiště a tyto zálohy se globálně replikují z hlediska odolnosti proti regionálním katastrofám. Automatické zálohování jsou užitečná ve scénářích, kdy omylem odstraníte nebo aktualizujete svůj účet, databázi nebo kontejner Azure Cosmos a později vyžadujete obnovení dat.

## <a name="automatic-and-online-backups"></a>Automatické zálohování a zálohování online

Díky Azure Cosmos DB, nejen k datům, ale také zálohování dat je vysoce redundantní a odolné vůči regionálním katastrofám. Následující kroky ukazují, jak Azure Cosmos DB provádí zálohování dat:

* Azure Cosmos DB automaticky provede úplnou zálohu databáze každé 4 hodiny a v jakémkoli okamžiku, budou ve výchozím nastavení uloženy pouze nejnovější dva zálohy. Pokud výchozí intervaly pro vaše úlohy nestačí, můžete změnit interval zálohování a dobu uchování z Azure Portal. Konfiguraci zálohování můžete změnit během nebo po vytvoření účtu Azure Cosmos. Pokud dojde k odstranění kontejneru nebo databáze, Azure Cosmos DB zachová existující snímky daného kontejneru nebo databáze po dobu 30 dnů.

* Azure Cosmos DB ukládá tyto zálohy do úložiště objektů BLOB v Azure, zatímco skutečná data se nacházejí lokálně v rámci Azure Cosmos DB.

* Aby se zajistila nízká latence, je snímek zálohy uložený ve službě Azure Blob Storage ve stejné oblasti jako aktuální oblast zápisu (nebo **jedna** z oblastí zápisu pro případ, že máte konfiguraci zápisu ve více oblastech). Aby se zajistila odolnost vůči regionálním selháním, všechny snímky zálohovaných dat ve službě Azure Blob Storage se prostřednictvím geograficky redundantního úložiště (GRS) znovu replikují do jiné oblasti. Oblast, do které se zálohy replikují, závisí na zdrojové oblasti a páru oblastí přidruženém ke zdrojové oblasti. Další informace najdete v článku [seznam geograficky redundantních párů oblastí Azure](../best-practices-availability-paired-regions.md) . K těmto zálohám nemáte přímý přístup. Pokud prostřednictvím žádosti o podporu požádáte o obnovení, tým Azure Cosmos DB vaši zálohu obnoví.

   Následující obrázek ukazuje, jak se kontejner Azure Cosmos se všemi třemi primárními fyzickými oddíly v Západní USA zálohuje do vzdáleného účtu Azure Blob Storage v Západní USA a pak se replikuje do Východní USA:

  :::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Periodické úplné zálohování všech entit Cosmos DB v GRS Azure Storage" border="false":::

* Zálohy budou provedeny, aniž by to ovlivnilo výkon nebo dostupnost vaší aplikace. Azure Cosmos DB provádí zálohování dat na pozadí bez toho, aby využívala žádnou další zřízenou propustnost (ru) nebo ovlivnila výkon a dostupnost vaší databáze.

## <a name="options-to-manage-your-own-backups"></a>Možnosti správy vlastních záloh

Pomocí Azure Cosmos DB účtů rozhraní SQL API můžete také spravovat vlastní zálohy pomocí jednoho z následujících přístupů:

* Pomocí [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) můžete data pravidelně přesouvat do úložiště podle vašeho výběru.

* Pomocí Azure Cosmos DB [Změna kanálu](change-feed.md) můžete pravidelně číst data pro úplné zálohování nebo pro přírůstkové změny a ukládat je do vlastního úložiště.

## <a name="modify-the-backup-interval-and-retention-period"></a>Úprava intervalu zálohování a doby uchování

Azure Cosmos DB automaticky provede úplnou zálohu dat pro každé 4 hodiny a v jakémkoli časovém okamžiku jsou uloženy nejnovější dvě zálohy. Tato konfigurace je výchozí možností a je nabízena bez jakýchkoli dalších nákladů. Můžete změnit výchozí interval zálohování a dobu uchování během vytváření účtu Azure Cosmos nebo po vytvoření účtu. Konfigurace zálohování se nastavuje na úrovni účtu Azure Cosmos a pro každý účet je potřeba ji nakonfigurovat zvlášť. Když nakonfigurujete možnosti zálohování pro účet, použije se u všech kontejnerů v rámci tohoto účtu. V současné době je možné změnit možnosti zálohování pouze na webu Azure Portal.

Pokud jste data omylem odstranili nebo jste poškodili, **před vytvořením žádosti o podporu pro obnovení dat nezapomeňte zvýšit dobu uchovávání záloh vašeho účtu aspoň na sedm dní. Je nejlepší zvýšit své uchovávání do 8 hodin od této události.** Díky tomu bude mít tým Azure Cosmos DB dostatek času na obnovení vašeho účtu.

Pro změnu výchozích možností zálohování pro existující účet Azure Cosmos použijte následující postup:

1. Přihlaste se k [Azure Portal.](https://portal.azure.com/)
1. Přejděte k účtu Azure Cosmos a otevřete podokno **zálohování & obnovení** . Aktualizujte interval zálohování a dobu uchování zálohy podle potřeby.

   * **Interval zálohování** – jedná se o interval, při kterém se Azure Cosmos DB pokusy o zálohování dat. Zálohování trvá nenulovou dobu a v některých případech to může způsobit selhání z důvodu navazujících závislostí. Azure Cosmos DB se pokusí provést zálohování v nakonfigurovaném intervalu, ale nezaručuje, že se zálohování dokončí v tomto časovém intervalu. Tuto hodnotu můžete nakonfigurovat v hodinách nebo minutách. Interval zálohování nemůže být kratší než 1 hodina a větší než 24 hodin. Změníte-li tento interval, projeví se nové intervaly od doby, kdy byla provedena poslední záloha.

   * **Uchovávání záloh** – představuje období, ve kterém je každá záloha zachována. Můžete ji nakonfigurovat v hodinách nebo dnech. Minimální doba uchovávání nesmí být kratší než dvojnásobek intervalu zálohování (v hodinách) a nemůže být delší než 720 hodin.

   * **Kopie uchovávaných dat** – ve výchozím nastavení se pro dvě záložní kopie vašich dat nabízí zdarma. Pokud potřebujete víc než dvě kopie, účtuje se další poplatek. Informace o přesné ceně za další kopie najdete na stránce s cenami v části [Spotřebované úložiště](https://azure.microsoft.com/pricing/details/cosmos-db/).

   :::image type="content" source="./media/online-backup-and-restore/configure-backup-interval-retention.png" alt-text="Periodické úplné zálohování všech entit Cosmos DB v GRS Azure Storage" border="true":::

Pokud při vytváření účtu konfigurujete možnosti zálohování, můžete nakonfigurovat **zásady zálohování**, které jsou buď **periodické** , nebo **průběžné**. Pravidelné zásady vám umožní nakonfigurovat interval zálohování a uchovávání záloh. Průběžné zásady jsou aktuálně k dispozici pouze pomocí registrace. Tým Azure Cosmos DB vyhodnotí vaše zatížení a schválí vaši žádost.

:::image type="content" source="./media/online-backup-and-restore/configure-periodic-continuous-backup-policy.png" alt-text="Periodické úplné zálohování všech entit Cosmos DB v GRS Azure Storage" border="true":::

## <a name="restore-data-from-an-online-backup"></a>Obnovení dat z online zálohování

Data můžete omylem odstranit nebo upravit v jednom z následujících scénářů:  

* Odstraňte celý účet Azure Cosmos.

* Odstraňte jednu nebo více databází Azure Cosmos.

* Odstraňte jeden nebo více kontejnerů Azure Cosmos.

* Odstraňte nebo upravte položky Azure Cosmos (například dokumenty) v rámci kontejneru. Tento konkrétní případ se obvykle označuje jako poškození dat.

* Sdílená databáze nabídek nebo kontejnery v rámci sdílené databáze nabídek jsou odstraněné nebo poškozené.

Azure Cosmos DB může obnovit data ve všech výše uvedených scénářích. Při obnovení se vytvoří nový účet Azure Cosmos, který bude obsahovat obnovená data. Název nového účtu, pokud není zadaný, bude mít formát `<Azure_Cosmos_account_original_name>-restored1` . Poslední číslice je zvýšena při pokusu o provedení několika obnovení. Nemůžete obnovit data na předem vytvořený účet Azure Cosmos.

Když účet Azure Cosmos omylem odstraníte, můžeme data obnovit do nového účtu se stejným názvem, za předpokladu, že se název účtu nepoužívá. Proto doporučujeme, abyste účet po jeho odstranění znovu nevytvořili. Protože to nejen brání obnoveným datům v použití stejného názvu, ale také umožňuje zjistit správný účet pro obnovení z obtížného.

Když databázi Azure Cosmos omylem odstraníte, můžeme obnovit celou databázi nebo podmnožinu kontejnerů v této databázi. Je také možné vybrat konkrétní kontejnery napříč databázemi a obnovit je do nového účtu Azure Cosmos.

Pokud omylem odstraníte nebo upravíte jednu nebo více položek v rámci kontejneru (případ poškození dat), je nutné zadat dobu, po kterou má být obnovena. Čas je důležitý, pokud dojde k poškození dat. Vzhledem k tomu, že je kontejner živý, je zálohování stále spuštěno, takže pokud počkáte mimo dobu uchování (výchozí nastavení je osm hodin), zálohy budou přepsány. **Aby nedošlo k přepsání zálohy, zvyšte dobu uchovávání záloh účtu na alespoň sedm dní. Je nejlepší zvýšit své uchování do 8 hodin od poškození dat.**

Pokud jste data omylem odstranili nebo jste poškodili, měli byste kontaktovat [podporu Azure](https://azure.microsoft.com/support/options/) do 8 hodin, aby tým Azure Cosmos DB vám mohl pomoci obnovit data ze zálohy. Tímto způsobem bude mít tým podpory Azure Cosmos DB dostatek času na obnovení účtu.

Pokud zřizujete propustnost na úrovni databáze, proces zálohování a obnovení v tomto případě proběhne na úrovni celé databáze, nikoli na úrovni jednotlivých kontejnerů. V takových případech nemůžete vybrat podmnožinu kontejnerů k obnovení.

## <a name="migrate-data-to-the-original-account"></a>Migrace dat na původní účet

Primárním cílem obnovování dat je obnovení dat, která jste omylem odstranili nebo upravili. Proto doporučujeme, abyste nejdřív zkontrolovali obsah obnovených dat, abyste se ujistili, že obsahuje, co očekáváte. Později můžete migrovat data zpátky na primární účet. I když je možné použít obnovený účet jako nový aktivní účet, není doporučená možnost, pokud máte produkční úlohy.  

Níže jsou různé způsoby, jak migrovat data zpátky na původní účet Azure Cosmos:

* Použijte [Nástroj pro migraci dat Azure Cosmos DB](import-data.md).
* Použijte [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Použijte [kanál změn](change-feed.md) v Azure Cosmos DB.
* Můžete napsat vlastní kód.

Nezapomeňte odstranit obnovené účty, jakmile budete migrováni data, protože se účtují průběžné poplatky.

## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte, jak obnovit data z účtu Azure Cosmos, nebo se dozvíte, jak migrovat data na účet Azure Cosmos.

* Pokud chcete vytvořit žádost o obnovení, obraťte se na podporu Azure, poznamenejte si [lístek z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Jak obnovit data z účtu Azure Cosmos](how-to-backup-and-restore.md)
* K přesunu dat do Azure Cosmos DB [použijte Cosmos DB změnit informační kanál](change-feed.md) .
* K přesunu dat do Azure Cosmos DB [použijte Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) .

