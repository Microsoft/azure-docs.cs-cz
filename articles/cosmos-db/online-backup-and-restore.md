---
title: Online zálohování a obnovení pomocí služby Azure Cosmos DB | Dokumentace Microsoftu
description: Zjistěte, jak provést automatické zálohování a obnovení na databázi Azure Cosmos DB.
keywords: zálohování a obnovení, online zálohování
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: govindk
ms.openlocfilehash: 613c61d9b881b7d736a50cadbf313c1f9aac57c9
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054821"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Automatické online zálohování a obnovení pomocí služby Azure Cosmos DB
Azure Cosmos DB automaticky provede zálohování vašich dat v pravidelných intervalech. Automatické zálohy jsou prováděny bez vlivu na výkon nebo dostupnost databázových operací. Všechny zálohy jsou uloženy odděleně v jiné službě úložiště a tyto zálohy jsou globálně replikuje odolnosti proti místní havárií. Automatické zálohování jsou určené pro scénáře, pokud omylem odstraníte kontejneru Cosmos DB a později vyžadují obnovení dat nebo řešení zotavení po havárii.  

Tento článek začíná rychlá rekapitulace toho, tak redundanci dat a dostupnost ve službě Cosmos DB a pak popisuje zálohování. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Vysoká dostupnost s Cosmos DB – rekapitulace
Cosmos DB je navržena jako [globálně distribuované](distribute-data-globally.md) – umožňuje škálovat propustnost nad několika oblastmi Azure spolu s zásad řízené převzetí služeb při selhání a transparentní rozhraní API pro vícenásobné navádění. Azure Cosmos DB nabízí [99,99 % dostupnosti smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/cosmos-db) pro všechny účty v jedné oblasti a všechny účty ve více oblastech s mírnější konzistencí a 99,999 % dostupnosti pro všechny účty databáze pro více oblastí pro čtení. Všechny operace zápisu ve službě Azure Cosmos DB se dříve, než potvrdil klientovi potvrzením bezpečně uložený na lokální disky podle kvorum replik v rámci místního datového centra. Vysoká dostupnost služby Cosmos DB spoléhá na místní úložiště a nezávisí na žádné externí úložiště technologie. Kromě toho pokud váš účet databáze je přidruženo více než jedné oblasti Azure, zápisů se replikují napříč i v jiných oblastech. Umožňuje škálovat propustnost a používat data podle s nízkou latencí, můžete použít stejně jako mnoho oblastmi spojenými s databázovým účtem můžete načítat. V každé oblasti čtení (replikovaného) data se ukládají trvale sady replik.  

Jak je znázorněno v následujícím diagramu, jeden kontejneru Cosmos DB je [horizontálně dělené](partition-data.md). "Oddílu" označuje symbolem kolečko v následujícím diagramu a každý oddíl se provádějí prostřednictvím sady replik s vysokou dostupností. To je místní distribuce v rámci jedné oblasti Azure (udávají na ose X). Dále každý oddíl (s jeho odpovídající sady replik) je pak globálně distribuovat napříč několika oblastmi spojené s vaším účtem databáze (třeba v tomto obrázku tři oblasti – USA – východ, USA – západ a střed Indie). "Sada oddíl" je globálně distribuovaná entity zahrnující několik kopií vašich dat v jednotlivých oblastech (udávají na ose Y). Oblastem spojeným s vaším účtem databáze lze přiřadit prioritu a Cosmos DB bude transparentní převzetí služeb při selhání k další oblasti v případě havárie. Můžete také ručně simulovat převzetí služeb při selhání k testování dostupnosti začátku do konce vaší aplikace.  

Následující obrázek ukazuje vysoký stupeň redundance pomocí služby Cosmos DB.

![Vysoký stupeň redundance pomocí služby Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Vysoký stupeň redundance pomocí služby Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Úplné, automatické, online zálohování
Jejda můžu odstranit Můj kontejner nebo databáze. Pomocí služby Cosmos DB jenom data, ale zálohy dat také probíhají vysoce redundantní a odolná proti selháním na regionální jiného problému ovlivňujícího. Tyto automatizované zálohy jsou prováděny aktuálně přibližně každé čtyři hodiny a nejnovější dvě zálohy se uchovávají po celou dobu. Pokud data náhodně vyřadit nebo poškozen, obraťte se na [podpory Azure](https://azure.microsoft.com/support/options/) do osmi hodin. 

Zálohy jsou prováděny bez vlivu na výkon nebo dostupnost databázových operací. Bez použití zřízených ru nebo by to mělo dopad na výkon a bez ovlivnění dostupnosti vaší databáze cosmos DB trvá zálohování na pozadí. 

Na rozdíl od data, která je uložená ve službě Azure Cosmos automatické zálohy jsou uložené ve službě Azure Blob Storage. Pro zajištění nízké latence a efektivní nahrávání, nahrání snímku zálohy do instance služby Azure Blob storage ve stejné oblasti jako aktuální oblasti pro zápis z vašeho účtu databáze Cosmos DB. Což zvyšuje odolnost vůči regionálního jednotlivých snímků zálohovaných dat ve službě Azure Blob Storage opět replikují přes geograficky redundantní úložiště (GRS) do jiné oblasti. Následující diagram znázorňuje, že v vzdálený účet Azure Blob Storage v oblasti západní USA se zálohuje celého kontejneru Cosmos DB (pomocí všechny tři primární oddíly v oblasti západní USA, v tomto příkladu) a potom GRS replikuje do USA – východ. 

Následující obrázek ukazuje pravidelné úplné zálohy všechny entity Cosmos DB v geograficky Redundantního úložiště Azure Storage.

![Pravidelné úplné zálohy všechny entity Cosmos DB v geograficky Redundantního úložiště Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Období uchování zálohy
Jak je popsáno výše, bude Azure Cosmos DB na úrovni oddílu trvá snímky dat každé čtyři hodiny. V každém okamžiku pouze posledních dvou snímky, zůstanou zachovány. Pokud však odstranění kontejnerů a databáze Azure Cosmos DB zůstane existující snímky pro všechny odstraněné oddíly v rámci daného kontejneru/databáze po dobu 30 dnů.

Pro rozhraní SQL API, pokud chcete zachovat vlastní snímky můžete exportovat do formátu JSON možnost v služby Azure Cosmos DB [nástroj pro migraci dat](import-data.md#export-to-json-file) naplánování dalšího zálohování.

> [!NOTE]
> Pokud jste "Zřídit propustnost pro skupinu kontejnerů na úrovni databáze," – Nezapomeňte se stane obnovení na úrovni účtu. úplné databáze. Je také potřeba zajistit oslovit do 8 hodin týmu podpory, pokud omylem odstraníte kontejner. Data nejde obnovit, pokud není kontaktovat tým podpory do 8 hodin. 


## <a name="restoring-a-database-from-an-online-backup"></a>Obnovení databáze z online zálohování

Pokud omylem odstraníte, databáze nebo kontejneru, můžete si [lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nebo [obraťte se na podporu Azure](https://azure.microsoft.com/support/options/) k obnovení dat z posledního automatického zálohování. Podpora Azure je dostupný pro vybrané plány, jako je například Standard, pro vývojáře, podporu není k dispozici s plánem Basic. Další informace o plánech podpory různých najdete v tématu [plánů podpory Azure](https://azure.microsoft.com/support/plans/) stránky. 

Pokud je potřeba obnovit databázi z důvodu problému poškození dat (včetně případů, kdy se odstraní dokumenty v rámci kontejneru), najdete v článku [zpracování poškození dat](#handling-data-corruption) potřebujete provést další kroky, aby se zabránilo poškozená data přepsání existující zálohy. Pro konkrétní snímek zálohy obnovit Cosmos DB vyžaduje, aby byla data k dispozici po dobu trvání cyklu zálohování tohoto snímku.

## <a name="handling-data-corruption"></a>Zpracování poškození dat.

Azure Cosmos DB uchovává poslední dvě zálohy každý oddíl v databázovém účtu. Tento model funguje dobře, když kontejner (kolekce dokumentů, tabulka, graf) nebo databáze je omylem odstranili, protože jedna z posledních verzí můžete obnovit. Ale v případě, když uživatelé může představovat problém poškození dat, Azure Cosmos DB může je nezajímat se o poškození dat a je možné, že poškození může přepsat existující zálohy. 

Jakmile se detekuje poškození, kontaktujte zákaznickou podporu s účet a kontejner informace o databázi s přibližný čas poškození. Další akce, které může uživatel provést v případě klíčových poškozen (odstranění dat aktualizace byla) uživatele odstraňte poškozený kontejner (kolekci/graf a tabulka) tak, aby zálohování jsou chráněny před přepsáním s poškozenými daty.  

Následující obrázek ukazuje vytvoření žádosti o podporu pro container(collection/graph/table) obnovení prostřednictvím portálu Azure portal k náhodnému odstranění nebo aktualizaci dat v rámci kontejneru

![Obnovit jako kontejner pro chybné aktualizace nebo odstranění dat ve službě Cosmos DB](./media/online-backup-and-restore/backup-restore-support.png)

Po dokončení obnovení se pro tento druh scénářů – obnovení dat do jiného účtu (s příponou "-obnovit") a kontejner. Tato obnovení se provádí na místě možnost poskytovat zákazníkům ověřování dat a přesun dat podle potřeby. Obnovené kontejner je ve stejné oblasti s stejné a zásady indexování. 

## <a name="next-steps"></a>Další postup

Replikovat vaši databázi v několika datových centrech, naleznete v tématu [distribuci dat pomocí služby Cosmos DB](distribute-data-globally.md). 

Do souboru požádejte podporu Azure [lístek na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

