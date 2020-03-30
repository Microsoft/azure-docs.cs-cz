---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673284"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurace aplikace pro přístup k Azure Storage
Existují dva způsoby ověření aplikace pro přístup ke službám úložiště:

* Sdílený klíč: Použití sdíleného klíče pouze pro účely testování
* Sdílený přístupový podpis (SAS): Použití SAS pro produkční aplikace

### <a name="shared-key"></a>Sdílený klíč
Ověřování pomocí sdíleného klíče znamená, že vaše aplikace použije název účtu a klíč účtu pro přístup ke službám úložiště. Pro účely rychlého zobrazení způsobu použití této knihovny použijeme ověřování pomocí sdíleného klíče v tomto již začínají.

> [!WARNING] 
> **Ověřování pomocí sdíleného klíče používejte pouze pro účely testování!** Název účtu a klíč účtu, které poskytují úplný přístup pro čtení a zápis do přidruženého účtu úložiště, budou distribuovány všem osobám, které si stáhnou vaši aplikaci. To **to není** dobrý postup, protože riskujete, že váš klíč ohroženne nedůvěryhodnými klienty.
> 
> 

Při použití ověřování pomocí sdíleného klíče vytvoříte [připojovací řetězec](../articles/storage/common/storage-configure-connection-string.md). Připojovací řetězec se skládá z:  

* **DefaultEndpointsProtocol** - můžete zvolit HTTP nebo HTTPS. Použití protokolu HTTPS je však vysoce doporučeno.
* **Název účtu** – název vašeho účtu úložiště
* **Klíč účtu** – na [portálu Azure](https://portal.azure.com), přejděte na svůj účet úložiště a kliknutím na ikonu **Klíče** najděte tyto informace.
* (Nepovinné) **EndpointSuffix** – používá se pro služby úložiště v oblastech s různými příponami koncových bodů, jako je Azure China nebo Azure Governance.

Zde je příklad připojovacího řetězce pomocí ověřování pomocí sdíleného klíče:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Sdílené přístupové podpisy (SAS)
Pro mobilní aplikaci je doporučená metoda ověřování požadavku klienta proti službě Azure Storage pomocí sdíleného přístupového podpisu (SAS). SAS umožňuje udělit klientovi přístup k prostředku po určitou dobu se zadanou sadou oprávnění.
Jako vlastník účtu úložiště budete muset vygenerovat SAS, aby je mobilní klienti spotřebovali. Chcete-li generovat SAS, budete pravděpodobně chtít napsat samostatnou službu, která generuje SAS, které mají být distribuovány vašim klientům. Pro účely testování můžete ke generování SAS použít [Průzkumník úložiště Microsoft Azure](https://storageexplorer.com) nebo Portál [Azure.](https://portal.azure.com) Při vytváření SAS můžete určit časový interval, přes který je SAS platný a oprávnění, která SAS uděluje klientovi.

Následující příklad ukazuje, jak pomocí Průzkumníka úložiště Microsoft Azure generovat SAS.

1. Pokud jste tak ještě [neučinili, nainstalujte Průzkumníka úložiště Microsoft Azure](https://storageexplorer.com)
2. Připojte se ke svému předplatnému.
3. Klikněte na svůj účet úložiště a klikněte na kartu "Akce" v levém dolním rohu. Chcete-li pro sas vygenerovat "připojovací řetězec", klepněte na tlačítko Získat sdílený přístupový podpis.
4. Tady je příklad připojovacího řetězce SAS, který uděluje oprávnění ke čtení a zápisu na úrovni služby, kontejneru a objektu pro službu objektu blob účtu účtu úložiště.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Jak můžete vidět, při použití SAS, nejste vystavení klíče účtu v aplikaci. Další informace o SAS a osvědčených postupech pro používání SAS najdete v části [Sdílené přístupové podpisy: Principy modelu SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

