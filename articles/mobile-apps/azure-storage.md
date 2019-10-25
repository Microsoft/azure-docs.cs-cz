---
title: Cloudové úložiště pro vytváření vysoce zabezpečených, odolných a škálovatelných aplikací pomocí Azure Storage
description: Přečtěte si o službách pro ukládání rozsáhlých strukturovaných a nestrukturovaných dat mobilní aplikace v cloudu.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 01407f843ef36095fc87feb3722e85dc477ad8bb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795650"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Cloudové úložiště pro vysoce zabezpečené, odolné a škálovatelné aplikace s Azure Storage
[Azure Storage](https://azure.microsoft.com/services/storage/) řešení cloudového úložiště Microsoftu pro moderní aplikace, které nabízí rozsáhle škálovatelné úložiště objektů pro datové objekty, službu systému souborů pro Cloud, úložiště pro zasílání zpráv pro spolehlivé zasílání zpráv a úložiště NoSQL. Azure Storage je:
- **Trvalá a vysoká dostupnost** – redundance zajišťuje, že vaše data jsou v případě přechodných hardwarových selhání bezpečná. Můžete se také rozhodnout data replikovat napříč několika datacentry nebo geografickými oblastmi, abyste se ochránili i proti místní pohromě nebo přírodní katastrofě. Data replikovaná tímto způsobem zůstávají v případě nečekaného výpadku vysoce dostupná.
- **Zabezpečení** – všechna data zapsaná do Azure Storage jsou službou šifrována. Azure Storage poskytuje jemně odstupňované řízení přístupu k datům.
- **Škálovatelné** – služby jsou navržené tak, aby splňovaly požadavky na úložiště dat a výkon pro dnešní aplikace.
- **Spravované** – Azure zpracovává údržbu, aktualizace a kritické problémy s hardwarem.
- Data jsou **přístupná** odkudkoli na světě přes protokol HTTP nebo HTTPS. Společnost Microsoft poskytuje klientské knihovny v nejrůznějších jazycích, jako jsou například .NET, Java, Node. js, Python, PHP, Ruby, přejít a další, a také vyspělé REST API. Skriptování je podporované v Azure PowerShell nebo Azure CLI a Azure Portal a Průzkumník služby Azure Storage nabízí jednoduchá vizuální řešení pro práci s daty.

K povolení cloudového úložiště v mobilních aplikacích použijte následující služby.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) nabízí řešení úložiště objektů pro Cloud a je optimalizované pro ukládání obrovských objemů nestrukturovaných dat, která neodpovídají konkrétnímu datovému modelu nebo definici, jako je například text nebo binární. Podporuje klientské knihovny v různých jazycích a je navržená pro:
- Poskytování obrázků nebo dokumentů přímo do prohlížeče
- Ukládání souborů pro distribuovaný přístup
- Streamování videa a zvuku
- Zápis do souborů protokolů
- Ukládání dat pro zálohování a obnovování, zotavení po havárii a pro archivaci
- Ukládání dat, které bude analyzovat místní nebo v Azure hostovaná služba

**Odkazy**
- [Azure Portal](https://portal.azure.com)
- [Nápovědě](/azure/storage/blobs/storage-blobs-introduction)
- [Rychlé starty](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Ukázky](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) je služba, která ukládá strukturovaná NoSQL data v cloudu a poskytuje úložiště klíčů a atributů s návrhem bez schématu. Služba Azure Table Storage ukládá velké objemy strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání zevnitř i mimo cloud Azure. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat. Mezi běžná použití služby Table Storage patří:
- Ukládá se služba TBs strukturovaných dat schopných obsluhovat webové aplikace.
- Ukládání datových sad, které nevyžadují složitá spojení, cizí klíče nebo uložené procedury a které je možné denormalizovat pro rychlý přístup.
- Rychlá dotazování na data pomocí clusterovaného indexu.
- Přístup k datům pomocí protokolu OData a dotazů LINQ s knihovnami .NET datové služby WCF.

Službu Table Storage můžete použít k ukládání a dotazování obrovských sad strukturovaných, nerelačních dat a vaše tabulky se budou s rostoucími požadavky škálovat.

**Odkazy**
- [Azure Portal](https://portal.azure.com)
- [Nápovědě](/azure/storage/tables/table-storage-overview)
- [Ukázky](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Rychlé starty](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Služba [Soubory Azure](https://azure.microsoft.com/services/storage/files/) umožňuje nastavit vysoce dostupné sdílené složky souborů sítě, ke kterým je možný přístup pomocí standardního protokolu SMB (Server Message Block). Víc virtuálních počítačů může sdílet stejné soubory s přístupem pro čtení i zápis. Soubory můžete číst také pomocí rozhraní REST nebo klientských knihoven pro úložiště. K souborům můžete přistupovat odkudkoli na světě pomocí adresy URL, která odkazuje na soubor a zahrnuje token sdíleného přístupového podpisu (SAS). Můžete generovat tokeny SAS, které po určitou dobu umožňují specifický přístup k privátním prostředkům.

Sdílené složky Azure lze použít k těmto činnostem:
- Nahrazení nebo doplnění místních souborových serverů: oblíbené operační systémy, jako je Windows, macOS a Linux, můžou přímo připojit sdílené složky Azure, ať jsou na světě. Sdílené složky Azure lze také pomocí Synchronizace souborů Azure replikovat na místní nebo cloudové servery Windows Server. Tím se zajistí výkonné a distribuované ukládání dat do mezipaměti v místě, kde se tato data používají.
- **Nazvednutí a posunutí aplikací** do cloudu, který očekává, že sdílená složka ukládá data aplikace nebo uživatelská data.
- **Zjednodušení vývoje pro Cloud**: pomocí souborů Azure je možné také v mnoha ohledech zjednodušit nové projekty vývoje pro Cloud. Například:
    - Nastavení sdílené aplikace: běžným vzorem pro distribuované aplikace je mít konfigurační soubory v centralizovaném umístění, kde je lze použít z mnoha instancí aplikace. Instance aplikací můžou načítat vlastní konfiguraci prostřednictvím souborového rozhraní REST API a uživatelé k nim můžou podle potřeby přistupovat připojením sdílené složky SMB v místním prostředí.
    - Diagnostická sdílená složka: sdílená složka Azure je vhodným místem pro cloudové aplikace, které zapisují protokoly, metriky a výpisy stavu systému. Instance aplikací můžou zapisovat protokoly prostřednictvím souborového rozhraní REST API a vývojáři k nim můžou přistupovat připojením sdílené složky na svém místním počítači. To umožňuje velkou flexibilitu, protože se vývojáři můžou pustit do vývoje pro cloud bez nutnosti opouštět stávající nástroje, které znají a mají rádi.

**Odkazy**
- [Azure Portal](https://portal.azure.com)
- [Nápovědě](/azure/storage/files/storage-files-introduction)
- [Rychlé starty](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queues"></a>Fronty Azure
[Azure Queue Storage](https://azure.microsoft.com/services/storage/queues/) je služba pro ukládání velkého počtu zpráv. Přístup ke zprávám odkudkoli na světě prostřednictvím ověřených volání pomocí protokolu HTTP nebo HTTPS. Zpráva fronty může mít velikost až 64 KB a fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytváření nevyřízených položek pro asynchronní zpracování.

**Odkazy**
- [Azure Portal](https://portal.azure.com)
- [Nápovědě](/azure/storage/queues/)
- [Rychlé starty](/azure/storage/queues/storage-quickstart-queues-portal)
- [Ukázky](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
