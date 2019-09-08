---
title: Vytvoření mezipaměti prostředí Azure HPC
description: Vytvoření instance mezipaměti prostředí Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 41361a3513c052d960726498d55745bf09afdfbb
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775192"
---
# <a name="configure-aggregated-namespace"></a>Konfigurovat agregovaný obor názvů
<!-- change link in GUI -->

Mezipaměť HPC Azure umožňuje klientům přístup k nejrůznějším systémům úložiště prostřednictvím virtuálního oboru názvů, který skrývá podrobnosti o back-endovém systému úložiště.

Když přidáte cíl úložiště, nastavíte cestu s přístupem klienta. Klientské počítače připojí tento FilePath. Cíl úložiště přidružený k této cestě můžete změnit. Můžete například nahradit hardwarový systém úložiště cloudovým úložištěm, aniž byste museli přepisovat klientské postupy.

## <a name="aggregated-namespace-example"></a>Příklad agregovaného oboru názvů

Naplánujte agregovaný obor názvů tak, aby klientské počítače mohly pohodlně oslovit informace, které potřebují, a správci a technici pracovního postupu můžou cesty snadno odlišit.

Představte si třeba systém, kde se instance mezipaměti HPC Azure používá ke zpracování dat uložených v objektu blob Azure. Tato analýza vyžaduje soubory šablon uložené v místním datovém centru.

Data šablony jsou uložena v datovém centru a informace potřebné pro tuto úlohu jsou uloženy v těchto podadresářích:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Tyto exporty zpřístupňuje systém úložiště Datacenter: 

    /
    /goldline
    /goldline/templates

Data, která se mají analyzovat, se zkopírovala do kontejneru úložiště objektů BLOB v Azure s názvem SourceCollection pomocí [nástroje CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)).

Pokud chcete mít snadný přístup přes mezipaměť, zvažte vytvoření cílů úložiště s těmito cestami k virtuálnímu oboru názvů:

| Back-end NFS – FilePath nebo kontejner objektů BLOB | Cesta k virtuálnímu oboru názvů |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| SourceCollection                        | /Source               |

Vzhledem k tomu, že jsou zdrojové cesty systému souborů NFS podadresáři stejného exportu, budete muset definovat několik cest k oboru názvů ze stejného cíle úložiště. 

| Cílový název hostitele úložiště  | Cesta exportu NFS      | Cesta k podadresáři | Cesta oboru názvů    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP adresa nebo název hostitele* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP adresa nebo název hostitele* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Klientská aplikace může připojit mezipaměť a snadno získat přístup k agregovaným cestám oboru názvů/source,/Templates/sku798 a/Templates/sku980.

## <a name="next-steps"></a>Další kroky

Až se rozhodnete, jak nastavit virtuální systém souborů, [vytvořte cíle úložiště](hpc-cache-add-storage.md) pro mapování back-endového úložiště na vaše klientské cesty k virtuálním zařízením.
