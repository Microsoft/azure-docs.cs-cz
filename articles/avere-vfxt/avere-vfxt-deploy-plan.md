---
title: Plánování systému Avere vFXT – Azure
description: Vysvětluje plánování před nasazením Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 6acc1ffd197ddba4290ff7c0751b259d98a70927
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754396"
---
# <a name="plan-your-avere-vfxt-system"></a>Plánování systému Avere vFXT

Tento článek vysvětluje, jak naplánovat nový Avere vFXT pro cluster Azure, který je umístěn a velikosti odpovídajícím způsobem pro vaše potřeby.

Než přejdete na Azure Marketplace nebo vytvoříte nějaké virtuální počítače, zvažte tyto podrobnosti:

* Jak bude cluster pracovat s jinými prostředky Azure?
* Kde by měly být umístěny prvky clusteru v privátních sítích a podsítích?
* Jaký typ back-endového úložiště budete používat a jak k němu bude cluster přistupovat?
* Jak výkonné musí být uzly clusteru pro podporu vašeho pracovního postupu?

Čtěte dál a dozvíte se více.

## <a name="learn-the-components-of-the-system"></a>Naučte se součásti systému

Může být užitečné pochopit součásti systému Avere vFXT pro Azure při zahájení plánování.

* Uzly clusteru – cluster se skládá ze tří nebo více virtuálních počítače nakonfigurovaných jako uzly clusteru. Více uzlů poskytuje systému vyšší propustnost a větší mezipaměť.

* Mezipaměť – kapacita mezipaměti je rovnoměrně rozdělena mezi uzly clusteru. Nastavte velikost mezipaměti pro uzlovou velikost při vytváření clusteru. velikosti uzlů jsou přidány, aby se stala celková velikost mezipaměti.

* Řadič clusteru – řadič clusteru je další virtuální virtuální ms umístěný ve stejné podsíti jako uzly clusteru. Řadič je potřeba k vytvoření clusteru a pro probíhající úlohy správy.

* Back-endové úložiště – data, která chcete mít v mezipaměti, jsou dlouhodobě uložená v systému hardwarového úložiště nebo v kontejneru objektů Blob Azure. Úložiště můžete přidat po vytvoření avere vFXT pro cluster Azure, nebo pokud používáte úložiště objektů blob můžete přidat a nakonfigurovat kontejner při vytváření clusteru.

* Klienti – klientské počítače, které používají soubory uložené v mezipaměti, se připojují ke clusteru pomocí virtuální cesty k souborům namísto přímého přístupu k úložným systémům. (Přečtěte si více v [Mount Avere vFXT clusteru](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Předplatné, skupina prostředků a síťová infrastruktura

Zvažte, kde budou prvky vašeho avere vFXT pro nasazení Azure. Následující diagram znázorňuje možné uspořádání komponent Avere vFXT pro azure:

![Diagram znázorňující řadič clusteru a virtuální chodclusteru v rámci jedné podsítě. Kolem hranice podsítě je hranice virtuální sítě. Uvnitř virtuální sítě je šestiúhelník představující koncový bod služby úložiště; je spojena s přerušovanou šipkou k úložišti objektů Blob mimo virtuální síť.](media/avere-vfxt-components-option.png)

Při plánování síťové infrastruktury clusteru Avere vFXT postupujte podle těchto pokynů:

* Vytvořte nové předplatné pro každé avere vFXT pro nasazení Azure. Spravujte všechny součásti v tomto předplatném.

  Mezi výhody použití nového předplatného pro každé nasazení patří:
  * Jednodušší sledování nákladů – Zobrazení a auditování všech nákladů z prostředků, infrastruktury a výpočetních cyklů v jednom předplatném.
  * Snadnější vyčištění – po dokončení projektu můžete odebrat celé předplatné.
  * Pohodlné rozdělení kvót prostředků – izolujte klienty Avere vFXT a cluster v jednom předplatném, abyste ochránili další kritické úlohy před možným omezením prostředků. Toto oddělení zabraňuje konfliktu při přivádění velkého počtu klientů pro vysoce výkonný výpočetní pracovní postup.

* Vyhledejte klientské výpočetní systémy v blízkosti clusteru vFXT. Back-end úložiště může být vzdálenější.  

* Vyhledejte cluster vFXT a virtuální ms řadiče clusteru společně – konkrétně by měly být:

  * Ve stejné virtuální síti
  * Ve stejné skupině prostředků
  * Použití stejného účtu úložiště
  
  Šablona vytvoření clusteru zpracovává tuto konfiguraci pro většinu situací.

* Cluster musí být umístěn ve vlastní podsíti, aby se zabránilo konfliktům adres IP s klienty nebo jinými výpočetními prostředky.

* Šablona vytvoření clusteru slouží k vytvoření většiny potřebných prostředků infrastruktury pro cluster, včetně skupin prostředků, virtuálních sítí, podsítí a účtů úložiště.

  Pokud chcete použít prostředky, které již existují, ujistěte se, že splňují požadavky v této tabulce.

  | Prostředek | Použít existující? | Požadavky |
  |----------|-----------|----------|
  | Skupina prostředků | Ano, pokud je prázdný | Musí být prázdný.|
  | Účet úložiště | **Ano,** pokud připojení existujícího kontejneru objektů Blob po vytvoření clusteru <br/>  **Ne,** pokud při vytváření clusteru vytváříte nový kontejner objektů Blob | Existující kontejner objektů blob musí být prázdný. <br/> &nbsp; |
  | Virtuální síť | Ano | Musí obsahovat koncový bod služby úložiště, pokud vytváříte nový kontejner objektů Blob Azure |
  | Podsíť | Ano | Nesmí obsahovat jiné zdroje. |

## <a name="ip-address-requirements"></a>Požadavky na IP adresu

Ujistěte se, že podsíť clusteru má dostatečně velký rozsah IP adres pro podporu clusteru.

Cluster Avere vFXT používá následující ADRESY IP:

* Jedna adresa IP správy clusteru. Tato adresa se může podle potřeby přesunout z uzlu do uzlu v clusteru, aby byla vždy k dispozici. Tato adresa slouží k připojení ke konfiguračnímu nástroji Ovládací panely Avere.
* Pro každý uzel clusteru:
  * Alespoň jedna adresa IP pro klienta. (Všechny adresy orientované na klienta jsou spravovány *vserverem*clusteru , který může podle potřeby přesouvat adresy IP mezi uzly.)
  * Jedna IP adresa pro komunikaci clusteru
  * Jedna IP adresa instance (přiřazená k virtuálnímu virtuálnímu počítačům)

Pokud používáte úložiště objektů blob Azure, může to také vyžadovat IP adresy z virtuální sítě vašeho clusteru:  

* Účet úložiště objektů blob Azure vyžaduje alespoň pět IP adres. Tento požadavek mějte na paměti, pokud vyhledejte úložiště objektů Blob ve stejné virtuální síti jako váš cluster.
* Pokud používáte úložiště objektů blob Azure, které je mimo virtuální síť clusteru, vytvořte koncový bod služby úložiště uvnitř virtuální sítě. Koncový bod nepoužívá adresu IP.

Máte možnost vyhledat síťové prostředky a úložiště objektů Blob (pokud se používá) v různých skupinách prostředků z clusteru.

## <a name="vfxt-node-size"></a>velikost uzlu vFXT

Virtuální počítače, které slouží jako uzly clusteru, určují propustnost požadavků a kapacitu úložiště mezipaměti. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Každý uzel vFXT bude identický. To znamená, že pokud vytvoříte cluster se třemi uzlovými, budete mít tři virtuální počítače stejného typu a velikosti.

| Typ instance | Virtuální procesory | Memory (Paměť)  | Místní úložiště SSD  | Max. datových disků | Propustnost disku bez mezipaměti | NIC (počet) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GB | 512 GiB  | 32 | 51 200 IOPS <br/> 768 mb/s | 16 000 mb/s (8)  |

Mezipaměť disků na uzel je konfigurovatelná a může zuřit od 1000 GB do 8000 GB. 4 TB na uzel je doporučená velikost mezipaměti pro Standard_E32s_v3 uzly.

Další informace o těchto virtuálních počítačích načtete v dokumentaci k Microsoft Azure: [Velikosti virtuálních počítačů optimalizované pro paměť](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Kvóta účtu

Ujistěte se, že vaše předplatné má schopnost spouštět cluster Avere vFXT, stejně jako všechny výpočetní nebo klientské systémy, které se používají. Podrobnosti načtete [kvótu pro cluster vFXT.](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

## <a name="back-end-data-storage"></a>Back-endové ukládání dat

Back-endové úložné systémy dodávají soubory do mezipaměti clusteru a také přijímají změněná data z mezipaměti. Rozhodněte se, jestli bude vaše pracovní sada dlouhodobě uložena v novém kontejneru objektů Blob nebo v existujícím úložném systému (cloud nebo hardware). Tyto back-end úložné systémy se nazývají *základní filers*.

### <a name="hardware-core-filers"></a>Hardwarové základní filery

Po vytvoření clusteru přidejte do clusteru vFXT systémy hardwarového úložiště. Můžete použít celou řadu oblíbených hardwarových systémů, včetně místních systémů, pokud je systém úložiště dostupný z podsítě clusteru.

Podrobné pokyny k přidání existujícího úložného systému do clusteru Avere vFXT najdete v části [Konfigurace úložiště.](avere-vfxt-add-storage.md)

### <a name="cloud-core-filers"></a>Filers jádra cloudu

Systém Avere vFXT for Azure může používat prázdné kontejnery objektů Blob pro back-endové úložiště. Kontejnery musí být prázdné při přidání do clusteru - systém vFXT musí být schopen spravovat své úložiště objektů bez nutnosti zachovat existující data.

> [!TIP]
> Pokud chcete použít úložiště objektů blob Azure pro back-end, vytvořte nový kontejner jako součást vytváření clusteru vFXT. Šablona vytvoření clusteru můžete vytvořit a nakonfigurovat nový kontejner objektů Blob tak, aby byl připraven k použití, jakmile je k dispozici clusteru. Přidání kontejneru později je složitější.
>
> Podrobnosti najdeš v článek [Vytvoření avere vFXT pro Azure.](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)

Po přidání prázdného kontejneru úložiště objektů Blob jako základního fileru do něj můžete kopírovat data prostřednictvím clusteru. Použijte paralelní, vícevláknový mechanismus kopírování. Přečtěte si [přesunutí dat do clusteru vFXT](avere-vfxt-data-ingest.md) a zjistěte, jak efektivně kopírovat data do nového kontejneru clusteru pomocí klientských počítačů a mezipaměti Avere vFXT.

## <a name="cluster-access"></a>Přístup ke clusteru

Avere vFXT pro Cluster Azure se nachází v privátní podsíti a cluster nemá veřejnou IP adresu. Musíte mít nějaký způsob, jak získat přístup k privátní podsíti pro správu clusteru a připojení klientů.

Možnosti přístupu zahrnují:

* Přejít na hostitele – přiřaďte k samostatnému virtuálnímu virtuálnímu počítačům v rámci privátní sítě veřejnou IP adresu a použijte ji k vytvoření tunelového propojení TLS do uzlů clusteru.

  > [!TIP]
  > Pokud nastavíte veřejnou IP adresu na řadiči clusteru, můžete ji použít jako hostitele skoku. Další informace naleznete [v řadiči clusteru jako hostiteli skoku.](#cluster-controller-as-jump-host)

* Virtuální privátní síť (VPN) – Nakonfigurujte vpn z bodu na místo nebo síť mezi privátní sítí v Azure a podnikovými sítěmi.

* Azure ExpressRoute – konfigurace privátního připojení prostřednictvím partnera ExpressRoute.

Podrobnosti o těchto možnostech načtěte [v dokumentaci k virtuální síti Azure o internetové komunikaci](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Řadič clusteru jako hostitel skoku

Pokud nastavíte veřejnou IP adresu na řadiči clusteru, můžete ji použít jako hostitele skoku ke kontaktování clusteru Avere vFXT mimo privátní podsíť. Protože však má řadič přístupová oprávnění k úpravám uzlů clusteru, vytváří to malé bezpečnostní riziko.

Chcete-li zlepšit zabezpečení řadiče s veřejnou IP adresou, skript nasazení automaticky vytvoří skupinu zabezpečení sítě, která omezuje příchozí přístup pouze k portu 22. Systém můžete dále chránit uzamčením přístupu k rozsahu zdrojových adres IP – to znamená povolit pouze připojení z počítačů, které chcete použít pro přístup ke clusteru.

Při vytváření clusteru můžete zvolit, zda chcete vytvořit veřejnou IP adresu na řadiči clusteru.

* Pokud vytvoříte **novou virtuální síť** nebo novou **podsíť**, bude řadiči clusteru přiřazena **veřejná** IP adresa.
* Pokud vyberete existující virtuální síť a podsíť, bude mít řadič clusteru pouze **privátní** adresy IP.

## <a name="vm-access-roles"></a>Role přístupu k virtuálním montovna

Azure používá [řízení přístupu na základě rolí](../role-based-access-control/index.yml) (RBAC) k autorizaci virtuálních počítačích clusteru k provádění určitých úloh. Řadič clusteru například potřebuje autorizaci k vytvoření a konfiguraci virtuálních virtuálních adaptérů uzlů clusteru. Uzly clusteru musí být schopny přiřadit nebo znovu přiřadit adresy IP jiným uzlům clusteru.

Dvě předdefinované role Azure se používají pro virtuální počítače Avere vFXT:

* Řadič clusteru používá integrovanou roli [Přispěvatel Avere](../role-based-access-control/built-in-roles.md#avere-contributor).
* Uzly clusteru používají předdefinovanou roli [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator).

Pokud potřebujete přizpůsobit přístupové role pro komponenty Avere vFXT, musíte definovat vlastní roli a pak ji přiřadit k virtuálním počítačům v době, kdy jsou vytvořeny. Šablonu nasazení na Azure Marketplace používat nejde. Informace o tom, že otevřete lístek na webu Azure Portal, jak je popsáno v části Získat pomoc se systémem , naleznete na zákaznických [službách](avere-vfxt-open-ticket.md)a podpoře společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

[Přehled nasazení](avere-vfxt-deploy-overview.md) poskytuje celkový přehled kroků potřebných k vytvoření systému Avere vFXT pro Azure a jeho přípravě na poskytování dat.
