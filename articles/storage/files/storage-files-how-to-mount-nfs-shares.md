---
title: Připojení sdílené složky Azure NFS – soubory Azure
description: Přečtěte si, jak připojit sdílenou síťovou složku systému souborů.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a993d9c1de35132198de5e3becc4f16d6a2a437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621293"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Postup připojení sdílené složky systému souborů NFS

Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v distribucích systému Linux buď pomocí protokolu SMB (Server Message Block Protocol), nebo protokolu NFS (Network File System). Tento článek se zaměřuje na připojení k systému souborů NFS. Podrobnosti o připojení ke službě SMB najdete v tématu [použití souborů Azure se systémem Linux](storage-how-to-use-files-linux.md). Podrobnosti o jednotlivých dostupných protokolech najdete v tématu [protokoly sdílené složky Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Omezení

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionální dostupnost

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Předpoklady

- [Vytvořte sdílenou SLOŽKU NFS](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > Ke sdíleným složkám NFS se dá dostat jenom z důvěryhodných sítí. Připojení ke sdílené složce systému souborů NFS musí pocházet z jednoho z následujících zdrojů:

- Použijte jedno z následujících síťových řešení:
    - Buď [vytvořte privátní koncový bod](storage-files-networking-endpoints.md#create-a-private-endpoint) (doporučeno), nebo [omezte přístup ke svému veřejnému koncovému bodu](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Nakonfigurujte síť VPN typu Point-to-Site (P2S) na platformě Linux pro použití se soubory Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Nakonfigurujte síť VPN typu Site-to-site pro použití se soubory Azure](storage-files-configure-s2s-vpn.md).
    - Nakonfigurujte [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Zakázat zabezpečený přenos

1. Přihlaste se k Azure Portal a přihlaste se k účtu úložiště, který obsahuje vytvořenou sdílenou složku NFS.
1. Vyberte možnost **Konfigurace**.
1. Pro **požadovaný zabezpečený přenos** vyberte **zakázáno** .
1. Vyberte **Uložit**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Snímek obrazovky konfigurace účtu úložiště se zakázaným zabezpečeným přenosem.":::

## <a name="mount-an-nfs-share"></a>Připojení sdílené složky NFS

1. Po vytvoření sdílené složky vyberte sdílenou složku a vyberte připojit se **ze systému Linux**.
1. Zadejte cestu pro připojení, kterou chcete použít, a potom skript zkopírujte.
1. Připojte se ke svému klientovi a použijte poskytnutý skript pro připojení.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Snímek obrazovky okna připojit sdílení souborů":::

Nyní jste připojili sdílenou složku systému souborů NFS.

### <a name="validate-connectivity"></a>Ověřit připojení

Pokud připojení selhalo, je možné, že váš privátní koncový bod nebyl nastaven správně nebo je nepřístupný. Podrobnosti o potvrzení připojení najdete v části [ověření připojení](storage-files-networking-endpoints.md#verify-connectivity) v článku koncové body sítě.

## <a name="next-steps"></a>Další kroky

- Další informace o souborech Azure s naším článkem najdete v tématu [Plánování nasazení služby soubory Azure](storage-files-planning.md).
- Pokud se setkáte s problémy, přečtěte si téma [řešení potíží s sdílenými složkami souborů Azure NFS](storage-troubleshooting-files-nfs.md)