---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175225"
---
Pokud máte adresu URL sdíleného přístupového podpisu (SAS), která vám uděluje přístup k prostředkům v účtu úložiště, můžete použít SAS v připojovacím řetězci. Vzhledem k tomu, že SAS obsahuje informace potřebné k ověření žádosti, připojovací řetězec s SAS poskytuje protokol, koncový bod služby a nezbytné přihlašovací údaje pro přístup k prostředku.

Pokud chcete vytvořit připojovací řetězec, který obsahuje sdílený přístupový podpis, zadejte řetězec v následujícím formátu:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Každý koncový bod služby je nepovinný, přestože připojovací řetězec musí obsahovat aspoň jeden.

> [!NOTE]
> Jako osvědčený postup se doporučuje používat protokol HTTPS s SAS.
>
> Pokud zadáváte SAS v připojovacím řetězci v konfiguračním souboru, možná budete muset v adrese URL zakódovat speciální znaky.
>
>

### <a name="service-sas-example"></a>Příklad SAS služby
Tady je příklad připojovacího řetězce, který obsahuje SAS služby pro úložiště objektů BLOB:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

A zde je příklad stejného připojovacího řetězce s kódováním speciálních znaků:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Příklad SAS účtu
Tady je příklad připojovacího řetězce, který obsahuje SAS účtu pro objekty BLOB a úložiště souborů. Všimněte si, že jsou zadané koncové body pro obě služby:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

A tady je příklad stejného připojovacího řetězce s kódováním adresy URL:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

