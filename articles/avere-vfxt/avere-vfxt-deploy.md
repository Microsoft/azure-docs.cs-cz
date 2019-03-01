---
title: Nasazení Avere vFXT pro Azure
description: Kroky k nasazení clusteru vFXT Avere v Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 7081d46af335f29e5723ef8d471814a1564907c2
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990200"
---
# <a name="deploy-the-vfxt-cluster"></a>Nasazení clusteru vFXT

Tento postup vás provede pomocí Průvodce nasazení k dispozici z webu Azure Marketplace. Průvodce automaticky nasadí do clusteru s použitím šablony Azure Resource Manageru. Po zadání parametrů ve formuláři a klikněte na tlačítko **vytvořit**, Azure automaticky provede tyto kroky:

* Vytvoří kontroler clusteru, což je základní virtuální počítač, který obsahuje software potřebný k nasazení a správě clusteru.
* Vytvoří skupinu prostředků a virtuální síťové infrastruktury, včetně vytváření nových elementů.
* Vytvoří virtuální počítače uzlu clusteru a nakonfiguruje jako Avere clusteru.
* Pokud o to požádá vytvoří nový kontejner objektů Blob v Azure a nakonfiguruje ho jako filtr core clusteru.

Po provedení kroků v tomto dokumentu, bude mít virtuální síť, podsíť, kontroleru a vFXT clusteru, jak je znázorněno v následujícím diagramu. Tento diagram znázorňuje základní filer volitelné objektů Blob v Azure, což zahrnuje nový kontejner úložiště objektů Blob (v rámci nového účtu úložiště, není vidět) a koncový bod služby pro Microsoft storage v podsíti. 

![Diagram znázorňující tři soustředných obdélníků se Avere součásti clusteru. Vnější obdélníku je označené jako "Skupiny prostředků" a obsahuje šestiúhelník označené jako "Blob storage (volitelné)". Další obdélníku v je označené jako "virtuální síť: 10.0.0.0/16 "a nesmí obsahovat žádné jedinečné součásti. Nejvnitřnější obdélníku je označené jako "Subnet:10.0.0.0/24" a obsahuje virtuální počítač s názvem "Řadič clusteru", množství tři virtuální počítače s názvem "vFXT uzly (vFXT clusteru)" a šestiúhelník označené jako koncový bod služby. Se šipkou připojení koncového bodu služby (což je v podsíti) a úložišti objektů blob (což je mimo podsíť a virtuální sítě ve skupině prostředků). Na šipku prochází podsítě a virtuální sítě hranice.](media/avere-vfxt-deployment.png)  

Před použitím šablonu pro vytvoření, ujistěte se, že se že odstranily tyto požadavky:  

1. [Nové předplatné](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Oprávnění vlastníka předplatného](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Kvóta pro vFXT clusteru](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Vlastní přístup role](avere-vfxt-prereqs.md#create-access-roles) -musí vytvořit roli řízení přístupu na základě role přiřadit uzlům clusteru. Máte možnost také vytvářet vlastní přístup role pro správce clusteru, ale většina uživatelů bude trvat výchozí role vlastníka, který dává oprávnění kontroleru odpovídající vlastníkovi skupiny prostředků. Čtení [předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md#owner) další podrobnosti.
1. [Koncový bod služby Storage (v případě potřeby)](avere-vfxt-prereqs.md#optional-create-a-storage-service-endpoint-in-your-virtual-network) – třeba nasadí použití existující virtuální síť a vytvoření úložiště objektů blob

Další informace o plánování a kroky nasazení clusteru, najdete v článku [plánování vašeho systému vFXT Avere](avere-vfxt-deploy-plan.md) a [Přehled nasazení](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Vytvoření Avere vFXT pro Azure

Přístup k šabloně vytváření na webu Azure Portal tak, že vyhledáte Avere a výběr "Avere vFXT ARM nasazení". 

![Okno prohlížeče na webu Azure portal pomocí chléb zlomky "Nové > Marketplace > vše". V vše stránce, má pole hledání termín "avere" a druhý výsledek "Avere vFXT ARM nasazení" je označeno červeně zvýraznit ji.](media/avere-vfxt-template-choose.png)

Po přečtení podrobné informace na stránce Avere vFXT nasazení ARM, klikněte na tlačítko **vytvořit** začít. 

![Azure marketplace s první stránka zobrazující šablony nasazení](media/avere-vfxt-deploy-first.png)

Šablona je rozdělen na čtyři kroky – dva shromáždění informací stránky a ověřování a potvrzení kroky. 

* Stránce se zaměřuje na nastavení pro kontroler clusteru virtuálních počítačů. 
* Stránka dvě shromažďuje parametry pro vytvoření clusteru a přidružené prostředky, jako jsou podsítě a úložiště. 
* Stránka tři obsahuje souhrn nastavení a ověří konfiguraci. 
* Čtvrtá stránka vysvětluje softwaru podmínky a ujednání a umožňuje spuštění v procesu vytváření clusteru. 

## <a name="page-one-parameters---cluster-controller-information"></a>Stránka parametrů - informace o řadičích pro cluster

První stránka šablony nasazení shromažďuje informace o kontroleru clusteru. 

![První stránka šablony nasazení](media/avere-vfxt-deploy-1.png)

Zadejte následující informace:

* **Název kontroleru clusteru** – nastavte název kontroleru clusteru virtuálních počítačů.

* **Uživatelské jméno řadič** – zadejte uživatelské jméno kořenový řadič clusteru virtuálních počítačů. 

* **Typ ověřování** – zvolte buď heslo nebo ověření veřejného klíče SSH pro připojení ke kontroleru. Metodu veřejného klíče SSH se doporučuje; Přečtěte si [jak vytvořit a používat klíče SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) Pokud potřebujete pomoc.

* **Heslo** nebo **veřejný klíč SSH** – v závislosti na typ ověřování, který jste vybrali, je nutné zadat veřejný klíč RSA nebo heslo v další pole. Tento přihlašovací údaj se používá s dříve zadané uživatelské jméno.

* **Role ID pro vytvoření clusteru Avere** – toto pole použít k určení role řízení přístupu pro správce clusteru. Výchozí hodnota je předdefinovaná role [vlastníka](../role-based-access-control/built-in-roles.md#owner). Oprávnění vlastníka pro kontroler clusteru jsou omezeny na skupinu prostředků clusteru. 

  Je nutné použít globálně jedinečný identifikátor, který odpovídá roli. Výchozí hodnota (Vlastník) identifikátor GUID je 8e3af657 a8ff-443c-a75c-2fe8c4bcb635. K vyhledání identifikátoru GUID pro vlastní roli, použijte tento příkaz: 

  ```azurecli
  az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
  ```

* **Předplatné** – vyberte předplatné pro Avere vFXT. 

* **Skupina prostředků** – vyberte existující prázdné skupiny prostředků clusteru vFXT Avere, nebo klikněte na tlačítko Vytvořit novou. "a zadejte nový název skupiny prostředků. 

* **Umístění** – vyberte umístění pro váš cluster a prostředky Azure.

Klikněte na tlačítko **OK** po dokončení. 

> [!NOTE]
> Pokud chcete řadič clusteru mít veřejnou IP adresu, vytvořte novou virtuální síť pro cluster místo výběru existující síť. Toto nastavení je na stránce dvě.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Informace o stránce dva parametry – vFXT clusteru

Na druhé stránce šablonu nasazení umožňuje nastavit velikost clusteru, typu uzlu, velikost mezipaměti a parametry úložiště, kromě jiných nastavení. 

![Druhá stránka šablony nasazení](media/avere-vfxt-deploy-2.png)

* **Počet uzlů clusteru vFXT Avere** – zvolte počet uzlů v clusteru. Tři uzly je minimální a maximální hodnota je 12. 

* **Heslo pro správu clusteru** – vytvořit heslo pro správu clusteru. Toto heslo se použije k uživatelskému jménu ```admin``` pro přihlášení ke clusteru ovládací panely pro monitorování clusteru a ke konfiguraci nastavení.

* **Role clusteru operations Avere** – zadejte název role řízení přístupu pro uzly clusteru. Toto je vlastní roli, který byl vytvořen v kroku požadavků. 

  V příkladu je popsáno v [vytvořit role clusteru uzel přístupu](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) uloží soubor jako ```avere-operator.json``` a odpovídající název role je ```avere-operator```.

* **Název clusteru vFXT Avere** – zadejte jedinečný název clusteru. 

* **Velikost** – Tato část ukazuje typ virtuálního počítače, který se použije pro uzly clusteru. I když existuje jenom jedna možnost doporučené, **změnit velikost** odkaz otevře tabulku s podrobnostmi o tento typ instance a odkaz na cenové kalkulačky.  <!-- old: Specify the VM type to use when creating the cluster nodes.  -->

* **Velikost na jeden uzel mezipaměti** -clusteru mezipaměti se pak rozdělí mezi uzly clusteru, takže celková velikost mezipaměti ve vašem clusteru vFXT Avere bude velikost mezipaměti podle počtu uzlů vynásobenému z počtu uzlů. 

  Doporučená konfigurace, je pro uzly Standard_E32s_v3 využívat 4 TB na jeden uzel.

* **Virtuální síť** – definovat novou virtuální síť k umístění clusteru, nebo vyberte existující virtuální síť, která splňuje požadavky popsané v [plánování vašeho systému vFXT Avere](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure). 

  > [!NOTE]
  > Pokud vytvoříte novou virtuální síť, clusteru kontrolér bude mít veřejnou IP adresu, aby měli přístup ke novou privátní síť. Pokud zvolíte existující virtuální síť, kontroler cluster je nakonfigurovaný bez veřejné IP adresy. 
  > 
  > Veřejně viditelné IP adresu na adaptéru clusteru nabízí jednodušší přístup ke clusteru vFXT, ale vytvoří malý bezpečnostní riziko. 
  >  * Veřejné IP adresy na adaptéru clusteru můžete použít jako hostitele jump pro připojení ke clusteru vFXT Avere z mimo privátní podsítě.
  >  * Pokud není nastavení veřejné IP adresy na řadiči, musíte použít jiný hostitel jump, připojení k síti VPN nebo ExpressRoute pro přístup ke clusteru. Například vytvořte řadič ve virtuální síti, který už má nakonfigurované připojení k síti VPN.
  >  * Pokud vytvoříte řadič s použitím veřejné IP adresy, by měl ochranu virtuálního počítače řadiče se skupinou zabezpečení sítě. Ve výchozím nastavení vFXT Avere pro nasazení v Azure vytvoří skupinu zabezpečení sítě a omezuje příchozí přístup jenom port 22 pro řadiče s veřejnými IP adresami. Systém jde dál chránit zamčením dolů přístup ke zdrojové váš rozsah IP adres – to znamená, povolit připojení pouze z počítačů, které máte v úmyslu používat pro přístup ke clusteru.

  Nasazení šablony také nakonfiguruje novou virtuální síť s koncového bodu služby storage pro Azure Blob storage a řízení přístupu k síti uzamčený, aby se jen IP adresy z podsítě clusteru. <!-- xxx make sure this is accurate --> <!-- do I need to say that this only happens if you choose to create storage? -->

* **Podsíť** – zvolte podsíť z existující virtuální sítě, nebo vytvořte novou. 

* **Vytvoření a použití služby blob storage** – zvolte **true** vytvořit nový kontejner objektů Blob v Azure a nakonfiguruje ho jako back endové úložiště pro nový cluster vFXT Avere. Tato možnost také vytvoří nový účet úložiště ve stejné skupině prostředků jako cluster a koncový bod úložiště služby Microsoft uvnitř clusteru podsítě. 
  
  Pokud zadáte existující virtuální sítě, musí mít koncový bod služby úložiště před vytvořením clusteru. (Další informace najdete v článku [plánování vašeho systému vFXT Avere](avere-vfxt-deploy-plan.md).)

  Nastavte pole na **false** Pokud nechcete vytvořit nový kontejner. V takovém případě musíte připojit a nakonfigurovat úložiště po vytvoření clusteru. Čtení [konfigurace úložiště](avere-vfxt-add-storage.md) pokyny. 

* **(Nové) Účet úložiště** - li vytvořit nový kontejner objektů Blob v Azure, zadejte název nového účtu úložiště. 

## <a name="validation-and-purchase"></a>Ověření a nákup

Třetí straně možnost shrne konfiguraci a ověří parametry. Po ověření proběhne úspěšně, klikněte na tlačítko **OK** tlačítka budete pokračovat. 

![Třetí stránka šablony nasazení – ověření](media/avere-vfxt-deploy-3.png)

Na stránce čtyři, klikněte na tlačítko **vytvořit** tlačítko a přijměte podmínky a vytvořit vFXT Avere pro Azure cluster. 

![Čtvrtá stránka šablony nasazení – podmínky a ujednání, vytvoříte tlačítko](media/avere-vfxt-deploy-4.png)

Nasazení clusteru trvá 15 – 20 minut.

## <a name="gather-template-output"></a>Shromážděte výstup šablony

Po dokončení vytváření clusteru šablony vFXT Avere vypíše některé důležité informace o novém clusteru. 

> [!TIP]
> Nezapomeňte si zkopírovat IP adresa pro správu z výstupu šablony. Budete potřebovat tato adresa pro správu clusteru.

Jak najít tyto informace, podle následujícího postupu:

1. Přejděte do skupiny prostředků pro váš cluster kontroleru.

1. Na levé straně a klikněte na tlačítko **nasazení**a potom **šablona microsoft avere.vfxt**.

   ![Skupina prostředků s nasazením na levé straně a microsoft-avere.vfxt – šablona zobrazuje v tabulce pod názvem nasazení stránka portálu](media/avere-vfxt-outputs-deployments.png)

1. Na levé straně a klikněte na tlačítko **výstupy**. Zkopírujte hodnoty v každém z těchto polí. 

   ![Vypíše hodnoty SSHSTRING RESOURCE_GROUP, umístění, NETWORK_RESOURCE_GROUP, sítě, PODSÍTĚ, SUBNET_ID, VSERVER_IPs a MGMT_IP zobrazení stránky v polích napravo od popisků](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>Další krok

Teď, když cluster běží a znáte jeho IP adresa pro správu, můžete [připojit k nástroji Konfigurace clusteru](avere-vfxt-cluster-gui.md) povolení podpory, přidejte úložiště v případě potřeby a přizpůsobit další nastavení clusteru.
