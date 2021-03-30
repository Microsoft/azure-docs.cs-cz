---
title: Plánování avere vFXT systému – Azure
description: Naplánujte avere vFXT pro cluster Azure, který je pro vaše potřeby nejvhodnější. Seznamte se s otázkami, které se dotazují před tím, než budou Azure Marketplace nebo vytváření virtuálních počítačů.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 9f852ec056f3252005bba0bc142c05a9cfbe6c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342395"
---
# <a name="plan-your-avere-vfxt-system"></a>Plánování systému Avere vFXT

Tento článek vysvětluje, jak naplánovat nový avere vFXT pro cluster Azure, který je umístěný a vhodný pro vaše potřeby.

Než se pustíte do Azure Marketplace nebo vytvoříte nějaké virtuální počítače, vezměte v úvahu tyto podrobnosti:

* Jak bude cluster spolupracovat s jinými prostředky Azure?
* Kde se mají prvky clusteru nacházet v privátních sítích a podsítích?
* Jaký typ back-endu úložiště použijete a jakým způsobem bude cluster přistupovat k němu?
* Jak výkonný je potřeba, aby uzly clusteru podporovaly váš pracovní postup?

Přečtěte si další informace.

## <a name="learn-the-components-of-the-system"></a>Naučte se komponenty systému

Při zahájení plánování může být užitečné pochopit komponenty avere vFXT pro systém Azure.

* Uzly clusteru – cluster se skládá ze tří nebo více virtuálních počítačů nakonfigurovaných jako uzly clusteru. Další uzly poskytují vyšší propustnost systému a větší mezipaměť.

* Cache – kapacita mezipaměti je rozdělená rovnoměrně mezi uzly clusteru. Při vytváření clusteru nastavte velikost mezipaměti pro jednotlivé uzly. velikosti uzlů se přidají jako celková velikost mezipaměti.

* Řadič clusteru – řadič clusteru je další virtuální počítač umístěný ve stejné podsíti jako uzly clusteru. Kontroler je nutný k vytvoření clusteru a pro probíhající úlohy správy.

* Back-endové úložiště – data, která chcete ukládat do mezipaměti, se ukládají dlouhodobě v systému hardwarového úložiště nebo v kontejneru objektů BLOB v Azure. Úložiště můžete přidat po vytvoření avere vFXT pro cluster Azure nebo při použití úložiště BLOB Storage můžete přidat a nakonfigurovat kontejner při vytváření clusteru.

* Klienti – klientské počítače, které používají soubory uložené v mezipaměti, se připojují ke clusteru pomocí cesty k virtuálnímu souboru místo přímého přístupu k systémům úložiště. (Další informace najdete v tématu [připojení clusteru avere vFXT](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Předplatné, skupina prostředků a síťová infrastruktura

Zvažte, kde budou prvky avere vFXT pro nasazení Azure. Následující diagram znázorňuje možné uspořádání pro součásti avere vFXT pro Azure:

![Diagram znázorňující řadič clusteru a virtuální počítače clusteru v rámci jedné podsítě. Kolem hranice podsítě je hranice virtuální sítě. Uvnitř virtuální sítě je šestiúhelník, který představuje koncový bod služby úložiště. je spojen s čárkovanou šipkou na úložiště objektů BLOB mimo virtuální síť.](media/avere-vfxt-components-option.png)

Při plánování síťové infrastruktury clusteru avere vFXT postupujte podle těchto pokynů:

* Vytvořte nové předplatné pro každý avere vFXT pro nasazení Azure. Umožňuje spravovat všechny komponenty v tomto předplatném.

  Mezi výhody použití nového předplatného pro každé nasazení patří:
  * Jednodušší sledování nákladů – zobrazení a audit všech nákladů z prostředků, infrastruktury a výpočetních cyklů v jednom předplatném
  * Jednodušší vyčištění – po dokončení projektu můžete odebrat celé předplatné.
  * Pohodlné dělení kvót prostředků – izolujte klienty a clustery avere vFXT v jednom předplatném, abyste chránili jiné kritické úlohy od možného omezení prostředků. Toto oddělení brání konflikt při zavedení velkého počtu klientů pro vysoce výkonný výpočetní pracovní postup.

* Najděte výpočetní systémy klientů blízko do clusteru vFXT. Úložiště back-endu může být víc vzdálené.  

* Najděte cluster vFXT a virtuální počítač řadiče clusteru dohromady – konkrétně by měly být:

  * Ve stejné virtuální síti
  * Ve stejné skupině prostředků
  * Použití stejného účtu úložiště
  
  Šablona pro vytvoření clusteru tuto konfiguraci zpracovává ve většině případů.

* Cluster se musí nacházet ve vlastní podsíti, aby nedocházelo ke konfliktům IP adres s klienty nebo jinými výpočetními prostředky.

* Pomocí šablony pro vytvoření clusteru vytvořte většinu potřebných prostředků infrastruktury pro cluster, včetně skupin prostředků, virtuálních sítí, podsítí a účtů úložiště.

  Pokud chcete použít prostředky, které již existují, ujistěte se, že splňují požadavky v této tabulce.

  | Prostředek | Použít existující? | Požadavky |
  |----------|-----------|----------|
  | Skupina prostředků | Ano, je-li prázdné | Musí být prázdné|
  | Účet úložiště | **Ano** , pokud se po vytvoření clusteru připojí existující kontejner objektů BLOB <br/>  **Ne** při vytváření nového kontejneru objektů BLOB během vytváření clusteru | Existující kontejner objektů BLOB musí být prázdný. <br/> &nbsp; |
  | Virtuální síť | Ano | Musí zahrnovat koncový bod služby úložiště při vytváření nového kontejneru objektů blob Azure. |
  | Podsíť | Ano | Nemůže obsahovat jiné prostředky. |

## <a name="ip-address-requirements"></a>Požadavky na IP adresu

Ujistěte se, že má podsíť clusteru velký rozsah IP adres pro podporu clusteru.

Cluster avere vFXT používá následující IP adresy:

* Jedna IP adresa pro správu clusteru. Tato adresa se dá v clusteru přesunout z uzlu podle potřeby tak, aby byla vždy dostupná. Tuto adresu použijte pro připojení k nástroji pro konfiguraci ovládacího panelu avere.
* Pro každý uzel clusteru:
  * Aspoň jedna IP adresa pro klientské klienta. (Všechny adresy směřující na klienta se spravují pomocí *VServer* clusteru, což může v případě potřeby přesunout IP adresy mezi uzly.)
  * Jedna IP adresa pro komunikaci clusteru
  * Jedna instance IP adresa (přiřazená k virtuálnímu počítači)

Pokud používáte úložiště objektů BLOB v Azure, může taky vyžadovat IP adresy z virtuální sítě vašeho clusteru:  

* Účet úložiště objektů BLOB v Azure vyžaduje aspoň pět IP adres. Tento požadavek mějte na paměti, pokud ve stejné virtuální síti jako cluster najdete úložiště objektů BLOB.
* Pokud používáte službu Azure Blob Storage, která je mimo virtuální síť clusteru, vytvořte v rámci virtuální sítě koncový bod služby úložiště. Koncový bod nepoužívá IP adresu.

Máte možnost vyhledat v clusteru síťové prostředky a úložiště objektů BLOB (Pokud se používají) v různých skupinách prostředků.

## <a name="vfxt-node-size"></a>Velikost uzlu vFXT

Virtuální počítače, které slouží jako uzly clusteru, určují propustnost požadavků a kapacitu úložiště vaší mezipaměti. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Každý uzel vFXT bude identický. To znamená, že pokud vytvoříte cluster se třemi uzly, budete mít tři virtuální počítače stejného typu a velikosti.

| Typ instance | Virtuální procesory | Memory (Paměť)  | Místní SSD úložiště  | Max. datových disků | Propustnost disku při neukládání do mezipaměti | Síťový adaptér (počet) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GB | 512 GiB  | 32 | 51 200 IOPS <br/> 768 MB/s | 16 000 MB/s (8)  |

Disková mezipaměť na uzel je konfigurovatelná a může být Rage z 1000 GB do 8000 GB. pro uzly Standard_E32s_v3 doporučená velikost mezipaměti je 4 TB na uzel.

Pokud chcete získat další informace o těchto virtuálních počítačích, přečtěte si dokumentaci k Microsoft Azure: [paměťově optimalizované velikosti virtuálních počítačů](../virtual-machines/sizes-memory.md)

## <a name="account-quota"></a>Kvóta účtu

Ujistěte se, že vaše předplatné má kapacitu pro spuštění clusteru avere vFXT, a také pro všechny výpočetní nebo klientské systémy. Podrobnosti o [kvótě pro čtení pro cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) .

## <a name="back-end-data-storage"></a>Úložiště dat back-endu

Back-endové systémy úložiště poskytují soubory do mezipaměti clusteru a také přijímají změněná data z mezipaměti. Rozhodněte, jestli se vaše pracovní sada bude ukládat jako dlouhodobá v novém kontejneru objektů BLOB nebo v existujícím systému úložiště (Cloud nebo hardware). Tyto back-endové systémy úložiště se nazývají *základní filers*.

### <a name="hardware-core-filers"></a>Hardware Core filers

Po vytvoření clusteru přidejte do clusteru vFXT hardwarové úložné systémy. Můžete použít celou řadu oblíbených hardwarových systémů, včetně místních systémů, pokud se systém úložiště dá získat z podsítě clusteru.

Podrobné pokyny, jak přidat existující systém úložiště do clusteru avere vFXT, najdete v tématu [Konfigurace úložiště](avere-vfxt-add-storage.md) .

### <a name="cloud-core-filers"></a>Cloud Core filers

Avere vFXT pro systém Azure může pro back-end úložiště používat prázdné kontejnery objektů BLOB. Kontejnery musí být při přidání do clusteru prázdné – systém vFXT musí být schopný spravovat úložiště objektů, aniž by bylo nutné zachovat stávající data.

> [!TIP]
> Pokud chcete pro back-end používat úložiště objektů BLOB v Azure, vytvořte nový kontejner jako součást vytváření clusteru vFXT. Šablona pro vytvoření clusteru může vytvořit a nakonfigurovat nový kontejner objektů BLOB tak, aby byl připravený k použití, jakmile bude cluster k dispozici. Přidání kontejneru později je složitější.
>
> Podrobnosti najdete [v článku Vytvoření avere vFXT pro Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) .

Po přidání prázdného kontejneru úložiště objektů BLOB jako základního souborového můžete do něj zkopírovat data prostřednictvím clusteru. Použijte paralelní vícevláknový kopírovací mechanizmus. Přečtěte si [přesun dat do clusteru vFXT](avere-vfxt-data-ingest.md) , kde se dozvíte, jak efektivně kopírovat data do nového kontejneru clusteru pomocí klientských počítačů a mezipaměti avere vFXT cache.

## <a name="cluster-access"></a>Přístup clusteru

Avere vFXT pro cluster Azure se nachází v privátní podsíti a cluster nemá veřejnou IP adresu. Musíte mít nějaký způsob, jak získat přístup k privátní podsíti pro správu clusteru a připojení klientů.

Mezi možnosti přístupu patří:

* Hostitel skoku – přiřaďte veřejnou IP adresu samostatnému virtuálnímu počítači v privátní síti a použijte ji k vytvoření tunelového propojení TLS na uzlech clusteru.

  > [!TIP]
  > Pokud jste v řadiči clusteru nastavili veřejnou IP adresu, můžete ji použít jako hostitele odkazů. Další informace najdete [v tématu řadič clusteru jako skokový hostitel](#cluster-controller-as-jump-host) .

* Virtuální privátní síť (VPN) – konfigurace typu Point-to-site nebo VPN typu Site-to-site mezi vaší privátní sítí v Azure a podnikových sítích.

* Azure ExpressRoute – konfigurace privátního připojení prostřednictvím partnera ExpressRoute.

Podrobnosti o těchto možnostech najdete v dokumentaci k [Azure Virtual Network o internetové komunikaci](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Řadič clusteru jako hostitel s odkazem

Pokud nastavíte veřejnou IP adresu na řadiči clusteru, můžete ji použít jako hostitele s odkazem, aby se mohl kontaktovat cluster avere vFXT z oblasti mimo privátní podsíť. Vzhledem k tomu, že kontroler má oprávnění k úpravám uzlů clusteru, vytvoří to malé bezpečnostní riziko.

Aby bylo možné zvýšit zabezpečení řadičem pomocí veřejné IP adresy, skript nasazení automaticky vytvoří skupinu zabezpečení sítě, která omezuje příchozí přístup pouze na port 22. Systém můžete dále chránit tím, že zamknete přístup k vaší škále zdrojových adres IP – to znamená jenom připojení z počítačů, které chcete používat pro přístup do clusteru.

Při vytváření clusteru můžete zvolit, jestli se má na řadiči clusteru vytvořit veřejná IP adresa.

* Pokud vytvoříte **novou virtuální síť** nebo **novou podsíť**, k řadiči clusteru se přiřadí **Veřejná** IP adresa.
* Pokud vyberete existující virtuální síť a podsíť, bude mít řadič clusteru jenom **privátní** IP adresy.

## <a name="vm-access-roles"></a>Role přístupu virtuálních počítačů

Azure používá [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/index.yml) k autorizaci virtuálních počítačů clusteru k provádění určitých úloh. Například řadič clusteru potřebuje autorizaci k vytvoření a konfiguraci virtuálních počítačů uzlů clusteru. Uzly clusteru musí být schopné přiřadit nebo změnit přiřazení IP adres jiným uzlům clusteru.

Pro virtuální počítače s avere vFXT se používají dvě předdefinované role Azure:

* Řadič clusteru používá integrovaný [Přispěvatel rolí avere](../role-based-access-control/built-in-roles.md#avere-contributor).
* Uzly clusteru používají integrovaný [avere operátor](../role-based-access-control/built-in-roles.md#avere-operator)role.

Pokud potřebujete přizpůsobit role přístupu pro součásti avere vFXT, musíte definovat vlastní roli a pak ji přiřadit k virtuálním počítačům v okamžiku jejich vytvoření. Šablonu nasazení nelze použít v Azure Marketplace. Pomocí služby Microsoft Customer Service and Support můžete v Azure Portal otevřít lístek, jak je popsáno v [tématu o získání pomoci s vaším systémem](avere-vfxt-open-ticket.md).

## <a name="next-steps"></a>Další kroky

[Přehled nasazení poskytuje přehled](avere-vfxt-deploy-overview.md) kroků nezbytných k vytvoření avere vFXT pro systém Azure a jeho přípravě na poskytování dat.