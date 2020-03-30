---
title: Šifrování v klidovém stavu v Azure Cosmos DB
description: Zjistěte, jak Azure Cosmos DB poskytuje šifrování dat v klidovém stavu a jak se implementuje.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: db20388b5277e000ffe7055e9840742d6af7788e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062602"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Šifrování dat ve službě Azure Cosmos DB 

Šifrování v klidovém stavu je fráze, která běžně odkazuje na šifrování dat na stálých úložných zařízeních, jako jsou jednotky SSD a pevné disky (HDD). Cosmos DB ukládá své primární databáze na SSD. Jeho mediální přílohy a zálohy jsou uloženy v úložišti objektů Blob Azure, které je obvykle zálohováno pevnými disky. S vydáním šifrování v klidovém stavu pro Cosmos DB jsou všechny vaše databáze, mediální přílohy a zálohy šifrovány. Vaše data jsou nyní šifrována při přenosu (přes síť) a v klidovém stavu (stálé úložiště), což umožňuje šifrování mezi koncovými body.

Jako služba PaaS, Cosmos DB je velmi snadné použití. Vzhledem k tomu, že všechna uživatelská data uložená v Cosmos DB je šifrována v klidovém stavu a v přenosu, není třeba provést žádnou akci. Dalším způsobem, jak to dát, je, že šifrování v klidovém stavu je ve výchozím nastavení "zapnuto". Neexistují žádné ovládací prvky, které by jej vypnuly nebo zapnuly. Azure Cosmos DB používá šifrování AES-256 ve všech oblastech, kde je spuštěný účet. Tuto funkci poskytujeme, zatímco nadále splňujeme naše [dostupnost a výkon sla](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementace šifrování v klidovém stavu pro Azure Cosmos DB

Šifrování v klidovém stavu je implementováno pomocí řady technologií zabezpečení, včetně zabezpečených systémů úložiště klíčů, šifrovaných sítí a kryptografických rozhraní API. Systémy, které dešifrují a zpracovávají data, musí komunikovat se systémy, které spravují klíče. Diagram ukazuje, jak je odděleno ukládání šifrovaných dat a správa klíčů. 

![Návrhový diagram](./media/database-encryption-at-rest/design-diagram.png)

Základní tok požadavku uživatele je následující:
- Účet uživatelské databáze je připraven a klíče úložiště jsou načteny prostřednictvím požadavku na poskytovatele prostředků služby Management Service.
- Uživatel vytvoří připojení k Cosmos DB prostřednictvím HTTPS/zabezpečeného přenosu. (Sady SDK abstraktní podrobnosti.)
- Uživatel odešle dokument JSON, který má být uložen přes dříve vytvořené zabezpečené připojení.
- Dokument JSON je indexován, pokud uživatel vypnul indexování.
- Data dokumentu JSON i indexu jsou zapsány do zabezpečeného úložiště.
- Pravidelně se data čtou ze zabezpečeného úložiště a zálohují do úložiště objektů blob Store azure šifrované.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>Otázka: Kolik víc stojí Azure Storage, když je povoleno šifrování služby úložiště?
A: Neexistuje žádný další příplatek.

### <a name="q-who-manages-the-encryption-keys"></a>Otázka: Kdo spravuje šifrovací klíče?
A: Klíče jsou spravovány společností Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>Otázka: Jak často se otáčí šifrovací klíče?
A: Microsoft má sadu interních pokynů pro rotaci šifrovacího klíče, které Cosmos DB následuje. Konkrétní pokyny se nezveřejňují. Společnost Microsoft publikuje [životní cyklus vývoje zabezpečení (SDL),](https://www.microsoft.com/sdl/default.aspx)který je považován za podmnožinu interních pokynů a obsahuje užitečné osvědčené postupy pro vývojáře.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Otázka: Mohu použít vlastní šifrovací klíče?
A: Ano, nyní tato funkce je k dispozici pro nové účty Cosmos a to by mělo být provedeno v době vytvoření účtu. Další informace naleznete v dokumentu [Klíče spravované zákazníkem.](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk)

### <a name="q-what-regions-have-encryption-turned-on"></a>Otázka: Které oblasti mají zapnuté šifrování?
A: Všechny oblasti Azure Cosmos DB mají šifrování zapnuté pro všechna uživatelská data.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Otázka: Má šifrování vliv na latenci výkonu a propustnost SLA?
A: Neexistuje žádný vliv nebo změny výkonu SLA nyní, že šifrování v klidovém stavu je povolena pro všechny stávající a nové účty. Můžete si přečíst více na stránce [SLA pro Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) zobrazíte nejnovější záruky.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Otázka: Podporuje místní emulátor šifrování v klidovém stavu?
A: Emulátor je samostatný vývoj a testování nástroj a nepoužívá služby správy klíčů, které používá spravované služby Cosmos DB. Naše doporučení je povolit nástroj BitLocker na jednotkách, kde ukládáte citlivá data testu emulátoru. [Emulátor podporuje změnu výchozího datového adresáře](local-emulator.md) a také použití známého umístění.

## <a name="next-steps"></a>Další kroky

Přehled zabezpečení Cosmos DB a nejnovější vylepšení najdete v tématu [zabezpečení databáze Azure Cosmos](database-security.md).
Další informace o certifikacích Microsoftu najdete v [Centru zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).
