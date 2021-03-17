---
title: Zobrazit stav úloh Azure import/export | Microsoft Docs
description: Přečtěte si, jak zobrazit stav úloh importu a exportu Azure a použitých jednotek. Seznamte se s faktory, které mají vliv na to, jak dlouho trvá zpracování úlohy.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8ef18ea663f3a77589d61ed89c50df38f5cf0d0e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176135"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Zobrazení stavu úloh služby Azure Import/Export

Tento článek poskytuje informace o tom, jak zobrazit jednotku a stav úlohy pro úlohy Azure import/export. Služba import/export Azure slouží k bezpečnému přenosu velkých objemů dat do objektů blob Azure a souborů Azure. Služba se používá také k exportu dat z úložiště objektů BLOB v Azure.  

## <a name="view-job-and-drive-status"></a>Zobrazení stavu úlohy a jednotky
Stav úloh importu nebo exportu můžete sledovat na kartě **Import/export** v Azure Portal.
1. Přihlaste se k https://portal.azure.com/ .
2. Vyhledejte **úlohy import/export**.

    ![Vyhledat úlohy importu/exportu](./media/storage-import-export-view-drive-status/open-import-export-tab.png)

 3. Na stránce se zobrazí seznam úloh import/export.

    ![Zobrazit stav úlohy](./media/storage-import-export-view-drive-status/job-state.png)

4. Pokud chcete zobrazit podrobnosti úlohy, vyberte úlohu a klikněte na ni.

   ![Zobrazit podrobný stav úlohy](./media/storage-import-export-view-drive-status/job-detail.png)
  
## <a name="view-job-status"></a>Zobrazení stavu úlohy

V závislosti na tom, kde se jednotka právě nachází, se zobrazí jedna z následujících stavů úlohy.

| Stav úlohy | Popis |
|:--- |:--- |
| Vytváření | Po vytvoření úlohy se její stav nastaví na **vytvořit**. Když je úloha ve stavu **vytváření** , služba import/export předpokládá, že se jednotky nedodaly do datového centra. Úloha může zůstat v tomto stavu po dobu až dvou týdnů, po jejímž uplynutí je služba automaticky odstranila. |
| Expedice | Po odeslání balíčku byste měli aktualizovat informace o sledování v Azure Portal.  Tím se úloha zapíná do stavu **expedice** . Úloha zůstane ve stavu **expedice** po dobu až dvou týdnů. 
| Přijato | Po přijetí všech jednotek v datovém centru se stav úlohy nastaví na **přijato**. |
| Probíhá přenos | Po zahájení zpracování aspoň jedné jednotky se stav úlohy nastaví na **přenos**. Další informace najdete v informacích o [stavu jednotky](#view-drive-status). |
| Zabalení | Po dokončení zpracování všech jednotek se úloha umístí do stavu **balení** , dokud se jednotky nevrátí zpět. |
| Dokončeno | Po odeslání všech jednotek zpět do vás, pokud byla úloha dokončena bez chyb, je úloha nastavena na **dokončeno**. Tato úloha se automaticky odstraní po 90 dnech v **dokončeném** stavu. |
| Uzavřeno | Po odeslání všech jednotek zpět, pokud došlo k nějakým chybám během zpracování úlohy, je úloha nastavena na **Uzavřeno**. Tato úloha se automaticky odstraní po 90 dnech v **zavřeném** stavu. |

## <a name="view-drive-status"></a>Zobrazení stavu jednotky

Následující tabulka popisuje životní cyklus jednotlivé jednotky při přechodu prostřednictvím úlohy importu nebo exportu. Aktuální stav jednotlivých jednotek v úloze se zobrazuje v Azure Portal.

V následující tabulce jsou popsány všechny stavy, které mohou jednotlivé jednotky v úloze Procházet.

| Stav jednotky | Popis |
|:--- |:--- |
| Dané | Při vytvoření úlohy z Azure Portal se pro úlohu importu **zadá** počáteční stav jednotky. V případě úlohy exportu, protože při vytvoření úlohy není zadána žádná jednotka, je **přijat** počáteční stav jednotky. |
| Přijato | Pokud služba import/export zpracovává jednotky, které byly přijaty od společnosti pro expedici pro úlohu importu, přejde do stavu **přijato** . V případě úlohy exportu je počáteční stav jednotky stav **přijato** . |
| NeverReceived | Jednotka se přesune do stavu **NeverReceived** , když dorazí balíček pro úlohu, ale balíček disk neobsahuje. Jednotka se také přesune do tohoto stavu, pokud datacentrum ještě nedostalo balíček a služba obdržela informace o expedici alespoň dva týdny. |
| Probíhá přenos | Jednotka se přesune do stavu **přenosu** , když služba začne přenášet data z jednotky na Azure Storage. |
| Dokončeno | Jednotka se přesune do stavu **dokončeno** , pokud služba úspěšně přenesla všechna data bez chyb.
| CompletedMoreInfo | Jednotka se přesune do stavu **CompletedMoreInfo** , když služba nastala problémy při kopírování dat z disku nebo na disk. Tyto informace mohou obsahovat chyby, varování nebo informativní zprávy týkající se přepsání objektů BLOB.
| ShippedBack | Jednotka se po odeslání z datového centra zpátky na zpáteční adresu přesune do stavu **ShippedBack** . |

Tento obrázek z Azure Portal zobrazuje stav jednotky ukázkové úlohy:

![Zobrazit stav jednotky](./media/storage-import-export-view-drive-status/drive-state.png)

Následující tabulka popisuje stavy selhání jednotek a akce prováděné pro jednotlivé stavy.

| Stav jednotky | Událost | Řešení/další krok |
|:--- |:--- |:--- |
| Nikdy Nepřijato | Jednotka označená jako **NeverReceived** (protože nebyla přijata jako součást dodávky úlohy) přijde do jiné dodávky. | Provozní tým přesune jednotku k **přijetí**. |
| – | Jednotka, která není součástí žádné úlohy, přijde v datovém centru jako součást jiné úlohy. | Jednotka je označena jako dodatečná jednotka. Po dokončení úlohy přidružené k původnímu balíčku se vám vrátí. |

## <a name="time-to-process-job"></a>Čas zpracování úlohy
Doba potřebná ke zpracování úlohy importu/exportu se liší v závislosti na mnoha faktorech, jako například:

-  Čas expedice
-  Zatížení v datacentru
-  Typ úlohy a velikost kopírovaných dat
-  Počet disků v úloze. 

Služba import/export nemá smlouvu SLA, ale služba se snaží dokončit kopírování 7 až 10 dní od přijetí těchto disků. Kromě stavu publikovaného v Azure Portal můžete pomocí rozhraní REST API sledovat průběh úlohy. Chcete-li zobrazit procentní průběh kopírování, použijte parametr procenta dokončení v volání rozhraní API operací [seznam úloh](/previous-versions/azure/dn529083(v=azure.100)) .


## <a name="next-steps"></a>Další kroky

* [Přenos dat pomocí nástroje příkazového řádku AzCopy](../storage/common/storage-use-azcopy-v10.md)
* [Ukázka REST API exportu pro import do Azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)