---
title: Účty Azure Storage s účty Azure Media Services | Microsoft Docs
description: Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 4bbadd7e10f0fd6896932dd79a5ca42d9906d2a2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251320"
---
# <a name="azure-storage-accounts"></a>Účty Azure Storage

Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. 

Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Důrazně se doporučuje používat účty úložiště ve stejném umístění jako účet Media Services, abyste se vyhnuli dalším nákladům na latenci a výstup dat.

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). <br/>Účty jen pro objekty blob nejsou povolené jako **primární**. 

Doporučujeme používat GPv2, abyste mohli využít výhod nejnovějších funkcí a výkonu. Další informace o účtech úložiště najdete v tématu [přehled Azure Storage účtu](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Pro použití s Azure Media Services se podporuje jenom vrstva Hot Access, i když je možné použít jiné úrovně přístupu k omezení nákladů na úložiště obsahu, který se aktivně nepoužívá.

Pro svůj účet úložiště můžete vybrat jiné SKU. Další informace najdete v tématu [účty úložiště](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Pokud chcete experimentovat s účty úložiště, použijte `--sku Standard_LRS`. Při výběru skladové položky pro produkci byste ale měli zvážit, `--sku Standard_RAGRS`, což zajišťuje geografickou replikaci pro kontinuitu podnikových prostředí. 

## <a name="assets-in-a-storage-account"></a>Prostředky v účtu úložiště

V Media Services V3 se k nahrávání souborů do assetů používají rozhraní API pro úložiště. Další informace najdete v tématu [koncept assetů](assets-concept.md).

> [!Note]
> Neměli byste se pokoušet změnit obsah kontejnerů objektů BLOB generovaných sadou Media Services SDK bez použití rozhraní API Media Services.
 
## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

K ochraně vašich prostředků v klidovém stavu, prostředky by se měla šifrovat pomocí šifrování na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště v Media Services v3:

|Možnost šifrování|Popis|Media Services v3|
|---|---|---|
|Šifrování úložiště služby Media Services| Šifrování AES-256, klíče spravované službou Media Services|Nepodporováno<sup>(1)</sup>|
|[Šifrování služby Storage pro neaktivní neaktivní data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru nabízených službou Azure Storage, klíče spravované službou Azure nebo podle zákazníka|Podporuje se|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta nabízené službou Azure storage, klíče spravované zákazníkem ve službě Key Vault|Nepodporováno|

<sup>1</sup> v Media Services V3 se šifrování úložiště (šifrování AES-256) podporuje jenom pro zpětnou kompatibilitu, když se vaše prostředky vytvořily pomocí Media Services V2. To znamená v3 spolupracuje s existující úložiště šifrované prostředky, ale nebude umožňovat vytváření nových.

## <a name="storage-account-errors"></a>Chyby účtu úložiště

Odpojený stav účtu služby Media Services znamená, že tento účet již nemá přístup k jednomu nebo více účtům připojeného úložiště z důvodu změny v přístupových klíčích k úložišti. Služba Media Services vyžaduje k provedení mnoha úkolů v účtu aktuální přístupové klíče k úložišti.

Důsledkem následujících primárních scénářů je to, že účet služby Media Services nemá přístup k účtům připojeného úložiště. 

|Problém|Řešení|
|---|---|
|Účet služby Media Services nebo účty připojeného úložiště byly migrovány do oddělených předplatných. |Účty úložiště nebo účet služby Media Services migrujte tak, aby všechny tyto účty byly ve stejném předplatném. |
|Účet služby Media Services používá účet připojeného úložiště v jiném předplatném, jako ve starším účtu Media Services, kde to bylo podporováno. Všechny starší účty služby Media Services byly převedeny na moderní účty založené na Azure Resource Manageru (ARM) a budou v odpojeném stavu. |Účet úložiště nebo účet služby Media Services migrujte tak, aby všechny tyto účty byly ve stejném předplatném.|

## <a name="azure-storage-firewall"></a>Azure Storage firewall

Azure Media Services nepodporuje účty úložiště s povolenými Azure Storage bránou firewall nebo [soukromými koncovými body](https://docs.microsoft.com/azure/storage/common/storage-network-security) .

## <a name="next-steps"></a>Další kroky

Informace o tom, jak připojit účet úložiště k vašemu Media Services účtu, najdete v tématu [Vytvoření účtu](create-account-cli-quickstart.md).
