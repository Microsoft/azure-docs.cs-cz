---
title: Plánování Avere vFXT systém – Azure
description: Popisuje plánování před nasazením Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 46978d19a0789bb43e861ca89661aa5b78eb4ec7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271063"
---
# <a name="plan-your-avere-vfxt-system"></a>Plánování systému Avere vFXT

Tento článek vysvětluje, jak naplánovat nové vFXT Avere pro cluster Azure, který je umístěn a odpovídající velikost pro vaše potřeby. 

Před přechodem na Azure Marketplace nebo vytvořením virtuálních počítačů, zvažte, jak budou cluster používat další prvky v Azure. Naplánujte, kde bude umístěný ve vaší privátní sítě a podsítě prostředky clusteru a rozhodnout, kde bude back endové úložné. Ujistěte se, že jsou uzly clusteru, který vytvoříte dostatečný výkon pro podporu pracovního postupu. 

Pokud se chcete dozvědět víc, čtěte dál.

## <a name="resource-group-and-network-infrastructure"></a>Skupiny a síťové infrastruktury prostředků

Zvažte, kde budou prvky vaší Avere vFXT pro nasazení v Azure. Následující diagram znázorňuje možných uspořádání vFXT Avere pro komponenty Azure:

![Diagram znázorňující cluster kontroleru a cluster virtuálních počítačů v rámci jedné podsítě. Hranice kolem podsíť je hranice sítě vnet. Uvnitř virtuální sítě je šestiúhelník představující koncový bod služby úložiště; je propojená s přerušovaným šipku do úložiště objektů Blob mimo virtuální síť.](media/avere-vfxt-components-option.png)

Při plánování síťové infrastruktury vašeho systému vFXT Avere, postupujte podle těchto pokynů:

* Všechny prvky se mají spravovat s novým předplatným vytvořeným Avere vFXT nasazení. Mezi výhody patří: 
  * Jednodušší náklady sledování – zobrazení a auditování všech nákladů z prostředků infrastruktury a výpočetních cyklů v jednom předplatném.
  * Čištění jednodušší – můžete odebrat celé předplatné po dokončení projektu.
  * Vhodné vytváření oddílů prostředků kvóty - chránit další důležité úlohy z prostředky omezování izolováním Avere vFXT klientů a clusteru v rámci jednoho předplatného. Tím předejdete konfliktu při zpětném přepnutí velký počet klientů pro vysoce výkonné výpočetní pracovního postupu.

* Vyhledejte vaše klientské systémy výpočetní blízko vFXT clusteru. Back endové úložiště může být více vzdálených.  

* VFXT clusteru a clusteru adaptéru virtuálního počítače se musí nacházet ve stejné virtuální síti (vnet), ve stejné skupině prostředků a použít stejný účet úložiště. Vytvoření šablony automatizované clusteru zpracovává to pro většinu situací.

* Cluster se musí nacházet ve vlastní podsíti nedošlo ke konfliktům IP adres s klienty nebo výpočetní prostředky. 

* Většinu prostředků infrastruktury potřebné pro cluster, včetně skupin prostředků, virtuální sítě, podsítě a účty úložiště můžete vytvořit šablonu pro vytvoření clusteru. Pokud chcete používat prostředky, které již existují, ujistěte se, že splňují požadavky uvedené v této tabulce. 

  | Prostředek | Použít existující? | Požadavky |
  |----------|-----------|----------|
  | Skupina prostředků | Ano, pokud je prázdný | Musí být prázdný| 
  | Účet úložiště | Ano, pokud připojení existující kontejner objektů Blob po vytvoření clusteru <br/>  Není-li vytvořit nový kontejner objektů Blob při vytváření clusteru | Existující kontejner objektů Blob musí být prázdný <br/> &nbsp; |
  | Virtuální síť | Ano | Musí zahrnovat koncový bod služby úložiště, pokud vytváříte nový kontejner objektů Blob v Azure | 
  | Podsíť | Ano |   |

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

## <a name="vfxt-node-size"></a>velikost uzlu vFXT

Virtuální počítače, které slouží jako uzly clusteru určit požadavek kapacitu propustnosti a úložiště mezipaměti. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Každý uzel vFXT budou stejné. To znamená pokud vytvoříte cluster se třemi uzly budete mít tři virtuální počítače stejného typu a velikosti. 

| Typ instance | vCPU | Memory (Paměť)  | Místní úložiště SSD  | Max. datových disků | Propustnost disku bez mezipaměti | Síťová karta (počet) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51 200 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ <br/> 768 MBps | 16 000 MB/s (8)  |

Mezipaměť disku na jeden uzel lze konfigurovat a můžete rage od 1 000 GB až 8000 GB. 4 TB na jeden uzel je doporučená velikost mezipaměti pro Standard_E32s_v3 uzly.

Další informace o těchto virtuálních počítačů najdete v dokumentaci k Microsoft Azure:

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

Pro zlepšení zabezpečení pro kontroler s veřejnou IP adresu, skript nasazení automaticky vytvoří tyto seznamy omezují příchozí přístup na port 22 pouze skupiny zabezpečení sítě. Systém jde dál chránit zamčením dolů přístup ke zdrojové váš rozsah IP adres – to znamená, povolit připojení pouze z počítačů, které máte v úmyslu používat pro přístup ke clusteru.

Při vytváření clusteru, můžete rozhodnout, zda se k vytvoření veřejné IP adresy na adaptéru clusteru. 

* Pokud vytvoříte novou virtuální síť nebo novou podsíť, řadič clusteru přiřadit veřejnou IP adresu.
* Pokud vyberete stávající virtuální síť a podsítě služby, kontrolér cluster bude mít pouze privátní IP adresy. 

## <a name="vm-access-roles"></a>Role virtuálních počítačů přístup 

Azure používá [řízení přístupu na základě rolí](../role-based-access-control/index.yml) (RBAC) k autorizaci clusteru virtuálních počítačů určených k provádění určitých úloh. Například řadič cluster, potřebuje autorizaci k vytvoření a konfigurace virtuální počítače uzlů clusteru. Uzly clusteru musí mít možnost přiřazení nebo změna přiřazení IP adresy do jiných uzlů clusteru.

Dvě předdefinované role Azure se používají pro virtuální počítače vFXT Avere: 

* Řadič cluster používá předdefinovaná role [Avere Přispěvatel](../role-based-access-control/built-in-roles.md#avere-contributor). 
* Uzly clusteru používat předdefinovaná role [Avere – operátor](../role-based-access-control/built-in-roles.md#avere-operator)

Pokud potřebujete vlastní nastavení role zabezpečeného přístupu pro Avere vFXT součásti, musíte definovat vlastní roli a přiřadit ji k virtuálním počítačům v době, kdy se vytvoří. Nasazení šablony nelze použít na webu Azure Marketplace. Microsoft zákaznický servis a podporu najdete tak, že otevřete lístek na webu Azure Portal, jak je popsáno v [získat nápovědu k systému](avere-vfxt-open-ticket.md). 

## <a name="next-step-understand-the-deployment-process"></a>Další krok: Vysvětlení procesu nasazení

[Přehled nasazení](avere-vfxt-deploy-overview.md) poskytuje celkový přehled o všechny kroky potřebné k vytvoření Avere vFXT systému Azure a získat připravené k obsluze data.  