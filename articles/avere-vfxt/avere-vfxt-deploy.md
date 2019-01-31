---
title: Nasazení Avere vFXT pro Azure
description: Kroky k nasazení clusteru vFXT Avere v Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: da329b5c50fe7c39d9773743b40c2f990e298963
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296371"
---
# <a name="deploy-the-vfxt-cluster"></a>Nasazení clusteru vFXT

Tento postup vás provede pomocí Průvodce nasazení k dispozici z webu Azure Marketplace. Průvodce automaticky nasadí do clusteru s použitím šablony Azure Resource Manageru. Po zadání parametrů ve formuláři a klikněte na tlačítko **vytvořit**, Azure automaticky provede tyto kroky: 

* Vytvoření kontroleru clusteru, což je základní virtuální počítač, který obsahuje software potřebný k nasazení a správě clusteru.
* Nastavte skupinu prostředků a virtuální síťové infrastruktury, včetně vytváření nových elementů v případě potřeby.
* Vytvořit virtuální počítače uzlu clusteru a lze nakonfigurovat jako Avere cluster.
* Pokud o to požádá, vytvořit nový kontejner objektů Blob v Azure a nakonfiguruje ho jako filtr core clusteru.

Po provedení kroků v tomto dokumentu, budete mít virtuální síť, podsíť, kontroleru a vFXT clusteru, jak je znázorněno v následujícím diagramu:

![Diagram znázorňující virtuální síť obsahuje volitelné blob storage a podsítě obsahující tři seskupené virtuálních počítačů označené jako clusterové uzly/vFXT vFXT a jeden kontroler s popiskem clusteru virtuálních počítačů](media/avere-vfxt-deployment.png)

Po vytvoření clusteru, měli byste [vytvořit koncový bod úložiště](#create-a-storage-endpoint-if-using-azure-blob) ve službě virtual network při použití služby Blob storage. 

Před použitím šablonu pro vytvoření, ujistěte se, že se že odstranily tyto požadavky:  

1. [Nové předplatné](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Oprávnění vlastníka předplatného](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Kvóta pro vFXT clusteru](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Vlastní přístup role](avere-vfxt-prereqs.md#create-access-roles) -musí vytvořit roli řízení přístupu na základě role přiřadit uzlům clusteru. Máte možnost také vytvářet vlastní přístup role pro správce clusteru, ale většina uživatelů bude trvat výchozí role vlastníka, který dává oprávnění kontroleru odpovídající vlastníkovi skupiny prostředků. Čtení [předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md#owner) další podrobnosti.

Další informace o plánování a kroky nasazení clusteru, najdete v článku [plánování vašeho systému vFXT Avere](avere-vfxt-deploy-plan.md) a [Přehled nasazení](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Vytvoření Avere vFXT pro Azure

Hledání Avere a výběrem "Avere vFXT pro nasazení Azure" přístup k vytvoření šablony na webu Azure Portal. <!-- xxx update if that name changes xxx --> 

<!-- **[XXX need production image of template deploy in search and/or entry page of template deploy XXX]** -->

Klikněte na tlačítko **vytvořit** začít. 

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

* **Skupina prostředků** – vyberte skupinu prostředků clusteru vFXT Avere, nebo klikněte na tlačítko Vytvořit novou. "a zadejte nový název skupiny prostředků. 

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

* **Velikost** -zadat typ virtuálního počítače pro použití při vytváření uzly clusteru. 

* **Velikost na jeden uzel mezipaměti** -clusteru mezipaměti se pak rozdělí mezi uzly clusteru, takže celková velikost mezipaměti ve vašem clusteru vFXT Avere bude velikost mezipaměti podle počtu uzlů vynásobenému z počtu uzlů. 

  Doporučená konfigurace je používat 1 TB na jeden uzel, pokud pomocí Standard_D16s_v3 uzly clusteru a použít 4 TB na jeden uzel, pokud pomocí Standard_E32s_v3 uzlů.

* **Virtuální síť** – vyberte existující virtuální síť k umístění clusteru nebo definovat novou virtuální síť vytvořit. 

  > [!NOTE]
  > Pokud vytvoříte novou virtuální síť, clusteru kontrolér bude mít veřejnou IP adresu, aby měli přístup ke novou privátní síť. Pokud zvolíte existující virtuální síť, kontroler cluster je nakonfigurovaný bez veřejné IP adresy. 
  > 
  > Veřejně viditelné IP adresu na adaptéru clusteru nabízí jednodušší přístup ke clusteru vFXT, ale vytvoří malý bezpečnostní riziko. 
  >  * Veřejné IP adresy na adaptéru clusteru můžete použít jako hostitele jump pro připojení ke clusteru vFXT Avere z mimo privátní podsítě.
  >  * Pokud není nastavení veřejné IP adresy na řadiči, musíte použít jiný hostitel jump, připojení k síti VPN nebo ExpressRoute pro přístup ke clusteru. Například vytvořte řadič ve virtuální síti, který už má nakonfigurované připojení k síti VPN.
  >  * Pokud vytvoříte řadič s použitím veřejné IP adresy, by měl ochranu virtuálního počítače řadiče se skupinou zabezpečení sítě. Ve výchozím nastavení vFXT Avere pro nasazení v Azure vytvoří skupinu zabezpečení sítě a omezuje příchozí přístup jenom port 22 pro řadiče s veřejnými IP adresami. Systém jde dál chránit zamčením dolů přístup ke zdrojové váš rozsah IP adres – to znamená, povolit připojení pouze z počítačů, které máte v úmyslu používat pro přístup ke clusteru.

* **Podsíť** – zvolte podsíť z existující virtuální sítě, nebo vytvořte novou. 

* **Používání úložiště blob** – zvolte, jestli chcete vytvořit nový kontejner objektů Blob v Azure a nakonfiguruje ho jako back endové úložiště pro nový cluster vFXT Avere. Pokud budete chtít vytvořit nový kontejner, musíte zadat účet úložiště pro tento kontejner. Pokud se rozhodnete vytvořit nový kontejner objektů blob, je nutné připojit úložiště po vytvoření clusteru (čtení [konfigurace úložiště](avere-vfxt-add-storage.md) pokyny). Nastavte pole na **false** Pokud nechcete vytvořit nový kontejner.

* **Účet úložiště** - li vytvořit nový kontejner objektů Blob v Azure, zadejte název účtu úložiště. Účet úložiště musí být standard pro obecné účely V2 účtu nakonfigurovanému pro místně redundantní úložiště a horká vrstva přístupu. [Konfigurace úložiště](avere-vfxt-add-storage.md#azure-storage-cloud-core-filer) článku obsahuje další podrobnosti o požadavcích na účet úložiště.

## <a name="validation-and-purchase"></a>Ověření a nákup

Stránka tři poskytuje souhrnné informace o konfiguraci a ověří parametry. Po ověření proběhne úspěšně, klikněte na tlačítko **OK** tlačítka budete pokračovat. 

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


## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Vytvoření koncového bodu úložiště (Pokud se používá objektů Blob v Azure)

Pokud používáte úložiště objektů Blob v Azure pro váš back endovým datům úložiště, měli byste vytvořit koncový bod služby úložiště ve službě virtual network. To [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) udržuje objektů Blob v Azure provoz místní namísto směrování přes internet.

1. Z portálu, klikněte na tlačítko **virtuální sítě** na levé straně.
1. Vyberte virtuální síť pro kontrolér. 
1. Klikněte na tlačítko **koncové body služby** na levé straně.
1. Klikněte na tlačítko **přidat** v horní části.
1. Nechte službu jako ``Microsoft.Storage`` a zvolte podsíť kontroleru.
1. V dolní části, klikněte na tlačítko **přidat**.

  ![Azure portal – snímek obrazovky s poznámkami postup vytvoření koncového bodu služby](media/avere-vfxt-service-endpoint.png)

## <a name="next-step"></a>Další krok

Teď, když cluster běží a znáte jeho IP adresa pro správu, můžete [připojit k nástroji Konfigurace clusteru](avere-vfxt-cluster-gui.md) povolení podpory, přidejte úložiště v případě potřeby a přizpůsobit další nastavení clusteru.
