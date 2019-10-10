---
title: Plánování avere vFXT systému – Azure
description: Vysvětluje plánování před nasazením avere vFXT pro Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 1317e900fd4448ded046ffea481313f8ea9f68e3
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256241"
---
# <a name="plan-your-avere-vfxt-system"></a>Plánování systému Avere vFXT

Tento článek vysvětluje, jak naplánovat nový avere vFXT pro cluster Azure, který je umístěný a vhodný pro vaše potřeby. 

Než přejdete na Azure Marketplace nebo vytvoříte nějaké virtuální počítače, vezměte v úvahu, jak bude cluster spolupracovat s dalšími prvky v Azure. Naplánujte, kde se budou prostředky clusteru nacházet v privátní síti a v podsítích, a rozhodněte se, kde bude vaše back-endové úložiště. Ujistěte se, že uzly clusteru, které vytvoříte, jsou dostatečně výkonné, aby podporovaly váš pracovní postup. 

Pokud se chcete dozvědět víc, čtěte dál.

## <a name="resource-group-and-network-infrastructure"></a>Skupina prostředků a síťová infrastruktura

Zvažte, kde budou prvky avere vFXT pro nasazení Azure. Následující diagram znázorňuje možné uspořádání pro součásti avere vFXT pro Azure:

![Diagram znázorňující řadič clusteru a virtuální počítače clusteru v rámci jedné podsítě. Kolem hranice podsítě je hranice virtuální sítě. Uvnitř virtuální sítě je šestiúhelník, který představuje koncový bod služby úložiště. je spojen s čárkovanou šipkou na úložiště objektů BLOB mimo virtuální síť.](media/avere-vfxt-components-option.png)

Při plánování síťové infrastruktury systému avere vFXT postupujte podle těchto pokynů:

* Všechny elementy by se měly spravovat s novým předplatným vytvořeným pro nasazení avere vFXT. Mezi výhody patří: 
  * Jednodušší sledování nákladů – zobrazení a audit všech nákladů z prostředků, infrastruktury a výpočetních cyklů v jednom předplatném
  * Jednodušší vyčištění – po dokončení projektu můžete odebrat celé předplatné.
  * Pohodlné vytváření oddílů kvót prostředků – Chraňte další kritické úlohy z možného omezování prostředků tím, že v jednom předplatném izolujete klienty a cluster avere vFXT. Tím se vyhnete konfliktu při zavedení velkého počtu klientů pro vysoce výkonný výpočetní pracovní postup.

* Najděte výpočetní systémy klientů blízko do clusteru vFXT. Úložiště back-endu může být víc vzdálené.  

* Cluster vFXT a virtuální počítač řadiče clusteru by se měly nacházet ve stejné virtuální síti (VNET), ve stejné skupině prostředků a používat stejný účet úložiště. Šablona automatizovaného vytváření clusterů ji zpracovává ve většině případů.

* Cluster se musí nacházet ve vlastní podsíti, aby nedocházelo ke konfliktům IP adres s klienty nebo výpočetními prostředky. 

* Šablona pro vytvoření clusteru může vytvořit většinu potřebných prostředků infrastruktury pro cluster, včetně skupin prostředků, virtuálních sítí, podsítí a účtů úložiště. Pokud chcete použít prostředky, které již existují, ujistěte se, že splňují požadavky v této tabulce. 

  | Prostředek | Použít existující? | Požadavky |
  |----------|-----------|----------|
  | Skupina prostředků | Ano, je-li prázdné | Musí být prázdné| 
  | Účet úložiště | Ano, pokud se po vytvoření clusteru připojí existující kontejner objektů BLOB <br/>  Ne při vytváření nového kontejneru objektů BLOB během vytváření clusteru | Existující kontejner objektů BLOB musí být prázdný. <br/> &nbsp; |
  | Virtuální síť | Ano | Musí zahrnovat koncový bod služby úložiště při vytváření nového kontejneru objektů blob Azure. | 
  | Podsíť | Ano |   |

## <a name="ip-address-requirements"></a>Požadavky na IP adresu 

Ujistěte se, že má podsíť clusteru velký rozsah IP adres pro podporu clusteru. 

Cluster avere vFXT používá následující IP adresy:

* Jedna IP adresa pro správu clusteru. Tato adresa se může přesunout z uzlu na uzel v clusteru, ale je vždy k dispozici, abyste se mohli připojit k nástroji pro konfiguraci ovládacích panelů avere.
* Pro každý uzel clusteru:
  * Aspoň jedna IP adresa pro klientské klienta. (Všechny adresy směřující na klienta se spravují pomocí *VServer*clusteru, které je možné podle potřeby přesouvat mezi uzly.)
  * Jedna IP adresa pro komunikaci clusteru
  * Jedna instance IP adresa (přiřazená k virtuálnímu počítači)

Pokud používáte úložiště objektů BLOB v Azure, může taky vyžadovat IP adresy z virtuální sítě vašeho clusteru:  

* Účet úložiště objektů BLOB v Azure vyžaduje aspoň pět IP adres. Tento požadavek mějte na paměti, pokud ve stejné virtuální síti jako cluster najdete úložiště objektů BLOB.
* Pokud používáte službu Azure Blob Storage, která je mimo virtuální síť pro váš cluster, měli byste v rámci virtuální sítě vytvořit koncový bod služby úložiště. Tento koncový bod nepoužívá IP adresu.

Máte možnost vyhledat v clusteru síťové prostředky a úložiště objektů BLOB (Pokud se používají) v různých skupinách prostředků.

## <a name="vfxt-node-size"></a>Velikost uzlu vFXT

Virtuální počítače, které slouží jako uzly clusteru, určují propustnost požadavků a kapacitu úložiště vaší mezipaměti. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Každý uzel vFXT bude identický. To znamená, že pokud vytvoříte cluster se třemi uzly, budete mít tři virtuální počítače stejného typu a velikosti. 

| Typ instance | vCPU | Paměť  | Místní SSD úložiště  | Max. datových disků | Propustnost disku při neukládání do mezipaměti | Síťový adaptér (počet) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51 200 IOPS <br/> 768 MB/s | 16 000 MB/s (8)  |

Disková mezipaměť na uzel je konfigurovatelná a může být Rage z 1000 GB do 8000 GB. pro uzly Standard_E32s_v3 doporučená velikost mezipaměti je 4 TB na uzel.

Pokud chcete získat další informace o těchto virtuálních počítačích, přečtěte si dokumentaci k Microsoft Azure:

* [Paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Kvóta účtu

Ujistěte se, že vaše předplatné má kapacitu pro spuštění clusteru avere vFXT, a také pro všechny výpočetní nebo klientské systémy. Podrobnosti o [kvótě pro čtení pro cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) .

## <a name="back-end-data-storage"></a>Úložiště dat back-endu

Kam má cluster avere vFXT ukládat vaše data, když není v mezipaměti? Rozhodněte, jestli se vaše pracovní sada bude ukládat jako dlouhodobá v novém kontejneru objektů BLOB nebo v existujícím cloudovém nebo hardwarovém úložném systému. 

Pokud chcete pro back-end používat úložiště objektů BLOB v Azure, měli byste vytvořit nový kontejner v rámci vytváření clusteru vFXT. Tato možnost vytvoří a nakonfiguruje nový kontejner tak, aby byl připravený k použití ihned po dokončení clusteru. 

Podrobnosti najdete [v článku Vytvoření avere vFXT pro Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) .

> [!NOTE]
> Jako základní filers pro systém avere vFXT lze použít pouze prázdné kontejnery úložiště objektů BLOB. VFXT musí být schopný spravovat své úložiště objektů, aniž by bylo nutné zachovat stávající data. 
>
> Přečtěte si [přesun dat do clusteru vFXT](avere-vfxt-data-ingest.md) , kde se dozvíte, jak efektivně kopírovat data do nového kontejneru clusteru pomocí klientských počítačů a mezipaměti avere vFXT cache.

Pokud chcete použít existující místní systém úložiště, musíte ho po vytvoření přidat do clusteru vFXT. Podrobné pokyny, jak přidat existující systém úložiště do clusteru avere vFXT, najdete v tématu [Konfigurace úložiště](avere-vfxt-add-storage.md) .

## <a name="cluster-access"></a>Přístup clusteru 

Avere vFXT pro cluster Azure se nachází v privátní podsíti a cluster nemá veřejnou IP adresu. Je nutné, abyste měli k dispozici metodu pro přístup k privátní podsíti pro správu clusteru a připojení klientů. 

Mezi možnosti přístupu patří:

* Hostitel skoku – přiřaďte veřejnou IP adresu samostatnému virtuálnímu počítači v privátní síti a použijte ji k vytvoření tunelu SSL na uzlech clusteru. 

  > [!TIP]
  > Pokud jste v řadiči clusteru nastavili veřejnou IP adresu, můžete ji použít jako hostitele odkazů. Další informace najdete [v tématu řadič clusteru jako skokový hostitel](#cluster-controller-as-jump-host) .

* Virtuální privátní síť (VPN) – konfigurace připojení typu Point-to-site nebo VPN typu Site-to-site k privátní síti.

* Azure ExpressRoute – konfigurace privátního připojení prostřednictvím partnera ExpressRoute. 

Podrobnosti o těchto možnostech najdete v dokumentaci k [Azure Virtual Network o internetové komunikaci](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Řadič clusteru jako hostitel s odkazem

Pokud nastavíte veřejnou IP adresu na řadiči clusteru, můžete ji použít jako hostitele s odkazem, aby se mohl kontaktovat cluster avere vFXT z oblasti mimo privátní podsíť. Vzhledem k tomu, že kontroler má oprávnění k úpravám uzlů clusteru, vytvoří to malé bezpečnostní riziko.  

Aby bylo možné zvýšit zabezpečení řadičem pomocí veřejné IP adresy, skript nasazení automaticky vytvoří skupinu zabezpečení sítě, která omezuje příchozí přístup pouze na port 22. Systém můžete dále chránit tím, že zamknete přístup k vaší škále zdrojových adres IP – to znamená jenom připojení z počítačů, které chcete používat pro přístup do clusteru.

Při vytváření clusteru můžete zvolit, jestli se má na řadiči clusteru vytvořit veřejná IP adresa. 

* Pokud vytvoříte novou síť VNet nebo novou podsíť, k řadiči clusteru se přiřadí veřejná IP adresa.
* Pokud vyberete existující virtuální síť a podsíť, bude mít řadič clusteru jenom privátní IP adresy. 

## <a name="vm-access-roles"></a>Role přístupu virtuálních počítačů 

Azure používá [řízení přístupu na základě role](../role-based-access-control/index.yml) (RBAC) k autorizaci virtuálních počítačů clusteru k provádění určitých úloh. Například řadič clusteru potřebuje autorizaci k vytvoření a konfiguraci virtuálních počítačů uzlů clusteru. Uzly clusteru musí být schopné přiřadit a přiřadit IP adresy jiným uzlům clusteru.

Pro virtuální počítače s avere vFXT se používají dvě předdefinované role Azure: 

* Řadič clusteru používá integrovaný [Přispěvatel rolí avere](../role-based-access-control/built-in-roles.md#avere-contributor). 
* Uzly clusteru používají vestavěný [avere operátor](../role-based-access-control/built-in-roles.md#avere-operator) role.

Pokud potřebujete přizpůsobit role přístupu pro součásti avere vFXT, musíte definovat vlastní roli a pak ji přiřadit k virtuálním počítačům v okamžiku jejich vytvoření. Šablonu nasazení nelze použít v Azure Marketplace. Pomocí služby Microsoft Customer Service and Support můžete v Azure Portal otevřít lístek, jak je popsáno v [tématu o získání pomoci s vaším systémem](avere-vfxt-open-ticket.md). 

## <a name="next-step-understand-the-deployment-process"></a>Další krok: pochopení procesu nasazení

[Přehled nasazení](avere-vfxt-deploy-overview.md) poskytuje velký obrázek všech kroků potřebných k vytvoření avere vFXT pro systém Azure a jeho přípravě na poskytování dat.  