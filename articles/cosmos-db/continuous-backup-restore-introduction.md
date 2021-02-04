---
title: Průběžné zálohování s funkcí obnovení k časovému okamžiku v Azure Cosmos DB
description: Funkce obnovení k bodu v čase Azure Cosmos DB pomáhá obnovit data z náhodné operace zápisu, odstranění nebo obnovení dat do libovolné oblasti. Seznamte se s cenami a aktuálními omezeními.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: 036f086c88267f6a20da51746ca875c48a248712
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538841"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Průběžné zálohování s funkcí obnovení k časovému okamžiku (Preview) v Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkce obnovení bodu v čase (režim průběžného zálohování) pro Azure Cosmos DB je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkce obnovení k bodu v čase aplikace Azure Cosmos DB (Preview) pomáhá v několika scénářích, jako jsou například tyto:

* Obnovení z náhodné operace zápisu nebo odstranění v rámci kontejneru.
* Obnovení odstraněného účtu, databáze nebo kontejneru.
* Obnovení do libovolné oblasti (kde zálohy existovala) v bodu obnovení v čase.

Azure Cosmos DB provádí zálohování dat na pozadí, aniž by byla využívala žádná dodatečná zřízená propustnost (ru) nebo ovlivnila výkon a dostupnost vaší databáze. Průběžné zálohování se provádí v každé oblasti, kde existuje účet. Následující obrázek ukazuje, jak kontejner s oblastí zápisu v Západní USA, oblasti čtení na východě a Východní USA 2 se zálohují do vzdáleného účtu Azure Blob Storage v příslušných oblastech. Ve výchozím nastavení každá oblast ukládá zálohu do místně redundantních účtů úložiště. Pokud má oblast povolené [zóny dostupnosti](high-availability.md#availability-zone-support) , zálohování se uloží do Zone-Redundant účtů úložiště.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Cosmos DB zálohování dat do Azure Blob Storage." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

Dostupný časový interval pro obnovení (označuje se také jako doba uchovávání) je nižší než následující dvě: "30 dní zpět v minulosti" nebo "až do času vytvoření prostředku". Časovým bodem pro obnovení může být jakékoli časové razítko v rámci doby uchovávání.

Ve verzi Public Preview můžete účet Azure Cosmos DB pro rozhraní SQL API nebo obsah bodu MongoDB v čase obnovit do jiného účtu pomocí [Azure Portal](continuous-backup-restore-portal.md), [rozhraní příkazového řádku Azure](continuous-backup-restore-command-line.md) (az CLI), [Azure PowerShell](continuous-backup-restore-powershell.md)nebo [Azure Resource Manager](continuous-backup-restore-template.md).

## <a name="what-is-restored"></a>Co se obnovilo?

V ustálených stavech se všechny mutace provedené na zdrojovém účtu (včetně databází, kontejnerů a položek) zálohují asynchronně během 100 sekund. Pokud zálohovací médium (které je úložiště Azure) je mimo provoz nebo není k dispozici, jsou mutace místně trvalé, dokud nebude médium k dispozici zpátky a pak se vyprázdní, aby nedošlo ke ztrátě přesnosti operací, které je možné obnovit.

Můžete se rozhodnout pro obnovení jakékoli kombinace zřízených kontejnerů propustnosti, sdílené databáze propustnosti nebo celého účtu. Akce obnovit obnoví všechna data a vlastnosti indexu do nového účtu. Proces obnovení zajišťuje, aby byla všechna data obnovená v účtu, databázi nebo kontejneru zaručená tak, aby byla konzistentní až do zadané doby obnovení. Doba obnovení bude záviset na množství dat, která je třeba obnovit.

> [!NOTE]
> V režimu průběžného zálohování se zálohují v každé oblasti, kde je dostupný účet Azure Cosmos DB. Zálohy provedené pro každý účet oblasti jsou místně redundantní a zóny redundantní, pokud má váš účet povolenou funkci [zóny dostupnosti](high-availability.md#availability-zone-support) pro tuto oblast. Akce obnovit vždy obnoví data do nového účtu.

## <a name="what-is-not-restored"></a>Co se neobnovilo?

Následující konfigurace nejsou obnoveny po obnovení k bodu v čase:

* Nastavení brány firewall, virtuální sítě a privátního koncového bodu.
* Nastavení konzistence. Ve výchozím nastavení se účet obnoví s konzistencí relace.  
* Regionu.
* Uložené procedury, triggery, UDF.

Tyto konfigurace můžete přidat do obnoveného účtu po dokončení obnovení.

## <a name="restore-scenarios"></a>Scénáře obnovení

Níže jsou uvedeny některé z klíčových scénářů, které jsou řešeny funkcí obnovení k určitému bodu v čase. Scénáře [a] až [c] ukazují, jak spustit obnovení, pokud je časové razítko obnovení předem známé. Existují však situace, kdy neznáte přesný čas náhodného odstranění nebo poškození. Scénáře [d] a [e] ukazují, jak _zjistit_ časové razítko obnovení pomocí nových rozhraní API pro informační kanál událostí v databázi nebo kontejnerech obnovitelné.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Události životního cyklu s časovými razítky pro účet obnovitelné." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Obnovit odstraněný účet** – všechny odstraněné účty, které můžete obnovit, se zobrazí v podokně **obnovení** . Například pokud se položka "účet A" odstraní v časovém razítku T3. V tomto případě je pro obnovení z [Azure Portal](continuous-backup-restore-portal.md#restore-deleted-account), [PowerShellu](continuous-backup-restore-powershell.md#trigger-restore)nebo rozhraní příkazového [řádku](continuous-backup-restore-command-line.md#trigger-restore)dostatečné časové razítko těsně před T3, umístěním, názvem cílového účtu, skupinou prostředků a názvem cílového účtu.  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Události životního cyklu s časovými razítky pro databázi a kontejner obnovitelné." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **Obnovení dat účtu v konkrétní oblasti** – například pokud "Account a" existuje ve dvou oblastech "východní USA" a "západní USA" na časovém razítku T3. Pokud potřebujete kopii účtu A v "Západní USA", můžete provést obnovení k určitému bodu v čase z [Azure Portal](continuous-backup-restore-portal.md), [PowerShellu](continuous-backup-restore-powershell.md#trigger-restore)nebo rozhraní příkazového [řádku](continuous-backup-restore-command-line.md#trigger-restore) s západní USA jako cílovým umístěním.

c. **Obnovení z náhodné operace zápisu nebo odstranění v rámci kontejneru se známým časovým razítkem pro obnovení** – například pokud **víte** , že obsah kontejneru 1 v rámci databáze 1 byl omylem upraven v časovém razítku T3. Můžete provést obnovení k určitému bodu v čase z [Azure Portal](continuous-backup-restore-portal.md#restore-live-account), [PowerShellu](continuous-backup-restore-powershell.md#trigger-restore)nebo [CLI](continuous-backup-restore-command-line.md#trigger-restore) do jiného účtu v časovém razítku T3 pro obnovení požadovaného stavu kontejneru.

d. **Obnovení účtu k předchozímu bodu v čase před náhodným odstraněním databáze** – v [Azure Portal](continuous-backup-restore-portal.md#restore-live-account)můžete použít podokno informační kanál událostí k určení, kdy se databáze odstranila, a najít čas obnovení. Podobně pomocí [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) a [PowerShellu](continuous-backup-restore-powershell.md#trigger-restore)můžete najít událost odstranění databáze tak, že vytvoříte výčet kanálu událostí databáze a potom aktivujete příkaz Obnovit s požadovanými parametry.

e. **Obnovte účet k předchozímu bodu v čase před náhodným odstraněním nebo úpravou vlastností kontejneru.** – V [Azure Portal](continuous-backup-restore-portal.md#restore-live-account)můžete pomocí podokna informační kanál událostí určit, kdy se má kontejner vytvořit, upravit nebo odstranit, aby se zjistil čas obnovení. Podobně pomocí [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) a [PowerShellu](continuous-backup-restore-powershell.md#trigger-restore)můžete zjistit všechny události kontejneru vynásobením kanálu událostí kontejneru a následným spuštěním příkazu RESTORE s požadovanými parametry.

## <a name="permissions"></a>Oprávnění

Azure Cosmos DB slouží k izolaci a omezení oprávnění obnovit pro účet průběžného zálohování konkrétní role nebo objektu zabezpečení. Vlastník účtu může spustit obnovení a přiřadit roli ostatním objektům zabezpečení k provedení operace obnovení. Další informace najdete v článku věnovaném [oprávněním](continuous-backup-restore-permissions.md) .

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Ceny

Účty Azure Cosmos DB s povoleným průběžným zálohováním budou mít za následek další měsíční poplatky za "uložení zálohy" a "obnovení dat". Náklady na obnovení se přidají při každém zahájení operace obnovení. Pokud nakonfigurujete účet s průběžným zálohováním, ale neobnovíte data, ve vyúčtování se budou zahrnovat jenom náklady na úložiště zálohování.

Následující příklad vychází z ceny za účet Azure Cosmos nasazený v oblasti mimo státní správu v USA. Ceny a výpočty se můžou lišit v závislosti na oblasti, kterou používáte, na stránce s [cenami Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) najdete nejnovější informace o cenách.

* Všechny účty, které jsou povolené pomocí zásad průběžného zálohování, účtují dodatečné měsíční poplatky za úložiště záloh, které se vypočte takto:

  $0,20/GB * velikost dat v GB v účtu * počet oblastí

* Při každém vyvolání rozhraní API obnovení dojde k jednomu časovému okamžiku. Poplatek je funkcí množství obnovení dat a počítá se takto:

  velikost dat v GB: $0.15/GB *

Pokud máte například 1 TB dat ve dvou oblastech, pak:

* Náklady na úložiště zálohování se počítají jako (1000 × 0,20 * 2) = $400 za měsíc.

* Náklady na obnovení se vypočtou jako (1000 × 0,15) = $150 za obnovení.

## <a name="current-limitations-public-preview"></a>Aktuální omezení (Public Preview)

Funkce obnovení v daném časovém bodě je ve verzi Public Preview a má následující omezení:

* Pro průběžné zálohování se podporují jenom Azure Cosmos DB rozhraní API pro SQL a MongoDB. Rozhraní API pro Cassandra, tabulky a Gremlin se zatím nepodporují.

* Existující účet s výchozími zásadami pravidelného zálohování nejde převést na použití režimu průběžné zálohování.

* Azure svrchované a Azure Government oblasti cloudu se zatím nepodporují.

* Účty s klíči spravovanými zákazníkem nejsou podporovány pro použití průběžného zálohování.

* Účty pro zápis ve více oblastech se nepodporují.

* Účty s povoleným odkazem na synapse se nepodporují.

* Obnovený účet se vytvoří ve stejné oblasti, ve které se nachází váš zdrojový účet. Účet nemůžete obnovit do oblasti, ve které neexistuje zdrojový účet.

* Okno obnovit je pouze 30 dnů a nelze je změnit.

* Zálohy nejsou automaticky odolné proti havárii. Abyste měli k dispozici odolnost účtu a zálohy, musíte explicitně přidat další oblast.

* I když probíhá obnovení, neupravujte ani neodstraňujte zásady správy identit a přístupu (IAM), které udělují oprávnění pro účet, nebo změňte konfiguraci virtuální sítě, brány firewall.

* Azure Cosmos DB rozhraní API pro účty SQL nebo MongoDB, které po vytvoření kontejneru vytvářejí jedinečný index, se nepodporují pro průběžné zálohování. Podporují se jenom kontejnery, které vytvářejí jedinečný index jako součást počátečního vytváření kontejneru. Pro účty MongoDB vytvoříte jedinečný index pomocí [příkazů rozšíření](mongodb-custom-commands.md).

* Funkce obnovení k určitému bodu v čase se vždycky obnoví na nový účet Azure Cosmos. Obnovení na existující účet se v tuto chvíli nepodporuje. Pokud vás zajímá poskytování zpětné vazby k místnímu obnovení, obraťte se na tým Azure Cosmos DB prostřednictvím zástupce vašeho účtu nebo [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

* Všechna nová rozhraní API vystavená pro výpis `RestorableDatabaseAccount` ,,, se `RestorableSqlDatabases` `RestorableSqlContainer` `RestorableMongodbDatabase` `RestorableMongodbCollection` vztahují na změny, i když je funkce ve verzi Preview.

* Po obnovení je možné, že pro určité kolekce se může znovu sestavit konzistentní index. Stav operace opětovného sestavení můžete kontrolovat prostřednictvím vlastnosti [IndexTransformationProgress](how-to-manage-indexing-policy.md) .

* Proces obnovení obnoví všechny vlastnosti kontejneru včetně jeho konfigurace TTL. V důsledku toho je možné, že obnovená data se okamžitě odstraní, pokud jste nakonfigurovali tento způsob. Aby se zabránilo této situaci, časové razítko obnovení musí být před přidáním vlastností TTL do kontejneru.

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte a spravujte průběžné zálohování pomocí [Azure Portal](continuous-backup-restore-portal.md), [PowerShellu](continuous-backup-restore-powershell.md), rozhraní příkazového [řádku](continuous-backup-restore-command-line.md)nebo [Azure Resource Manager](continuous-backup-restore-template.md).
* [Správa oprávnění](continuous-backup-restore-permissions.md) potřebných pro obnovení dat pomocí režimu průběžné zálohování.
* [Model prostředků režimu průběžné zálohování](continuous-backup-restore-resource-model.md)
