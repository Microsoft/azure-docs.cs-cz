---
title: Konfigurace účtu Azure Cosmos DB s pravidelným zálohováním
description: Tento článek popisuje, jak nakonfigurovat účty Azure Cosmos DB s pravidelným zálohováním pomocí intervalu zálohování. a uchování. Také postup, jak kontaktovat podporu pro obnovení vašich dat.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 69a9f0a82f5c19504564825e47f69ab8414e0909
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565823"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>Konfigurace účtu Azure Cosmos DB s pravidelným zálohováním
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB automaticky v pravidelných intervalech zálohuje vaše data. Automatické zálohování nemá vliv na výkon ani dostupnost databázových operací. Všechny zálohy se ukládají samostatně ve službě úložiště a tyto zálohy se globálně replikují z hlediska odolnosti proti regionálním katastrofám. Díky Azure Cosmos DB, nejen k datům, ale také zálohování dat je vysoce redundantní a odolné vůči regionálním katastrofám. Následující kroky ukazují, jak Azure Cosmos DB provádí zálohování dat:

* Azure Cosmos DB automaticky provede úplnou zálohu databáze každé 4 hodiny a v jakémkoli okamžiku, budou ve výchozím nastavení uloženy pouze nejnovější dva zálohy. Pokud výchozí intervaly pro vaše úlohy nestačí, můžete změnit interval zálohování a dobu uchování z Azure Portal. Konfiguraci zálohování můžete změnit během nebo po vytvoření účtu Azure Cosmos. Pokud dojde k odstranění kontejneru nebo databáze, Azure Cosmos DB zachová existující snímky daného kontejneru nebo databáze po dobu 30 dnů.

* Azure Cosmos DB ukládá tyto zálohy do úložiště objektů BLOB v Azure, zatímco skutečná data se nacházejí lokálně v rámci Azure Cosmos DB.

* Aby se zajistila nízká latence, je snímek zálohy uložený ve službě Azure Blob Storage ve stejné oblasti jako aktuální oblast zápisu (nebo **jedna** z oblastí zápisu pro případ, že máte konfiguraci zápisu ve více oblastech). Aby se zajistila odolnost vůči regionálním selháním, všechny snímky zálohovaných dat ve službě Azure Blob Storage se prostřednictvím geograficky redundantního úložiště (GRS) znovu replikují do jiné oblasti. Oblast, do které se zálohy replikují, závisí na zdrojové oblasti a páru oblastí přidruženém ke zdrojové oblasti. Další informace najdete v článku [seznam geograficky redundantních párů oblastí Azure](../best-practices-availability-paired-regions.md) . K těmto zálohám nemáte přímý přístup. Pokud prostřednictvím žádosti o podporu požádáte o obnovení, tým Azure Cosmos DB vaši zálohu obnoví.

  Následující obrázek ukazuje, jak se kontejner Azure Cosmos se všemi třemi primárními fyzickými oddíly v Západní USA zálohuje do vzdáleného účtu Azure Blob Storage v Západní USA a pak se replikuje do Východní USA:

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="Pravidelné úplné zálohování všech Cosmos DB entit v GRS Azure Storage." lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* Zálohy budou provedeny, aniž by to ovlivnilo výkon nebo dostupnost vaší aplikace. Azure Cosmos DB provádí zálohování dat na pozadí, aniž by byla využívala žádná dodatečná zřízená propustnost (ru) nebo ovlivnila výkon a dostupnost vaší databáze.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>Úprava intervalu zálohování a doby uchování

Azure Cosmos DB automaticky provede úplnou zálohu dat pro každé 4 hodiny a v jakémkoli časovém okamžiku jsou uloženy nejnovější dvě zálohy. Tato konfigurace je výchozí možností a je nabízena bez jakýchkoli dalších nákladů. Výchozí interval zálohování a dobu uchovávání můžete změnit při vytváření účtu Azure Cosmos nebo po jeho vytvoření. Konfigurace zálohování se nastavuje na úrovni účtu Azure Cosmos a pro každý účet je potřeba ji nakonfigurovat zvlášť. Když nakonfigurujete možnosti zálohování pro účet, použije se u všech kontejnerů v rámci tohoto účtu. V současné době je možné změnit možnosti zálohování pouze na webu Azure Portal.

Pokud jste data omylem odstranili nebo jste poškodili, **před vytvořením žádosti o podporu pro obnovení dat nezapomeňte zvýšit dobu uchovávání záloh vašeho účtu aspoň na sedm dní. Je nejlepší zvýšit své uchovávání do 8 hodin od této události.** Díky tomu bude mít tým Azure Cosmos DB dostatek času na obnovení vašeho účtu.

Pro změnu výchozích možností zálohování pro existující účet Azure Cosmos použijte následující postup:

1. Přihlaste se k [Azure Portal.](https://portal.azure.com/)
1. Přejděte k účtu Azure Cosmos a otevřete podokno **zálohování & obnovení** . Aktualizujte interval zálohování a dobu uchování zálohy podle potřeby.

   * **Interval zálohování** – jedná se o interval, při kterém se Azure Cosmos DB pokusy o zálohování dat. Zálohování trvá nenulovou dobu a v některých případech to může způsobit selhání z důvodu navazujících závislostí. Azure Cosmos DB se pokusí provést zálohování v nakonfigurovaném intervalu, ale nezaručuje, že se zálohování dokončí v tomto časovém intervalu. Tuto hodnotu můžete nakonfigurovat v hodinách nebo minutách. Interval zálohování nemůže být kratší než 1 hodina a větší než 24 hodin. Změníte-li tento interval, projeví se nové intervaly od doby, kdy byla provedena poslední záloha.

   * **Uchovávání záloh** – představuje období, ve kterém je každá záloha zachována. Můžete ji nakonfigurovat v hodinách nebo dnech. Minimální doba uchovávání nesmí být kratší než dvojnásobek intervalu zálohování (v hodinách) a nemůže být delší než 720 hodin.

   * **Kopie uchovávaných dat** – ve výchozím nastavení se pro dvě záložní kopie vašich dat nabízí zdarma. Pokud potřebujete víc než dvě kopie, účtuje se navíc. V části spotřebované úložiště na stránce s [cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) se dozvíte přesnou cenu za nadbytečné kopie.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-interval-retention.png" alt-text="Nakonfigurujte interval zálohování a uchování pro existující účet Azure Cosmos." border="true":::

Pokud při vytváření účtu konfigurujete možnosti zálohování, můžete nakonfigurovat **zásady zálohování**, které jsou buď **periodické** , nebo **průběžné**. Pravidelné zásady vám umožní nakonfigurovat interval zálohování a uchovávání záloh. Průběžné zásady jsou aktuálně k dispozici pouze pomocí registrace. Tým Azure Cosmos DB vyhodnotí vaše zatížení a schválí vaši žádost.

:::image type="content" source="./media/configure-periodic-backup-restore/configure-periodic-continuous-backup-policy.png" alt-text="Nakonfigurujte pravidelné nebo nepřetržité zásady zálohování pro nové účty Azure Cosmos." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>Požadavek na obnovení dat ze zálohy

Pokud databázi nebo kontejner omylem odstraníte, můžete si [vytvořit lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nebo [zavolat podporu Azure](https://azure.microsoft.com/support/options/) , která obnoví data z automatických online zálohování. Podpora Azure je dostupná jenom pro vybrané plány, jako je **Standard**, **vývojář** a plány vyšší než. Podpora Azure není k dispozici u plánu **Basic** . Další informace o různých plánech podpory najdete na stránce [plány podpory Azure](https://azure.microsoft.com/support/plans/) .

Aby bylo možné obnovit konkrétní snímek zálohy, Azure Cosmos DB vyžaduje, aby data byla k dispozici během cyklu zálohování pro daný snímek.
Před vyžádáním obnovení byste měli mít následující podrobnosti:

* Připravte si ID předplatného.

* Na základě toho, jak se data omylem odstranila nebo změnila, byste měli připravit na Další informace. Doporučuje se, abyste měli k dispozici informace pro minimalizaci back-in, které by mohly být v některých případech v případě citlivého času škodlivé.

* Pokud se odstraní celý účet Azure Cosmos DB, musíte zadat název odstraněného účtu. Pokud vytvoříte další účet se stejným názvem jako odstraněný účet, sdílejte ho s týmem podpory, protože pomáhá určit správný účet pro výběr. Doporučuje se pro každý odstraněný účet zaregistrovat různé lístky podpory, protože minimalizují nejasnost stavu obnovení.

* Pokud odstraníte jednu nebo více databází, měli byste poskytnout účet Azure Cosmos a názvy databází Azure Cosmos a určit, jestli existuje nová databáze se stejným názvem.

* Pokud se odstraní aspoň jeden kontejner, měli byste zadat název účtu Azure Cosmos, názvy databází a názvy kontejnerů. A určete, zda kontejner se stejným názvem existuje.

* Pokud jste data omylem odstranili nebo jste poškodili, měli byste kontaktovat [podporu Azure](https://azure.microsoft.com/support/options/) do 8 hodin, aby tým Azure Cosmos DB vám mohl pomoci obnovit data ze zálohy. **Před vytvořením žádosti o podporu pro obnovení dat nezapomeňte [zvýšit dobu uchovávání záloh](#configure-backup-interval-retention) vašeho účtu aspoň na sedm dní. Je nejlepší zvýšit své uchovávání do 8 hodin od této události.** Tímto způsobem bude mít tým podpory Azure Cosmos DB dostatek času na obnovení účtu.

Kromě názvu účtu Azure Cosmos, názvů databází a názvů kontejnerů byste měli určit bod v čase, do kterého lze data obnovit. Je důležité, aby bylo co nejpřesněji možné určit nejlepší dostupné zálohy v daném čase. **Je také důležité zadat čas ve standardu UTC.**

Následující snímek obrazovky ukazuje, jak vytvořit žádost o podporu pro kontejner (kolekce/graf/tabulka) pro obnovení dat pomocí Azure Portal. Zadejte další podrobnosti, jako je například typ dat, účel obnovení, čas odstranění dat, který nám pomůžete určit prioritu žádosti.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Vytvořte žádost o podporu zálohování pomocí Azure Portal." border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Pokyny pro obnovení dat ze zálohy

Data můžete omylem odstranit nebo upravit v jednom z následujících scénářů:  

* Odstraňte celý účet Azure Cosmos.

* Odstraňte jednu nebo více databází Azure Cosmos.

* Odstraňte jeden nebo více kontejnerů Azure Cosmos.

* Odstraňte nebo upravte položky Azure Cosmos (například dokumenty) v rámci kontejneru. Tento konkrétní případ se obvykle označuje jako poškození dat.

* Sdílená databáze nabídek nebo kontejnery v rámci sdílené databáze nabídek jsou odstraněné nebo poškozené.

Azure Cosmos DB může obnovit data ve všech výše uvedených scénářích. Při obnovení se vytvoří nový účet Azure Cosmos, který bude obsahovat obnovená data. Název nového účtu, pokud není zadaný, bude mít formát `<Azure_Cosmos_account_original_name>-restored1` . Poslední číslice je zvýšena při pokusu o provedení několika obnovení. Nemůžete obnovit data na předem vytvořený účet Azure Cosmos.

Pokud účet Azure Cosmos omylem odstraníte, můžeme data obnovit do nového účtu se stejným názvem, pokud se název účtu nepoužívá. Proto doporučujeme, abyste účet po jeho odstranění znovu nevytvořili. Protože to nejen brání obnoveným datům v použití stejného názvu, ale také umožňuje zjistit správný účet pro obnovení z obtížného.

Když databázi Azure Cosmos omylem odstraníte, můžeme obnovit celou databázi nebo podmnožinu kontejnerů v této databázi. Je také možné vybrat konkrétní kontejnery napříč databázemi a obnovit je do nového účtu Azure Cosmos.

Pokud omylem odstraníte nebo upravíte jednu nebo více položek v rámci kontejneru (případ poškození dat), je nutné zadat dobu, po kterou má být obnovena. Čas je důležitý, pokud dojde k poškození dat. Vzhledem k tomu, že je kontejner živý, je zálohování stále spuštěno, takže pokud počkáte mimo dobu uchování (výchozí nastavení je osm hodin), zálohy budou přepsány. Aby nedošlo k přepsání zálohy, zvyšte dobu uchovávání záloh účtu na alespoň sedm dní. Je nejlepší zvýšit své uchování do 8 hodin od poškození dat.

Pokud jste data omylem odstranili nebo jste poškodili, měli byste kontaktovat [podporu Azure](https://azure.microsoft.com/support/options/) do 8 hodin, aby tým Azure Cosmos DB vám mohl pomoci obnovit data ze zálohy. Tímto způsobem bude mít tým podpory Azure Cosmos DB dostatek času na obnovení účtu.

> [!NOTE]
> Po obnovení dat se do obnoveného účtu nemigrují všechny možnosti zdroje nebo nastavení. Následující nastavení se přenesou na nový účet:
> * Seznamy řízení přístupu VNET
> * Uložené procedury, triggery a uživatelsky definované funkce
> * Nastavení pro více oblastí  

Pokud zřizujete propustnost na úrovni databáze, proces zálohování a obnovení v tomto případě proběhne na úrovni celé databáze, nikoli na úrovni jednotlivých kontejnerů. V takových případech nemůžete vybrat podmnožinu kontejnerů k obnovení.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>Požadovaná oprávnění ke změně uchovávání nebo obnovení z portálu
Objekty zabezpečení, které jsou součástí role [CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator), Owner nebo přispěvatel, můžou požadovat obnovení nebo změnit dobu uchování.

## <a name="understanding-costs-of-extra-backups"></a>Porozumění nákladům na nadbytečné zálohy
K dispozici jsou dvě zálohy zdarma a další zálohy se účtují podle cen na základě oblastí pro úložiště zálohování popsané v tématu [ceny za úložiště zálohování](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/). Pokud je například uchovávání záloh nastaveno na 240 hodiny, což je 10 dní a interval zálohování na 24 hodin. To zahrnuje 10 kopií zálohovaných dat. Za předpokladu 1 TB dat v Západní USA 2 budou náklady 0,12 × 1000 * 8 pro úložiště zálohování v daném měsíci. 


## <a name="options-to-manage-your-own-backups"></a>Možnosti správy vlastních záloh

Pomocí Azure Cosmos DB účtů rozhraní SQL API můžete také spravovat vlastní zálohy pomocí jednoho z následujících přístupů:

* Pomocí [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) můžete data pravidelně přesouvat do úložiště podle vašeho výběru.

* Pomocí Azure Cosmos DB [Změna kanálu](change-feed.md) můžete pravidelně číst data pro úplné zálohování nebo pro přírůstkové změny a ukládat je do vlastního úložiště.

## <a name="post-restore-actions"></a>Akce po obnovení

Primárním cílem obnovování dat je obnovení dat, která jste omylem odstranili nebo upravili. Proto doporučujeme, abyste nejdřív zkontrolovali obsah obnovených dat, abyste se ujistili, že obsahuje, co očekáváte. Pokud vše vypadá dobře, můžete migrovat data zpátky na primární účet. I když je možné použít obnovený účet jako nový aktivní účet, není doporučená možnost, pokud máte produkční úlohy. 

Po obnovení dat se zobrazí oznámení o názvu nového účtu (obvykle ve formátu `<original-name>-restored1` ) a času, kdy byl účet obnoven. Obnovený účet bude mít stejnou zřízenou propustnost, zásady indexování a je ve stejné oblasti jako původní účet. Obnovený účet může zobrazit uživatel, který je správcem předplatného nebo spolusprávce.

### <a name="migrate-data-to-the-original-account"></a>Migrace dat na původní účet

Níže jsou různé způsoby, jak migrovat data zpátky na původní účet:

* Použijte [Nástroj pro migraci dat Azure Cosmos DB](import-data.md).
* Použijte [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Použijte [kanál změn](change-feed.md) v Azure Cosmos DB.
* Můžete napsat vlastní kód.

Doporučuje se odstranit kontejner nebo databázi hned po migraci dat. Pokud obnovené databáze nebo kontejnery neodstraníte, budou se vám účtovat náklady na jednotky, úložiště a výstupy žádostí.

## <a name="next-steps"></a>Další kroky

* Pokud chcete vytvořit žádost o obnovení, obraťte se na podporu Azure, poznamenejte [si lístek z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Nakonfigurujte a spravujte průběžné zálohování pomocí [Azure Portal](continuous-backup-restore-portal.md), [PowerShellu](continuous-backup-restore-powershell.md), rozhraní příkazového [řádku](continuous-backup-restore-command-line.md)nebo [Azure Resource Manager](continuous-backup-restore-template.md).
* [Správa oprávnění](continuous-backup-restore-permissions.md) potřebných pro obnovení dat pomocí režimu průběžné zálohování.
