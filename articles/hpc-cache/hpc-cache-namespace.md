---
title: Vytvoření mezipaměti Azure HPC
description: Jak vytvořit instanci mezipaměti Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582185"
---
# <a name="plan-the-aggregated-namespace"></a>Plánování agregovaného oboru názvů

Azure HPC Cache umožňuje klientům přístup k různým systémům úložiště prostřednictvím virtuálního oboru názvů, který skryje podrobnosti o back-endovém úložném systému.

Když přidáte cíl úložiště, nastavíte cestu k souboru směřující ke klientovi. Klientské počítače připojit tuto cestu k souboru a může soubor číst požadavky do mezipaměti namísto připojení úložného systému přímo.

Vzhledem k tomu, že Azure HPC Cache spravuje tento virtuální systém souborů, můžete změnit cíl úložiště beze změny cesty směřující ke klientovi. Můžete například nahradit systém hardwarového úložiště cloudovým úložištěm, aniž byste museli přepisovat postupy pro klienty.

## <a name="aggregated-namespace-example"></a>Příklad agregovaného oboru názvů

Naplánujte agregovaný obor názvů tak, aby klientské počítače mohly pohodlně přistupovat k potřebným informacím a aby správci a technici pracovního postupu mohli snadno rozlišit cesty.

Zvažte například systém, kde se instance mezipaměti Azure HPC používá ke zpracování dat uložených v objektu Blob Azure. Analýza vyžaduje soubory šablon, které jsou uloženy v místním datovém centru.

Data šablony jsou uložena v datovém centru a informace potřebné pro tuto úlohu jsou uloženy v těchto podadresářích:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Systém úložiště datového centra zpřístupňuje tyto exporty:

    /
    /goldline
    /goldline/templates

Data, která mají být analyzována, byla zkopírována do kontejneru úložiště objektů Blob Azure s názvem "sourcecollection" pomocí [nástroje CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Chcete-li povolit snadný přístup prostřednictvím mezipaměti, zvažte vytvoření cílů úložiště pomocí těchto cest virtuálního oboru názvů:

| Back-end úložný systém <br/> (Cesta k souboru systému souborů NFS nebo kontejner objektů blob) | Cesta virtuálního oboru názvů |
|-----------------------------------------|------------------------|
| /goldline/šablony/acme2017/sku798     | /šablony/sku798      |
| /goldline/templates/acme2017/sku980     | /šablony/sku980      |
| sourcecollection                        | /zdroj/               |

Cíl úložiště systému souborů NFS může mít více cest virtuálního oboru názvů, pokud každá z nich odkazuje na jedinečnou cestu exportu.

Vzhledem k tomu, že zdrojové cesty systému souborů NFS jsou podadresáře stejného exportu, budete muset definovat více cest oboru názvů ze stejného cíle úložiště.

| Název hostitele cíle úložiště  | Cesta exportu nfs      | Cesta podadresáře | Cesta oboru názvů    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP adresa nebo název hostitele* | /goldline/šablony  | acme2017/sku798   | /šablony/sku798 |
| *IP adresa nebo název hostitele* | /goldline/šablony  | acme2017/sku980   | /šablony/sku980 |

Klientská aplikace může připojit mezipaměť a snadno přistupovat ``/source`` ``/templates/sku798``k ``/templates/sku980``agregovaným cestám k souborům oboru názvů , a .

## <a name="next-steps"></a>Další kroky

Po rozhodnutí, jak nastavit virtuální systém souborů, [vytvořte cíle úložiště](hpc-cache-add-storage.md) pro mapování back-endového úložiště na virtuální cesty virtuálních souborů směřujících ke klientům.
