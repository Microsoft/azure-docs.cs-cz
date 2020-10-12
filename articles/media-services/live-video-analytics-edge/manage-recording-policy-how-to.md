---
title: Správa zásad nahrávání – Azure
description: Toto téma vysvětluje, jak spravovat zásady nahrávání.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: d3a1be915dc1cc8714e49cc7b2fe68bbe9cad161
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011477"
---
# <a name="manage-recording-policy"></a>Správa zásad nahrávání

Živé video analýzy můžete použít na IoT Edge pro [průběžné nahrávání videa](continuous-video-recording-concept.md), kde můžete nahrávat video do cloudu po týdny nebo měsíce. Délku (ve dnech) cloudového archivu můžete spravovat pomocí [nástrojů pro správu životního cyklu](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) , které jsou integrované v Azure Storage.  

Váš účet Media Service je propojený s účtem Azure Storage a při nahrávání videa do cloudu se obsah zapisuje do [assetu](../latest/assets-concept.md)služby Media Service. Každý Asset je namapován na kontejner v účtu úložiště. Správa životního cyklu vám umožňuje definovat [zásadu](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) pro účet úložiště, kde můžete zadat [pravidlo](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) , například následující.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

Výše uvedené pravidlo:

* Platí pro všechny objekty blob bloku v účtu úložiště.
* Určuje, že když se objekty blob dostanou déle než 30 dní, přesunou se z [vrstvy Hot Accessu na studenou](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal).
* A když jsou objekty blob mladší než 90 dní, budou se odstraňovat.

Vzhledem k tomu, že Live video Analytics archivuje vaše video v určených časových jednotkách, váš Asset bude obsahovat řadu objektů blob, jeden objekt blob na segment. Když zásady správy životního cyklu nastanou a odstraní starší objekty blob, budete mít nadále přístup k zbývajícím objektům blob a jejich přehrávání prostřednictvím rozhraní API služby Media Service. Další informace najdete v tématu [Přehrávání nahrávek](playback-recordings-how-to.md). 

## <a name="limitations"></a>Omezení

Následují známá omezení pro správu životního cyklu:

* V rámci této zásady můžete mít maximálně 100 pravidel a každé pravidlo může určovat až 10 kontejnerů. Takže pokud jste potřebovali různé zásady zaznamenávání (například za 3 dny pro kameru, která se týká parkovacího vytížení, 30 dní pro fotoaparát v Docku zatížení a 180 dní pro fotoaparát za čítačem rezervací), pak s jedním účtem služby Media Service můžete přizpůsobit pravidla pro nejvíce 1000 kamer.
* Aktualizace zásad správy životního cyklu nejsou okamžité. Další podrobnosti najdete v [části Nejčastější dotazy](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) .
* Pokud se rozhodnete použít zásadu, při které se objekty blob přesunou na studenou úroveň, může to mít vliv na přehrávání této části archivu. Můžou se zobrazit další latence nebo občasné chyby. Media Services nepodporuje přehrávání obsahu v archivní úrovni.

## <a name="next-steps"></a>Další kroky

[Přehrávání záznamů](playback-recordings-how-to.md)
