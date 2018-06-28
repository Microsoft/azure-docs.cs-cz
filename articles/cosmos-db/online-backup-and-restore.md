---
title: Online zálohování a obnovení s Azure Cosmos DB | Microsoft Docs
description: Zjistěte, jak provést automatické zálohování a obnovení na databázi Azure Cosmos DB.
keywords: zálohování a obnovení, zálohování online
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 19f61893eb9250fbd5bbf930e98aa89ac74fd0c3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028732"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Automatické online zálohování a obnovení databáze Cosmos Azure
Azure Cosmos DB automaticky provede zálohování všech dat v pravidelných intervalech. Automatické zálohování se provádějí bez vlivu na výkon nebo dostupnost databázových operací. Všechny zálohy se ukládají odděleně v jiné službě úložiště a tyto zálohy jsou globálně replikovat pro odolnost proti místní havárie. Automatické zálohování jsou určené pro scénáře, pokud omylem odstraníte vaší Cosmos DB kontejneru a později vyžadují obnovení dat nebo řešení zotavení po havárii.  

Tento článek začíná rychlé rekapitulace data redundance a dostupnosti v Cosmos DB a pak popisuje zálohování. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Vysoká dostupnost s Cosmos DB - rekapitulace
Cosmos DB je navržený jako [globálně distribuované](distribute-data-globally.md) – umožňuje škálovat propustnosti nad několika oblastmi Azure společně s zásad řízené převzetí služeb při selhání a transparentní rozhraní API více funkci. Nabízí Azure Cosmos DB [99,99 % dostupnost SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) pro všechny účty jedné oblasti a všechny oblasti s více účty s zmírnit konzistence a 99.999 % čtení dostupnosti pro všechny účty databáze více oblast. Dříve, než potvrdil klientovi jsou trvale potvrzena na místní disky ve kvora replik v rámci místního datového centra všech zápisů v Azure Cosmos DB. Vysoká dostupnost databáze Cosmos spoléhá na místní úložiště a nezávisí na žádné technologie externího úložiště. Kromě toho pokud databázový účet je přidružen více než jedné oblasti Azure, replikují se v jiných oblastech také vaše zápisy. Škálovat vaše data propustnost a přístup v nízkou latenci, může mít mnoho pro čtení oblasti spojené s vaším účtem databáze podle potřeby. V každé oblasti pro čtení je spolehlivě (replikovaných) dat zachová pro sady replik.  

Jak je znázorněno v následujícím diagramu, je jediný kontejner Cosmos DB [vodorovně oddílů](partition-data.md). Kruh v následujícím diagramu je označený jako "Oddíl" a každý oddíl je zpřístupnit pomocí sady replik. Toto je místní distribuční v rámci jedné oblasti Azure (označen osy X). Dále každý oddíl (s jeho odpovídající sady replik) je pak globálně distribuované nad několika oblastmi spojené s vaším účtem databáze (například v tento obrázek tří oblastí – Východ USA, západní USA a střed). "Oddílu sada" je globálně distribuované entity, která obsahuje více kopií vašich dat v jednotlivých oblastech (označen osy Y). Můžete přiřadit prioritu oblasti spojené s vaším účtem databáze a databáze Cosmos bude transparentně převzetí služeb při selhání další oblasti v případě havárie. Můžete také ručně simulovat převzetí služeb při selhání začátku do konce dostupnosti vaší aplikace.  

Následující obrázek ukazuje vysoký stupeň redundance s Cosmos DB.

![Vysoký stupeň redundance s Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Vysoký stupeň redundance s Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Úplné, automatické, online zálohování
Bohužel se I odstraněn Moje kontejner nebo databáze. S Cosmos databáze jenom data, ale zálohování dat spočívá ve vysoce redundantní a odolné regionální havárie. Tyto automatizované zálohy jsou aktuálně prováděné přibližně každé čtyři hodiny a nejnovější dvě zálohy se ukládají za všech okolností. Pokud data omylem vyřadit nebo poškozený, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/) během osmi hodin. 

Zálohy jsou převzaty bez vlivu na výkon nebo dostupnost databázových operací. Cosmos DB přebírá zálohování na pozadí bez použití vašeho zřízené RUs nebo které mají vliv na výkon a bez ovlivnění dostupnosti vaší databáze. 

Na rozdíl od vaše data, která je uložena v rámci Cosmos DB automatické zálohování ukládají ve službě Azure Blob Storage. Pokud chcete zajistit nízkou latenci nebo efektivní nahrávání, je snímek zálohy nahrán do instanci Azure Blob storage ve stejné oblasti jako aktuální oblasti zápisu účtu databáze Cosmos DB. V zájmu odolnosti proti místní po havárii každý snímek zálohovaných dat do Azure Blob Storage opět replikují přes geograficky redundantní úložiště (GRS) do jiné oblasti. Následující diagram znázorňuje, že celého kontejneru Cosmos DB (s všechny tři primární oddíly v západní USA, v tomto příkladu) je zálohovány v vzdáleného účtu úložiště objektů Blob Azure v západní USA a pak GRS replikované východní USA. 

Následující obrázek ukazuje pravidelné úplné zálohy všech Cosmos DB entit ve službě Azure Storage GRS.

![Pravidelné úplné zálohy všechny entity Cosmos DB v GRS Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Doba uchovávání záloh
Jak je popsáno výše, bude Azure Cosmos DB na úrovni oddílu trvá snímky dat každé čtyři hodiny. V každém okamžiku jsou uchovány pouze posledních dvou snímků. Ale pokud kolekce nebo databáze, se odstraní, Azure Cosmos DB zachová stávající snímky pro všechny odstraněné oddíly v rámci dané kolekce a databáze po dobu 30 dnů.

Pro rozhraní API pro SQL, pokud chcete zachovat vlastní snímky, můžete použít export do formátu JSON možnost v Azure Cosmos DB [nástroj pro migraci dat](import-data.md#export-to-json-file) naplánovat další zálohy.

> [!NOTE]
> Pokud jste "Zřídit propustnost pro sadu kontejnery na úrovni databáze" – mít na paměti, se odehrává na úrovni Úplná účet databáze obnovení. Je také potřeba zajistit pro přístup do 8 hodin týmu podpory Pokud omylem odstraněn váš kontejner – kolekce, tabulka nebo grafu v případě používat tato nová funkce. 


## <a name="restoring-a-database-from-an-online-backup"></a>Obnovení databáze z online zálohování

Pokud omylem odstraníte databáze nebo kolekce, můžete [souboru lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nebo [volání podpory Azure](https://azure.microsoft.com/support/options/) k obnovení dat z poslední automatické zálohování. Podpora Azure je k dispozici pro vybrané plány pouze například Standard, Developer, není k dispozici základní plán podpory. Další informace o různých podporu plány najdete v tématu [plánům podpory Azure](https://azure.microsoft.com/support/plans/) stránky. 

Pokud je nutné obnovit databázi z důvodu problému s poškozením dat (zahrnuje případech, kde jsou odstraněny dokumenty v rámci kolekce), najdete v části [zpracování poškození dat](#handling-data-corruption) jako je třeba provést další kroky, aby se zabránilo poškozená data přepsání existující zálohy. Pro konkrétní snímek zálohy obnovit Cosmos DB vyžaduje, aby data byla k dispozici po dobu trvání cyklu zálohování pro tento snímek.

## <a name="handling-data-corruption"></a>Zpracování poškození dat

Azure Cosmos DB ponechá posledních dvou zálohování všech oddílů v databázovém účtu. Tento model funguje dobře, pokud kontejner (kolekce dokumentů, graf, tabulku) nebo databáze je omylem odstranit, protože jeden z poslední verze může být obnovena. Ale v případě, když uživatelé mohou zavést kvůli problému s poškozením dat, Azure Cosmos DB může být vědomi poškození dat a je možné, že poškození může přepsat existující zálohy. 

Jakmile se detekuje poškození, oslovení na zákaznickou podporu s informacemi o účtu a kolekce s Přibližná doba, po poškození. Další akce, které může uživatel provést pro poškozený (odstranění data aktualizace byla) uživatel by měl odstranit poškozená kontejner (kolekce nebo grafu nebo tabulky), tak, aby zálohování jsou chráněny před přepsáním s poškozenými daty.  

Následující obrázek ukazuje vytvoření žádosti o podporu pro obnovení container(collection/graph/table) prostřednictvím portálu Azure pro náhodného odstranění nebo aktualizaci dat v rámci kontejneru

![Obnovit do kolekce pro chybné aktualizace nebo odstranění dat v databázi systému Cosmos](./media/online-backup-and-restore/backup-restore-support.png)

Pokud je pro tento typ scénáře - provést obnovení dat je obnovit na jiný účet (s příponou "-obnovit") a kolekce. Na místě zajistit šance zákazníkům proveďte ověření dat a přesouvat data podle potřeby není provést toto obnovení. Obnovená kolekce je ve stejné oblasti s stejné RUs a zásady indexování. 

## <a name="next-steps"></a>Další postup

K replikaci databáze v několika datových centrech, najdete v části [distribuci dat globálně pomocí Cosmos DB](distribute-data-globally.md). 

Soubor kontaktujte podporu Azure [souboru lístek z portálu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

