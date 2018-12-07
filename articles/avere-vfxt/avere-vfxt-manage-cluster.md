---
title: Správa clusteru vFXT Avere – Azure
description: Jak spravovat Avere cluster – přidat nebo odebrat uzly, restartovat, zastavit nebo odstranit vFXT cluster
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 82b5e175f0340aae7e35b177d9faa4831982984c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999066"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Správa clusteru Avere vFXT

Po vytvoření clusteru může být potřeba přidat uzly clusteru nebo zastavení nebo restartování clusteru. A po dokončení projektu je potřeba vědět, jak zastavit a trvalé odebrání clusteru. 

V závislosti na úloze správy clusteru můžete potřebovat udělat pomocí ovládacího panelu Avere, skriptu pro vytváření clusteru vfxt.py nebo na webu Azure portal. 

Tato tabulka poskytuje přehled, z nichž lze použít nástroje pro každý úkol. 

| Akce | Avere ovládací panely | vfxt.PY  | portál Azure |
| --- | --- | --- | --- |
| Přidat uzly clusteru | ne | ano | ne |
| Odebrání uzlů clusteru | ano | ne | ne |
| Zastavit uzel clusteru. | Ano (můžete také restartovat služby nebo po restartování) | ne | provozování dolů uzlu virtuálního počítače z portálu se interpretuje jako selhání uzlu |
| Spusťte zastavené uzel | ne | ne | ano |
| Odstranit jednoho uzlu clusteru | ne | ne | ano |
| Restartování clusteru |  |  |  |
| Vypnout nebo zastavení clusteru bezpečně | ano | ano | ne |
| Odstranit cluster  | ne | ano | Ano, ale není možné zaručit integritu dat |

Podrobné pokyny pro každý nástroj jsou uvedeny níže.

## <a name="about-stopped-instances-in-azure"></a>Informace o zastavení instance v Azure

Při vypnutí nebo zastavit všechny virtuální počítač Azure, zastaví nabíhání poplatků za výpočetní prostředky, ale stále se musíte zaplatit za jeho úložiště. Pokud vypnete vFXT uzlu nebo celý vFXT clusteru a že se ji restartovat, používejte na webu Azure portal k odstranění souvisejících virtuálních počítačů. 

Na webu Azure Portal *zastavena* uzlu (který může být restartován) se zobrazuje stav **zastavena** na webu Azure Portal; *odstranit* uzlu se zobrazuje stav **zastavena (přidělení zrušeno)**  a už budou vám účtovány poplatky za výpočetní prostředky ani prostředky úložiště.

Před odstraněním virtuálního počítače, ujistěte se, že všechny změněná data byl zapsán z mezipaměti do back endové úložné pomocí možnosti Avere ovládacích panelech nebo vfxt.py zastavte nebo vypněte cluster.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Správa clusteru s Avere ovládací panely 

Ovládací Panel Avere lze použít pro tyto úlohy: 

* Zastavit nebo restartovat jednotlivé uzly
* Odebrání uzlu z clusteru
* Zastavení nebo restartování celý cluster

Avere ovládací panely upřednostňuje integritu dat, takže se pokusí o zápis změněná data do back-endového úložiště pravděpodobně destruktivní operace. Díky tomu je bezpečnější možnost než Avere portálu. 

Panel ovládacího prvku Avere přístup z webového prohlížeče. Postupujte podle pokynů v [přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md) Pokud potřebujete pomoc.

### <a name="manage-nodes-with-avere-control-panel"></a>Spravujte uzly pomocí Avere ovládací panely

**FXT uzly** nastavení stránky obsahuje ovládací prvky pro správu jednotlivých uzlů.

Vypnout, restartovat nebo odebrání uzlu, uzlu v seznamu vyhledejte na **FXT uzly** stránky a klepněte na příslušné tlačítko v jeho **akce** sloupce.

> [!NOTE] 
> IP adresy se mohou pohybovat mezi uzly clusteru při změně počtu aktivních uzlů.

Čtení [Cluster > uzly FXT](<http://library.averesystems.com/ops_guide/4_7/gui_fxt_nodes.html#gui-fxt-nodes>) v Průvodci Avere clusteru nastavení pro další informace.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Zastavení nebo restartování clusteru pomocí Avere ovládací panely

**Údržbě systému** stránka nastavení obsahuje příkazy pro restartování služby clusteru, restartování clusteru nebo bezpečné provozování clusteru dolů. Čtení [správy > Údržba systému](<http://library.averesystems.com/ops_guide/4_7/gui_system_maintenance.html#gui-system-maintenance>) (v Avere clusteru nastavení průvodce) podrobnosti.

Cluster se vypíná, odešle stavové zprávy pro **řídicí panel** kartu v první. Po chvíli se relace Avere ovládací panely, přestane reagovat, což znamená, že byl vypnut clusteru.

## <a name="manage-the-cluster-with-vfxtpy"></a>Správa clusteru s vfxt.py 

Skript vfxt.py lze použít pro tyto úlohy správy clusteru:

* Přidání nových uzlů do clusteru
* Zastavení nebo spuštění clusteru  
* Odstranit cluster

Jako Avere ovládací panely zkuste vfxt.py operace Ujistěte se, že je změněná data trvale uložen na back endové úložné před vypnutím nebo zničení clusteru nebo uzlu. Díky tomu je bezpečnější možnost než Avere portálu.

vfxt.PY je předinstalován v řadiči clusteru virtuálních počítačů. <!-- (If you want to install it on another system, refer to https://github.com/Azure/AvereSDK) xxx change when this repo goes  public -->

Průvodce kompletní vfxt.py využití je k dispozici na Githubu: [Správa clusteru s vfxt.py v cloudu](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Přidat uzly clusteru s vfxt.py

Ukázkový skript příkaz pro přidání uzlů clusteru je součástí Správce clusteru. Vyhledejte ``./add-nodes`` na řadiči a otevřít ji v editoru a přizpůsobit s informacemi o clusteru. 

Použití tohoto příkazu musí být spuštěná clusteru. 

Zadejte následující hodnoty: 

* Název skupiny prostředků clusteru a také pro prostředky sítě a úložiště, pokud nejsou stejné jako clusteru
* Umístění clusteru
* Síť s clustery a podsítě 
* Role přístupu uzlu clusteru 
* IP adresa pro správu clusteru a heslo správce 
* Počet uzlů pro přidání (1, 2 nebo 3)
* Uzel instance typu a mezipaměti velikost hodnoty 

Pokud nepoužíváte prototyp, je nutné vytvořit příkaz podobný následující, včetně všech informace popsané výše. 

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \ 
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role ROLE_NAME \
   --log ~/vfxt.log
```

Další informace najdete v článku [přidání uzlů do clusteru](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) v Průvodci vfxt.py využití.

### <a name="stop-a-cluster-with-vfxtpy"></a>Zastavení clusteru s vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Spustit zastavený cluster s vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

Protože clusteru je zastaven, musíte předat identifikátory instancí k určení uzly clusteru. Čtení [zadání clusteru, které chcete upravit](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) v příručce využití vfxt.py Další informace.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Zrušení clusteru s vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

Možnost ``--quick-destroy`` lze použít, pokud nechcete k zápisu změněná data z mezipaměti clusteru.

Čtení [příručce využití vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) pro další informace.  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Spravovat virtuální počítače clusteru z webu Azure portal 

Na webu Azure portal je možné odstranit virtuální počítače clusteru jednotlivě, ale integrity dat není zaručeno, pokud není čistě nejprve clusteru. 

Na webu Azure portal je možné pro tyto úlohy správy clusteru: 

* Spusťte zastavené vFXT uzel
* Zastavení uzlu jednotlivé vFXT (clusteru interpretuje jako selhání uzlu)
* Zrušení clusteru vFXT *Pokud* není potřeba zajistit, že je změněná data v clusteru mezipaměti zapsáno do základní filtr
* Trvale odstranit vFXT uzlů a jiných prostředků clusteru po jejich vyply bezpečně

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Restartování instance vFXT z portálu Azure portal

Pokud je potřeba restart zastavené uzel, musíte použít na webu Azure portal. Vyberte **virtuálních počítačů** v levé nabídce a potom klikněte na název virtuálního počítače v seznamu, otevřete její stránku Přehled.

Klikněte na tlačítko **Start** tlačítko v horní části stránky přehled pro opětovnou aktivaci virtuálního počítače.

![Azure portal obrazovky zobrazující možnost spuštění zastaveného virtuálního počítače](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Odstranit uzly clusteru

Pokud chcete odstranit jednoho uzlu z clusteru vFXT, ale ponechat zbytek clusteru, měli byste nejprve [odebrat uzel z clusteru](#manage-nodes-with-avere-control-panel) pomocí ovládacího panelu Avere.

> [!CAUTION]
> Při odstranění uzlu bez první odebrání z clusteru vFXT, data budou ztracena.

Chcete-li trvale odstranit jednu nebo víc instancí, které se používají jako uzel vFXT, použijte na webu Azure portal.
Vyberte **virtuálních počítačů** v levé nabídce a potom klikněte na název virtuálního počítače v seznamu, otevřete její stránku Přehled.

Klikněte na tlačítko **odstranit** tlačítko v horní části stránky přehled trvale zničit virtuálního počítače.

Tuto metodu můžete po jejich vyply bezpečně trvale odebrat uzly clusteru. 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Odstranit cluster na webu Azure Portal

> [!NOTE] 
> Pokud chcete, aby všechny zbývající změny klienta v mezipaměti k zápisu do back endové úložné, použijte vfxt.py `--destroy` možnost nebo pomocí ovládacího panelu Avere řádně vypnout clusteru před odebráním uzlu instance na webu Azure Portal.

Instance uzlu může trvale zničit tak, že odstraníte na webu Azure Portal. Můžete je jednotlivě jak je popsáno výše, nebo můžete použít odstranit **virtuálních počítačů** stránku a najít všechny virtuální počítače clusteru, vyberte pomocí zaškrtávacích políček a klikněte na tlačítko **odstranit** tlačítko je odebrat vše v jedna akce.

![Seznam virtuálních počítačů na portálu, Filtroval termín "clusteru", s třemi čtyři zaškrtnuto a zvýrazní](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Odstranit prostředky další clusteru z webu Azure portal

Pokud jste vytvořili speciálně pro vFXT cluster další prostředky, můžete chtít odebrat jako součást opětné clusteru. Prvky, které obsahují data, která potřebujete nebo všechny položky, které jsou sdíleny s jinými projekty by neměl zničit.

Kromě odstranění uzlů clusteru, zvažte odebrání těchto součástí: 

* Kontroler clusteru virtuálních počítačů
* Datové disky, které jsou spojené s uzly clusteru
* Síťová rozhraní a veřejné IP adresy přidružené k součástem clusteru
* Virtuální sítě
* Účty úložiště (**pouze** pokud neobsahují žádné důležité data)
* Skupina dostupnosti 

![Azure portal vytvořil "všechny prostředky" seznamu, který ukazuje prostředky pro testovací cluster](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Odstranit skupinu prostředků clusteru z webu Azure portal

Pokud jste vytvořili skupinu prostředků speciálně pro cluster, lze zničit všechny související prostředky pro cluster pomocí zničení skupinu prostředků. 

> [!Caution] 
> Pokud jste si jisti, že žádné hodnoty se nachází ve skupině jenom odstranit skupinu prostředků. Například Ujistěte se, že přesunete veškerá potřebná data z libovolné úložiště kontejnerů v rámci skupiny prostředků.  

Pokud chcete odstranit skupinu prostředků, klikněte na tlačítko **skupiny prostředků** v nabídce vlevo na portálu a filtrovat seznam skupin prostředků na vyberte si tu edici jste vytvořili pro vFXT cluster. Vyberte skupinu prostředků a klikněte na tlačítko se třemi tečkami na pravé straně panelu. Zvolte **Odstranit skupinu prostředků**. Portál vás vyzve k potvrzení odstranění, které je nevratná operace.  

![Skupina prostředků zobrazuje akce "Odstranit skupinu prostředků"](media/avere-vfxt-delete-resource-group.png)
