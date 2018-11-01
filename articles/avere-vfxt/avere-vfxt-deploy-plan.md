---
title: Plánování Avere vFXT systém – Azure
description: Popisuje plánování před nasazením Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f0e5523565dc561ed457dbc340835ad1889cb876
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633840"
---
# <a name="plan-your-avere-vfxt-system"></a>Plánování Avere vFXT systému

Tento článek vysvětluje, jak naplánovat nové vFXT Avere pro Azure clusteru, ujistěte se, že je umístěn a odpovídající velikost pro vaše potřeby cluster, který vytvoříte. 

Před přechodem na Azure Marketplace nebo vytvořením virtuálních počítačů, zvažte, jak budou cluster používat další prvky v Azure. Naplánujte, kde bude umístěný ve vaší privátní sítě a podsítě prostředky clusteru a rozhodnout, kde bude back endové úložné. Ujistěte se, že jsou uzly clusteru, který vytvoříte dostatečný výkon pro podporu pracovního postupu. 

Pokud se chcete dozvědět víc, čtěte dál.

## <a name="resource-group-and-network-infrastructure"></a>Skupiny a síťové infrastruktury prostředků

Zvažte, kde budou prvky vaší Avere vFXT pro nasazení v Azure. Následující diagram znázorňuje možných uspořádání vFXT Avere pro komponenty Azure:

![Diagram znázorňující cluster kontroleru a cluster virtuálních počítačů v rámci jedné podsítě. Hranice kolem podsíť je hranice sítě vnet. Uvnitř virtuální sítě je šestiúhelník představující koncový bod služby úložiště; je propojená s přerušovaným šipku do úložiště objektů Blob mimo virtuální síť.](media/avere-vfxt-components-option.png)

Při plánování síťové infrastruktury vašeho systému vFXT Avere, postupujte podle těchto pokynů:

* Všechny prvky se mají spravovat s novým předplatným vytvořeným Avere vFXT nasazení. Tato strategie zjednodušuje sledování nákladů a čištění a vám také pomůže kvóty prostředků oddílu. Avere vFXT, protože se používají u velkého počtu klientů izolaci klientů a clusteru v rámci jednoho předplatného chrání další důležité úlohy z možných omezování během zřizování prostředků.

* Vyhledejte vaše klientské systémy výpočetní blízko vFXT clusteru. Back endové úložiště může být více vzdálených.  

* Pro jednoduchost vyhledejte vFXT clusteru a clusteru adaptéru virtuálního počítače ve stejné virtuální síti (vnet) a ve stejné skupině prostředků. Měli by také použít stejný účet úložiště. 

* Cluster se musí nacházet ve vlastní podsíti nedošlo ke konfliktům IP adres s klienty nebo výpočetní prostředky. 

## <a name="ip-address-requirements"></a>Požadavky IP adres 

Ujistěte se, že má váš cluster podsíť dostatečně velký rozsah IP adres pro podporu clusteru. 

Avere vFXT cluster používá následující IP adresy:

* IP adresa pro jeden cluster pro správu. Tato adresa můžete přesunout na všechny uzly v clusteru, ale je vždy k dispozici, takže můžete připojit k nástroji Konfigurace Avere ovládací panely.
* Pro každý uzel clusteru:
  * Nejméně jednu IP adresu klienta. (Všechny adresy klienta se spravují v clusteru *vserver*, které lze přesunout mezi uzly a podle potřeby.)
  * Jedna IP adresa pro komunikaci s clustery
  * Jedna instance IP adresa (přiřazená k virtuálnímu počítači)

Pokud používáte úložiště objektů Blob v Azure, také může být potřeba IP adresy z virtuální sítě pro váš cluster:  

* Účet úložiště objektů Blob v Azure vyžaduje minimálně 5 IP adres. Mějte tento požadavek Pokud máte přístup k úložišti objektů Blob ve stejné virtuální síti jako cluster.
* Pokud používáte úložiště objektů Blob v Azure, který se nachází mimo virtuální síť pro cluster, měli byste vytvořit koncový bod služby úložiště uvnitř virtuální sítě. Tento koncový bod nepoužívá IP adresu.

Máte možnost k vyhledání síťové prostředky a úložiště objektů Blob (Pokud se používá) v různých skupinách prostředků z clusteru.

## <a name="vfxt-node-sizes"></a>velikosti uzlů vFXT 

Virtuální počítače, které slouží jako uzly clusteru určit požadavek kapacitu propustnosti a úložiště mezipaměti. Můžete vybrat ze dvou typů instance s jinou paměť, procesor a charakteristiky místního úložiště. 

Každý uzel vFXT budou stejné. To znamená pokud vytvoříte cluster se třemi uzly budete mít tři virtuální počítače stejného typu a velikosti. 

| Typ instance | vCPU | Memory (Paměť)  | Místní úložiště SSD  | Max. datových disků | Propustnost disku bez mezipaměti | Síťová karta (počet) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D16s_v3 | 16  | 64 GiB  | 128 GiB  | 32 | 25 600 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ <br/> 384 MB/s | 8 000 MB/s (8) |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51 200 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ <br/> 768 MB/s | 16 000 MB/s (8)  |

Mezipaměť disku na jeden uzel lze konfigurovat a můžete rage od 1 000 GB až 8000 GB. Doporučená velikost mezipaměti pro uzly Standard_D16s_v3 je 1 TB na jeden uzel a 4 TB na jeden uzel se doporučuje pro Standard_E32s_v3 uzly.

Další informace o těchto virtuálních počítačů přečtěte si následující dokumenty Microsoft Azure:

* [Velikosti virtuálních počítačů pro obecné účely](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
* [Paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Kvóty účtu

Ujistěte se, že vaše předplatné má kapacitu ke spouštění clusteru vFXT Avere, jakož i computing nebo klientské systémy, která se používá. Čtení [kvóty pro vFXT cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) podrobnosti.

## <a name="back-end-data-storage"></a>Back endovým datům úložiště

Pokud není v mezipaměti, pracovní sada se uloží v novém kontejneru objektů Blob nebo ve stávajícím cloudem nebo hardware úložný systém?

Pokud chcete používat úložiště objektů Blob v Azure pro back-endu, měli byste vytvořit nový kontejner jako součást vytváření clusteru vFXT. Použití ``create-cloud-backed-container`` nasazovací skript a zadejte úložiště účtu. pro nový kontejner objektů Blob. Tato možnost se vytvoří a nakonfiguruje nový kontejner, tak, aby byl připravený k použití ihned poté, co bude cluster připravený. Čtení [vytvářet uzly a nakonfigurujte cluster](avere-vfxt-deploy.md#create-nodes-and-configure-the-cluster) podrobnosti.

> [!NOTE]
> Pouze prázdné kontejnery úložiště objektů Blob může sloužit jako základní křížového Avere vFXT systému. VFXT musí mít možnost spravovat ukládání objektu bez nutnosti zachovat existující data. 
>
> Čtení [přesun dat do clusteru vFXT](avere-vfxt-data-ingest.md) informace o kopírování dat do nového kontejneru clusteru efektivně pomocí klientských počítačů a Avere vFXT mezipaměti.

Pokud chcete použít existující místní systém úložiště, musíte přidat do clusteru vFXT po jeho vytvoření. ``create-minimal-cluster`` Nasazovací skript vytvoří vFXT cluster s žádné úložiště back-end. Čtení [konfigurace úložiště](avere-vfxt-add-storage.md) podrobné pokyny o tom, jak přidat existující systém úložiště do clusteru vFXT Avere. 

## <a name="next-step-understand-the-deployment-process"></a>Další krok: procesu nasazení

[Přehled nasazení](avere-vfxt-deploy-overview.md) poskytuje celkový přehled o všechny kroky potřebné k vytvoření Avere vFXT systému Azure a získat připravené k obsluze data.  