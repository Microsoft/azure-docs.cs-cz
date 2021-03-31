---
title: Použití mezipaměti HPC Azure a Azure NetApp Files
description: Jak používat mezipaměť prostředí Azure HPC ke zvýšení přístupu k datům uloženým pomocí Azure NetApp Files
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86497008"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Použití mezipaměti prostředí Azure HPC s Azure NetApp Files

Jako cíl úložiště pro mezipaměť HPC Azure můžete použít [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) . Tento článek vysvětluje, jak můžou tyto dvě služby spolupracovat, a poskytuje tipy pro jejich nastavení.

Azure NetApp Files kombinuje svůj ONTAP operační systém s škálovatelností a rychlostí Microsoft Azure. Tato kombinace umožňuje uživatelům přesunout zavedené pracovní postupy do cloudu bez nutnosti přepisovat kód.

Přidání komponenty mezipaměti HPC Azure může zlepšit přístup k souborům tím, že v jednom agregovaném oboru názvů doplní více Azure NetApp Files svazků. Může poskytovat hraniční ukládání do mezipaměti pro svazky umístěné v jiné oblasti služeb. Může taky zlepšit výkon na vyžádání pro svazky, které se vytvořily na úrovních nižší úrovně služby za účelem úspory nákladů.

## <a name="overview"></a>Přehled

Pokud chcete použít systém Azure NetApp Files jako back-end úložiště s využitím Azure HPC cache, postupujte podle tohoto postupu.

1. Vytvořte Azure NetApp Files systém a svazky podle pokynů v [části Plánování systému níže](#plan-your-azure-netapp-files-system).
1. Vytvořte mezipaměť Azure HPC v oblasti, kde potřebujete přístup k souboru. (Použijte pokyny v tématu [vytvoření mezipaměti prostředí Azure HPC](hpc-cache-create.md).)
1. [Definujte cíle úložiště](#create-storage-targets-in-the-cache) v mezipaměti, které odkazují na svazky Azure NetApp Files. Vytvořte pro každou jedinečnou IP adresu, která se používá pro přístup ke svazkům, jeden cíl úložiště mezipaměti.
1. Klienti [připojovat mezipaměť prostředí Azure HPC](#mount-storage-targets) místo přímého připojování svazků Azure NetApp Files.

## <a name="plan-your-azure-netapp-files-system"></a>Plánování Azure NetApp Filesho systému

Při plánování Azure NetApp Files systému věnujte pozornost položkám v této části, abyste se ujistili, že je můžete hladce integrovat s mezipamětí prostředí Azure HPC.

Než začnete vytvářet svazky pro použití s mezipamětí Azure HPC, přečtěte si také [dokumentaci Azure NetApp Files](../azure-netapp-files/index.yml) .

### <a name="nfs-client-access-only"></a>Pouze klientský přístup k systému souborů NFS

Mezipaměť HPC Azure v současné době podporuje jenom přístup k systému souborů NFS. Nedá se použít s bitovými svazky v režimu ACL SMB nebo POSIX.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Exkluzivní podsíť pro Azure NetApp Files

Azure NetApp Files používá pro své svazky jednu delegovanou podsíť. Tuto podsíť nemůžou používat žádné jiné prostředky. Pro Azure NetApp Files se dá použít taky jenom jedna podsíť ve virtuální síti. Další informace najdete v [pokynech k Azure NetApp Filesmu plánování sítě](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Velikost delegované podsítě

Při vytváření Azure NetApp Filesho systému pro použití s mezipamětí Azure HPC použijte minimální velikost delegované podsítě.

Minimální velikost, která je zadána pomocí síťové masky/28, poskytuje 16 IP adres. V praxi používá Azure NetApp Files jenom tři z dostupných IP adres pro přístup k svazkům. To znamená, že stačí vytvořit tři cíle úložiště v mezipaměti HPC Azure, aby pokryly všechny svazky.

Pokud je delegovaná podsíť moc velká, je možné, že Azure NetApp Files svazky budou používat víc IP adres, než jedna instance mezipaměti HPC Azure může zpracovat. Jedna mezipaměť může mít maximálně 10 cílů úložiště.

Příklad rychlého startu v dokumentaci Azure NetApp Files používá 10.7.0.0/16 pro delegovanou podsíť, která dává příliš velkou podsíť.

### <a name="capacity-pool-service-level"></a>Úroveň služby fondu kapacity

Při volbě úrovně služby pro fond kapacit zvažte svůj pracovní postup. Pokud často píšete data zpátky do Azure NetApp Filesho svazku, může být výkon mezipaměti omezený, pokud je čas zpětného zápisu pomalý. Vyberte vysokou úroveň služby pro svazky, které budou obsahovat časté zápisy.

Svazky s nízkými úrovněmi služeb také mohou zobrazovat prodlevu na začátku úkolu, zatímco mezipaměť předběžně vyplní obsah. Po zprovoznění a spuštění mezipaměti s dobrou pracovní sadou souborů by zpoždění mělo být nepatrné.

Je důležité naplánovat úroveň služby fondu kapacity předem, protože po vytvoření se nedá změnit. Nový svazek by se musel vytvořit v jiném fondu kapacity a kopírovaná data.

Všimněte si, že můžete změnit kvótu úložiště svazku a velikost fondu kapacity bez přerušení přístupu.

## <a name="create-storage-targets-in-the-cache"></a>Vytvoření cílů úložiště v mezipaměti

Po nastavení systému Azure NetApp Files a vytvoření mezipaměti prostředí Azure HPC definujte cíle úložiště v mezipaměti, které odkazují na svazky systému souborů.

Vytvořte jeden cíl úložiště pro každou IP adresu, kterou používají vaše Azure NetApp Files svazky. IP adresa je uvedena na stránce pokyny pro připojení svazku.

Pokud více svazků sdílí stejnou IP adresu, můžete použít jeden cíl úložiště pro všechny.  

Podle [pokynů pro připojení v dokumentaci Azure NetApp Files](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) vyhledejte IP adresy, které se mají použít.

IP adresy můžete také najít pomocí Azure CLI:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Export názvů v systému Azure NetApp Files má komponentu s jednou cestou. Nepokoušejte se vytvořit cíl úložiště pro kořenový export ``/`` v Azure NetApp Files, protože tento export neposkytuje přístup k souboru.

Pro tyto cíle úložiště nejsou k dispozici žádná zvláštní omezení pro cesty k virtuálnímu oboru názvů.

## <a name="mount-storage-targets"></a>Připojit cíle úložiště

Klientské počítače by měly připojit mezipaměť místo přímého připojení Azure NetApp Filesch svazků. Postupujte podle pokynů v tématu [připojení mezipaměti prostředí Azure HPC](hpc-cache-mount.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o nastavení a použití [Azure NetApp Files](../azure-netapp-files/index.yml)
* Pokud potřebujete pomoc s plánováním a nastavením systému Azure HPC cache pro použití Azure NetApp Files, obraťte se na [podporu](hpc-cache-support-ticket.md).
