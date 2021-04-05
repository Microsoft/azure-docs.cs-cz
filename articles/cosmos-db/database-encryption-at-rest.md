---
title: Šifrování v klidovém Azure Cosmos DB
description: Přečtěte si, jak Azure Cosmos DB poskytuje šifrování neaktivních dat a jejich implementaci.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: abe4bb3a53e3177857cb8041fd255ec916985054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93089861"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Šifrování dat ve službě Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Šifrování v klidovém stavu je slovní spojení, které běžně označuje šifrování dat na zařízeních s nestálým úložištěm, jako jsou jednotky SSD (Solid State Drive) a jednotky pevného disku (HDD). Cosmos DB ukládá své primární databáze na SSD. Jeho přílohy a zálohy médií jsou uložené ve službě Azure Blob Storage, která je obecně zálohovaná pomocí HDD. S vydáním šifrování v klidovém stavu pro Cosmos DB se šifrují všechny vaše databáze, přílohy médií a zálohy. Vaše data se teď šifrují při přenosu (přes síť) a v klidovém stavu (nestálá úložiště), což vám dává ucelené šifrování.

Jako služba PaaS je Azure Cosmos DB velmi snadné použít. Vzhledem k tomu, že všechna uživatelská data uložená v Azure Cosmos DB jsou šifrována v klidovém stavu a v přenosu, není nutné provádět žádnou akci. Dalším způsobem, jak to dát, je toto šifrování v klidovém stavu "zapnuto". Neexistují žádné ovládací prvky, které by bylo možné vypnout nebo zapnout. Azure Cosmos DB používá šifrování AES-256 ve všech oblastech, kde je účet spuštěný. Tuto funkci poskytujeme, i když budeme nadále vyhovovat naší [SLA dostupnosti a výkonu](https://azure.microsoft.com/support/legal/sla/cosmos-db). Data uložená v účtu Azure Cosmos se automaticky a hladce šifrují pomocí klíčů spravovaných Microsoftem (klíče spravované službou). Volitelně můžete zvolit přidání druhé vrstvy šifrování s vlastními klíči, jak je popsáno v článku [klíče spravované zákazníkem](how-to-setup-cmk.md) .

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementace šifrování v klidovém umístění pro Azure Cosmos DB

Šifrování v klidovém stavu se implementuje pomocí řady bezpečnostních technologií, včetně zabezpečených systémů úložiště klíčů, šifrovaných sítí a kryptografických rozhraní API. Systémy, které dešifrují a zpracovávají data, musí komunikovat se systémy, které spravují klíče. Diagram znázorňuje, jak se oddělí úložiště šifrovaných dat a Správa klíčů. 

:::image type="content" source="./media/database-encryption-at-rest/design-diagram.png" alt-text="Diagram návrhu" border="false":::

Základní tok požadavku uživatele je následující:
- Účet uživatelské databáze je připravený a klíče úložiště se načtou prostřednictvím žádosti poskytovatele prostředků služby správy.
- Uživatel vytvoří připojení k Cosmos DB prostřednictvím protokolu HTTPS nebo zabezpečeného přenosu. (Sady SDK zaabstraktují podrobnosti.)
- Uživatel pošle dokument JSON, který se uloží v dříve vytvořeném zabezpečeném připojení.
- Dokument JSON je indexovaný, pokud uživatel nevypnul indexování.
- Dokument JSON a data indexu se zapisují do zabezpečeného úložiště.
- Data se pravidelně čtou ze zabezpečeného úložiště a zálohují do úložiště objektů BLOB v Azure Encrypted.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>Otázka: kolik Azure Storage náklady, pokud je Šifrování služby Storage povoleno?
Odpověď: nejsou žádné další náklady.

### <a name="q-who-manages-the-encryption-keys"></a>Otázka: kdo spravuje šifrovací klíče?
Odpověď: klíče spravuje Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>Otázka: jak často jsou šifrovací klíče otočeny?
Odpověď: Microsoft obsahuje sadu vnitřních pokynů pro rotaci šifrovacích klíčů, které Cosmos DB následující. Konkrétní pokyny nejsou publikovány. Společnost Microsoft zveřejňuje prostředí [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx), které se zobrazuje jako podmnožina interních pokynů, a má užitečné osvědčené postupy pro vývojáře.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Otázka: můžu použít vlastní šifrovací klíče?
Odpověď: Ano, tato funkce je nyní k dispozici pro nové účty Azure Cosmos DB a ta by se měla provést v době vytváření účtu. Další informace najdete v dokumentu [klíčů spravovaných zákazníkem](./how-to-setup-cmk.md) .

### <a name="q-what-regions-have-encryption-turned-on"></a>Otázka: u kterých oblastí je šifrování zapnuté?
O: všechny oblasti Azure Cosmos DB mají zapnuté šifrování pro všechna uživatelská data.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Otázka: má šifrování vliv na latenci a propustnost SLA?
Odpověď: neexistuje žádný dopad ani změny SLA výkonu nyní, pokud je povolené šifrování v klidovém umístění pro všechny existující a nové účty. Můžete si přečíst další informace na stránce [SLA pro Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) , kde najdete nejnovější záruky.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Otázka: místní emulátor podporuje šifrování v klidovém prostředí?
Odpověď: emulátor je samostatný nástroj pro vývoj a testování a nepoužívá služby správy klíčů, které používá spravovaná služba Cosmos DB. Naším doporučením je povolit nástroj BitLocker na jednotkách, kde ukládáte data citlivých testů pro emulátor. [Emulátor podporuje změnu výchozího datového adresáře](local-emulator.md) a použití dobře známého umístění.

## <a name="next-steps"></a>Další kroky

* Druhou vrstvu šifrování můžete přidat pomocí vlastních klíčů. Další informace najdete v článku [klíče spravované zákazníkem](how-to-setup-cmk.md) .
* Přehled zabezpečení Cosmos DB a nejnovější vylepšení najdete v tématu [zabezpečení databáze Azure Cosmos](database-security.md).
* Další informace o certifikaci Microsoftu najdete v [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).