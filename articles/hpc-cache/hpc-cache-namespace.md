---
title: Vysvětlení agregovaného oboru názvů mezipaměti HPC Azure
description: Jak naplánovat virtuální obor názvů pro mezipaměť HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 1c28f549cf93d77f6aef6bcde6a2225345a79cc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612944"
---
# <a name="plan-the-aggregated-namespace"></a>Plánování agregovaného oboru názvů

Mezipaměť HPC Azure umožňuje klientům přístup k nejrůznějším systémům úložiště prostřednictvím virtuálního oboru názvů, který skrývá podrobnosti o back-endovém systému úložiště.

Po přidání cíle úložiště nastavíte pro cíl úložiště jednu nebo více cest k oboru názvů na straně klienta. Klientské počítače připojí tuto cestu k souboru a můžou do mezipaměti ukládat požadavky na čtení souborů místo přímého připojení systému úložiště.

Vzhledem k tomu, že Azure HPC cache spravuje tento virtuální systém souborů, můžete změnit cíl úložiště, aniž byste museli měnit cestu klienta. Můžete například nahradit hardwarový systém úložiště cloudovým úložištěm, aniž byste museli přepisovat postupy na straně klienta.

## <a name="aggregated-namespace-example"></a>Příklad agregovaného oboru názvů

Naplánujte agregovaný obor názvů tak, aby klientské počítače mohly pohodlně oslovit informace, které potřebují, a aby mohli správci a technici pracovních postupů snadno odlišit cesty.

Představte si třeba systém, kde se instance mezipaměti HPC Azure používá ke zpracování dat uložených v objektu blob Azure. Tato analýza vyžaduje soubory šablon uložené v místním datovém centru.

Data šablony jsou uložena v datovém centru a informace potřebné pro tuto úlohu jsou uloženy v těchto podadresářích:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

Tyto exporty zpřístupňuje systém úložiště Datacenter:

* */*
* */goldline*
* */goldline/templates*

Data, která se mají analyzovat, se zkopírovala do kontejneru úložiště objektů BLOB v Azure s názvem SourceCollection pomocí [nástroje CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Pokud chcete mít snadný přístup přes mezipaměť, zvažte vytvoření cílů úložiště s těmito cestami k virtuálnímu oboru názvů:

| Back-endové úložné systémy <br/> (Cesta k souboru NFS nebo kontejner objektů BLOB) | Cesta k virtuálnímu oboru názvů |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| SourceCollection                        | /Source               |

Cíl úložiště NFS může mít několik cest k virtuálnímu oboru názvů, pokud každý z nich odkazuje na jedinečnou cestu exportu. (Čtení [cest k oboru názvů systému souborů NFS](add-namespace-paths.md#nfs-namespace-paths) pro zjištění doporučeného maximálního počtu cest oboru názvů na cíl úložiště systému souborů NFS)

Vzhledem k tomu, že zdrojové cesty NFS jsou podadresářům stejného exportu, budete muset definovat několik cest k oboru názvů ze stejného cíle úložiště.

| Cílový název hostitele úložiště  | Cesta exportu NFS     | Cesta k podadresáři | Cesta oboru názvů    |
|--------------------------|---------------------|-------------------|-------------------|
| *IP adresa nebo název hostitele* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *IP adresa nebo název hostitele* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Klientská aplikace může připojit mezipaměť a snadno získat přístup k agregovaným cestám k souborům oboru názvů, ``/source`` ``/templates/sku798`` a ``/templates/sku980`` .

Alternativním přístupem může být vytvořit virtuální cestu, například `/templates` odkazy na nadřazený adresář, `acme2017` a potom nechat klienty přejít na jednotlivé `sku798` a `sku980` adresáře po připojení mezipaměti. Nemůžete však vytvořit cestu k oboru názvů, která je podadresářem jiné cesty k oboru názvů. Takže pokud vytvoříte cestu k `acme2017` adresáři, nemůžete vytvořit také žádné cesty oboru názvů pro přímý přístup k jeho podadresářům.

Stránka nastavení **oboru názvů** mezipaměti HPC Azure zobrazuje systém souborů s klientským přístupem a umožňuje přidat nebo upravit cesty. Další informace najdete [v tématu Nastavení agregovaného oboru názvů](add-namespace-paths.md) .

## <a name="next-steps"></a>Další kroky

Až se rozhodnete, jak nastavit virtuální systém souborů, proveďte tyto kroky a vytvořte ho:

* [Vytvořte cíle úložiště](hpc-cache-add-storage.md) , abyste přidali vaše back-endové úložné systémy do mezipaměti HPC Azure.
* [Přidání cest k oboru názvů](add-namespace-paths.md) pro vytvoření agregovaného oboru názvů, který klientské počítače používají pro přístup k souborům
