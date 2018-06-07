---
title: Zobrazení stavu úlohy Azure Import/Export | Microsoft Docs
description: Zjistěte, jak zobrazit stav úlohy importu a exportu a použití disků.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 176cbf190b6442682a222eb4f24b6583fb87a46b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660746"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Zobrazení stavu úlohy Azure Import/Export

Tento článek obsahuje informace o tom, jak zobrazit stav disku a úlohy Azure Import/Export úloh. Služba Azure Import/Export se používá k zabezpečeným přenosům velké objemy dat objektů BLOB služby Azure a Azure Files. Služba také slouží k exportu dat z úložiště objektů Blob v Azure.  

## <a name="view-job-and-drive-status"></a>Zobrazit stav úlohy a jednotky
Můžete sledovat stav import nebo export úloh z portálu Azure. Klikněte **importu a exportu** kartě. Na stránce se zobrazí seznam vašich úloh.

![Zobrazení stavu úlohy](./media/storage-import-export-service/jobstate.png)


## <a name="view-job-status"></a>Zobrazení stavu úlohy

Najdete v jednom z následujících stavů úlohy v závislosti na tom, kde je vaše jednotka v procesu.

| Stav úlohy | Popis |
|:--- |:--- |
| Vytváření | Po vytvoření úlohy, je její stav nastavit na **vytváření**. Když úloha **vytváření** stavu, službu Import/Export předpokládá jednotky nebyly byla odeslaná do datového centra. Úlohy mohou zůstat v tomto stavu až dvou týdnů, po které se automaticky odstraní službou. |
| Expedice | Po dodáte vašeho balíčku, by měl aktualizovat informace o sledování na portálu Azure.  Tím dojde k úlohu do **přesouvání** stavu. Úloha zůstane v **přesouvání** stavu dobu až dvou týdnů. 
| Přijato | Po všech jednotkách přijme v datovém centru, stav úlohy je nastavený na **přijaté**. |
| Probíhá přesun | Po zahájení zpracování alespoň jednu jednotku, stav úlohy je nastavený na **přenos**. Další informace, přejděte na [jednotky stavy](#view-drive-status). |
| Balení | Po dokončení zpracování všech jednotkách, úloha je umístěn v **balení** stavu, dokud jednotky jsou sice vám. |
| Dokončené | Po všech jednotkách jsou sice pro vás, pokud úloha byla dokončena bez chyb, pak úloha je nastavena na **dokončeno**. Úloha se automaticky odstraněny po 90 dnech v **dokončeno** stavu. |
| Zavřeno | Po všech jednotkách jsou sice pro vás, pokud během zpracování úlohy byly všechny chyby, úlohy je nastavena na **uzavřeno**. Úloha se automaticky odstraněny po 90 dnech v **uzavřeno** stavu. |

## <a name="view-drive-status"></a>Zobrazit stav disku

Následující tabulka popisuje životní cyklus jednotlivé jednotky jako přechází prostřednictvím úlohu import nebo export. Aktuální stav každé jednotky, v rámci úlohy je vidět na portálu Azure.

Následující tabulka popisuje všechny stavy, které může předávat každé jednotky, v rámci úlohy.

| Stav disku | Popis |
|:--- |:--- |
| Zadaný | Pro úlohu importu při vytvoření úlohy z portálu Azure, je počáteční stav pro jednotku **zadaná**. Pro úlohy exportu, protože není zadána žádná jednotka při vytvoření úlohy, stav počáteční jednotky je **přijaté**. |
| Přijato | Jednotka přechází do **přijaté** stavu, když službu Import/Export zpracovala jednotky, které bylo přijato z společnosti přesouvání úlohy importu. Pro úlohy exportu, je stav počáteční jednotku **přijaté** stavu. |
| NeverReceived | Jednotka přesune **NeverReceived** stav, když dorazí balíčku pro úlohy, ale balíček neobsahuje jednotku. Na jednotku se také přesune do tento stav, pokud to bylo dva týdny, protože služba přijala přesouvání informace, ale balíček nebyl ještě přijaty v datacentru. |
| Probíhá přesun | Přesune na jednotku **přenos** stavu, kdy začne služba přenosu dat z jednotky do Azure Storage. |
| Dokončené | Přesune na jednotku **dokončeno** stavu, když má služba úspěšně přenesla všechna data bez chyb.
| CompletedMoreInfo | Přesune na jednotku **CompletedMoreInfo** stavu, kdy služba narazila některé problémy při kopírování dat z nebo na jednotku. Informace může obsahovat chyby, upozornění a informativní zprávy o přepsání objektů BLOB.
| ShippedBack | Přesune na jednotku **ShippedBack** stav, když byla zpět na návratovou adresu dodaný mezi datovým centrem. |

Tuto bitovou kopii z portálu Azure zobrazuje stav disku úlohu příklad:

![Zobrazení stavu jednotky](./media/storage-import-export-service/drivestate.png)

Následující tabulka popisuje stavy selhání jednotky a akcí provedených pro každý stav.

| Stav disku | Událost | Řešení / další krok |
|:--- |:--- |:--- |
| NeverReceived | Jednotku, která je označena jako **NeverReceived** (protože nebyla přijata jako součást úlohy dodávky) dorazí v jiné dodávky. | Provozní tým přesune jednotky a **přijaté**. |
| neuvedeno | Jednotku, která není součástí všechny úlohy dorazí na datovém centru jako součást jiná úloha. | Jednotka je označen jako další disk a je vrácen v případě dokončení úlohy spojené s původní balíčku. |

## <a name="time-to-process-job"></a>Čas do procesu úlohy
Dobu potřebnou ke zpracování úlohu importu a exportu se liší podle počtu faktorů, jako:

-  Doba dodávky
-  Načíst v datacentru
-  Typ úlohy a velikosti kopírování dat
-  Počet disků v rámci úlohy. 

Import a Export služba nemá SLA ale službu snaží dokončení kopírování v 7 až 10 dnů po disky jsou přijaty. Kromě stavu odeslány na portálu Azure rozhraní REST API umožňuje sledovat průběh úlohy. Procento dokončení parametr ve [seznamu úloh]() volání operace rozhraní API poskytuje průběh kopírování procento.


## <a name="next-steps"></a>Další postup

* [Nastavit nástroj WAImportExport](storage-import-export-tool-how-to.md)
* [Přenos dat pomocí příkazového řádku azcopy](storage-use-azcopy.md)
* [Ukázka Azure Import, Export REST API](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

