---
title: Účty služby Azure Storage
titleSuffix: Azure Media Services
description: Přečtěte si, jak vytvořit účet úložiště Azure pro použití s Azure Media Services.
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
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499832"
---
# <a name="azure-storage-accounts"></a>Účty úložiště Azure

Chcete-li začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Mediálních služeb. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services.

Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Důrazně doporučujeme používat účty úložiště ve stejném umístění jako účet Mediální služby, aby se zabránilo další latence a náklady na odchozí data.

Musíte mít jeden účet **primárního** úložiště a můžete mít libovolný počet účtů **sekundárního** úložiště přidružených k účtu Mediálních služeb. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). Pouze účty objektů blob nejsou povoleny jako **primární**.

Doporučujeme používat GPv2, abyste mohli využívat nejnovější funkce a výkon. Další informace o účtech úložiště najdete v [tématu Přehled účtu Úložiště Azure](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Pro použití s Azure Media Services je podporovaná jenom úroveň aktivního přístupu, i když ostatní vrstvy přístupu se můžou použít ke snížení nákladů na úložiště u obsahu, který se aktivně nepoužívá.

Existují různé skladové sou, které si můžete vybrat pro svůj účet úložiště. Další informace naleznete v [tématu účty úložiště](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Pokud chcete experimentovat s účty `--sku Standard_LRS`úložiště, použijte . Při výběru skladové položky pro `--sku Standard_RAGRS`produkční prostředí byste však měli zvážit , která poskytuje geografickou replikaci pro kontinuitu provozu.

## <a name="assets-in-a-storage-account"></a>Majetek na účtu úložiště

Ve službě Media Services v3 se k odesílání souborů do datových zdrojů používají api úložiště. Další informace najdete [v tématu Prostředky ve službě Azure Media Services v3](assets-concept.md).

> [!Note]
> Nepokoušejte se změnit obsah kontejnerů objektů blob, které byly generovány sadou Media Services SDK bez použití api mediálních služeb.

## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

Chcete-li chránit vaše prostředky v klidovém stavu, měly by být zašifrovány šifrováním na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště ve službě Media Services v3:

|Možnost šifrování|Popis|Media Services v3|
|---|---|---|
|Šifrování úložiště služby Media Services| Šifrování AES-256, klíč spravovaný společností Media Services. |Není podporováno. <sup>(1.</sup>|
|[Šifrování služby úložiště pro data v klidovém stavu](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru, které nabízí Azure Storage, klíč spravovaný Azure nebo zákazníkem.|Podporuje se.|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta, které nabízí úložiště Azure, klíč spravovaný zákazníkem v trezoru klíčů.|Není podporováno.|

<sup>1</sup> Ve službě Media Services v3 je šifrování úložiště (šifrování AES-256) podporováno pouze pro zpětnou kompatibilitu, když byly vaše datové zdroje vytvořeny pomocí služby Media Services v2, což znamená, že v3 pracuje se stávajícími šifrovanými prostředky úložiště, ale neumožní vytváření nových.

## <a name="storage-account-errors"></a>Chyby účtu úložiště

Odpojený stav účtu služby Media Services znamená, že tento účet již nemá přístup k jednomu nebo více účtům připojeného úložiště z důvodu změny v přístupových klíčích k úložišti. Služba Media Services vyžaduje k provedení mnoha úkolů v účtu aktuální přístupové klíče k úložišti.

Důsledkem následujících primárních scénářů je to, že účet služby Media Services nemá přístup k účtům připojeného úložiště.

|Problém|Řešení|
|---|---|
|Účet služby Media Services nebo účty připojeného úložiště byly migrovány do oddělených předplatných. |Migrujte účty úložiště nebo účet Mediálních služeb tak, aby byly všechny ve stejném předplatném. |
|Účet služby Media Services používá účet připojeného úložiště v jiném předplatném, jako ve starším účtu Media Services, kde to bylo podporováno. Všechny dřívější účty Media Services byly převedeny na moderní účty založené na Azure Resources Manager a budou mít odpojený stav. |Migrujte účet úložiště nebo účet Mediálních služeb tak, aby byly všechny ve stejném předplatném.|

## <a name="azure-storage-firewall"></a>Brána firewall azure storage

Azure Media Services nepodporuje účty úložiště s povolenou bránou firewall Azure Storage nebo [privátními koncovými body.](https://docs.microsoft.com/azure/storage/common/storage-network-security)

## <a name="next-steps"></a>Další kroky

Informace o tom, jak k účtu Mediálních služeb připojit účet úložiště, najdete [v tématu Vytvoření účtu](create-account-cli-quickstart.md).
