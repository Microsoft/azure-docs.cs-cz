---
title: Nasazení avere vFXT pro Azure
description: Naučte se používat Průvodce nasazením, který je dostupný z Azure Marketplace k nasazení clusteru s avere vFXT pro Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 110fc658ffc3b72de91bbf1c9562f3efeecaf55b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342412"
---
# <a name="deploy-the-vfxt-cluster"></a>Nasazení clusteru vFXT

Tento postup vás provede použitím Průvodce nasazením dostupným z Azure Marketplace. Průvodce automaticky nasadí cluster pomocí Azure Resource Manager šablony. Až zadáte parametry ve formuláři a kliknete na **vytvořit**, Azure automaticky dokončí tyto úlohy:

* Vytvoří řadič clusteru, což je základní virtuální počítač obsahující software potřebný k nasazení a správě clusteru.
* Nastaví skupinu prostředků a infrastrukturu virtuální sítě, včetně vytváření nových elementů.
* Vytvoří virtuální počítače uzlu clusteru a nakonfiguruje je jako cluster avere.
* V případě vyžádání vytvoří nový kontejner objektů blob Azure a nakonfiguruje ho jako základní souborového clusteru.

Po provedení kroků v tomto dokumentu budete mít virtuální síť, podsíť, řadič clusteru a cluster vFXT, jak je znázorněno v následujícím diagramu. Tento diagram znázorňuje volitelný souborového Azure Blob Core, který zahrnuje nový kontejner úložiště objektů BLOB (v novém účtu úložiště, není zobrazený) a koncový bod služby pro úložiště Microsoft v podsíti.

![Diagram znázorňující tři soustředné obdélníky s komponentami clusteru avere. Vnější obdélník je označený jako skupina prostředků a obsahuje šestiúhelník s označením BLOB Storage (volitelné). Další obdélník v je označený jako "virtuální síť: 10.0.0.0/16" a neobsahuje žádné jedinečné součásti. Nejvnitřnější obdélník je označený jako podsíť: 10.0.0.0/24 a obsahuje virtuální počítač s názvem řadič clusteru, zásobník tří virtuálních počítačů s názvem vFXT Nodes (vFXT cluster) a šestiúhelník s popiskem "koncový bod služby". V rámci skupiny prostředků se nachází šipka připojující koncový bod služby (která se nachází uvnitř podsítě) a úložiště objektů BLOB (které se nachází mimo podsíť a virtuální síť). Šipka projde hranicemi podsítě a virtuální sítě.](media/avere-vfxt-deployment.png)

Než začnete používat šablonu pro vytváření, ujistěte se, že jste vyřešili tyto požadavky:  

* [Nové předplatné](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Oprávnění vlastníka předplatného](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Kvóta pro cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Koncový bod služby úložiště (Pokud je potřeba)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – vyžaduje se pro nasazení, která používají existující virtuální síť a vytváření úložiště objektů BLOB.

Další informace o krocích a plánováních nasazení clusteru najdete v tématu plánování [avere systému vFXT](avere-vfxt-deploy-plan.md) a [Přehled nasazení](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Vytvoření avere vFXT pro Azure

V Azure Portal přejděte na šablonu pro vytvoření tak, že vyhledáte avere a vyberete avere vFXT pro šablonu Azure ARM.

![V okně prohlížeče se zobrazí Azure Portal s názvem "New > Marketplace > vše". V poli vše na stránce vše má vyhledávací pole výraz "avere" a druhý výsledek, "avere vFXT pro šablonu Azure ARM" se zvýrazní červeně.](media/avere-vfxt-template-choose.png)

Po přečtení podrobností na stránce šablony avere vFXT pro Azure ARM klikněte na tlačítko **vytvořit** a začněte.

![Azure Marketplace s první stránkou šablony nasazení](media/avere-vfxt-deploy-first.png)

Tato šablona je rozdělená do čtyř kroků – dvou stránek shromažďování informací, plus ověřovací a potvrzovací kroky.

* Stránka jedna shromáždí nastavení pro virtuální počítač řadiče clusteru.
* Stránka dvě shromáždí parametry pro vytvoření clusteru a další prostředky, jako jsou podsítě a úložiště.
* Stránka tři shrnuje vaše volby a ověří konfiguraci.
* Stránka čtyři vysvětluje podmínky a ujednání o softwaru a umožňuje spuštění procesu vytváření clusteru.

## <a name="page-one-parameters---cluster-controller-information"></a>Jedna z parametrů stránky – informace o řadiči clusteru

První stránka šablony nasazení se zaměřuje na řadič clusteru.

![První stránka šablony nasazení](media/avere-vfxt-deploy-1.png)

Zadejte následující informace:

* **Název řadiče clusteru** – nastavte název pro virtuální počítač řadiče clusteru.

* **Uživatelské jméno kontroleru** – nastaví kořenové uživatelské jméno pro virtuální počítač řadiče clusteru.

* **Typ ověřování** – pro připojení k řadiči vyberte buď možnost heslo, nebo ověření veřejného klíče SSH. Doporučuje se metoda veřejného klíče SSH; Pokud potřebujete podporu, přečtěte si, [jak vytvořit a používat klíče SSH](../virtual-machines/linux/ssh-from-windows.md) .

* **Heslo** nebo **veřejný klíč SSH** – v závislosti na typu ověřování, který jste zvolili, musíte zadat veřejný klíč RSA nebo heslo do následujících polí. Toto pověření se používá společně s uživatelským jménem, které jste zadali dříve.

* **Předplatné** – vyberte předplatné pro avere vFXT.

* **Skupina prostředků** – vyberte existující prázdnou skupinu prostředků pro cluster avere vFXT, nebo klikněte na vytvořit nový a zadejte název nové skupiny prostředků.

* **Umístění** – vyberte umístění Azure pro váš cluster a prostředky.

Po dokončení klikněte na **OK** .

> [!NOTE]
> Pokud chcete, aby měl řadič clusteru veřejnou IP adresu, vytvořte místo výběru existující sítě novou virtuální síť pro cluster. Toto nastavení je na druhé stránce.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Stránka – dva parametry – informace o clusteru vFXT

Druhá stránka šablony nasazení vám umožní nastavit velikost clusteru, typ uzlu, velikost mezipaměti a parametry úložiště mezi další nastavení.

![Druhá stránka šablony nasazení](media/avere-vfxt-deploy-2.png)

* **Počet uzlů clusteru avere vFXT** – vyberte počet uzlů v clusteru. Minimum je tři uzly a maximum je 12.

* **Heslo pro správu clusteru** – vytvořte heslo pro správu clusteru. Toto heslo se používá s uživatelským jménem ```admin``` pro přihlášení k ovládacímu panelu clusteru, kde můžete monitorovat cluster a konfigurovat nastavení clusteru.

* **Název clusteru avere vFXT** – udělte clusteru jedinečný název.

* **Velikost** – v této části se zobrazuje typ virtuálního počítače, který se bude používat pro uzly clusteru. I když je k dispozici jenom jedna z doporučených možností, odkaz **Změna velikosti** otevře tabulku s podrobnostmi o tomto typu instance a odkazem na cenovou kalkulačku.

* **Velikost mezipaměti na uzel** – mezipaměť clusteru se rozprostře mezi uzly clusteru, takže celková velikost mezipaměti v clusteru avere vFXT bude tato velikost vynásobená počtem uzlů.

  Doporučená konfigurace: pro uzly Standard_E32s_v3 použijte 4 TB na uzel.

* **Virtuální síť** – Definujte novou virtuální síť, která bude obsahovat cluster, nebo vyberte existující síť, která splňuje požadavky popsané v tématu [plánování avere systému vFXT](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Pokud vytvoříte novou virtuální síť, bude mít řadič clusteru veřejnou IP adresu, abyste mohli získat přístup k nové privátní síti. Pokud zvolíte existující virtuální síť, je řadič clusteru nakonfigurovaný bez veřejné IP adresy.
  >
  > Veřejně viditelná IP adresa na řadiči clusteru poskytuje snazší přístup ke clusteru vFXT, ale vytváří malé bezpečnostní riziko.
  >* Veřejná IP adresa na řadiči clusteru vám umožní použít ho jako skokový hostitel pro připojení ke clusteru avere vFXT z oblasti mimo soukromou podsíť.
  >* Pokud v řadiči nemáte veřejnou IP adresu, budete pro přístup ke clusteru potřebovat jiného hostitele, připojení k síti VPN nebo ExpressRoute. Například použijte existující virtuální síť, která už má nakonfigurované připojení k síti VPN.
  >* Pokud vytvoříte kontrolér s veřejnou IP adresou, měli byste virtuální počítač kontroleru ochránit pomocí skupiny zabezpečení sítě. Ve výchozím nastavení vytvoří avere vFXT pro nasazení Azure skupinu zabezpečení sítě, která omezuje příchozí přístup jenom na port 22 pro řadiče s veřejnými IP adresami. Systém můžete dále chránit tím, že zamknete přístup k vaší škále zdrojových adres IP – to znamená jenom připojení z počítačů, které chcete používat pro přístup do clusteru.

  Nová virtuální síť je taky nakonfigurovaná s koncovým bodem služby úložiště pro úložiště objektů BLOB v Azure a s uzamčeným řízením přístupu k síti, aby povolovala jenom IP adresy z podsítě clusteru.

* **Podsíť** – vyberte podsíť nebo vytvořte novou.

* **Vytvoření a použití úložiště objektů BLOB** – Pokud chcete vytvořit nový kontejner objektů blob Azure a nakonfigurovat ho jako back-endové úložiště pro nový cluster avere vFXT, vyberte **true** . Tato možnost také vytvoří nový účet úložiště ve skupině prostředků clusteru a vytvoří koncový bod služby Microsoft Storage v podsíti clusteru.
  
  Pokud zadáte existující virtuální síť, musí mít před vytvořením clusteru koncový bod služby úložiště. (Další informace najdete v tématu [plánování avere systému vFXT](avere-vfxt-deploy-plan.md).)

  Pokud nechcete vytvořit nový kontejner, nastavte toto pole na **hodnotu false** . V takovém případě musíte po vytvoření clusteru připojit a nakonfigurovat úložiště. Pokyny najdete v tématu [Konfigurace úložiště](avere-vfxt-add-storage.md) .

* **Účet úložiště (nový)** – při vytváření nového kontejneru objektů BLOB v Azure zadejte název nového účtu úložiště.

## <a name="validation-and-purchase"></a>Ověření a nákup

Stránka tři shrnuje konfiguraci a ověří parametry. Po úspěšném ověření zaškrtněte souhrn a klikněte na tlačítko **OK** .

> [!TIP]
> Nastavení vytváření tohoto clusteru můžete uložit kliknutím na odkaz **Stáhnout šablonu a parametry** vedle tlačítka **OK** . Tyto informace mohou být užitečné, pokud potřebujete vytvořit podobný cluster později, například k vytvoření náhradního clusteru ve scénáři zotavení po havárii. (Další informace najdete v tématu [pokyny pro zotavení po havárii](disaster-recovery.md) .)

![Třetí stránka šablony nasazení – ověření](media/avere-vfxt-deploy-3.png)

Stránka čtyři obsahuje pravidla použití a odkazy na informace o ochraně osobních údajů a cenách.

Zadejte všechny chybějící kontaktní informace a potom kliknutím na tlačítko **vytvořit** přijměte podmínky a vytvořte cluster avere VFXT pro Azure.

![Čtvrtá stránka šablony nasazení – podmínky a ujednání, tlačítko vytvořit](media/avere-vfxt-deploy-4.png)

Nasazení clusteru trvá 15-20 minut.

## <a name="gather-template-output"></a>Shromáždit výstup šablony

Když šablona avere vFXT dokončí vytváření clusteru, vypíše důležité informace o novém clusteru.

> [!TIP]
> Nezapomeňte zkopírovat **IP adresu pro správu** z výstupu šablony. Tuto adresu potřebujete ke správě clusteru.

Vyhledání informací:

1. Přejít do skupiny prostředků pro váš řadič clusteru.

1. Na levé straně klikněte na **nasazení** a pak na **Microsoft-avere. vfxt-Template**.

   ![Stránka portálu skupiny prostředků s nasazeními vybranými vlevo a Microsoft-avere. vfxt-Template, která se zobrazuje v tabulce pod názvem nasazení](media/avere-vfxt-outputs-deployments.png)

1. Na levé straně klikněte na možnost **výstupy**. Zkopírujte hodnoty do každého pole.

   ![Vytvoří výstup stránky, která zobrazuje SSHSTRING, RESOURCE_GROUP, umístění, NETWORK_RESOURCE_GROUP, síť, podsíť, SUBNET_ID, VSERVER_IPs a MGMT_IP hodnoty v polích napravo od popisků.](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Další kroky

Teď, když je cluster spuštěný a znáte jeho IP adresu pro správu, se [připojte k nástroji pro konfiguraci clusteru](avere-vfxt-cluster-gui.md).

Použijte konfigurační rozhraní k přizpůsobení clusteru, včetně těchto úloh nastavení:

* [Povolit podporu](avere-vfxt-enable-support.md)
* [Přidat úložiště](avere-vfxt-add-storage.md) (v případě potřeby)