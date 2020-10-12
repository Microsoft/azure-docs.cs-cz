---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67673284"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurace aplikace pro přístup k Azure Storage
Existují dva způsoby, jak aplikaci ověřit pro přístup ke službám úložiště:

* Shared Key: použijte sdílený klíč pouze pro účely testování
* Sdílený přístupový podpis (SAS): použití SAS pro produkční aplikace

### <a name="shared-key"></a>Sdílený klíč
Ověřování pomocí sdíleného klíče znamená, že vaše aplikace bude k přístupu ke službám úložiště používat název vašeho účtu a klíč účtu. Pro účely rychlého zobrazení této knihovny budeme používat ověřování pomocí sdíleného klíče v tomto Začínáme.

> [!WARNING] 
> **Ověřování pomocí sdíleného klíče používejte jenom pro účely testování.** Váš název účtu a klíč účtu, který umožňuje úplný přístup pro čtení i zápis k přidruženému účtu úložiště, se distribuuje každému uživateli, který stáhne vaši aplikaci. Nejedná **se o dobrý** postup při ohrožení bezpečnosti klíče nedůvěryhodnými klienty.
> 
> 

Pokud používáte ověřování pomocí sdíleného klíče, vytvoří se [připojovací řetězec](../articles/storage/common/storage-configure-connection-string.md). Připojovací řetězec se skládá z těchto:  

* **DefaultEndpointsProtocol** – můžete zvolit protokol HTTP nebo HTTPS. Používání protokolu HTTPS se ale důrazně doporučuje.
* **Název účtu** – název účtu úložiště
* **Klíč účtu** – na webu [Azure Portal](https://portal.azure.com)přejděte do svého účtu úložiště a kliknutím na ikonu **klíče** tyto informace vyhledejte.
* Volitelné **EndpointSuffix** – používá se pro služby úložiště v oblastech s různými příponami koncových bodů, jako je například Azure Čína nebo Azure zásad správného řízení.

Tady je příklad připojovacího řetězce s použitím ověřování pomocí sdíleného klíče:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Sdílené přístupové podpisy (SAS)
U mobilní aplikace je doporučeným způsobem, jak ověřit požadavek klienta na službu Azure Storage, použití sdíleného přístupového podpisu (SAS). Pomocí SAS můžete klientovi udělit přístup k prostředku po určitou dobu a zadanou sadu oprávnění.
Jako vlastník účtu úložiště budete muset vygenerovat SAS pro mobilní klienty, kteří budou využívat. Pokud chcete vygenerovat SAS, budete pravděpodobně chtít napsat samostatnou službu, která generuje SAS pro distribuci do vašich klientů. Pro účely testování můžete k vygenerování SAS použít [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com) nebo [Azure Portal](https://portal.azure.com) . Při vytváření SAS můžete zadat časový interval, za který je SAS platný, a oprávnění, která SAS udělí klientovi.

Následující příklad ukazuje, jak použít Průzkumník služby Microsoft Azure Storage k vygenerování SAS.

1. Pokud jste to ještě neudělali, [nainstalujte Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com)
2. Připojte se ke svému předplatnému.
3. Klikněte na svůj účet úložiště a v levém dolním rohu klikněte na kartu akce. Kliknutím na načíst sdílený přístupový podpis vygenerujte připojovací řetězec pro váš SAS.
4. Tady je příklad připojovacího řetězce SAS, který uděluje oprávnění ke čtení a zápisu na úrovni služby, kontejneru a objektu pro službu BLOB účtu úložiště.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Jak vidíte, při použití SAS nezveřejňujete klíč účtu ve vaší aplikaci. Další informace o SAS a osvědčených postupech pro použití SAS získáte, když rezervujete [sdílené přístupové podpisy: Principy modelu SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

