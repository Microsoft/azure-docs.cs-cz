---
title: Použití souborů Azure HPC Cache a Azure NetApp
description: Jak pomocí azure hpc cache zlepšit přístup k datům uloženým se soubory Azure NetApp
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238683"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Použití mezipaměti Azure HPC se soubory Azure NetApp

[Soubory Azure NetApp](https://azure.microsoft.com/services/netapp/) můžete použít jako cíl úložiště pro azure hpc cache. Tento článek vysvětluje, jak mohou obě služby spolupracovat, a poskytuje tipy pro jejich nastavení.

Soubory Azure NetApp kombinují svůj operační systém ONTAP s škálovatelností a rychlostí Microsoft Azure. Tato kombinace umožňuje uživatelům přesunout zavedené pracovní postupy do cloudu bez přepisování kódu.

Přidání součásti Azure HPC Cache může zlepšit přístup k souborům tím, že představí více svazků souborů Azure NetApp v jednom agregovaném oboru názvů. Může poskytovat ukládání okrajů do mezipaměti svazků umístěných v jiné oblasti služeb. Může také zlepšit výkon na vyžádání pro svazky, které byly vytvořeny na nižších úrovních úrovně služeb, aby se ušetřily náklady.

## <a name="overview"></a>Přehled

Pokud chcete používat systém Azure NetApp Files jako back-endové úložiště s Azure HPC Cache, postupujte podle tohoto procesu.

1. Vytvořte systém Azure NetApp Files a svazky podle pokynů v [naplánujte svůj systém níže](#plan-your-azure-netapp-files-system).
1. Vytvořte mezipaměť Azure HPC v oblasti, kde potřebujete přístup k souborům. (Použijte pokyny v [části Vytvoření mezipaměti Azure HPC](hpc-cache-create.md).)
1. [Definujte cíle úložiště](#create-storage-targets-in-the-cache) v mezipaměti, které odkazují na svazky souborů Azure NetApp. Vytvořte jeden cíl úložiště mezipaměti pro každou jedinečnou ADRESU IP použitou pro přístup ke svazkům.
1. Vyvažte klienty [připojit Azure HPC Cache](#mount-storage-targets) namísto připojení svazků Azure NetApp Files přímo.

## <a name="plan-your-azure-netapp-files-system"></a>Plánování systému Azure NetApp Files

Při plánování systému Azure NetApp Files věnujte pozornost položkám v této části a ujistěte se, že ho můžete hladce integrovat s Azure HPC Cache.

Před vytvořením svazků pro použití s azure hpc cache si taky přečtěte [dokumentaci k souborům Azure NetApp.](../azure-netapp-files/index.yml)

### <a name="nfs-client-access-only"></a>Pouze přístup klienta služby NFS

Azure HPC Cache aktuálně podporuje pouze přístup nfs. Nelze jej použít s bitovými svazky v režimu SMB ACL nebo POSIX.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Výhradní podsíť pro soubory Azure NetApp

Soubory Azure NetApp používá pro své svazky jednu delegovanou podsíť. Tuto podsíť nemohou používat žádné jiné prostředky. Pro soubory Azure NetApp lze také použít jenom jednu podsíť ve virtuální síti. Další informace najdete v [pokynech pro plánování sítě Soubory Azure NetApp](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Velikost delegované podsítě

Při vytváření systému Azure NetApp Files pro použití s mezipaměťazure HPC použijte minimální velikost delegované podsítě.

Minimální velikost, která je zadána maskou sítě /28, obsahuje 16 adres IP. V praxi soubory Azure NetApp používá pouze tři z těchto dostupných IP adres pro přístup ke svazku. To znamená, že stačí vytvořit tři cíle úložiště ve vaší mezipaměti HPC Azure, abyste pokryli všechny svazky.

Pokud je delegovaná podsíť příliš velká, je možné, že svazky souborů Azure NetApp budou používat více IP adres, než dokáže zpracovat jedna instance mezipaměti Azure HPC. Jedna mezipaměť může mít maximálně deset cílů úložiště.

Příklad rychlého startu v dokumentaci k souborům Azure NetApp používá 10.7.0.0/16 pro delegovanou podsíť, která poskytuje podsíť, která je příliš velká.

### <a name="capacity-pool-service-level"></a>Úroveň služby kapacitního fondu

Při výběru úrovně služeb pro fond kapacit zvažte pracovní postup. Pokud často zapisujete data zpět na svazek Souborů Azure NetApp, může být výkon mezipaměti omezen, pokud je čas zpětného zápisu pomalý. Zvolte vysokou úroveň služeb pro svazky, které budou mít časté zápisy.

Svazky s nízkou úrovní služeb mohou také vykazovat určité zpoždění na začátku úlohy, zatímco mezipaměť předem vyplňuje obsah. Po cache je v provozu s dobrou pracovní sadu souborů, zpoždění by se stát nepostřehnutelné.

Je důležité naplánovat úroveň služby fondu kapacity předem, protože ji nelze po vytvoření změnit. Nový svazek by musel být vytvořen v jiném fondu kapacity a data zkopírována.

Všimněte si, že můžete změnit kvótu úložiště svazku a velikost fondu kapacity bez narušení přístupu.

## <a name="create-storage-targets-in-the-cache"></a>Vytvoření cílů úložiště v mezipaměti

Po nastavení systému Azure NetApp Files a vytvoření mezipaměti Azure HPC definujte cíle úložiště v mezipaměti, které odkazují na svazky systému souborů.

Vytvořte jeden cíl úložiště pro každou IP adresu používanou svazky souborů Azure NetApp. Adresa IP je uvedena na stránce s pokyny pro připojení svazku.

Pokud více svazků sdílí stejnou ADRESU IP, můžete použít jeden cíl úložiště pro všechny z nich.  

Podle [pokynů k připojení v dokumentaci k souborům Azure NetApp](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) vyhledejte IP adresy, které chcete použít.

Ip adresy najdete taky pomocí azure cli:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Export názvů v systému Azure NetApp Files mají jednu součást cesty. Nepokoušejte se vytvořit cíl úložiště ``/`` pro kořenový export v souborech Azure NetApp, protože tento export neposkytuje přístup k souborům.

Neexistují žádná zvláštní omezení na cesty virtuálního oboru názvů pro tyto cíle úložiště.

## <a name="mount-storage-targets"></a>Připojení cílů úložiště

Klientské počítače by měly připojit mezipaměť namísto připojení svazků Azure NetApp Files přímo. Postupujte podle pokynů v [části Připojení mezipaměti Azure HPC](hpc-cache-mount.md).

## <a name="next-steps"></a>Další kroky

* Další informace o nastavení a používání [souborů Azure NetApp](../azure-netapp-files/index.yml)
* Chcete-li pomoci s plánováním a nastavením systému Azure HPC Cache pro použití souborů Azure NetApp, [obraťte se na podporu](hpc-cache-support-ticket.md).
