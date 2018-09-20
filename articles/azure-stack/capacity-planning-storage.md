---
title: Úložiště plánování kapacity pro Azure Stack | Dokumentace Microsoftu
description: Další informace o plánování pro nasazení Azure Stack kapacity úložiště.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: 9ea46860817d60c2ffbde68c0fc5ae6f6ca14877
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368616"
---
# <a name="azure-stack-storage-capacity-planning"></a>Plánování kapacity úložiště s Azure Stack
Azure Stack úložiště plánování kapacity informace jako pomoc při plánování řešení se úložiště udržet pod naleznete v následujících částech.

## <a name="uses-and-organization-of-storage-capacity"></a>Použití a organizace kapacita úložiště
Hyperkonvergované konfiguraci služby Azure Stack umožňuje sdílení fyzickými úložnými zařízeními. Tři hlavní divizí úložiště k dispozici jsou v rozmezí od infrastruktury, dočasné úložiště tenantské virtuální počítače a úložiště, zálohování, objekty BLOB, tabulky a fronty služby konzistentní úložiště Azure (ACS).

## <a name="spaces-direct-cache-and-capacity-tiers"></a>Prostorů s přímým přístupem, mezipaměť a kapacitní vrstvy
Je kapacita úložiště používá pro operační systém, místní protokolování, výpisů paměti a jiné dočasné infrastruktury úložiště potřebám. Tato kapacita místní úložiště je nezávislá na infrastruktuře (zařízení a kapacita) úložných zařízení, převedené pod správu konfigurace prostorů úložiště s přímým. Zbývající část úložných zařízení nachází v jeden fond kapacity úložiště bez ohledu na počet serverů v jednotce škálování. Tato zařízení jsou dvou typů: mezipaměti a kapacity.  Zařízení mezipaměti jsou jenom – vyrovnávací paměť. Prostory s přímým přístupem budou používat tato zařízení se zpětným zápisem a ukládání do mezipaměti pro čtení. Kapacity těchto zařízení, i když je použít, nejsou potvrzeny formátovaný, "visible" kapacita formátovaný virtuálních disků. Úložných zařízení se používají pro tento účel a zadejte "domovské umístění" data spravovaná přes prostory úložiště.

Všechny kapacity úložiště je přidělena a spravuje infrastruktury Azure stacku. Operátor, který se potřebuje rozhodovat o konfiguraci, přidělení, nebo řešení s možností při rozhodování o rozšiřování kapacity. Tato rozhodnutí o návrhu se provedly bylo v souladu s požadavky na řešení a jsou automatizované během buď počáteční instalaci a nasazování nebo při rozšiřování kapacity. Podrobnosti o odolnosti proti chybám, rezervované kapacity pro znovu sestaví a další podrobnosti byly zohledněny jako součást návrhu. 

Operátory můžete si vybrat mezi všechny flash nebo hybridní úložiště konfigurace:

![Plánování kapacity služby Azure storage](media/azure-stack-capacity-planning/storage.png)

Ve všech flash konfigurace mezipaměti je NVMe řadu SATA SSD nebo NVMe kapacity. V hybridní konfiguraci mezipaměti je volby NVMe a SATA SSD, zatímco je kapacita HDD.

Stručný přehled prostorů úložiště s přímým a konfiguraci úložiště služby Azure Stack je následujícím způsobem:
- Jeden fondu prostorů úložiště na jednotce škálování (všechna zařízení úložiště jsou nakonfigurované v rámci jeden fond)
- Virtuální disky se vytvoří jako tři kopie zrcadlení pro nejvyšší výkon a odolnost proti chybám
- Každý virtuální disk je formátován jako systém souborů ReFS
- Virtuální diskové kapacity se počítá a přidělených způsobem, ponechat jeden kapacity zařízení množství datové kapacity volné ve fondu. Jedná se o ekvivalent jednu jednotku kapacity na server.
- Každý systém souborů ReFS budou mít povoleno neaktivních dat šifrování nástrojem BitLocker. 

Virtuální – disky automaticky vytvořen a jejich kapacity jsou následující:




|Název|Výpočet kapacity|Popis|
|-----|-----|-----|
|Místní/spouštěcí zařízení|Minimální 340 GB<sup>1</sup>|Úložiště jednotlivých serverů pro bitové kopie operačního systému a "local" virtuální počítače infrastruktury|
|Infrastruktura|3,5 TB|Veškeré využití infrastruktury Azure stacku|
|VmTemp|Viz níže<sup>2</sup>|Dočasný disk připojený mají tenantské virtuální počítače a tato data uložená v těchto virtuálních disků|
|ACS|Viz níže <sup>3</sup>|Azure konzistentní kapacitou pro obsluhu objekty BLOB, tabulky a fronty|

<sup>1</sup> minimální kapacitu úložiště vyžaduje partnera řešení Azure Stack.

<sup>2</sup> velikost virtuálního disku použité pro dočasné disky virtuálního počítače se počítá jako poměr fyzické paměti serveru. Jak je uvedeno v následující tabulce pro velikosti virtuálních počítačů Azure IaaS, dočasný disk je poměr fyzické paměti přidělené virtuálnímu počítači. Přidělení Hotovo "dočasného úložiště na"disku ve službě Azure Stack se provede tak, aby sběr většinu případů použití, ale nemusí být schopen dál uspokojit se úložiště udržet pod všechny dočasného disku. Poměr zvolili je kompromis mezi při spotřebě nejsou většinou kapacity úložiště řešení pro dočasné diskové kapacity pouze zpřístupnění dočasné úložiště. Jeden server v jednotce škálování se vytvoří jeden disk dočasného úložiště. Kapacita dočasného úložiště nebude nárůst 10 % celkové dostupné kapacity úložiště ve fondu úložiště jednotce škálování. Výpočet je něco jako v následujícím příkladu:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> virtuální – disky vytvořené pro použití službou ACS jsou jednoduché dělení zbývající kapacity. Jak je uvedeno, všechny virtuální – disky jsou třícestný zrcadlový svazek a za jednu jednotku kapacity kapacity pro každý server je volné. Různé virtuální – disky uvedené výše se nejprve přiděluje a zbývající kapacity se pak použije pro ACS virtuální – disky.

## <a name="next-steps"></a>Další postup
[Další informace o plánování tabulky kapacity Azure Stack](capacity-planning-spreadsheet.md)
