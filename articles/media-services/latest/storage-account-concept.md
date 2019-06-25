---
title: Nahrávání a úložiště pomocí Azure Media Services v cloudu | Dokumentace Microsoftu
description: Tento článek cloudu nahrávání a koncepty úložiště.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/11/2019
ms.author: juliako
ms.openlocfilehash: 9cbb995eb3310a2263185d6fd6dba20efce37f38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65550148"
---
# <a name="cloud-upload-and-storage"></a>Nahrávání na cloud a úložiště

Pokud chcete začít, správa, šifrování, kódování, analýza a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. 

Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Doporučujeme, aby používaly účty úložiště ve stejném umístění jako účet Media Services, aby se zabránilo další latenci a data náklady na odchozí přenosy

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). 

>[!NOTE]
> Účty jen pro objekty blob nejsou povolené jako **primární**. 

Doporučujeme používat účty GPv2, takže můžete využít vybrat mezi horkou a studenou úrovní úložiště. Další informace o účtech úložiště najdete v tématu [přehled účtu Azure Storage](../../storage/common/storage-account-overview.md). 

Existují různé skladové položky, které můžete použít pro váš účet úložiště. Další informace najdete v tématu [účty úložiště](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Pokud chcete experimentovat s účty úložiště, použijte `--sku Standard_LRS`. Ale při výběru SKU pro produkční prostředí byste měli zvážit, `--sku Standard_RAGRS`, která poskytuje geografické replikace zajišťuje nepřetržitý chod podniků. 

## <a name="assets-in-a-storage-account"></a>Prostředky v účtu úložiště

V Media Services v3 rozhraní API úložiště slouží k nahrání souborů do prostředků. Další informace najdete v tématu [prostředky koncept](assets-concept.md).

> [!Note]
> By se neměly pokoušet ke změně obsahu kontejnery objektů blob, které byly generovány pomocí sady Media Services SDK bez použití rozhraní API služby Media Services.
 
## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

K ochraně vašich prostředků v klidovém stavu, prostředky by se měla šifrovat pomocí šifrování na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště v Media Services v3:

|Možnost šifrování|Popis|Media Services v3|
|---|---|---|
|Šifrování úložiště služby Media Services| Šifrování AES-256, klíče spravované službou Media Services|Nepodporuje<sup>(1)</sup>|
|[Šifrování služby Storage pro neaktivní uložená Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru nabízených službou Azure Storage, klíče spravované službou Azure nebo podle zákazníka|Podporováno|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta nabízené službou Azure storage, klíče spravované zákazníkem ve službě Key Vault|Nepodporuje se|

<sup>1</sup> v Media Services v3 šifrování úložiště (šifrování AES-256) je pouze podporována pro zpětné kompatibility při vaše prostředky se vytvořily pomocí Media Services v2. To znamená v3 spolupracuje s existující úložiště šifrované prostředky, ale nebude umožňovat vytváření nových.

## <a name="storage-account-errors"></a>Chyby účtu úložiště

Odpojený stav účtu služby Media Services znamená, že tento účet již nemá přístup k jednomu nebo více účtům připojeného úložiště z důvodu změny v přístupových klíčích k úložišti. Služba Media Services vyžaduje k provedení mnoha úkolů v účtu aktuální přístupové klíče k úložišti.

Důsledkem následujících primárních scénářů je to, že účet služby Media Services nemá přístup k účtům připojeného úložiště. 

|Problém|Řešení|
|---|---|
|Účet služby Media Services nebo účty připojeného úložiště byly migrovány do oddělených předplatných. |Účty úložiště nebo účet služby Media Services migrujte tak, aby všechny tyto účty byly ve stejném předplatném. |
|Účet služby Media Services používá účet připojeného úložiště v jiném předplatném, jako ve starším účtu Media Services, kde to bylo podporováno. Všechny starší účty služby Media Services byly převedeny na moderní účty založené na Azure Resource Manageru (ARM) a budou v odpojeném stavu. |Účet úložiště nebo účet služby Media Services migrujte tak, aby všechny tyto účty byly ve stejném předplatném.|

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit účet úložiště k účtu Media Services, najdete v článku [vytvořit účet](create-account-cli-quickstart.md).
