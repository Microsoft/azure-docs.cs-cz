---
title: Účty služby Azure Storage
titleSuffix: Azure Media Services
description: Naučte se vytvořit účet služby Azure Storage pro použití s Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f37b453a294a0d0a7b9a99bfebe8f3eff09e8956
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291190"
---
# <a name="azure-storage-accounts"></a>Účty úložiště Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services.

Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Doporučujeme používat účty úložiště ve stejném umístění jako účet Media Services, abyste se vyhnuli dalším nákladům na latenci a výstup dat.

Musíte mít jeden **primární** účet úložiště a můžete mít k vašemu Media Servicesmu účtu přiřazený libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). Účty jenom BLOB nejsou povolené jako **primární**.

Doporučujeme používat GPv2, abyste mohli využít výhod nejnovějších funkcí a výkonu. Další informace o účtech úložiště najdete v tématu [přehled Azure Storage účtu](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Pro použití s Azure Media Services je podporována pouze úroveň Hot Access, i když ostatní úrovně přístupu lze použít ke snížení nákladů na úložiště obsahu, který se aktivně nepoužívá.

Pro svůj účet úložiště můžete vybrat jiné SKU. Další informace najdete v tématu [účty úložiště](/cli/azure/storage/account?view=azure-cli-latest). Pokud chcete experimentovat s účty úložiště, použijte `--sku Standard_LRS` . Když však vybíráte SKU pro produkční prostředí, měli byste zvážit `--sku Standard_RAGRS` , což zajišťuje geografickou replikaci pro kontinuitu podnikových aplikací.

## <a name="assets-in-a-storage-account"></a>Prostředky v účtu úložiště

V Media Services V3 se k nahrávání souborů do assetů používají rozhraní API pro úložiště. Další informace najdete v tématu [assety in Azure Media Services V3](assets-concept.md).

> [!Note]
> Nepokoušejte se změnit obsah kontejnerů objektů BLOB generovaných sadou Media Services SDK bez použití rozhraní API Media Services.

## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

Aby bylo možné chránit vaše prostředky v klidovém stavu, prostředky by měly být šifrovány šifrováním na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště v Media Services V3:

|Možnost šifrování|Description|Media Services v3|
|---|---|---|
|Media Services šifrování úložiště| Šifrování AES-256, klíč spravovaný pomocí Media Services. |Nepodporováno. <sup>(1)</sup>|
|[Šifrování služby Storage pro neaktivní neaktivní data](../../storage/common/storage-service-encryption.md)|Šifrování na straně serveru, které nabízí Azure Storage, klíč spravuje Azure nebo zákazník.|Podporuje se.|
|[Šifrování na straně klienta úložiště](../../storage/common/storage-client-side-encryption.md)|Šifrování na straně klienta, které nabízí služba Azure Storage, klíč spravovaný zákazníkem v Key Vault.|Nepodporováno|

<sup>1</sup> v Media Services V3 se šifrování úložiště (šifrování AES-256) podporuje jenom pro zpětnou kompatibilitu, když se vaše prostředky vytvořily pomocí Media Services V2, což znamená, že V3 funguje se stávajícími šifrovanými prostředky úložiště, ale neumožňuje vytváření nových.

## <a name="storage-account-errors"></a>Chyby účtu úložiště

Odpojený stav účtu služby Media Services znamená, že tento účet již nemá přístup k jednomu nebo více účtům připojeného úložiště z důvodu změny v přístupových klíčích k úložišti. Služba Media Services vyžaduje k provedení mnoha úkolů v účtu aktuální přístupové klíče k úložišti.

Důsledkem následujících primárních scénářů je to, že účet služby Media Services nemá přístup k účtům připojeného úložiště.

|Problém|Řešení|
|---|---|
|Účet služby Media Services nebo účty připojeného úložiště byly migrovány do oddělených předplatných. |Migrujte účty úložiště nebo Media Services účet tak, aby byly všechny ve stejném předplatném. |
|Účet služby Media Services používá účet připojeného úložiště v jiném předplatném, jako ve starším účtu Media Services, kde to bylo podporováno. Všechny účty počátečního Media Services se převedly na moderní účty Azure Resource Manageru a budou mít odpojený stav. |Migrujte účet úložiště nebo účet Media Services, aby byly všechny ve stejném předplatném.|

## <a name="azure-storage-firewall"></a>Azure Storage firewall

Azure Media Services nepodporuje účty úložiště s povolenými Azure Storage bránou firewall nebo [soukromými koncovými body](../../storage/common/storage-network-security.md) .

## <a name="next-steps"></a>Další kroky

Informace o tom, jak připojit účet úložiště k vašemu Media Services účtu, najdete v tématu [Vytvoření účtu](./create-account-howto.md).
