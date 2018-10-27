---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 6911e06dc023027ab32b99387b9f7d3f5e708f86
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165561"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurace aplikace pro přístup k úložišti Azure
Existují dva způsoby, jak ověřování vaší aplikace pro přístup ke službám úložiště:

* Sdílený klíč: Sdílený klíč použít pouze pro účely testování
* Sdílený přístupový podpis (SAS): Použití SAS aplikacích v produkčním prostředí

### <a name="shared-key"></a>Sdílený klíč
Ověření pomocí sdíleného klíče, znamená, že aplikace použije název vašeho účtu a klíč účtu pro přístup ke službám úložiště. Pro účely rychle ukazující, jak použít tuto knihovnu jsme použít ověření sdíleným klíčem v této příručce Začínáme.

> [!WARNING] 
> **Pouze pro účely testování používejte ověření sdíleným klíčem!** Název účtu a klíč účtu, které poskytují přístup k úplné čtení a zápis do přidruženého účtu úložiště, budou distribuována do konkrétního uživatele, který stahuje vaší aplikace. Toto je **není** dobrého praxi jako riskujete, že váš klíč, dojde k ohrožení bezpečnosti nedůvěryhodné klienty.
> 
> 

Pokud používáte ověřování pomocí sdíleného klíče, vytvoříte [připojovací řetězec](../articles/storage/common/storage-configure-connection-string.md). Připojovací řetězec se skládá z:  

* **DefaultEndpointsProtocol** – můžete použít protokol HTTP nebo HTTPS. Nicméně pomocí protokolu HTTPS je důrazně doporučujeme.
* **Název účtu** – název účtu úložiště
* **Klíč účtu** – na [webu Azure Portal](https://portal.azure.com), přejděte do svého účtu úložiště a klikněte na tlačítko **klíče** ikonu jak najít tyto informace.
* (Volitelné) **EndpointSuffix** – používá se pro služby storage v oblastech s jinému koncovému bodu přípony, jako je například Azure China nebo Azure zásad správného řízení.

Tady je příklad připojovacího řetězce ověření pomocí sdíleného klíče:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Sdílené přístupové podpisy (SAS)
Mobilní aplikace je doporučeným způsobem ověření požadavku pro klienta pro službu Azure Storage s použitím sdílený přístupový podpis (SAS). SAS můžete udělit přístup klienta k prostředku pro zadaný časový úsek, s konkrétní sadou oprávnění.
Jako vlastník účtu úložiště budete muset vygenerovat SAS pro vaši mobilní klienti využívat. Generovat SAS, budete pravděpodobně chtít zápisu samostatná služba, která generuje SAS pro distribuci do vašim klientům. Pro účely testování můžete použít [Microsoft Azure Storage Explorer](http://storageexplorer.com) nebo [webu Azure Portal](https://portal.azure.com) SAS můžete vygenerovat. Když vytvoříte SAS, můžete zadat časový interval nad tím, které je podpis platný a oprávnění, která uděluje SAS ke klientovi.

Následující příklad ukazuje, jak generovat SAS pomocí Průzkumníka služby Microsoft Azure Storage.

1. Pokud jste tak dosud neučinili, [instalace Průzkumníka služby Microsoft Azure Storage](http://storageexplorer.com)
2. Připojte se ke svému předplatnému.
3. Klikněte na tlačítko na vašem účtu úložiště a klikněte na kartu "Akce" vlevo dole. Klikněte na tlačítko "Získat sdílený přístupový podpis" se vygenerovat řetězec"připojení" pro váš SAS.
4. Tady je příklad připojovacího řetězce SAS, že uděluje oprávnění čtení a zápis na úrovni objektu služby blob service účtu úložiště pro službu kontejneru a.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Jak vidíte, při použití SAS, nevystavujete klíč vašeho účtu ve vaší aplikaci. Další informace o SAS a osvědčené postupy pro pomocí SAS můžete najít [sdílené přístupové podpisy: vysvětlení modelu SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

