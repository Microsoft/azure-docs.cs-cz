---
title: Šifrování v klidovém stavu ve službě Azure Cosmos DB
description: Zjistěte, jak službu Azure Cosmos DB poskytuje šifrování neaktivních uložených dat a jak je implementován.
author: rafats
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 308e5f829425741b4fbef3eff6738f8c95dca97f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471262"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Šifrování dat ve službě Azure Cosmos DB 

Šifrování v klidovém stavu je frázi, která se běžně používá k šifrování dat na zařízeních stálé úložiště, jako je jednotkami SSD (Solid-State Drive) a pevných disků (HDD). Cosmos DB ukládá své primární databáze na jednotkách SSD. Jeho přílohy média a zálohy jsou uloženy v Azure Blob storage, která je obecně zálohovat pevných discích. S vydáním šifrování v klidovém stavu pro službu Cosmos DB všechny databáze, přílohy média a zálohy zašifrují. Vaše data teď šifrují během přenosu (v síti) a v klidovém stavu (stálé úložiště), získáte-koncové šifrování.

Je velmi snadno se používá služba PaaS, Cosmos DB. Vzhledem k tomu, že všechna uživatelská data uložená ve službě Cosmos DB je v klidovém stavu a přenosu šifrovaný, není nutné provádět žádnou akci. Dalším způsobem, jak to put je, že je "na" ve výchozím nastavení šifrování v klidovém stavu. Neexistují žádná opatření můžete zapnout nebo vypnout. Tato funkce zajišťuje My zatím budeme pokračovat podle našich [SLA o dostupnosti a výkonu](https://azure.microsoft.com/support/legal/sla/cosmos-db).

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

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>DOTAZ: Jak Azure Storage stát Pokud je povolené šifrování služby Storage?
Odpověď: Se neúčtují žádné další poplatky.

### <a name="q-who-manages-the-encryption-keys"></a>DOTAZ: Kdo spravuje šifrovací klíče?
Odpověď: Klíče jsou spravované microsoftem.

### <a name="q-how-often-are-encryption-keys-rotated"></a>DOTAZ: Jak často jsou otočeny šifrovací klíče?
Odpověď: Microsoft má sadu interní pokyny pro rotaci klíčů šifrování, který následuje po Cosmos DB. Konkrétní pokyny nejsou publikovány. Publikování Microsoft [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), která je zobrazena jako podmnožinu interní pokyny a obsahuje užitečné osvědčené postupy pro vývojáře.

### <a name="q-can-i-use-my-own-encryption-keys"></a>DOTAZ: Můžete použít vlastní šifrovací klíče?
Odpověď: Cosmos DB je služba, PaaS a jsme intenzivně pracovala na udržet službu snadno používá. Všimli jsme si, že je tento dotaz pokládán často jako dotaz proxy pro splnění požadavků dodržování předpisů jako PCI-DSS. Jako součást vytváření tuto funkci jsme ve spolupráci s auditory dodržování předpisů k zajištění, že zákazníci, kteří používají služby Cosmos DB vyhovuje jejich požadavkům na bez nutnosti provádět správu samotných kláves.

### <a name="q-what-regions-have-encryption-turned-on"></a>DOTAZ: Jaké oblasti obracejí šifrování?
Odpověď: Všechny oblasti Azure Cosmos DB mají šifrování zapnout pro všechna uživatelská data.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>DOTAZ: Funkce šifrování ovlivní výkon latenci a propustnost smluv SLA?
Odpověď: Neexistuje žádný dopad nebo změny výkonu smlouvy o úrovni služeb teď, když je povolené šifrování v klidovém stavu pro všechny stávající i nové účty. Další informace o [smlouvu SLA pro službu Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) stránky zobrazíte nejnovější záruky.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>DOTAZ: Podporuje místní emulátor šifrování v klidovém stavu?
Odpověď: Emulátor je samostatný nástroj pro vývoj/testování a nepoužívá služby správy klíčů, které používá spravované služby Cosmos DB. Naše doporučení je účelem zapnutí nástroje BitLocker na jednotkách, kde jsou ukládání citlivých emulátor testovací data. [Emulátor podporuje Změna výchozího datového adresáře](local-emulator.md) a rovněž použitím dobře známé umístění.

## <a name="next-steps"></a>Další postup

Přehled zabezpečení služby Cosmos DB a nejnovější vylepšení naleznete v tématu [zabezpečení databáze Azure Cosmos DB](database-security.md).
Další informace o certifikacích Microsoft najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).
