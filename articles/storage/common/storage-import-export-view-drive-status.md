---
title: Zobrazit stav úloh Azure import/export | Microsoft Docs
description: Zjistěte, jak zobrazit stav úloh importu/exportu a využitých jednotek.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 7a000ab4a465e3b19efe6f2853bcd19dca47e440
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85514133"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Zobrazení stavu úloh služby Azure Import/Export

Tento článek poskytuje informace o tom, jak zobrazit jednotku a stav úlohy pro úlohy Azure import/export. Služba import/export Azure slouží k bezpečnému přenosu velkých objemů dat do objektů blob Azure a souborů Azure. Služba se používá také k exportu dat z úložiště objektů BLOB v Azure.  

## <a name="view-job-and-drive-status"></a>Zobrazení stavu úlohy a jednotky
Stav úloh importu nebo exportu můžete sledovat z Azure Portal. Klikněte na kartu **Import/export** . Na stránce se zobrazí seznam úloh.

![Zobrazit stav úlohy](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Zobrazení stavu úlohy

V závislosti na tom, kde se jednotka právě nachází, se zobrazí jedna z následujících stavů úlohy.

| Stav úlohy | Description |
|:--- |:--- |
| Vytváření | Po vytvoření úlohy se její stav nastaví na **vytvořit**. Když je úloha ve stavu **vytváření** , služba import/export předpokládá, že se jednotky nedodaly do datového centra. Úloha může zůstat v tomto stavu po dobu až dvou týdnů, po jejímž uplynutí je služba automaticky odstranila. |
| Shipping | Po odeslání balíčku byste měli aktualizovat informace o sledování v Azure Portal.  Tím se úloha přepíná do stavu **expedice** . Úloha zůstane ve stavu **expedice** po dobu až dvou týdnů. 
| Přijato | Po přijetí všech jednotek v datovém centru se stav úlohy nastaví na **přijato**. |
| Pojování | Po zahájení zpracování aspoň jedné jednotky se stav úlohy nastaví na **přenos**. Další informace najdete v informacích o [stavu jednotky](#view-drive-status). |
| Zabalení | Po dokončení zpracování všech jednotek se úloha umístí do stavu **balení** , dokud se jednotky nevrátí zpět. |
| Dokončeno | Po odeslání všech jednotek zpět do vás, pokud byla úloha dokončena bez chyb, je úloha nastavena na **dokončeno**. Tato úloha se automaticky odstraní po 90 dnech v **dokončeném** stavu. |
| Zavřeno | Po odeslání všech jednotek zpět, pokud došlo k nějakým chybám během zpracování úlohy, je úloha nastavena na **Uzavřeno**. Tato úloha se automaticky odstraní po 90 dnech v **zavřeném** stavu. |

## <a name="view-drive-status"></a>Zobrazení stavu jednotky

Následující tabulka popisuje životní cyklus jednotlivé jednotky při přechodu prostřednictvím úlohy importu nebo exportu. Aktuální stav jednotlivých jednotek v úloze se zobrazuje v Azure Portal.

V následující tabulce jsou popsány všechny stavy, které mohou jednotlivé jednotky v úloze Procházet.

| Stav jednotky | Description |
|:--- |:--- |
| Dané | Při vytvoření úlohy z Azure Portal se pro úlohu importu **zadá**počáteční stav jednotky. V případě úlohy exportu, protože při vytvoření úlohy není zadána žádná jednotka, je **přijat**počáteční stav jednotky. |
| Přijato | Pokud služba import/export zpracovává jednotky, které byly přijaty od společnosti pro expedici pro úlohu importu, přejde do stavu **přijato** . V případě úlohy exportu je počáteční stav jednotky stav **přijato** . |
| NeverReceived | Jednotka se přesune do stavu **NeverReceived** , když dorazí balíček pro úlohu, ale balíček disk neobsahuje. Jednotka se také přesune do tohoto stavu, pokud byla za dva týdny, protože služba obdržela informace o expedici, ale balíček ještě nedorazil do datového centra. |
| Pojování | Jednotka se přesune do stavu **přenosu** , když služba začne přenášet data z jednotky na Azure Storage. |
| Dokončeno | Jednotka se přesune do stavu **dokončeno** , pokud služba úspěšně přenesla všechna data bez chyb.
| CompletedMoreInfo | Jednotka se přesune do stavu **CompletedMoreInfo** , pokud služba zjistila určité problémy při kopírování dat z disku nebo na disk. Tyto informace mohou obsahovat chyby, varování nebo informativní zprávy týkající se přepsání objektů BLOB.
| ShippedBack | Jednotka se po odeslání z datového centra zpátky na zpáteční adresu přesune do stavu **ShippedBack** . |

Tento obrázek z Azure Portal zobrazuje stav jednotky ukázkové úlohy:

![Zobrazit stav jednotky](./media/storage-import-export-service/drivestate.png)

Následující tabulka popisuje stavy selhání jednotek a akce prováděné pro jednotlivé stavy.

| Stav jednotky | Událost | Řešení/další krok |
|:--- |:--- |:--- |
| NeverReceived | Jednotka, která je označena jako **NeverReceived** (protože nebyla přijata jako součást dodávky úlohy), dorazí do jiné dodávky. | Provozní tým přesune jednotku k **přijetí**. |
| Není k dispozici | Jednotka, která není součástí žádné úlohy, přijde v datovém centru jako součást jiné úlohy. | Jednotka je označena jako dodatečná jednotka a při dokončení úlohy přidružené k původnímu balíčku se vám vrátí. |

## <a name="time-to-process-job"></a>Čas zpracování úlohy
Doba potřebná ke zpracování úlohy importu/exportu se liší v závislosti na mnoha faktorech, jako například:

-  Čas expedice
-  Zatížení v datacentru
-  Typ úlohy a velikost kopírovaných dat
-  Počet disků v úloze. 

Služba import/export nemá smlouvu SLA, ale služba se snaží dokončit kopírování 7 až 10 dní od přijetí těchto disků. Kromě stavu publikovaného na portálu Azure Portal je možné pomocí rozhraní REST API sledovat průběh úlohy. Procentuální hodnota parametru dokončení v volání rozhraní API [seznamu úloh](/previous-versions/azure/dn529083(v=azure.100)) poskytuje procentní průběh kopírování.


## <a name="next-steps"></a>Další kroky

* [Nastavení nástroje WAImportExport](storage-import-export-tool-how-to.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Ukázka REST API exportu pro import do Azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
