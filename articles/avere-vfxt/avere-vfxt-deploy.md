---
title: Nasazení Avere vFXT pro Azure
description: Kroky k nasazení clusteru vFXT Avere v Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: eb0f5a4a4219c63334e0a5be3ea4378c3c317bec
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288097"
---
# <a name="deploy-the-vfxt-cluster"></a>Nasazení clusteru vFXT

Nejjednodušší způsob, jak vytvořit vFXT cluster v Azure je pro použití kontroléru clusteru. Kontroler clusteru je virtuální počítač, který obsahuje požadované skripty, šablony a infrastruktury softwaru pro vytváření a správu clusteru vFXT.

Nasazení nového clusteru vFXT zahrnuje tyto kroky:

1. [Vytvoření clusteru řadiče](#create-the-cluster-controller-vm).
1. Pokud používáte úložiště objektů Blob v Azure, [vytvořit koncový bod úložiště](#create-a-storage-endpoint-if-using-azure-blob) ve vaší virtuální síti.
1. [Připojení k řadiči clusteru](#access-the-controller). Zbývající kroky se provádějí z kontroleru clusteru virtuálních počítačů. 
1. [Vytvoření role přístupu](#create-the-cluster-node-access-role) pro uzly clusteru. Je k dispozici prototyp.
1. [Upravit skript pro vytvoření clusteru](#edit-the-deployment-script) typu vFXT clusteru, kterou chcete vytvořit.
1. [Spuštění skriptu pro vytváření clusteru](#run-the-script).

Další informace o plánování a kroky nasazení clusteru, najdete v článku [plánování vašeho systému vFXT Avere](avere-vfxt-deploy-plan.md) a [Přehled nasazení](avere-vfxt-deploy-overview.md). 

Po provedení kroků v tomto dokumentu, budete mít virtuální síť, podsíť, kontroleru a vFXT clusteru, jak je znázorněno v následujícím diagramu:

![Diagram znázorňující virtuální síť obsahuje volitelné blob storage a podsítě obsahující tři seskupené virtuálních počítačů označené jako clusterové uzly/vFXT vFXT a jeden kontroler s popiskem clusteru virtuálních počítačů](media/avere-vfxt-deployment.png)

Než začnete, ujistěte se, že jste vyřešili těchto nezbytných podmínkách:  

1. [Nové předplatné](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Oprávnění vlastníka předplatného](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Kvóta pro vFXT clusteru](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

Volitelně můžete vytvořit role uzlu clusteru [před](avere-vfxt-pre-role.md) vytváření adaptéru clusteru, ale je snazší to udělat později.

## <a name="create-the-cluster-controller-vm"></a>Vytvoření virtuálního počítače řadiče clusteru

Prvním krokem je vytvoření virtuálního počítače, které vytvoříte a nakonfigurujete vFXT uzly clusteru. 

Kontroler clusteru je virtuální počítač s Linuxem se Avere vFXT clusteru vytváření softwaru a skripty a předinstalovaným. Nemusí operací zpracování napájení nebo úložný prostor, abyste si mohli vybrat cenově dostupné možnosti. Tento virtuální počítač se použije čas od času v celém životním vFXT clusteru.

Existují dva způsoby vytvoření virtuálního počítače řadiče clusteru. [Šablony Azure Resource Manageru](#create-controller---arm-template) je k dispozici [níže](#create-controller---arm-template) ke zjednodušení procesu, ale můžete vytvořit také kontroler, z [image na Azure Marketplace](#create-controller---azure-marketplace-image). 

Můžete vytvořit novou skupinu prostředků jako součást vytváření kontroleru.

> [!TIP]
>
> Rozhodněte, jestli se mají použít veřejnou IP adresu na adaptéru clusteru. Veřejná IP adresa usnadňuje přístup k vFXT clusteru, ale vytvoří malý bezpečnostní riziko.
>
>  * Veřejné IP adresy na adaptéru clusteru můžete použít jako hostitele jump pro připojení ke clusteru vFXT Avere z mimo privátní podsítě.
>  * Pokud není nastavení veřejné IP adresy na řadiči, musíte použít jiný hostitel jump, připojení k síti VPN nebo ExpressRoute pro přístup ke clusteru. Například vytvořte řadič ve virtuální síti, který má připojení k síti VPN nakonfigurovaná.
>  * Pokud vytvoříte řadič s použitím veřejné IP adresy, by měl ochranu virtuálního počítače řadiče se skupinou zabezpečení sítě. Povolte přístup jenom přes port 22 pro poskytnutí přístupu k Internetu.

### <a name="create-controller---resource-manager-template"></a>Vytvoření kontroleru – šablona Resource Manageru

Můžete vytvořit řadič uzel clusteru z portálu, klikněte na tlačítko "Nasazení do Azure" níže. Toto nasazení vytvoří virtuální počítač, který bude vytvářet a spravovat Avere vFXT cluster šablony.

[![pro vytvoření kontroleru](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fazuredeploy.json)

Zadejte následující informace.

V **BASIC** části:  

* **Předplatné** pro cluster
* **Skupina prostředků** pro cluster 
* **Umístění** 

V **nastavení** části:

* Jestli chcete vytvořit novou virtuální síť

  * Pokud vytvoříte novou virtuální síť, Správce clusteru se přiřadí veřejnou IP adresu tak, že jste k němu přistoupit. Skupina zabezpečení sítě se také vytvoří pro tuto virtuální síť, která omezuje příchozí provoz na jediný port 22.
  * Pokud chcete použít ExpressRoute nebo sítě VPN pro připojení k řadiči clusteru, nastavte tuto hodnotu na `false` a zadejte existující virtuální síť v zbývající pole. Kontroler cluster použije tuto virtuální síť pro síťovou komunikaci. 

* Skupina prostředků virtuální sítě, název a název podsítě – zadejte názvy stávajících prostředků (Pokud používáte stávající virtuální síť) nebo zadejte nové názvy, pokud vytváříte novou virtuální síť
* **Název kontroleru** – nastavte název virtuálního počítače řadiče
* Uživatelské jméno správce řadiče – výchozí hodnota je `azureuser`
* Klíč SSH - vložit veřejný klíč pro přidružení k uživatelské jméno správce. Čtení [jak vytvořit a používat klíče SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) Pokud potřebujete pomoc.

V části **podmínky a ujednání**: 

* Přečtěte si podmínky služby a klikněte na zaškrtávací políčko, přijměte je. 

  > [!NOTE] 
  > Pokud nejste vlastníkem předplatného, mít vlastníka, přijměte podmínky, můžete pomocí následujících kontrolu požadovaných součástí kroků [přijměte podmínky předem softwaru](avere-vfxt-prereqs.md#accept-software-terms-in-advance). 

Klikněte na tlačítko **nákupní** po dokončení. Po pěti nebo šesti minut bude uzel vaše řadiče do provozu.

Na stránce výstupy shromažďovat informace o řadičích, které potřebujete k vytvoření clusteru. Čtení [informace potřebné k vytvoření clusteru](#information-needed-to-create-the-cluster) Další informace.

### <a name="create-controller---azure-marketplace-image"></a>Vytvoření řadiče - image na Azure Marketplace

Najít šablonu adaptéru tak, že na webu Azure Marketplace pro název ``Avere``. Vyberte **Avere vFXT pro kontroler Azure** šablony.

Pokud jste tak již neučinili, přijměte podmínky a povolit programový přístup pro Marketplace image kliknutím "chcete nasadit prostřednictvím kódu programu?" odkaz pod **vytvořit** tlačítko.

![Snímek obrazovky s odkazem programový přístup, který je pod tlačítko Vytvořit](media/avere-vfxt-deploy-programmatically.png)

Klikněte na tlačítko **povolit** tlačítko a uložte nastavení.

![Snímek obrazovky znázorňující kliknutí myší povolit programový přístup](media/avere-vfxt-enable-program.png)

Vraťte se do hlavní stránky **Avere vFXT pro kontroler Azure** šablonu a klikněte na tlačítko **vytvořit**. 

V prvním panelu zadejte nebo potvrďte tyto základní možnosti:

* **Předplatné**
* **Skupina prostředků** (Pokud chcete vytvořit novou skupinu, zadejte nový název.)
* **Název virtuálního počítače** – název kontroleru
* **Oblast**
* **Možnosti dostupnosti** – není potřeba redundance
* **Obrázek** -image uzlu Avere vFXT kontroleru
* **Velikost** – ponechte výchozí nastavení nebo zvolte jiný typ levné
* **Účet správce** – nastavení přihlášení ke clusteru kontroleru: 
  * Vyberte uživatelské jméno/heslo nebo veřejný klíč SSH (doporučeno).
  
    > [!TIP] 
    > Klíč SSH je bezpečnější. Čtení [jak vytvořit a používat klíče SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) Pokud potřebujete pomoc. 
  * Zadejte uživatelské jméno 
  * Vložte klíč SSH, nebo zadejte a potvrďte heslo
* **Příchozí pravidla portů** – Pokud se používá veřejnou IP adresu, otevřený port 22 (SSH)

Klikněte na tlačítko **Další** nastavit možnosti disku:

* **Typ disku operačního systému** – stačí na výchozí hodnotu pevný disk
* **Použití nespravovaných disků** – není potřeba
* **Datové disky** – nepoužívat

Klikněte na tlačítko **Další** pro síťové možnosti:

* **Virtuální síť** – vyberte virtuální síť, ke kontroleru nebo zadejte název pro vytvoření nové sítě vnet. Pokud nechcete použít veřejnou IP adresu na kontroleru, vezměte v úvahu umístění v síti vnet, která má ExpressRoute nebo jiné metody přístupu už nastavili.
* **Podsíť** -vybrat podsíť v rámci virtuální sítě (volitelné). Pokud vytvoříte novou virtuální síť, můžete vytvořit novou podsíť ve stejnou dobu.
* **Veřejná IP adresa** – Pokud chcete použít veřejnou IP adresu, můžete zadat sem. 
* **Skupina zabezpečení sítě** – ponechte výchozí nastavení (**základní**) 
* **Veřejné příchozí porty** – Pokud chcete povolit přístup z provoz SSH pomocí veřejné IP adresy, použití tohoto ovládacího prvku. 
* **Akcelerovanými síťovými službami** není k dispozici pro tento virtuální počítač.

Klikněte na tlačítko **Další** nastavit možnosti správy:

* **Diagnostika spouštění** – změňte na **vypnuto**
* **Operační systém hosta diagnostiky** – ponechte zakázáno
* **Účet úložiště diagnostiky** – volitelně vyberte nebo zadejte nový účet pro uložení diagnostických informací.
* **Identita spravované služby** – změňte tuto možnost k **na**, vytváří instančnímu objektu služby Azure AD pro správce clusteru.
* **Automatické vypnutí** -vynechat 

V tomto okamžiku můžete kliknout na **revize + vytvořit** Pokud nechcete používat instanci značky. V opačném případě klikněte na tlačítko **Další** dvakrát přejděte **hosta config** stránce a na stránku značek. Po dokončení existuje, klikněte na tlačítko **revize + vytvořit**. 

Jakmile se ověří váš výběr, klikněte na tlačítko **vytvořit** tlačítko.  

Vytvoření trvá pět nebo šest minut.

## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Vytvoření koncového bodu úložiště (Pokud se používá objektů Blob v Azure)

Pokud používáte úložiště objektů Blob v Azure pro váš back endovým datům úložiště, měli byste vytvořit koncový bod služby úložiště ve službě virtual network. To [koncový bod služby](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) udržuje objektů Blob v Azure provoz místní namísto směrování přes internet.

1. Z portálu, klikněte na tlačítko **virtuální sítě** na levé straně.
1. Vyberte virtuální síť pro kontrolér. 
1. Klikněte na tlačítko **koncové body služby** na levé straně.
1. Klikněte na tlačítko **přidat** v horní části.
1. Nechte službu jako ``Microsoft.Storage`` a zvolte podsíť kontroleru.
1. V dolní části, klikněte na tlačítko **přidat**.

  ![Azure portal – snímek obrazovky s poznámkami postup vytvoření koncového bodu služby](media/avere-vfxt-service-endpoint.png)

## <a name="information-needed-to-create-the-cluster"></a>Informace potřebné k vytvoření clusteru

Po vytvoření clusteru kontroleru, ujistěte se, že máte informace, které potřebujete pro další kroky. 

Informace potřebné pro připojení ke kontroleru: 

* Kontroler uživatelské jméno a klíč SSH (nebo heslo)
* IP adresa adaptéru nebo jiné metody pro připojení k řadiči virtuálního počítače

Informace potřebné pro cluster: 

* Název skupiny prostředků
* Lokalita Azure 
* Název virtuální sítě
* Název podsítě
* Název role uzlu clusteru – tento název je nastavena, když vytvoříte roli, je popsáno [níže](#create-the-cluster-node-access-role)
* Název účtu úložiště při vytváření kontejneru objektů Blob

Pokud uzel řadiče vytvoříte pomocí šablony Resource Manageru, můžete získat informace z [výstup šablony](#find-template-output). 

Pokud jste použili k vytvoření kontroleru image Azure Marketplace, většina z těchto položek můžete zadat přímo. 

Najdete všechny chybějící položky tak, že přejdete na stránku informace virtuálního počítače řadiče. Klikněte například na **všechny prostředky** a vyhledejte názvu kontroleru a pak klikněte na název kontroleru zobrazíte její podrobnosti.

### <a name="find-template-output"></a>Najít výstup šablony

Jak najít tyto informace ze Správce prostředků výstup šablony, postupujte takto:

1. Přejděte do skupiny prostředků pro váš cluster kontroleru.

1. Na levé straně a klikněte na tlačítko **nasazení**a potom **Microsoft.Template**.

   ![Portálu stránce skupiny prostředků s nasazením na levé straně a Microsoft.Template zobrazující v tabulce v části název nasazení](media/avere-vfxt-deployment-template.png)

1. Na levé straně a klikněte na tlačítko **výstupy**. Zkopírujte hodnoty v každém z těchto polí. 

   ![výstupy stránku SSHSTRING, RESOURCE_GROUP, umístění, síť, podsíť a SUBNET_ID zobrazené v polích napravo od popisků](media/avere-vfxt-template-outputs.png)

## <a name="access-the-controller"></a>Přístup ke kontroleru

Proveďte zbývající kroky nasazení, budete muset připojit ke kontroleru clusteru.

1. Metoda pro připojení k řadiči clusteru závisí na nastavení.

   * Nastavíte-li kontroler má veřejnou IP adresu, SSH na IP adresu kontroleru jako uživatelské jméno správce (například ``ssh azureuser@40.117.136.91``).
   * Pokud kontroler nemá veřejnou IP adresu, používat síť VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) připojení k virtuální síti.

1. Po přihlášení k řadiči, ověřit spuštěním `az login`. Zkopírování ověřovacího kódu v prostředí a potom pomocí webového prohlížeče k načtení [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin) a proveďte ověření pomocí systému společnosti Microsoft. Vraťte se do okna pro potvrzení.

   ![Výstup příkazového řádku příkazu "AZ login" zobrazením prohlížeče odkaz a ověřování kódu](media/avere-vfxt-azlogin.png)

1. Přidáte předplatné spuštěním tohoto příkazu pomocí ID předplatného:  ```az account set --subscription YOUR_SUBSCRIPTION_ID```

## <a name="create-the-cluster-node-access-role"></a>Umožňuje vytvořit roli přístup k uzlu clusteru

> [!NOTE] 
> Pokud si nejste vlastník předplatného a dosud nebyla vytvořena role, jste vlastníkem předplatného, postupujte podle těchto kroků nebo použijte postup uvedený v [vytvořit přístup role Avere vFXT clusteru runtime bez kontroleru](avere-vfxt-pre-role.md).

[Řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) poskytuje vFXT uzly clusteru oprávnění k provedení nezbytných úloh.  

Jako součást normální vFXT operace clusteru jednotlivé vFXT uzly se muset zabývat záležitostmi, jako je čtení vlastností prostředků Azure, spravovat úložiště a řídit, ostatní uzly nastavení síťového rozhraní. 

1. V řadiči, otevřete ``/avere-cluster.json`` soubor v editoru.

   ![Konzola znázorňující příkaz seznamu a potom "vi /avere-cluster.json"](media/avere-vfxt-open-role.png)

1. Upravte soubor uvést ID předplatného a odstranit řádek nad ním. Uložte soubor jako ``avere-cluster.json``.

   ![Konzola znázorňující ID předplatného a "Odebrat tento řádek" vybraných pro odstranění textový editor](media/avere-vfxt-edit-role.png)

1. Použijte tento příkaz pro vytvoření role:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

Název role předá do clusteru vytváření skriptu v dalším kroku. 

## <a name="create-nodes-and-configure-the-cluster"></a>Vytvoření uzlů a konfigurace clusteru

K vytvoření clusteru vFXT Avere, jednu z ukázkových skriptech na kontroleru a spustíme ji. Ukázky skriptů jsou umístěné v kořenovém adresáři (`/`) na řadiči clusteru.

* Pokud chcete vytvořit kontejner objektů Blob, který chcete použít jako Avere vFXT úložiště back-end systém, použijte ``create-cloudbacked-cluster`` skriptu.

* Pokud přidáte úložiště později, použijte ``create-minimal-cluster`` skriptu.

> [!TIP]
> Prototyp skriptů pro přidávání uzlů a zničení vFXT clusteru jsou také zahrnuté v `/` tohoto kontroleru clusteru virtuálních počítačů.

### <a name="edit-the-deployment-script"></a>Upravit skript pro nasazení

Ukázkový skript otevře v editoru. Můžete chtít uložit upravený skript s jiným názvem, aby nedošlo k přepsání původní ukázce.

Zadejte hodnoty pro tyto proměnné skriptu.

* Název skupiny prostředků

  * Pokud používáte sítě nebo úložiště komponent, které jsou v různých skupinách prostředků, zrušte komentář u proměnné a také zadat tyto názvy. 

```python
# Resource groups
# At a minimum specify the resource group.  If the network resources live in a
# different group, specify the network resource group.  Likewise for the storage
# account resource group.
RESOURCE_GROUP=
#NETWORK_RESOURCE_GROUP=
#STORAGE_RESOURCE_GROUP=
```

* Název místa
* Název virtuální sítě
* Název podsítě
* Azure AD název modulu runtime role – Pokud jste postupovali podle příkladu v [vytvořit role clusteru uzel přístupu](#create-the-cluster-node-access-role), použijte ``avere-cluster``. 
* Název účtu úložiště (Pokud vytváříte nový kontejner objektů Blob)
* Název clusteru - nemůže mít dva clustery vFXT se stejným názvem ve stejné skupině prostředků. 
* Heslo správce – zvolte zabezpečené heslo pro monitorování a správu clusteru. Toto heslo je přiřazena uživateli ``admin``. 
* Typ instance uzlu - naleznete v tématu [velikosti uzlů vFXT](avere-vfxt-deploy-plan.md#vfxt-node-sizes) informace
* Velikost mezipaměti uzlu – viz [velikosti uzlů vFXT](avere-vfxt-deploy-plan.md#vfxt-node-sizes) informace

Uložte soubor a ukončete.

### <a name="run-the-script"></a>Spuštění skriptu

Spusťte skript zadáním názvu souboru, který jste vytvořili. (Příklad: `./create-cloudbacked-cluster-west1`)  

> [!TIP]
> Zvažte spuštění tohoto příkazu uvnitř [terminálu multiplexor](http://linuxcommand.org/lc3_adv_termmux.php) jako `screen` nebo `tmux` v případě ztráty připojení.  

Výstup se taky zaznamenává do `~/vfxt.log`.

Po dokončení skriptu zkopírujte IP adresu správy, která je potřebná pro správu clusteru.

![Výstup příkazového řádku souboru, který zobrazuje IP adresa pro správu poblíž konce](media/avere-vfxt-mgmt-ip.png)

## <a name="next-step"></a>Další krok

Teď, když cluster běží a znáte jeho IP adresa pro správu, můžete [připojit k nástroji Konfigurace clusteru](avere-vfxt-cluster-gui.md) povolit podporu a v případě potřeby přidejte úložiště.
