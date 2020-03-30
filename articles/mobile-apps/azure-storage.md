---
title: Cloudové úložiště pro vytváření vysoce zabezpečených, odolných a škálovatelných aplikací s Azure Storage
description: Přečtěte si o službách pro ukládání velkých strukturovaných a nestrukturovaných dat mobilních aplikací v cloudu.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240975"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Cloudové úložiště pro vysoce zabezpečené, odolné a škálovatelné aplikace s Azure Storage
[Azure Storage](https://azure.microsoft.com/services/storage/) je cloudové úložiště microsoftu pro moderní aplikace, které nabízí masivně škálovatelné úložiště objektů pro datové objekty, službu souborového systému pro cloud, úložiště pro zasílání zpráv pro spolehlivé zasílání zpráv a úložiště NoSQL. Azure Storage je:
- **Trvanlivé a vysoce dostupné:** Redundance zajišťuje, že vaše data jsou v bezpečí v případě selhání přechodného hardwaru. Můžete se také rozhodnout data replikovat napříč několika datacentry nebo geografickými oblastmi, abyste se ochránili i proti místní pohromě nebo přírodní katastrofě. Data replikovaná tímto způsobem zůstávají v případě nečekaného výpadku vysoce dostupná.
- **Zabezpečte:** Všechna data zapsaná do Služby Azure Storage je šifrovaná službou. Azure Storage poskytuje jemně odstupňované řízení přístupu k datům.
- **Škálovatelné:** Služby jsou navrženy tak, aby byly masivně škálovatelné tak, aby splňovaly požadavky na ukládání dat a výkon dnešních aplikací.
- **Spravováno:** Azure zpracovává údržbu hardwaru, aktualizace a kritické problémy za vás.
- **Přístupné:** Data jsou přístupná z libovolného místa na světě přes HTTP nebo HTTPS. Společnost Microsoft poskytuje klientské knihovny v různých jazycích, jako je například .NET, Java, Node.js, Python, PHP, Ruby a Go a starší rozhraní REST API. Skriptování je podporované v Azure PowerShellu nebo v Azure CLI. Portál Azure a Průzkumník úložiště Azure nabízejí jednoduchá vizuální řešení pro práci s vašimi daty.

Pomocí následujících služeb můžete povolit cloudové úložiště ve svých mobilních aplikacích.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) nabízí řešení úložiště objektů pro cloud. Úložiště objektů blob je optimalizované pro ukládání obrovského množství nestrukturovaných dat, která neodpovídají určitému datovému modelu nebo definici, jako je text nebo binární. Podporuje různé jazyky, které používají klientské knihovny. Úložiště objektů blob je navržené tak, aby:
- Podávejte obrázky nebo dokumenty přímo do prohlížeče.
- Uložte soubory pro distribuovaný přístup.
- Streamujte video a zvuk.
- Zapisovat do souborů protokolu.
- Uklápěte data pro zálohování a obnovení, zotavení po havárii a archivaci.
- Uklápěte data pro analýzu místní službou nebo službou hostovotou Azure.

**Odkazy**
- [Portál Azure](https://portal.azure.com)
- [Dokumentace službě Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction)
- [Rychlé starty](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [ukázky](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table storage](https://azure.microsoft.com/services/storage/tables/) je služba, která ukládá strukturovaná data NoSQL v cloudu a poskytuje úložiště klíčů nebo atributů s návrhem bez schématu. Služba Azure Table Storage ukládá velké objemy strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání z cloudu Azure i mimo něj. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat. Úložiště tabulek se obvykle používá k:
- Uklápěte terabajty strukturovaných dat schopných obsluhovat aplikace ve webovém měřítku.
- Ukládat datové sady, které nevyžadují složité spojení, cizí klíče nebo uložené procedury a mohou být denormalized pro rychlý přístup.
- Rychle dotazujte data pomocí clusterovaného indexu.
- Přístup k datům pomocí protokolu OData a linq dotazů s datovými službami .NET služby WCF (Windows Communication Foundation).

Úložiště tabulky můžete použít k ukládání a dotazování obrovské sady strukturovaných, nerelační data. Tabulky se škálují podle rostoucí poptávky.

**Odkazy**
- [Portál Azure](https://portal.azure.com)
- [Dokumentace k úložišti Azure Table](/azure/storage/tables/table-storage-overview)
- [ukázky](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Rychlé starty](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Soubory Azure
Pomocí [souborů Azure](https://azure.microsoft.com/services/storage/files/)můžete nastavit vysoce dostupné sdílené síťové sdílené složky, ke kterým lze přistupovat pomocí standardního protokolu SMB (Server Message Block). Více virtuálních počítačů může sdílet stejné soubory s přístupem pro čtení i zápis. Soubory můžete také číst pomocí rozhraní REST nebo knihoven klienta úložiště. K souborům můžete přistupovat z libovolného místa na světě pomocí adresy URL, která odkazuje na soubor a obsahuje token sdíleného přístupového podpisu (SAS). Můžete generovat tokeny SAS. Umožňují konkrétní přístup k soukromému majetku po určitou dobu.

Sdílené složky Azure lze použít k těmto činnostem:
- **Nahraďte nebo doplňte místní souborové servery:** Populární operační systémy, jako jsou Windows, macOS a Linux, můžou přímo připevnit sdílené složky Azure, ať jsou kdekoli na světě. Sdílené složky Azure lze také pomocí Synchronizace souborů Azure replikovat na místní nebo cloudové servery Windows Server. Tím se zajistí výkonné a distribuované ukládání dat do mezipaměti v místě, kde se tato data používají.
- **Zvedací a směřování:** Migrujte aplikace do cloudu, které očekávají, že sdílená složka uloží souborovou aplikaci nebo uživatelská data.
- **Zjednodušte vývoj cloudu:** Soubory Azure lze také použít v mnoha způsoby, jak zjednodušit nové projekty vývoje cloudu. Například:
    - **Nastavení sdílené aplikace:** Běžným vzorem pro distribuované aplikace je mít konfigurační soubory v centralizovaném umístění, kde jsou přístupné z mnoha instancí aplikace. Instance aplikace můžete načíst jejich konfiguraci prostřednictvím rozhraní API úložiště souborů. Uživatelé k nim mohou přistupovat podle potřeby připojením sdílené složky SMB místně.
    - **Diagnostický podíl:** Sdílená složka Azure je vhodné místo pro cloudové aplikace pro zápis jejich protokoly, metriky a výpisy stavu systému. Protokoly mohou být zapsány instancemi aplikace prostřednictvím rozhraní API SOUBORU REST. Vývojáři k nim mohou přistupovat připojením sdílené složky na místním počítači. Tato funkce umožňuje velkou flexibilitu. Vývojáři mohou přijmout vývoj cloudu, aniž by museli opustit stávající nástroje, které znají.

**Odkazy**
- [Portál Azure](https://portal.azure.com)
- [Dokumentace ke službě Azure Files](/azure/storage/files/storage-files-introduction)
- [Rychlé starty](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure Queue storage](https://azure.microsoft.com/services/storage/queues/) je služba pro ukládání velkého počtu zpráv. Přístup ke zprávám z libovolného místa na světě prostřednictvím ověřených volání pomocí protokolu HTTP nebo HTTPS. Zpráva fronty může mít velikost až 64 kB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytvoření nevyřízených položek práce pro zpracování asynchronně.

**Odkazy**
- [Portál Azure](https://portal.azure.com)
- [Dokumentace ke službě Azure Queue Storage](/azure/storage/queues/)
- [Rychlé starty](/azure/storage/queues/storage-quickstart-queues-portal)
- [ukázky](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
