---
title: Vytvoření mezipaměti prostředí Azure HPC (verze Preview)
description: Vytvoření instance mezipaměti prostředí Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: rohogue
ms.openlocfilehash: fa6408d33504c6ac73eacec079e9c08acd4cece2
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255262"
---
# <a name="plan-the-aggregated-namespace"></a>Plánování agregovaného oboru názvů

Azure HPC cache (Preview) umožňuje klientům přístup k nejrůznějším systémům úložiště prostřednictvím virtuálního oboru názvů, který skrývá podrobnosti o back-endovém systému úložiště.

Když přidáte cíl úložiště, nastavíte cestu k souboru pro klienta. Klientské počítače připojí tuto cestu k souboru a můžou do mezipaměti ukládat požadavky na čtení souborů místo přímého připojení systému úložiště.

Vzhledem k tomu, že Azure HPC cache spravuje tento virtuální systém souborů, můžete změnit cíl úložiště, aniž byste museli měnit cestu klienta. Můžete například nahradit hardwarový systém úložiště cloudovým úložištěm, aniž byste museli přepisovat klientské postupy.

## <a name="aggregated-namespace-example"></a>Příklad agregovaného oboru názvů

Naplánujte agregovaný obor názvů tak, aby klientské počítače mohly pohodlně oslovit informace, které potřebují, a aby mohli správci a technici pracovních postupů snadno odlišit cesty.

Představte si třeba systém, kde se instance mezipaměti HPC Azure používá ke zpracování dat uložených v objektu blob Azure. Tato analýza vyžaduje soubory šablon uložené v místním datovém centru.

Data šablony jsou uložena v datovém centru a informace potřebné pro tuto úlohu jsou uloženy v těchto podadresářích:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Tyto exporty zpřístupňuje systém úložiště Datacenter:

    /
    /goldline
    /goldline/templates

Data, která se mají analyzovat, se zkopírovala do kontejneru úložiště objektů BLOB v Azure s názvem SourceCollection pomocí [nástroje CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Pokud chcete mít snadný přístup přes mezipaměť, zvažte vytvoření cílů úložiště s těmito cestami k virtuálnímu oboru názvů:

| Back-endové úložné systémy <br/> (Cesta k souboru NFS nebo kontejner objektů BLOB) | Cesta k virtuálnímu oboru názvů |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| SourceCollection                        | /Source               |

Cíl úložiště NFS může mít několik cest k virtuálnímu oboru názvů, pokud každý z nich odkazuje na jedinečnou cestu exportu.

Vzhledem k tomu, že zdrojové cesty NFS jsou podadresářům stejného exportu, budete muset definovat několik cest k oboru názvů ze stejného cíle úložiště.

| Cílový název hostitele úložiště  | Cesta exportu NFS      | Cesta k podadresáři | Cesta oboru názvů    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP adresa nebo název hostitele* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP adresa nebo název hostitele* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Klientská aplikace může připojit mezipaměť a snadno přistupovat k agregovaným cestám k souborům oboru názvů ``/source``, ``/templates/sku798`` a ``/templates/sku980``.

## <a name="next-steps"></a>Další kroky

Až se rozhodnete, jak nastavit virtuální systém souborů, [vytvořte cíle úložiště](hpc-cache-add-storage.md) pro mapování vašeho back-endu úložiště na cesty k virtuálním souborům, ke kterým se klient vztahuje.
