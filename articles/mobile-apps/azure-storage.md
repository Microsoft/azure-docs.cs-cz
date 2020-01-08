---
title: Cloudové úložiště pro vytváření vysoce zabezpečených, odolných a škálovatelných aplikací pomocí Azure Storage
description: Přečtěte si o službách pro ukládání rozsáhlých strukturovaných a nestrukturovaných dat mobilní aplikace v cloudu.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 53754976a6e1f6d893cf7d6ab73d57c95dae193f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453254"
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
- [Azure Portal](https://portal.azure.com)
- [Dokumentace ke službě Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction)
- [Rychlé starty](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Ukázky](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) je služba, která ukládá strukturovaná NoSQL data v cloudu a poskytuje úložiště klíčů nebo atributů s návrhem bez schématu. Služba Azure Table Storage ukládá velké objemy strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání zevnitř i mimo cloud Azure. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat. Tabulka úložiště se obvykle používá pro:
- Ukládejte terabajty strukturovaných dat schopných obsluhovat webové aplikace.
- Ukládat datové sady, které nevyžadují složitá spojení, cizí klíče nebo uložené procedury a které je možné denormalizovat pro rychlý přístup.
- Rychlá dotazování dat pomocí clusterovaného indexu.
- Přístup k datům pomocí protokolu OData a dotazů LINQ s knihovnami Windows Communication Foundation (WCF) Data Services .NET.

Tabulkové úložiště můžete použít k ukládání a dotazování obrovských sad strukturovaných, nerelačních dat. Vaše tabulky se škálují jako zvýšení poptávky.

**Odkazy**
- [Azure Portal](https://portal.azure.com)
- [Dokumentace ke službě Azure Table Storage](/azure/storage/tables/table-storage-overview)
- [Ukázky](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Rychlé starty](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Pomocí služby [soubory Azure](https://azure.microsoft.com/services/storage/files/)můžete nastavit vysoce dostupné sdílené síťové složky, ke kterým je možné získat přístup pomocí standardního protokolu SMB (Server Message Block). Víc virtuálních počítačů může sdílet stejné soubory s přístupem pro čtení i zápis. Soubory můžete také číst pomocí rozhraní REST nebo klientských knihoven pro úložiště. K souborům můžete přistupovat odkudkoli na světě pomocí adresy URL, která odkazuje na soubor a zahrnuje token sdíleného přístupového podpisu (SAS). Můžete generovat tokeny SAS. Umožňují konkrétní přístup k privátnímu assetu po určitou dobu.

Sdílené složky Azure lze použít k těmto činnostem:
- **Nahrazení nebo doplnění místních souborových serverů:** Oblíbené operační systémy, jako je Windows, macOS a Linux, můžou přímo připojit sdílené složky Azure, ať jsou na světě. Sdílené složky Azure lze také pomocí Synchronizace souborů Azure replikovat na místní nebo cloudové servery Windows Server. Tím se zajistí výkonné a distribuované ukládání dat do mezipaměti v místě, kde se tato data používají.
- **Aplikace výtahu a posunutí:** Migrujte aplikace do cloudu, který očekává, že sdílená složka ukládá data do souboru aplikace nebo uživatele.
- **Zjednodušení vývoje pro Cloud:** Soubory Azure můžete také použít mnoha způsoby, jak zjednodušit nové projekty vývoje pro Cloud. Příklad:
    - **Nastavení sdílené aplikace:** Běžným vzorem pro distribuované aplikace je mít konfigurační soubory v centralizovaném umístění, kde k nim můžete přicházet z mnoha instancí aplikace. Instance aplikace mohou načíst svou konfiguraci pomocí REST API souboru. Uživatelé k nim mají přístup podle potřeby připojením sdílené složky SMB místně.
    - **Diagnostická sdílená složka:** Sdílená složka Azure je vhodným místem pro cloudové aplikace, které zapisují protokoly, metriky a výpisy stavu systému. Protokoly mohou být zapsány instancemi aplikace prostřednictvím souboru REST API. Vývojáři k nim mají přístup připojením sdílené složky na svém místním počítači. Tato funkce umožňuje skvělou flexibilitu. Vývojáři můžou využít vývoj v cloudu, aniž by museli opustit stávající nástroje, které znají.

**Odkazy**
- [Azure Portal](https://portal.azure.com)
- [Dokumentace ke službě soubory Azure](/azure/storage/files/storage-files-introduction)
- [Rychlé starty](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure Queue Storage](https://azure.microsoft.com/services/storage/queues/) je služba pro ukládání velkého počtu zpráv. Přístup ke zprávám odkudkoli na světě prostřednictvím ověřených volání pomocí protokolu HTTP nebo HTTPS. Zpráva fronty může mít velikost až 64 KB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytváření nevyřízených položek pro asynchronní zpracování.

**Odkazy**
- [Azure Portal](https://portal.azure.com)
- [Dokumentace ke službě Azure Queue Storage](/azure/storage/queues/)
- [Rychlé starty](/azure/storage/queues/storage-quickstart-queues-portal)
- [Ukázky](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
