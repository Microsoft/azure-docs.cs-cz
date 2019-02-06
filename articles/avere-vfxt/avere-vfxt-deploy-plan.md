---
title: Plánování Avere vFXT systém – Azure
description: Popisuje plánování před nasazením Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: a097110bac7dad630f9a85dd8b20678db0c739cf
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744652"
---
# <a name="plan-your-avere-vfxt-system"></a>Plánování systému Avere vFXT

Tento článek vysvětluje, jak naplánovat nové vFXT Avere pro Azure clusteru, ujistěte se, že je umístěn a odpovídající velikost pro vaše potřeby cluster, který vytvoříte. 

Před přechodem na Azure Marketplace nebo vytvořením virtuálních počítačů, zvažte, jak budou cluster používat další prvky v Azure. Naplánujte, kde bude umístěný ve vaší privátní sítě a podsítě prostředky clusteru a rozhodnout, kde bude back endové úložné. Ujistěte se, že jsou uzly clusteru, který vytvoříte dostatečný výkon pro podporu pracovního postupu. 

Pokud se chcete dozvědět víc, čtěte dál.

## <a name="resource-group-and-network-infrastructure"></a>Skupiny a síťové infrastruktury prostředků

Zvažte, kde budou prvky vaší Avere vFXT pro nasazení v Azure. Následující diagram znázorňuje možných uspořádání vFXT Avere pro komponenty Azure:

![Diagram znázorňující cluster kontroleru a cluster virtuálních počítačů v rámci jedné podsítě. Hranice kolem podsíť je hranice sítě vnet. Uvnitř virtuální sítě je šestiúhelník představující koncový bod služby úložiště; je propojená s přerušovaným šipku do úložiště objektů Blob mimo virtuální síť.](media/avere-vfxt-components-option.png)

Při plánování síťové infrastruktury vašeho systému vFXT Avere, postupujte podle těchto pokynů:

* Všechny prvky se mají spravovat s novým předplatným vytvořeným Avere vFXT nasazení. Mezi výhody patří: 
  * Jednodušší náklady sledování – zobrazení a auditování všech nákladů z prostředků infrastruktury a výpočetních cyklů v jednom předplatném.
  * Čištění jednodušší – můžete odebrat celé předplatné po dokončení projektu.
  * Pohodlné dělení prostředků kvóty – prostředky omezení šířky pásma při přenosu do velkého počtu klientů, které jsou používané pro vysoce výkonné výpočetní pracovní postup izolaci klientů vFXT Avere chránit další důležité úlohy a v clusteru jedno předplatné.

* Vyhledejte vaše klientské systémy výpočetní blízko vFXT clusteru. Back endové úložiště může být více vzdálených.  

* Pro jednoduchost vyhledejte vFXT clusteru a clusteru adaptéru virtuálního počítače ve stejné virtuální síti (vnet) a ve stejné skupině prostředků. Měli by také použít stejný účet úložiště. (Kontroler clusteru vytvoří cluster a může také sloužit pro správu příkazového řádku clusteru.)  

  > [!NOTE] 
  > Šablonu pro vytvoření clusteru můžete vytvořit novou skupinu prostředků a účet úložiště pro cluster. Můžete zadat existující skupinu prostředků, ale musí být prázdný.

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
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51 200 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ <br/> 768 MBps | 16 000 MB/s (8)  |

Mezipaměť disku na jeden uzel lze konfigurovat a můžete rage od 1 000 GB až 8000 GB. Doporučená velikost mezipaměti pro uzly Standard_D16s_v3 je 1 TB na jeden uzel a 4 TB na jeden uzel se doporučuje pro Standard_E32s_v3 uzly.

Další informace o těchto virtuálních počítačů přečtěte si následující dokumenty Microsoft Azure:

* [Velikosti virtuálních počítačů pro obecné účely](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
* [Paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Kvóty účtu

Ujistěte se, že vaše předplatné má kapacitu ke spouštění clusteru vFXT Avere, jakož i computing nebo klientské systémy, která se používá. Čtení [kvóty pro vFXT cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) podrobnosti.

## <a name="back-end-data-storage"></a>Back endovým datům úložiště

Pokud byste Avere vFXT cluster ukládání dat, když není v mezipaměti? Rozhodněte, jestli vaši pracovní sadu bude uložené dlouhodobém horizontu v novém kontejneru objektů Blob nebo ve stávajícím cloudem nebo systém hardware úložiště. 

Pokud chcete používat úložiště objektů Blob v Azure pro back-endu, měli byste vytvořit nový kontejner jako součást vytváření clusteru vFXT. Tato možnost se vytvoří a nakonfiguruje nový kontejner, tak, aby byl připravený k použití ihned poté, co bude cluster připravený. 

Čtení [vytvořit Avere vFXT pro Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) podrobnosti.

> [!NOTE]
> Pouze prázdné kontejnery úložiště objektů Blob může sloužit jako základní křížového Avere vFXT systému. VFXT musí mít možnost spravovat ukládání objektu bez nutnosti zachovat existující data. 
>
> Čtení [přesun dat do clusteru vFXT](avere-vfxt-data-ingest.md) informace o kopírování dat do nového kontejneru clusteru efektivně pomocí klientských počítačů a Avere vFXT mezipaměti.

Pokud chcete použít existující místní systém úložiště, musíte přidat do clusteru vFXT po jeho vytvoření. Čtení [konfigurace úložiště](avere-vfxt-add-storage.md) podrobné pokyny o tom, jak přidat existující systém úložiště do clusteru vFXT Avere.

## <a name="cluster-access"></a>Přístup ke clusteru 

VFXT Avere pro Azure cluster se nachází v privátní podsítě a cluster nemá veřejnou IP adresu. Musíte mít některé metody přístupu k privátní podsítě pro správu clusteru a připojení klientů. 

Možnosti přístupu patří:

* Jump hostitele – přiřadit veřejnou IP adresu na samostatný virtuální počítač v rámci privátní sítě a použijte ji k vytvoření tunelu SSL na uzly clusteru. 

  > [!TIP]
  > Pokud nastavíte veřejné IP adresy na adaptéru clusteru, můžete jako jump hostitele. Čtení [řadič clusteru podle odkazů hostitele](#cluster-controller-as-jump-host) Další informace.

* Virtuální privátní sítě (VPN) – konfigurace VPN typu point-to-site a site-to-site k privátní síti.

* Azure ExpressRoute – konfigurace soukromého připojení prostřednictvím partnera ExpressRoute. 

Podrobnosti o těchto možnostech najdete v článku [dokumentace ke službě Azure Virtual Network o internetovou komunikaci](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Kontroler clusteru podle odkazů hostitele

Pokud nastavíte veřejné IP adresy na adaptéru clusteru, můžete ho použít jako hostitele jump kontaktovat cluster vFXT Avere z mimo privátní podsítě. Protože kontroler má přístupová oprávnění k úpravě uzly clusteru, to však znamená malé bezpečnostní riziko.  

Pro důkladnější zabezpečení s použitím veřejné IP adresy použijte skupinu zabezpečení sítě umožňující příchozí přístup jenom přes port 22. Případně jde dál chránit systém zamčením dolů přístup ke zdrojové váš rozsah IP adres – to znamená, povolit připojení pouze z počítačů, které máte v úmyslu používat pro přístup ke clusteru.

Při vytváření clusteru, můžete rozhodnout, zda se k vytvoření veřejné IP adresy na adaptéru clusteru. 

* Pokud vytvoříte novou virtuální síť nebo novou podsíť, řadič clusteru přiřadit veřejnou IP adresu.
* Pokud vyberete stávající virtuální síť a podsítě služby, kontrolér cluster bude mít pouze privátní IP adresy. 

## <a name="next-step-understand-the-deployment-process"></a>Další krok: Vysvětlení procesu nasazení

[Přehled nasazení](avere-vfxt-deploy-overview.md) poskytuje celkový přehled o všechny kroky potřebné k vytvoření Avere vFXT systému Azure a získat připravené k obsluze data.  