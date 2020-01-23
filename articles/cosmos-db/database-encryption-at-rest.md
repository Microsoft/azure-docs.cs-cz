---
title: Šifrování v klidovém stavu ve službě Azure Cosmos DB
description: Zjistěte, jak službu Azure Cosmos DB poskytuje šifrování neaktivních uložených dat a jak je implementován.
author: markjbrown
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: 366a8cc2d8b08c9508053eaeb8bf70622fd870cf
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548097"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Šifrování dat v Azure Cosmos DB 

Šifrování v klidovém stavu je frázi, která se běžně používá k šifrování dat na zařízeních stálé úložiště, jako je jednotkami SSD (Solid-State Drive) a pevných disků (HDD). Cosmos DB ukládá své primární databáze na jednotkách SSD. Jeho přílohy média a zálohy jsou uloženy v Azure Blob storage, která je obecně zálohovat pevných discích. S vydáním šifrování v klidovém stavu pro službu Cosmos DB všechny databáze, přílohy média a zálohy zašifrují. Vaše data teď šifrují během přenosu (v síti) a v klidovém stavu (stálé úložiště), získáte-koncové šifrování.

Je velmi snadno se používá služba PaaS, Cosmos DB. Vzhledem k tomu, že všechna uživatelská data uložená ve službě Cosmos DB je v klidovém stavu a přenosu šifrovaný, není nutné provádět žádnou akci. Dalším způsobem, jak to put je, že je "na" ve výchozím nastavení šifrování v klidovém stavu. Neexistují žádná opatření můžete zapnout nebo vypnout. Azure Cosmos DB používá šifrování AES-256 ve všech oblastech, kde je účet spuštěný. Tato funkce zajišťuje My zatím budeme pokračovat podle našich [SLA o dostupnosti a výkonu](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Provádění šifrování v klidovém stavu pro službu Azure Cosmos DB

Šifrování v klidovém stavu je implementovaný s využitím řadu bezpečnostních technologií, včetně systémů zabezpečené úložiště klíčů, šifrované sítě a rozhraní API kryptografických. Systémy, které dešifrovat a zpracovávat data mají ke komunikaci se systémy, které spravují klíče. Diagram znázorňuje, jak je oddělen ukládání šifrovaných dat a správy klíčů. 

![Diagram návrhu](./media/database-encryption-at-rest/design-diagram.png)

Základní tok uživatelského požadavku je následujícím způsobem:
- Uživatelský účet databáze je připraven a úložiště klíčů jsou načteny prostřednictvím požadavku u poskytovatele prostředků správy služeb.
- Uživatel vytvoří připojení ke službě Cosmos DB přes HTTPS/zabezpečení přenosu. (Se sadami SDK abstraktní podrobnosti.)
- Uživatel odešle dokument JSON ukládaly pomocí dříve vytvořeného zabezpečené připojení.
- Dokument JSON se indexovat, pokud uživatel vypnul indexování.
- JSON dokumentu a index data se zapisují do zabezpečené úložiště.
- Data se pravidelně číst ze zabezpečeného úložiště a zálohovat na Store Blob šifrované Azure.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>Otázka: jak služby Azure Storage stát Pokud je povolené šifrování služby Storage?
Odpověď: neexistuje žádné další poplatky.

### <a name="q-who-manages-the-encryption-keys"></a>Dotaz: kdo spravuje šifrovací klíče?
Odpověď: klíče spravuje Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>Otázka: jak často jsou otočeny šifrovací klíče?
Odpověď: Microsoft má sadu interní pokyny pro rotaci klíčů šifrování, který následuje po Cosmos DB. Konkrétní pokyny nejsou publikovány. Publikování Microsoft [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), která je zobrazena jako podmnožinu interní pokyny a obsahuje užitečné osvědčené postupy pro vývojáře.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Otázka: Mohu použít vlastní šifrovací klíče?
Odpověď: Ano, tato funkce je nyní k dispozici pro nové účty Cosmos a ta by se měla provádět v době vytváření účtu. Další informace najdete v dokumentu [spravované klíče zákazníka](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk) .

### <a name="q-what-regions-have-encryption-turned-on"></a>Otázka: co zapnutým šifrováním máte oblastech?
Odpověď: všechny oblasti Azure Cosmos DB mají šifrování zapnout pro všechna uživatelská data.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Otázka: šifrování mají vliv na výkon latenci a propustnost smluv SLA?
Odpověď: neexistuje žádný dopad nebo změny výkonu smlouvy o úrovni služeb teď, když je povolené šifrování v klidovém stavu pro všechny stávající i nové účty. Další informace o [smlouvu SLA pro službu Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) stránky zobrazíte nejnovější záruky.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Otázka: místní emulátor podporuje šifrování v klidovém stavu?
Odpověď: emulátor je samostatný nástroj pro vývoj/testování a nepoužívá služby správy klíčů, které používá spravované služby Cosmos DB. Naše doporučení je účelem zapnutí nástroje BitLocker na jednotkách, kde jsou ukládání citlivých emulátor testovací data. [Emulátor podporuje Změna výchozího datového adresáře](local-emulator.md) a rovněž použitím dobře známé umístění.

## <a name="next-steps"></a>Další kroky

Přehled zabezpečení Cosmos DB a nejnovější vylepšení najdete v tématu [zabezpečení databáze Azure Cosmos](database-security.md).
Další informace o certifikacích Microsoft najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).
