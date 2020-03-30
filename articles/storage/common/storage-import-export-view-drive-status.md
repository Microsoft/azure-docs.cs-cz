---
title: Zobrazení stavu úloh importu a exportu Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak zobrazit stav úloh importu a exportu a použitých jednotek.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 222c893f06d9adf77f8a8124af18bc03c5d20bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72821439"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Zobrazení stavu úloh služby Azure Import/Export

Tento článek obsahuje informace o tom, jak zobrazit jednotku a stav úlohy pro úlohy importu a exportu Azure. Služba Azure Import/Export se používá k bezpečnému přenosu velkého množství dat do objektů Blob Azure a souborů Azure. Služba se také používá k exportu dat z úložiště objektů blob Azure.  

## <a name="view-job-and-drive-status"></a>Zobrazit stav úlohy a jednotky
Můžete sledovat stav importu nebo exportu úloh z portálu Azure. Klikněte na kartu **Importovat nebo exportovat.** Na stránce se zobrazí seznam úloh.

![Zobrazit stav úlohy](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Zobrazení stavu úlohy

V závislosti na tom, kde se jednotka v procesu nachází, se zobrazí jeden z následujících stavů úlohy.

| Stav úlohy | Popis |
|:--- |:--- |
| Vytváření | Po vytvoření úlohy je její stav nastaven na **vytvořit**. Pokud je úloha ve stavu **Vytváření,** služba Import a export předpokládá, že jednotky nebyly dodány do datového centra. Úloha může zůstat v tomto stavu po dobu až dvou týdnů, po které je automaticky odstraněn službou. |
| Shipping | Po odeslání balíčku byste měli aktualizovat informace o sledování na webu Azure Portal.  Tím se úloha změní na stav **Expedice.** Úloha zůstává ve stavu **Doprava** po dobu až dvou týdnů. 
| Přijato | Po přijetí všech jednotek v datovém centru je stav úlohy nastaven na **přijato**. |
| Přenos | Po zahájení zpracování alespoň jedné jednotky je stav úlohy nastaven na **převést**. Další informace naleznete v disku [Drive States](#view-drive-status). |
| Balení | Po dokončení zpracování všech jednotek je úloha umístěna do stavu **Balení,** dokud nebudou jednotky odeslány zpět k vám. |
| Dokončeno | Po odeslání všech jednotek zpět k vám, pokud byla úloha dokončena bez chyb, je úloha nastavena na **Dokončeno**. Úloha je automaticky odstraněna po 90 dnech ve stavu **Dokončeno.** |
| Zavřeno | Po odeslání všech jednotek zpět k vám, pokud během zpracování úlohy došlo k chybám, je **úloha nastavena**na uzavřeno . Úloha je automaticky odstraněna po 90 dnech ve stavu **Uzavřeno.** |

## <a name="view-drive-status"></a>Zobrazení stavu jednotky

Níže uvedená tabulka popisuje životní cyklus jednotlivé jednotky při přechodu prostřednictvím úlohy importu nebo exportu. Aktuální stav každé jednotky v úloze se zobrazuje na webu Azure Portal.

Následující tabulka popisuje každý stav, kterým může každá jednotka v úloze projít.

| Stav jednotky | Popis |
|:--- |:--- |
| Zadaný | Pro úlohu importu při vytvoření úlohy z portálu Azure je **zadán**počáteční stav jednotky . Pro úlohu exportu, protože při vytvoření úlohy není zadána žádná jednotka, je počáteční stav jednotky **Přijato**. |
| Přijato | Jednotka přechází do stavu **Přijato,** když služba Import/Export zpracovala jednotky, které byly přijaty od přepravní společnosti pro úlohu importu. Pro úlohu exportu je počátečnístav jednotky Stav **Přijato.** |
| NeverReceived | Jednotka se přesune do stavu **NeverReceived,** když dorazí balíček pro úlohu, ale balíček neobsahuje jednotku. Jednotka se také přesune do tohoto stavu, pokud uplynuly dva týdny od přijetí informací o dopravě služby, ale balíček ještě nedorazil do datového centra. |
| Přenos | Jednotka se přesune do stavu **Přenos,** když služba začne přenášet data z jednotky do Azure Storage. |
| Dokončeno | Jednotka se přesune do stavu **Dokončeno,** když služba úspěšně přenesla všechna data bez chyb.
| CompletedMoreInfo | Jednotka se přesune do stavu **CompletedMoreInfo,** když služba zjistila některé problémy při kopírování dat z nebo na jednotku. Informace mohou zahrnovat chyby, upozornění nebo informační zprávy o přepsání objektů BLOB.
| ShippedBack | Jednotka se přesune do stavu **ShippedBack,** když byla dodána z datového centra zpět na zpáteční adresu. |

Tato bitová kopie z portálu Azure zobrazuje stav jednotky ukázkové úlohy:

![Zobrazit stav jednotky](./media/storage-import-export-service/drivestate.png)

Následující tabulka popisuje stavy selhání jednotky a akce pro každý stav.

| Stav jednotky | Událost | Rozlišení / Další krok |
|:--- |:--- |:--- |
| NeverReceived | Jednotka, která je označena jako **NeverReceived** (protože nebyla přijata jako součást dodávky projektu) dorazí do jiné dodávky. | Operační tým přesune jednotku na **Přijato**. |
| Není dostupné. | Jednotka, která není součástí žádné úlohy, dorazí do datového centra jako součást jiné úlohy. | Jednotka je označena jako další jednotka a je vrácena po dokončení úlohy přidružené k původnímu balíčku. |

## <a name="time-to-process-job"></a>Čas na zpracování úlohy
Doba, kterou zpracování úlohy importu a exportu trvá, se liší v závislosti na řadě faktorů, například:

-  Čas expedice
-  Načtení v datovém centru
-  Typ a velikost úlohy kopírovaných dat
-  Počet disků v úloze. 

Služba Import/Export nemá sla, ale služba se snaží dokončit kopii za 7 až 10 dní po přijetí disků. Kromě stavu zveřejněného na webu Azure Portal lze k sledování průběhu úlohy použít i api REST. Parametr procento dokončení ve volání rozhraní API operace [Seznam úloh](/previous-versions/azure/dn529083(v=azure.100)) poskytuje průběh procentuální kopie.


## <a name="next-steps"></a>Další kroky

* [Nastavení nástroje WAImportExport](storage-import-export-tool-how-to.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Ukázka rozhraní REST exportu azure importu](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
