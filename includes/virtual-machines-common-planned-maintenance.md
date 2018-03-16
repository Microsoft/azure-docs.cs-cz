---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 193003cef0aed464596e913c0df86e6123292b9f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
Azure pravidelně provádí aktualizace ke zlepšení spolehlivosti, výkonu a zabezpečení infrastruktury hostitele pro virtuální počítače. Tyto aktualizace rozsahu od opravy softwarové součásti v hostitelské prostředí (např. operační systém, hypervisor a různé agenty nasazené na hostiteli), upgrade síťových součástí, na vyřazení z provozu hardwaru. Většina tyto aktualizace se provádějí bez žádný vliv na hostované virtuální počítače. Ale existují případy, kdy aktualizace mít vliv:

- Pokud údržby nevyžaduje restartování, Azure používá místní migrace pro pozastavení virtuálního počítače během hostitele je aktualizována.

- Pokud údržby vyžaduje restartování, zobrazí oznámení o při plánované údržby. V těchto případech budete také mít k dispozici časový interval, kde můžete spustit údržby sami, v čase, který vám vyhovuje.

Tato stránka popisuje, jak Microsoft Azure provádí oba typy údržby. Další informace o neplánované události (výpadků) najdete v tématu Správa dostupnosti virtuálních počítačů [Windows] (.. / articles/virtual-machines/windows/manage-availability.md) nebo [Linux](../articles/virtual-machines/linux/manage-availability.md).

Aplikace běžící na virtuálním počítači může shromažďovat informace o nadcházejících aktualizace pomocí služby Azure Metadata pro [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) nebo [Linux] (.. / articles/virtual-machines/linux/instance-metadata-service.md).

"Jak na to" informace na správu plánované maintence najdete v tématu "Zpracování plánované údržby oznámení" pro [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) nebo [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="in-place-vm-migration"></a>Místní migrace virtuálních počítačů

Když se aktualizace nevyžadují úplné restartování, použije se při migraci za provozu na místě. Během aktualizace se virtuální počítač pozastaví na přibližně 30 sekund, zachování paměti v paměti RAM, během hostitelské prostředí potřebné aktualizace a opravy. Virtuální počítač je pak obnoveno a automaticky synchronizuje hodiny virtuálního počítače.

U virtuálních počítačů ve skupinách dostupnosti aktualizace domény jsou aktualizované současně. Všechny virtuální počítače v jedné doméně aktualizace (UD) jsou pozastavena, aktualizovat a potom obnovit před plánovanou údržbu přejde k další UD.

Některé aplikace může být ovlivněno tyto typy aktualizací. Aplikace, které provádějí v reálném čase událostí zpracování, jako jsou datové proudy media nebo překódování nebo vysokou propustnost sítě scénáře, nemusí být navržena k tolerovat 30 sekundové pauzy. <!-- sooooo, what should they do? --> 


## <a name="maintenance-requiring-a-reboot"></a>Údržby, které vyžadují restartování

Pokud virtuální počítače je potřeba restartovat kvůli plánované údržbě, zobrazí se zpráva předem. Plánovaná údržba má dvě fáze: okno samoobslužné služby a plánované údržby.

**Samoobslužné služby okno** umožňuje zahájení údržby na virtuálních počítačů. Během této doby se můžete dotazovat každý virtuální počítač a zobrazit jejich stav zkontrolujte výsledek vaší poslední žádosti údržby.

Spouštění samoobslužné služby údržby virtuálního počítače je přesunuta do uzel, který již byl aktualizován a pak ho znovu zapne. Protože virtuální počítač se restartuje, dojde ke ztrátě dočasným diskovým a dynamické IP adresy přidružené k rozhraní virtuální sítě se aktualizují.

-Li spustit samoobslužné služby údržby a dojde k chybě během procesu, operace se zastavila, virtuální počítač není aktualizován a také odebere se ze iterace plánované údržby. Můžete se kontaktovat v později pomocí nového plánu a nabízí novou možnost udělat Údržba samoobslužné služby. 

Po uplynutí období samoobslužné služby, **plánované údržby** začne. Během této doby čas můžete stále dotazu pro okna údržby, ale již nebude možné spustit údržby sami.

## <a name="availability-considerations-during-planned-maintenance"></a>Důležité informace o dostupnosti během plánované údržby 

Pokud se rozhodnete k čekání, dokud okno plánované údržby, existuje několik možností vzít v úvahu pro údržbu nejvyšší availabilty vaše virtuálních počítačů. 

### <a name="paired-regions"></a>Spárované oblastí

Každé oblasti Azure je spárován s jinou oblast v rámci stejné geography, společně provádění pár místní. Během plánované údržby bude Azure aktualizovat pouze virtuální počítače v jedné oblasti pár oblast. Například při aktualizaci virtuálních počítačů v oblasti Střed USA – sever nebude Azure zároveň aktualizovat žádné virtuální počítače v oblasti Střed USA – jih. V ostatních oblastech, jako je Severní Evropa, však může údržba probíhat ve stejnou dobu jako v oblasti Východní USA. Pochopení fungování oblast páry vám může pomoci lépe distribuovat virtuální počítače v oblastech. Další informace najdete v tématu [oblast Azure páry](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="availability-sets-and-scale-sets"></a>Skupiny dostupnosti a sady škálování

Pokud nasazujete zatížení na virtuálních počítačích Azure, můžete vytvořit virtuální počítače v rámci dostupnosti nastavit pro zajištění vysoké dostupnosti do vaší aplikace. To zajistí, že během výpadku nebo údržby událostí, je k dispozici alespoň jeden virtuální počítač.

V rámci skupiny dostupnosti jednotlivé virtuální počítače jsou rozloženy až 20 aktualizace domény (UDs). Během plánované údržby je v každém okamžiku dopad pouze jednu aktualizaci domény. Upozorňujeme, že pořadí aktualizace domén ovlivněný neodehrává nutně postupně. 

Sady škálování virtuálního počítače se Azure výpočtový prostředek, který umožňuje nasadit a spravovat sadu identické virtuální počítače jako jeden prostředek. Sada škálování je automaticky nasadí napříč doménami aktualizace, jako jsou virtuální počítače v nastavení dostupnosti. Stejně jako s skupiny dostupnosti s sady škálování pouze jednu aktualizaci doméně ovlivní v každém okamžiku.

Další informace o konfiguraci virtuálních počítačů pro vysokou dostupnost, najdete v části Správa dostupnosti virtuálních počítačů pro [Windows](../articles/virtual-machines/windows/manage-availability.md) nebo [Linux](../articles/virtual-machines/linux/manage-availability.md).
