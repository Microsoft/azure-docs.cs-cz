---
title: Připojení mezipaměti Azure HPC
description: Jak připojit klienty ke službě Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582218"
---
# <a name="mount-the-azure-hpc-cache"></a>Připojení mezipaměti Azure HPC

Po vytvoření mezipaměti mohou klienti nfs přistupovat pomocí jednoduchého příkazu připojit.

Příkaz mount se skládá ze dvou prvků:

* Jedna z adres připojení mezipaměti (uvedená na stránce přehledu mezipaměti)
* Cesta virtuálního oboru názvů, kterou jste nastavili při vytváření cíle úložiště

![Snímek obrazovky s přehledem stránky instance Azure HPC Cache se zvýrazněním kolem seznamu přípojných adres v pravém dolním](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Adresy připojení mezipaměti odpovídají síťovým rozhraním uvnitř podsítě mezipaměti. Ve skupině prostředků jsou tyto síťové karty `-cluster-nic-` uvedeny s názvy končícími a číslem. Tato rozhraní neměňte ani neodstraňujte, jinak bude mezipaměť nedostupná.

Cesty virtuálního oboru názvů jsou zobrazeny na stránce **Cíle úložiště.** Kliknutím na konkrétní cílový název úložiště zobrazíte jeho podrobnosti, včetně agregovaných cest oboru názvů, které jsou k němu přidruženy.

![Snímek obrazovky s cílovým panelem úložiště mezipaměti s polem zvýraznění kolem položky ve sloupci Cesta v tabulce](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Syntaxe příkazu Připojit

Použijte příkaz připojení, jako je následující:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*možnosti*}

Příklad:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Po úspěšném spuštění tohoto příkazu by měl být ``hpccache`` obsah exportu úložiště viditelný v adresáři v klientovi.

> [!NOTE] 
> Klienti musí mít přístup k virtuální síti a podsíti, ve které je uložena vaše mezipaměť. Můžete například vytvořit klientské virtuální počítače v rámci stejné virtuální sítě nebo použít koncový bod, bránu nebo jiné řešení ve virtuální síti pro přístup zvenčí. Nezapomeňte, že nic jiného nemůže být hostováno uvnitř podsítě mezipaměti.

### <a name="mount-command-options"></a>Možnosti příkazu Připojit

Pro robustní připojení klienta předejte v příkazu připojení tato nastavení a argumenty: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Doporučená nastavení příkazů pro připojení | |
--- | --- 
``hard`` | Měkké připojení k azure hpc mezipaměti jsou spojeny s selhání aplikací a možné ztráty dat. 
``proto=netid`` | Tato možnost podporuje vhodné zpracování chyb sítě NFS.
``mountproto=netid`` | Tato možnost podporuje vhodné zpracování chyb sítě pro operace připojení.
``retry=n`` | Nastavte, ``retry=30`` aby se zabránilo přechodným selháním připojení. (V úchytech v popředí se doporučuje jiná hodnota.)

## <a name="next-steps"></a>Další kroky

* Pokud chcete přesunout data do cílů úložiště mezipaměti, [přečtěte si naplnění nového úložiště objektů blob Azure](hpc-cache-ingest.md).
