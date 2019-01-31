---
title: Zobrazení stavu úlohy Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit stav úlohy importu/exportu a použití disků.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 225164fe00f70839446f8b74155cd3959f745a49
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467743"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Zobrazení stavu úlohy Azure Import/Export

Tento článek obsahuje informace o tom, jak zobrazit stav disku a úlohy pro úlohy Azure Import/Export. Služba Azure Import/Export umožňuje bezpečně přenášet velké objemy dat do objektů BLOB Azure a službou soubory Azure. Služba také umožňuje exportovat data z úložiště objektů Blob v Azure.  

## <a name="view-job-and-drive-status"></a>Zobrazení úlohy a stav jednotky
Můžete sledovat stav importu nebo exportu úloh na webu Azure Portal. Klikněte na tlačítko **Import/Export** kartu. Na stránce se zobrazí seznam vašich úloh.

![Zobrazení stavu úlohy](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Zobrazení stavu úlohy

Přečtěte si jedno z následujících stavů úloh v závislosti na tom, kde je jednotka v procesu.

| Stav úlohy | Popis |
|:--- |:--- |
| Vytváření | Jakmile se vytvoří úloha, její stav je nastavený na **vytváření**. Během úlohy **vytváření** stavu služby Import/Export předpokládá jednotky nebyly byl odeslán do datového centra. Úloha může zůstat v tomto stavu po dobu až dvou týdnů, po jejichž uplynutí je automaticky odstraní službu. |
| Expedice | Po dodání vašeho balíčku, měli byste aktualizovat informace o sledování na webu Azure Portal.  Tím dojde k úloze do **přesouvání** stavu. Úloha zůstává ve **přesouvání** stavu po dobu až dvou týdnů. 
| Přijato | Po přijetí všech jednotkách v datovém centru, stav úlohy je nastavený na **přijaté**. |
| Probíhá přesun | Po zahájení zpracování alespoň jednu jednotku, stav úlohy je nastavený na **přenos**. Další informace najdete v části [stavy jednotek](#view-drive-status). |
| Balení | Po dokončení zpracování všech jednotkách, úloha se umístí do **balení** stavu, dokud jednotky jsou odeslání zpět. |
| Dokončeno | Po všech jednotkách se dodávají zpět na vás, pokud byla úloha dokončena bez chyb, než úloha je nastaven **dokončeno**. Úloha je automaticky odstraněny po 90 dnech v **dokončeno** stavu. |
| Zavřeno | Po všech jednotkách se dodávají zpět na vás, pokud došlo k chybám při zpracování úlohy, úlohy je nastavena na **uzavřeno**. Úloha je automaticky odstraněny po 90 dnech v **uzavřeno** stavu. |

## <a name="view-drive-status"></a>Zobrazení stavu jednotky

Následující tabulka popisuje životní cyklus jednotlivé jednotky jako přechází prostřednictvím úlohu importu nebo exportu. Aktuální stav každé jednotky, v rámci úlohy se zobrazí na webu Azure Portal.

Následující tabulka popisuje každý stav, který může procházet každé jednotky, v rámci úlohy.

| Stav disku | Popis |
|:--- |:--- |
| Zadaný | Pro úlohu importu při vytvoření úlohy se na webu Azure Portal, je počáteční stav pro jednotku **zadaná**. Pro úlohu exportu protože není zadána žádná jednotka při vytvoření úlohy, stav počáteční jednotky je **přijaté**. |
| Přijato | Na jednotce přejde do **přijaté** stavu, když má služba Import/Export zpracovává jednotky, které byly přijaty z přepravní společnost pro úlohu importu. Pro úlohu exportu stav počáteční jednotku je **přijaté** stavu. |
| NeverReceived | Přesune jednotky **NeverReceived** stav, když dorazí balíček pro úlohu, ale balíček neobsahuje jednotce. Jednotky se také přesune do tohoto stavu, pokud to bylo dva týdny, protože služba přijala dodací informace, ale balíček ještě nedorazily v datovém centru. |
| Probíhá přesun | Přesune jednotky **přenos** stavu, kdy začne služba přenosu dat z jednotky do služby Azure Storage. |
| Dokončeno | Přesune jednotky **dokončeno** stavu, kdy služba byla úspěšně převedena všechna data bez chyb.
| CompletedMoreInfo | Přesune jednotky **CompletedMoreInfo** stavu, když službu došlo k některým problémům při kopírování dat z nebo na jednotku. Informace může patřit chyby, upozornění nebo informační zprávy o přepsání objektů BLOB.
| ShippedBack | Přesune jednotky **ShippedBack** stav, když byla byla odeslaná z datacentra zpět na návratovou adresu. |

Tuto image z webu Azure portal zobrazuje stav jednotky úlohu příkladu:

![Stav zobrazení](./media/storage-import-export-service/drivestate.png)

Následující tabulka popisuje stavy selhání jednotky a akce prováděné na jednotlivých stavech.

| Stav disku | Událost | Řešení / další krok |
|:--- |:--- |:--- |
| NeverReceived | Jednotku, která je označena jako **NeverReceived** (protože nebyla přijata jako součást úlohy dodávky) dorazí v jiném dodávky. | Přesune jednotku pro provozní tým **přijaté**. |
| neuvedeno | Jednotce, která není součástí jakoukoliv úlohu dorazí datacentra jako součást jiná úloha. | Na jednotce je označen jako dodatečné jednotky a vrátí se vám po dokončení úlohy spojené s původní balíček. |

## <a name="time-to-process-job"></a>Čas do procesu úlohy
Množství čas potřebný ke zpracování úlohu importu/exportu se liší v závislosti na řadě faktorů, jako například:

-  Dobu dodání
-  Načíst v datovém centru
-  Typ úlohy a velikosti dat kopírování
-  Počet disků v rámci úlohy. 

Služba Import/Export nemá žádnou smlouvu SLA, ale službě se snaží dokončete kopírování v 7 až 10 dní, po přijetí disky. Kromě stavu zveřejněný na webu Azure Portal rozhraní REST API je možné sledovat průběh úlohy. Dokončeno v parametru [vypisovat úlohy](/previous-versions/azure/dn529083(v=azure.100)) volání operace rozhraní API poskytuje průběh kopírování procento.


## <a name="next-steps"></a>Další postup

* [Nastavení nástroje WAImportExport](storage-import-export-tool-how-to.md)
* [Přenos dat pomocí příkazového řádku azcopy](storage-use-azcopy.md)
* [Ukázkový Import exportovat rozhraní REST API služby Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
