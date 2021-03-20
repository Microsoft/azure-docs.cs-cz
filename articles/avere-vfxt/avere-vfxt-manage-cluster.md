---
title: Správa clusteru avere vFXT – Azure
description: Jak spravovat cluster avere – přidání nebo odebrání uzlů, restartování, zastavení nebo zničení clusteru vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4135bfe528c33a2beaeb21438181deb5b19ad12e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85505490"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Správa clusteru Avere vFXT

V určitém okamžiku životního cyklu avere vFXT pro cluster Azure možná budete muset přidat uzly clusteru nebo spustit nebo restartovat cluster. Po dokončení projektu budete muset zjistit, jak cluster zastavit a trvale ho odebrat.

Tento článek vysvětluje, jak přidat nebo odebrat uzly clusteru a další základní operace clusteru. Pokud potřebujete změnit nastavení clusteru nebo sledovat jeho práci, použijte [ovládací panel avere](avere-vfxt-cluster-gui.md).

V závislosti na úloze správy bude možná nutné použít jeden ze tří různých nástrojů: avere Control Panel, vfxt.py Command line Cluster Management a Azure Portal.

Tato tabulka obsahuje přehled toho, které nástroje lze pro jednotlivé úlohy použít.

| Akce | Ovládací panel avere | vfxt.py  | portál Azure |
| --- | --- | --- | --- |
| Přidání uzlů clusteru | ne | ano | ne |
| Odebrat uzly clusteru | ano | ne | ne |
| Zastavení uzlu clusteru | Ano (může také restartovat služby nebo restartovat) | ne | vypínání virtuálního počítače uzlu z portálu je interpretováno jako selhání uzlu. |
| Spustit zastavený uzel | ne | ne | ano |
| Zničení jednoho uzlu clusteru | ne | ne | ano |
| Restartujte cluster. |  |  |  |
| Bezpečně vypínejte nebo Zastavte cluster. | ano | ano | ne |
| Zničit cluster  | ne | ano | Ano, ale integrita dat není zaručena. |

Podrobné pokyny pro jednotlivé nástroje jsou uvedené níže.

## <a name="about-stopped-instances-in-azure"></a>O zastavených instancích v Azure

Když vypnete nebo zastavíte některý virtuální počítač Azure, zastaví se vám poplatky za výpočetní prostředky, ale ještě musíte platit za své úložiště. Pokud vypnete uzel vFXT nebo celý cluster vFXT a nechcete ho restartovat, měli byste k odstranění souvisejících virtuálních počítačů použít Azure Portal.

V Azure Portal *zastavený* uzel (který může být restartován) zobrazuje stav **zastaveno** v Azure Portal. *Odstraněný* uzel zobrazuje stav **Zastaveno (přidělení zrušeno)** a již nezahrnuje poplatky za výpočetní prostředky nebo úložiště.

Před odstraněním virtuálního počítače se ujistěte, že všechna změněná data byla zapsána z mezipaměti do back-endového úložiště pomocí ovládacích panelů avere nebo možností vfxt.py pro zastavení nebo vypnutí clusteru.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Správa clusteru pomocí ovládacího panelu avere

Ovládací panel avere lze použít pro tyto úlohy:

* Zastavení nebo restartování jednotlivých uzlů
* Odebrání uzlu z clusteru
* Zastavení nebo restartování celého clusteru

Ovládací panel avere určuje prioritu integrity dat, takže se pokusí zapsat změněná data do back-endu úložiště před možnou destruktivní operací. Díky tomu je tato možnost bezpečnější než Azure Portal.

Přístup k ovládacímu panelu avere z webového prohlížeče. Pokud potřebujete podporu, postupujte podle pokynů v [části přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md) .

### <a name="manage-nodes-with-avere-control-panel"></a>Správa uzlů pomocí ovládacího panelu avere

Stránka nastavení **uzlů FXT** obsahuje ovládací prvky pro správu jednotlivých uzlů.

Chcete-li vypnout, restartovat nebo odebrat uzel, vyhledejte uzel v seznamu na stránce **uzly FXT** a klikněte na příslušné tlačítko ve sloupci jeho **Akce** .

> [!NOTE]
> Pokud se počet aktivních uzlů změní, můžou se IP adresy přesouvat mezi uzly clusteru.

Další informace najdete v tématu [cluster > FXT Nodes](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) v Průvodci nastavením clusteru avere.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Zastavení nebo restartování clusteru pomocí ovládacího panelu avere

Stránka nastavení **údržby systému** obsahuje příkazy pro restartování clusterových služeb, restartování clusteru nebo bezpečného napájení clusteru. Podrobnosti naleznete v tématu [správa > údržba systému](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (v Průvodci nastavením clusteru avere).

Když se cluster začne vypínat, odešle stavové zprávy na kartu **řídicí panel** . Po chvíli se zprávy zastaví a nakonec relace ovládacího panelu avere přestane reagovat, což znamená, že se cluster vypnul.

## <a name="manage-the-cluster-with-vfxtpy"></a>Správa clusteru pomocí vfxt.py

vfxt.py je nástroj příkazového řádku pro vytváření a správu clusteru.

vfxt.py je předem nainstalovaný na virtuálním počítači s řadičem clusteru. Pokud ho chcete nainstalovat na jiný systém, přečtěte si dokumentaci na adrese <https://github.com/Azure/AvereSDK> .

Skript vfxt.py se dá použít pro tyto úlohy správy clusteru:

* Přidání nových uzlů do clusteru
* Zastavení nebo spuštění clusteru  
* Zničit cluster

Podobně jako v Ovládacích panelech avere se operace vfxt.py snaží zajistit, aby se změněná data ukládala trvale do back-endu úložiště před vypnutím nebo zničením clusteru nebo uzlu. Díky tomu je tato možnost bezpečnější než Azure Portal.

Kompletní příručka k používání vfxt.py je k dispozici na GitHubu: [Správa cloudových clusterů pomocí vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Přidání uzlů clusteru pomocí vfxt.py

Do řadiče clusteru je zahrnut ukázkový skript příkazu pro přidání uzlů clusteru. Vyhledejte ``./add-nodes`` na řadiči a otevřete ho v editoru, abyste ho mohli přizpůsobit pomocí informací o clusteru.

Aby bylo možné použít tento příkaz, musí být spuštěn cluster.

Zadejte následující hodnoty:

* Název skupiny prostředků pro cluster, a to i pro prostředky sítě a úložiště, pokud nejsou ve stejné skupině prostředků jako cluster
* Umístění clusteru
* Síť a podsíť clusteru
* Role přístupu k uzlu clusteru (použijte předdefinovaný [avere operátor](../role-based-access-control/built-in-roles.md#avere-operator)role)
* IP adresa pro správu clusteru a heslo pro správu
* Počet uzlů, které se mají přidat (1, 2 nebo 3)
* Typ instance uzlu a hodnoty velikosti mezipaměti

Pokud prototyp nepoužíváte, musíte vytvořit příkaz podobný následujícímu, včetně všech výše popsaných informací.

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

Další informace najdete v tématu [Přidání uzlů do clusteru](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) v příručce pro použití vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Zastavení clusteru pomocí vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Spuštění zastaveného clusteru s vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Vzhledem k tomu, že je cluster zastavený, je nutné předat identifikátory instancí, abyste určili uzly clusteru. Další informace najdete v tématu [určení clusteru, který se má upravit](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) v průvodci používáním vfxt.py.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Zničit cluster pomocí vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

Možnost se ``--quick-destroy`` dá použít, pokud nechcete ukládat změněná data z mezipaměti clusteru.

Další informace najdete v [příručce k používání vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) .

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Správa virtuálních počítačů clusteru z Azure Portal

Azure Portal lze použít ke zničení virtuálních počítačů clusteru jednotlivě, ale integrita dat není zaručena, Pokud cluster není nejprve vypnutý.

Azure Portal lze použít pro tyto úlohy správy clusteru:

* Spustit zastavený uzel vFXT
* Zastavení jednotlivého uzlu vFXT (cluster to interpretuje jako selhání uzlu)
* *Pokud* nepotřebujete mít jistotu, že se změněná data v mezipaměti clusteru zapisují do základního souborového, odstraňte cluster vFXT.
* Trvalé odebrání uzlů vFXT a dalších prostředků clusteru poté, co byly bezpečně vypnuty

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Restartujte vFXT instance z Azure Portal

Pokud potřebujete restartovat zastavený uzel, je nutné použít Azure Portal. V nabídce vlevo vyberte **virtuální počítače** a potom kliknutím na název virtuálního počítače v seznamu otevřete jeho stránku s přehledem.

Kliknutím na tlačítko **Start** v horní části stránky přehled znovu aktivujete virtuální počítač.

![Obrazovka Azure Portal znázorňující možnost spuštění zastaveného virtuálního počítače](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Odstranit uzly clusteru

Pokud chcete odstranit jeden uzel z clusteru vFXT, ale zachovat zbývající část clusteru, měli byste nejdřív [odebrat uzel z clusteru](#manage-nodes-with-avere-control-panel) pomocí ovládacího panelu avere.

> [!CAUTION]
> Pokud odstraníte uzel, aniž byste ho nejdřív odebrali z clusteru vFXT, může dojít ke ztrátě dat.

K trvalému zničení jedné nebo více instancí používaných jako uzel vFXT použijte Azure Portal.
V nabídce vlevo vyberte **virtuální počítače** a potom kliknutím na název virtuálního počítače v seznamu otevřete jeho stránku s přehledem.

Kliknutím na tlačítko **Odstranit** v horní části stránky přehled trvale odstraníte virtuální počítač.

Tuto metodu můžete použít k trvalému odebrání uzlů clusteru poté, co byly bezpečně vypnuty.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Zničit cluster z Azure Portal

> [!NOTE]
> Pokud chcete, aby se všechny zbývající změny klienta v mezipaměti zapisovaly do back-endu úložiště, buď použijte možnost vfxt.py, `--destroy` nebo pomocí ovládacího panelu avere vypněte cluster čistě před odebráním instancí uzlu v Azure Portal.

Instance uzlů můžete zničit trvale jejich odstraněním v Azure Portal. Můžete je odstranit jednou, jak je popsáno výše, nebo můžete použít stránku **Virtual Machines** k vyhledání všech virtuálních počítačů clusteru, jejich zaškrtnutí pomocí zaškrtávacích políček a kliknutím na tlačítko **Odstranit** je odstranit vše v jedné akci.

![Seznam virtuálních počítačů na portálu filtrovaných podle výrazu "cluster" se třemi ze čtyř zaškrtnutých a zvýrazněných](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Odstranění dalších prostředků clusteru z Azure Portal

Pokud jste vytvořili další prostředky konkrétně pro cluster vFXT, můžete je chtít odebrat jako součást odtrhnout cluster. Nezničit prvky, které obsahují data, která potřebujete, nebo jakékoli položky, které jsou sdíleny s jinými projekty.

Kromě odstranění uzlů clusteru zvažte odebrání těchto součástí:

* Virtuální počítač řadiče clusteru
* Datové disky přidružené k uzlům clusteru
* Síťová rozhraní a veřejné IP adresy přidružené ke komponentám clusteru
* Virtuální sítě
* Kontejnery úložiště a účty úložiště (**jenom** v případě, že neobsahují žádná důležitá data)
* Skupina dostupnosti

![Azure Portal seznamu všechny prostředky zobrazující prostředky vytvořené pro testovací cluster](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Odstraní skupinu prostředků clusteru z Azure Portal.

Pokud jste vytvořili skupinu prostředků konkrétně pro vytvoření clusteru, můžete zničit všechny související prostředky pro cluster zničením skupiny prostředků.

> [!Caution]
> Jenom zničit skupinu prostředků, jenom pokud jste si jisti, že ve skupině není žádná hodnota. Ujistěte se například, že jste přesunuli veškerá potřebná data z kontejnerů úložiště v rámci skupiny prostředků.  

Pokud chcete odstranit skupinu prostředků, klikněte v levé nabídce na portálu na **skupiny** prostředků a vyfiltrujte seznam skupin prostředků tak, abyste našli ten, který jste vytvořili pro cluster vFXT. Vyberte skupinu prostředků a klikněte na tři tečky na pravé straně panelu. Zvolte **Odstranit skupinu prostředků**. Portál vás vyzve k potvrzení odstranění, což je nevratné.

![Skupina prostředků, která zobrazuje akci "odstranit skupinu prostředků"](media/avere-vfxt-delete-resource-group.png)
