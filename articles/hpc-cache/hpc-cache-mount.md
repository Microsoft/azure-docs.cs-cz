---
title: Připojení mezipaměti HPC Azure
description: Postup připojení klientů ke službě Azure HPC cache Service
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: v-erkell
ms.openlocfilehash: 46f221fd7c340b7f321d317f0e7493448d83177c
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036962"
---
# <a name="mount-the-azure-hpc-cache"></a>Připojení mezipaměti HPC Azure

Po vytvoření mezipaměti k ní budou mít klienti NFS přístup pomocí jednoduchého příkazu Mount.

Použijte adresy pro připojení uvedené na stránce s přehledem mezipaměti a cestu k virtuálnímu oboru názvů, kterou jste nastavili při vytváření cíle úložiště. 

![snímek stránky s přehledem instance mezipaměti HPC v Azure se zvýrazněným políčkem v pravém dolním rohu seznamu adres připojení](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Adresy připojení mezipaměti odpovídají síťovým rozhraním v podsíti mezipaměti. Tyto síťové adaptéry se zobrazí ve skupině prostředků s názvy končícími `-cluster-nic-` a číslem. Tato rozhraní neměňte ani neodstraňujte, jinak se mezipaměť nestane nedostupnou.

Cesty k virtuálnímu oboru názvů se zobrazí na stránce **cíle úložiště** . Kliknutím na název cíle zobrazíte podrobnosti, včetně agregované cesty oboru názvů (nebo cest), které jsou k němu přidružené. 
![snímek obrazovky s cílovým panelem úložiště v mezipaměti s zvýrazňujícím polem kolem položky ve sloupci cesta v tabulce](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Syntaxe příkazu Mount

Použijte příkaz Mount podobný následujícímu:

> sudo Mount *cache_mount_address*:/*namespace_path* *local_path* {*Options*}

Příklad: 

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard,intr
root@test-client:/tmp# 
```

Po úspěšném provedení tohoto příkazu by měl být obsah exportu úložiště viditelný v ``hpccache`` adresáři na klientovi. 

> [!NOTE] 
> Klienti musí být schopni získat přístup k virtuální síti a podsíti, která je v mezipaměti. Můžete například vytvořit klientské virtuální počítače ve stejné virtuální síti nebo použít koncový bod, bránu nebo jiné řešení ve virtuální síti pro přístup zvenčí. Mějte na paměti, že v podsíti mezipaměti nemůžete hostovat nic dalšího.

### <a name="mount-command-options"></a>Možnosti příkazu připojit

V případě robustního připojení klienta předejte tato nastavení a argumenty do příkazu mount: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Doporučené nastavení příkazu pro připojení | |
--- | --- 
``hard`` | K selhání aplikací a možné ztrátě dat se připojí provizorní připojení k vFXT clusteru. 
``proto=netid`` | Tato možnost podporuje odpovídající zpracování chyb sítě systému souborů NFS.
``mountproto=netid`` | Tato možnost podporuje odpovídající zpracování chyb sítě pro operace připojení.
``retry=n`` | Nastavte ``retry=30`` , aby nedocházelo k přechodným chybám připojení. (V připojeních na popředí se doporučuje jiná hodnota.)

## <a name="next-steps"></a>Další postup

* Pokud chcete přesunout data do cílů úložiště mezipaměti, přečtěte si téma [naplnění nového úložiště objektů BLOB v Azure](hpc-cache-ingest.md).
