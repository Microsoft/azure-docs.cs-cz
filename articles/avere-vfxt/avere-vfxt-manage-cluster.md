---
title: Správa clusteru Avere vFXT – Azure
description: Jak spravovat cluster Avere - přidání nebo odebrání uzlů, restartování, zastavení nebo zničení clusteru vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 94db4a93025b6e3d633368d924e3e0c518d108ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153475"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Správa clusteru Avere vFXT

V určitém okamžiku životního cyklu vašeho clusteru Avere vFXT pro Azure může být nutné přidat uzly clusteru nebo spustit nebo restartovat cluster. Po dokončení projektu budete potřebovat vědět, jak cluster zastavit a trvale jej odebrat.

Tento článek vysvětluje, jak přidat nebo odebrat uzly clusteru a další základní operace clusteru. Pokud potřebujete změnit nastavení clusteru nebo sledovat jeho práci, použijte [Ovládací panely Avere](avere-vfxt-cluster-gui.md).

V závislosti na úloze správy může být nutné použít jeden ze tří různých nástrojů: Ovládací panely Avere, skript pro správu clusteru vfxt.py příkazového řádku a portál Azure.

Tato tabulka poskytuje přehled o tom, které nástroje lze pro jednotlivé úkoly použít.

| Akce | Ovládací panel Avere | vfxt.py  | portál Azure |
| --- | --- | --- | --- |
| Přidání uzlů clusteru | ne | ano | ne |
| Odebrání uzlů clusteru | ano | ne | ne |
| Zastavení uzlu clusteru | ano (může také restartovat služby nebo restartovat) | ne | vypnutí virtuálního virtuálního soudu uzlu z portálu je interpretováno jako selhání uzlu |
| Spuštění zastaveného uzlu | ne | ne | ano |
| Zničení jednoho uzlu clusteru | ne | ne | ano |
| Restartování clusteru |  |  |  |
| Bezpečné vypnutí nebo zastavení clusteru | ano | ano | ne |
| Zničte cluster  | ne | ano | Ano, ale integrita dat není zaručena. |

Podrobné pokyny pro každý nástroj jsou uvedeny níže.

## <a name="about-stopped-instances-in-azure"></a>O zastavených instancích v Azure

Když vypnete nebo zastavíte jakýkoli virtuální počítač Azure, přestane za dávat poplatky za výpočetní prostředky, ale pořád musíte zaplatit za jeho úložiště. Pokud vypnete uzel vFXT nebo celý cluster vFXT a nemáte v úmyslu ho restartovat, měli byste použít portál Azure k odstranění souvisejících virtuálních počítačů.

Na webu Azure Portal *zobrazí zastavený* uzel (který se dá restartovat) stavu **zastaveného** na webu Azure Portal. *Odstraněný* uzel zobrazuje stav **zastavený (navrácený)** a již neúčtuje poplatky za výpočetní prostředky nebo úložiště.

Před odstraněním virtuálního virtuálního ms se ujistěte, že všechna změněná data byla zapsána z mezipaměti do back-endového úložiště pomocí ovládacího panelu Avere nebo vfxt.py možnostmi zastavení nebo vypnutí clusteru.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Správa clusteru pomocí ovládacího panelu Avere

Ovládací panel Avere lze použít pro tyto úkoly:

* Zastavení nebo restartování jednotlivých uzlů
* Odebrání uzlu z clusteru
* Zastavení nebo restartování celého clusteru

Ovládací panel Avere upřednostňuje integritu dat, takže se pokusí zapsat všechna změněná data do back-endového úložiště před možná destruktivní operací. Díky tomu je bezpečnější možnost než portál Azure.

Přístup k ovládacímu panelu Avere z webového prohlížeče. Pokud potřebujete pomoc, postupujte podle pokynů v [aplikaci Access the vFXT clusteru.](avere-vfxt-cluster-gui.md)

### <a name="manage-nodes-with-avere-control-panel"></a>Správa uzlů pomocí ovládacího panelu Avere

Stránka nastavení **uzlů FXT** obsahuje ovládací prvky pro správu jednotlivých uzlů.

Chcete-li uzel vypnout, restartovat nebo odebrat, vyhledejte uzel v seznamu na stránce **FXT Uzly** a klikněte na příslušné tlačítko ve sloupci **Akce.**

> [!NOTE]
> Ip adresy se mohou pohybovat mezi uzly clusteru při změně počtu aktivních uzlů.

Další informace najdete v průvodci clusterem [> FXT v](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) průvodci nastavením clusteru Avere.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Zastavení nebo restartování clusteru pomocí ovládacího panelu Avere

Stránka **Nastavení údržby systému** obsahuje příkazy pro restartování clusterových služeb, restartování clusteru nebo bezpečné vypnutí clusteru. Podrobnosti najdete v části [Správa > údržby systému](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (v průvodci nastavením clusteru Avere).

Když se cluster začne vypínat, odešle zprávy o stavu na kartu **Řídicí panel.** Po několika okamžicích zprávy zastavit a nakonec Avere Ovládací panely relace přestane reagovat, což znamená, že cluster byl vypnut.

## <a name="manage-the-cluster-with-vfxtpy"></a>Správa clusteru pomocí vfxt.py

vfxt.py je nástroj příkazového řádku pro vytváření a správu clusteru.

vfxt.py je předinstalovaný na virtuálním počítači řadiče clusteru. Chcete-li jej nainstalovat do jiného systému, přečtěte si dokumentaci k webu <https://github.com/Azure/AvereSDK>.

Skript vfxt.py lze použít pro tyto úlohy správy clusteru:

* Přidání nových uzlů do clusteru
* Zastavení nebo spuštění clusteru  
* Zničení clusteru

Stejně jako Ovládací panely Avere se vfxt.py operace snaží zajistit, aby změněná data byla před vypnutím nebo zničením clusteru nebo uzlu trvale uložena v úložišti back-end. Díky tomu je bezpečnější možnost než portál Azure.

Kompletní průvodce vfxt.py využitím je k dispozici na [GitHubu: Správa cloudových clusterů s vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Přidání uzlů clusteru s vfxt.py

Ukázkový příkazový skript pro přidání uzlů clusteru je součástí řadiče clusteru. Vyhledejte ``./add-nodes`` na ovladači a otevřete jej v editoru a přizpůsobte jej informacemi o clusteru.

Chcete-li použít tento příkaz, musí být spuštěn cluster.

Zadávat následující hodnoty:

* Název skupiny prostředků pro cluster a také pro síťové prostředky a prostředky úložiště, pokud nejsou ve stejné skupině prostředků jako cluster
* Umístění clusteru
* Síť clusteru a podsíť
* Role přístupu k uzlu clusteru (použijte předdefinovanou roli [Avere Operator)](../role-based-access-control/built-in-roles.md#avere-operator)
* Adresa IP pro správu clusteru a heslo pro správu
* Počet uzlů, které chcete přidat (1, 2 nebo 3)
* Typ instance uzlu a hodnoty velikosti mezipaměti

Pokud nepoužíváte prototyp, musíte vytvořit příkaz, jako je následující, včetně všech výše popsaných informací.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Další informace našlápne [te článek Přidání uzlů do clusteru](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) v průvodci vfxt.py použití.

### <a name="stop-a-cluster-with-vfxtpy"></a>Zastavení clusteru pomocí vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Spuštění zastaveného clusteru s vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Vzhledem k tomu, že je cluster zastaven, musíte předat identifikátory instancí, abyste určili uzly clusteru. Další informace najdete v [části Určení clusteru, který chcete upravit](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) v příručce vfxt.py použití.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Zničte cluster s vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

Tuto ``--quick-destroy`` možnost lze použít, pokud nechcete ukládat změněná data z mezipaměti clusteru.

Další informace naleznete v [průvodci používáním vfxt.py.](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>)

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Správa virtuálních počítačů clusteru z portálu Azure

Portál Azure lze použít ke zničení virtuálních počítačích clusteru jednotlivě, ale integrita dat není zaručena, pokud není cluster ukončit čistě první.

Portál Azure se dá použít pro tyto úlohy správy clusteru:

* Spuštění zastaveného uzlu vFXT
* Zastavení jednotlivého uzlu vFXT (cluster interpretuje to jako selhání uzlu)
* Pokud není *nutné* zajistit, aby byla změněná data v mezipaměti clusteru zapsána do základního fileru, zničte cluster vFXT.
* Trvale odeberte uzly vFXT a další prostředky clusteru po bezpečném vypnutí

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Restartování instancí vFXT z portálu Azure

Pokud potřebujete restartovat zastavený uzel, musíte použít portál Azure. V levém menu vyberte **Virtuální počítače** a kliknutím na název virtuálního počítače v seznamu otevřete jeho stránku s přehledem.

Kliknutím na tlačítko **Start** v horní části stránky s přehledem znovu aktivujte virtuální ho.

![Obrazovka portálu Azure zobrazující možnost spuštění zastaveného virtuálního počítače](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Odstranění uzlů clusteru

Pokud chcete odstranit jeden uzel ze clusteru vFXT, ale zachovat zbytek clusteru, měli byste [nejprve odebrat uzel ze clusteru](#manage-nodes-with-avere-control-panel) pomocí ovládacího panelu Avere.

> [!CAUTION]
> Pokud odstraníte uzel bez předchozího odebrání z clusteru vFXT, data mohou být ztracena.

Chcete-li trvale zničit jednu nebo více instancí používaných jako uzel vFXT, použijte portál Azure.
V levém menu vyberte **Virtuální počítače** a kliknutím na název virtuálního počítače v seznamu otevřete jeho stránku s přehledem.

Kliknutím na tlačítko **Odstranit** v horní části stránky přehledu trvale zničíte virtuální ho.

Tuto metodu můžete použít k trvalému odebrání uzlů clusteru po jejich bezpečném vypnutí.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Zničení clusteru z portálu Azure

> [!NOTE]
> Pokud chcete, aby všechny zbývající změny klienta v mezipaměti, které `--destroy` mají být zapsány do úložiště back-end, použijte možnost vfxt.py nebo použijte Ovládací panely Avere vypnout cluster uvolněte čistě před odebráním instance uzlu na portálu Azure.

Instance uzlů můžete trvale zničit jejich odstraněním na webu Azure Portal. Můžete je odstranit jeden po druhém, jak je popsáno výše, nebo můžete použít stránku **Virtuální počítače** k vyhledání všech virtuálních počítačů clusteru, jejich zaškrtnutí pomocí zaškrtávacích políček a kliknutím na tlačítko **Odstranit** je všechny odeberete v jedné akci.

![Seznam virtuálních disponiárů na portálu, filtrovaný podle termínu "cluster", se třemi ze čtyř zkontrolovaných a zvýrazněných](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Odstranění dalších prostředků clusteru z webu Azure Portal

Pokud jste vytvořili další prostředky speciálně pro cluster vFXT, můžete je odebrat jako součást stržení clusteru. Nezničte prvky, které obsahují data, která potřebujete, ani žádné položky, které jsou sdíleny s jinými projekty.

Kromě odstranění uzlů clusteru zvažte odebrání těchto součástí:

* Virtuální ms řadiče clusteru
* Datové disky přidružené k uzlům clusteru
* Síťová rozhraní a veřejné IP adresy přidružené k součástem clusteru
* Virtuální sítě
* Skladovací kontejnery a účty úložiště **(pouze** pokud neobsahují žádná důležitá data)
* Skupina dostupnosti

![Seznam "všechny prostředky" na webu Azure portal zobrazující prostředky vytvořené pro testovací cluster](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Odstranění skupiny prostředků clusteru z webu Azure Portal

Pokud jste vytvořili skupinu prostředků speciálně pro cluster, můžete zničit všechny související prostředky pro cluster zničením skupiny prostředků.

> [!Caution]
> Skupinu prostředků zničte pouze v případě, že jste si jisti, že ve skupině není nic hodnotného. Například ujistěte se, že jste přesunuli všechna potřebná data z kontejnerů úložiště v rámci skupiny prostředků.  

Chcete-li odstranit skupinu prostředků, klikněte v levé nabídce portálu na **skupiny prostředků** a vyfiltrujte seznam skupin prostředků a vyhledejte ten, který jste vytvořili pro cluster vFXT. Vyberte skupinu prostředků a klepněte na tři tečky vpravo od panelu. Zvolte **Odstranit skupinu prostředků**. Portál vás požádá o potvrzení vymazání, což je nevratné.

![Skupina prostředků zobrazující akci Odstranit skupinu prostředků](media/avere-vfxt-delete-resource-group.png)
