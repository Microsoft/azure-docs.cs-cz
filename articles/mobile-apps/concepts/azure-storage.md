---
title: Cloudové úložiště pro vytváření vysoce zabezpečených, odolných a škálovatelných aplikací pomocí Azure Storage
description: Přečtěte si o službách pro ukládání rozsáhlých strukturovaných a nestrukturovaných dat mobilní aplikace v cloudu.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: e45726cfc54963c546274e0777dd0a10d89da72d
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2020
ms.locfileid: "84483314"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Cloudové úložiště pro vysoce zabezpečené, odolné a škálovatelné aplikace s Azure Storage
[Azure Storage](https://azure.microsoft.com/services/storage/) řešení cloudového úložiště Microsoftu pro moderní aplikace, které nabízí rozsáhle škálovatelné úložiště objektů pro datové objekty, službu systému souborů pro Cloud, úložiště pro zasílání zpráv pro spolehlivé zasílání zpráv a úložiště NoSQL. Azure Storage je:
- **Odolné a vysoce dostupné:** Redundance zajišťuje, že vaše data jsou v případě přechodných hardwarových selhání bezpečná. Můžete se také rozhodnout data replikovat napříč několika datacentry nebo geografickými oblastmi, abyste se ochránili i proti místní pohromě nebo přírodní katastrofě. Data replikovaná tímto způsobem zůstávají v případě nečekaného výpadku vysoce dostupná.
- **Zabezpečení:** Všechna data zapsaná do Azure Storage jsou službou šifrována. Azure Storage poskytuje jemně odstupňované řízení přístupu k datům.
- **Škálovatelné:** Služby jsou navržené tak, aby splňovaly požadavky na úložiště dat a výkon pro dnešní aplikace.
- **Spravované:** Azure zpracovává údržbu, aktualizace a kritické problémy s hardwarem.
- K **dispozici:** Data jsou přístupná odkudkoli na světě přes protokol HTTP nebo HTTPS. Microsoft poskytuje klientské knihovny v nejrůznějších jazycích, jako jsou .NET, Java, Node. js, Python, PHP, Ruby a REST API. V Azure PowerShell nebo rozhraní příkazového řádku Azure se podporuje skriptování. Azure Portal a Průzkumník služby Azure Storage nabízejí jednoduchá vizuální řešení pro práci s daty.

K povolení cloudového úložiště v mobilních aplikacích použijte následující služby.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) nabízí řešení úložiště objektů pro Cloud. Úložiště objektů BLOB je optimalizované pro ukládání velkých objemů nestrukturovaných dat, která nevyhovují konkrétnímu datovému modelu nebo definici, jako je například text nebo binární. Podporuje různé jazyky, které používají klientské knihovny. BLOB Storage je navržený tak, aby:
- Slouží k obsluze obrázků nebo dokumentů přímo do prohlížeče.
- Ukládání souborů pro distribuovaný přístup
- Streamování videa a zvuku
- Zapisovat do souborů protokolu.
- Ukládat data pro zálohování a obnovení, zotavení po havárii a archivaci.
- Data pro analýzu můžete ukládat pomocí místní služby nebo služby hostované v Azure.

**Odkazy**
- [portál Azure](https://portal.azure.com)
- [Dokumentace službě Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction)
- [Rychlé starty](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [ukázky](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) je služba, která ukládá strukturovaná NoSQL data v cloudu a poskytuje úložiště klíčů nebo atributů s návrhem bez schématu. Služba Azure Table Storage ukládá velké objemy strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání zevnitř i mimo cloud Azure. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat. Tabulka úložiště se obvykle používá pro:
- Ukládejte terabajty strukturovaných dat schopných obsluhovat webové aplikace.
- Ukládat datové sady, které nevyžadují složitá spojení, cizí klíče nebo uložené procedury a které je možné denormalizovat pro rychlý přístup.
- Rychlá dotazování dat pomocí clusterovaného indexu.
- Přístup k datům pomocí protokolu OData a dotazů LINQ s knihovnami Windows Communication Foundation (WCF) Data Services .NET.

Tabulkové úložiště můžete použít k ukládání a dotazování obrovských sad strukturovaných, nerelačních dat. Vaše tabulky se škálují jako zvýšení poptávky.

**Odkazy**
- [portál Azure](https://portal.azure.com)
- [Dokumentace ke službě Azure Table Storage](/azure/storage/tables/table-storage-overview)
- [ukázky](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Rychlé starty](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure Queue Storage](https://azure.microsoft.com/services/storage/queues/) je služba pro ukládání velkého počtu zpráv. Přístup ke zprávám odkudkoli na světě prostřednictvím ověřených volání pomocí protokolu HTTP nebo HTTPS. Zpráva fronty může mít velikost až 64 KB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytváření nevyřízených položek pro asynchronní zpracování.

**Odkazy**
- [portál Azure](https://portal.azure.com)
- [Dokumentace ke službě Azure Queue Storage](/azure/storage/queues/)
- [Rychlé starty](/azure/storage/queues/storage-quickstart-queues-portal)
- [Ukázky](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
