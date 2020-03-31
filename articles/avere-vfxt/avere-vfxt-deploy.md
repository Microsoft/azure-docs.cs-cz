---
title: Nasazení Avere vFXT pro Azure
description: Postup nasazení clusteru Avere vFXT v Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: e70d1dfebcf25ee8f4e90a062cee6dd72a663e02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252594"
---
# <a name="deploy-the-vfxt-cluster"></a>Nasazení clusteru vFXT

Tento postup vás provede pomocí průvodce nasazením, který je k dispozici na Azure Marketplace. Průvodce automaticky nasazuje cluster pomocí šablony Správce prostředků Azure. Po zadání parametrů ve formuláři a klepnutí na tlačítko **Vytvořit**Azure automaticky dokončí tyto úkoly:

* Vytvoří řadič clusteru, což je základní virtuální počítače, který obsahuje software potřebný k nasazení a správě clusteru.
* Nastaví skupinu prostředků a infrastrukturu virtuální sítě, včetně vytváření nových prvků.
* Vytvoří virtuální počítače uzlů clusteru a nakonfiguruje je jako cluster Avere.
* Pokud je to požadováno, vytvoří nový kontejner objektů Blob Azure a nakonfiguruje jej jako filer jádra clusteru.

Po provedení pokynů v tomto dokumentu budete mít virtuální síť, podsíť, řadič clusteru a cluster vFXT, jak je znázorněno na následujícím diagramu. Tento diagram znázorňuje volitelný základní filer Azure blob, který zahrnuje nový kontejner úložiště objektů Blob (v novém účtu úložiště, není zobrazen) a koncový bod služby pro úložiště Microsoftu uvnitř podsítě.

![diagram zobrazující tři soustředné obdélníky s komponentami clusteru Avere. Vnější obdélník je označen jako Skupina prostředků a obsahuje šestiúhelník s označením Úložiště objektů blob (volitelné)". Další obdélník v je označen 'Virtuální síť: 10.0.0.0/16' a neobsahuje žádné jedinečné součásti. Nejvnitřnější obdélník je označen "Podsíť:10.0.0.0/24" a obsahuje virtuální ho virtuálního zařízení s označením "Řadič clusteru", stoh tří virtuálních disponů označených "vFXT uzly (vFXT cluster)" a šestiúhelník označený "Koncový bod služby". Existuje šipka spojující koncový bod služby (který je uvnitř podsítě) a úložiště objektů blob (který je mimo podsíť a virtuální síť ve skupině prostředků). Šipka prochází hranicemi podsítě a virtuální sítě.](media/avere-vfxt-deployment.png)

Před použitím šablony pro vytvoření se ujistěte, že jste vyřešili tyto požadavky:  

* [Nové předplatné](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Oprávnění vlastníka předplatného](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Kvóta pro cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Koncový bod služby úložiště (v případě potřeby)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – vyžadováno pro nasazení, která používají existující virtuální síť a vytvářejí úložiště objektů blob

Další informace o krocích nasazení clusteru a plánování najdete v [článku Plánování systému Avere vFXT](avere-vfxt-deploy-plan.md) a [přehledu nasazení](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Vytvoření avere vFXT pro Azure

Přístup k šabloně vytvoření na webu Azure portal vyhledáním Avere a výběrem možnosti "Avere vFXT for Azure ARM Template".

![Okno prohlížeče zobrazující portál Azure s drobky chleba "New > Marketplace > Všechno". Na stránce Vše má vyhledávací pole termín "avere" a druhý výsledek "Avere vFXT for Azure ARM Template" je nastíněn červeně, aby se zvýraznil.](media/avere-vfxt-template-choose.png)

Po přečtení podrobností na stránce Avere vFXT for Azure ARM Template klikněte na jeho tlačítko **Vytvořit** a začněte.

![Azure Marketplace s první stránkou šablony nasazení zobrazující](media/avere-vfxt-deploy-first.png)

Šablona je rozdělena do čtyř kroků - dvě stránky pro shromažďování informací a kroky ověření a potvrzení.

* Stránka jedna shromažďuje nastavení pro virtuální počítače řadiče clusteru.
* Stránka dvě shromažďuje parametry pro vytvoření clusteru a další prostředky, jako jsou podsítě a úložiště.
* Stránka tři shrnuje vaše volby a ověřuje konfiguraci.
* Stránka čtyři vysvětluje smluvní podmínky softwaru a umožňuje spustit proces vytváření clusteru.

## <a name="page-one-parameters---cluster-controller-information"></a>Parametry stránky 1 – informace o řadiči clusteru

První stránka šablony nasazení se zaměřuje na řadič clusteru.

![První stránka šablony nasazení](media/avere-vfxt-deploy-1.png)

Vyplňte následující informace:

* **Název řadiče clusteru** – Nastavte název virtuálního zařízení řadiče clusteru.

* **Uživatelské jméno řadiče** – nastavte kořenové uživatelské jméno pro virtuální ms řadiče clusteru.

* **Typ ověřování** – Pro připojení k řadiči zvolte ověřování pomocí hesla nebo ssh veřejného klíče. Doporučuje se metoda veřejného klíče SSH; přečtěte si [jak vytvořit a používat klávesy SSH,](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) pokud potřebujete pomoc.

* **Heslo** nebo **veřejný klíč SSH** – V závislosti na vybraném typu ověřování musíte zadat veřejný klíč RSA nebo heslo v následujících polích. Toto pověření se používá s uživatelské jméno zapředpokladu dříve.

* **Předplatné** – vyberte předplatné avere vFXT.

* **Skupina prostředků** – Vyberte existující prázdnou skupinu prostředků pro cluster Avere vFXT nebo klikněte na "Vytvořit nový" a zadejte nový název skupiny prostředků.

* **Umístění** – vyberte umístění Azure pro váš cluster a prostředky.

Po dokončení klepněte na **tlačítko OK.**

> [!NOTE]
> Pokud chcete, aby řadič clusteru měl veřejnou IP adresu, vytvořte pro cluster novou virtuální síť namísto výběru existující sítě. Toto nastavení je na straně dvě.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Stránka dva parametry - informace o clusteru vFXT

Druhá stránka šablony nasazení umožňuje mimo jiné nastavit velikost clusteru, typ uzlu, velikost mezipaměti a parametry úložiště.

![Druhá stránka šablony nasazení](media/avere-vfxt-deploy-2.png)

* **Počet uzlů clusteru Avere vFXT** – zvolte počet uzlů v clusteru. Minimální je tři uzly a maximum je dvanáct.

* **Heslo pro správu clusteru** – Vytvořte heslo pro správu clusteru. Toto heslo se používá ```admin``` s uživatelským jménem pro přihlášení k ovládacímu panelu clusteru, kde můžete sledovat cluster a konfigurovat nastavení clusteru.

* **Název clusteru Avere vFXT** - Přiznejte clusteru jedinečný název.

* **Velikost** – tato část zobrazuje typ virtuálního počítače, který se bude používat pro uzly clusteru. Přestože existuje pouze jedna doporučená možnost, odkaz **Změnit velikost** otevře tabulku s podrobnostmi o tomto typu instance a odkazem na cenovou kalkulačku.

* **Velikost mezipaměti na uzel** – mezipaměť clusteru je rozložena mezi uzly clusteru, takže celková velikost mezipaměti v clusteru Avere vFXT bude tato velikost vynásobená počtem uzlů.

  Doporučená konfigurace: Pro uzly Standard_E32s_v3 použijte 4 TB na uzel.

* **Virtuální síť** – Definujte novou virtuální síť pro použití clusteru nebo vyberte existující síť, která splňuje požadavky popsané v [části Naplánujte si systém Avere vFXT](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Pokud vytvoříte novou virtuální síť, bude mít řadič clusteru veřejnou IP adresu, abyste měli přístup k nové privátní síti. Pokud zvolíte existující virtuální síť, řadič clusteru se nakonfiguruje bez veřejné IP adresy.
  >
  > Veřejně viditelná adresa IP na řadiči clusteru poskytuje snadnější přístup ke clusteru vFXT, ale vytváří malé bezpečnostní riziko.
  >* Veřejná IP adresa na řadiči clusteru umožňuje použít jako hostitele skoku pro připojení ke clusteru Avere vFXT mimo privátní podsíť.
  >* Pokud nemáte veřejnou IP adresu na řadiči, budete potřebovat další skok hostitele, připojení VPN nebo ExpressRoute pro přístup ke clusteru. Použijte například existující virtuální síť, která již má nakonfigurované připojení VPN.
  >* Pokud vytvoříte řadič s veřejnou IP adresou, měli byste virtuální ho virtuálního zařízení řadiče chránit pomocí skupiny zabezpečení sítě. Ve výchozím nastavení vytvoří nasazení Avere vFXT pro Azure skupinu zabezpečení sítě, která omezuje příchozí přístup pouze na port 22 pro řadiče s veřejnými IP adresami. Systém můžete dále chránit uzamčením přístupu k rozsahu zdrojových adres IP – to znamená povolit pouze připojení z počítačů, které chcete použít pro přístup ke clusteru.

  Nová virtuální síť je také nakonfigurovaná s koncovým bodem služby úložiště pro úložiště objektů Blob Azure a s uzamčeným řízením přístupu k síti, aby bylo možné povolit pouze IP adresy z podsítě clusteru.

* **Podsíť** - Zvolte podsíť nebo vytvořte novou.

* **Vytvoření a použití úložiště objektů blob** – zvolte **true** k vytvoření nového kontejneru objektů Blob Azure a nakonfigurujte ho jako back-endové úložiště pro nový cluster Avere vFXT. Tato možnost také vytvoří nový účet úložiště ve skupině prostředků clusteru a vytvoří koncový bod služby úložiště společnosti Microsoft uvnitř podsítě clusteru.
  
  Pokud dodáte existující virtuální síť, musí mít koncový bod služby úložiště před vytvořením clusteru. (Další informace naleznete v [například Naplánujte si systém Avere vFXT](avere-vfxt-deploy-plan.md).)

  Pokud nechcete vytvořit nový kontejner, nastavte toto pole na **hodnotu false.** V takovém případě je nutné připojit a nakonfigurovat úložiště po vytvoření clusteru. Přečtěte [si článek Konfigurace úložiště](avere-vfxt-add-storage.md) pro pokyny.

* **(Nový) účet úložiště** – pokud vytváříte nový kontejner objektů Blob Azure, zadejte název pro nový účet úložiště.

## <a name="validation-and-purchase"></a>Ověření a nákup

Stránka tři shrnuje konfiguraci a ověřuje parametry. Po úspěšném ověření zkontrolujte souhrn a klikněte na tlačítko **OK.**

> [!TIP]
> Nastavení vytvoření tohoto clusteru můžete uložit kliknutím na odkaz **Stáhnout šablonu a parametry** vedle tlačítka **OK.** Tyto informace mohou být užitečné, pokud potřebujete vytvořit podobný cluster později – například k vytvoření náhradního clusteru ve scénáři zotavení po havárii. (Další informace naleznete v pokynech pro zotavení po [havárii.)](disaster-recovery.md)

![Třetí stránka šablony nasazení – ověření](media/avere-vfxt-deploy-3.png)

Stránka čtyři obsahuje podmínky použití a odkazy na informace o ochraně osobních údajů a cenách.

Zadejte všechny chybějící kontaktní informace a kliknutím na tlačítko **Vytvořit** přijměte podmínky a vytvořte cluster Avere vFXT pro Azure.

![Čtvrtá stránka šablony nasazení - podmínky, tlačítko Vytvořit](media/avere-vfxt-deploy-4.png)

Nasazení clusteru trvá 15-20 minut.

## <a name="gather-template-output"></a>Shromáždit výstup šablony

Když šablona Avere vFXT dokončí vytvoření clusteru, výstupy důležité informace o novém clusteru.

> [!TIP]
> Ujistěte se, že zkopírujte **adresu IP pro správu** z výstupu šablony. Tuto adresu potřebujete ke správě clusteru.

Chcete-li najít informace:

1. Přejděte do skupiny prostředků pro řadič clusteru.

1. Na levé straně klepněte na **položku Nasazení**a potom na **položku Microsoft-avere.vfxt -template**.

   ![Stránka portálu skupiny prostředků s možností nasazení na levé straně a šablonou Microsoft-avere.vfxt v tabulce v části Název nasazení](media/avere-vfxt-outputs-deployments.png)

1. Na levé straně klikněte na **Výstupy**. Zkopírujte hodnoty v každém poli.

   ![Stránka výstupů zobrazující hodnoty SSHSTRING, RESOURCE_GROUP, UMÍSTĚNÍ, NETWORK_RESOURCE_GROUP, SÍŤ, PODSÍŤ, SUBNET_ID, VSERVER_IPs a MGMT_IP v polích vpravo od popisků](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Další kroky

Nyní, když je cluster spuštěn a znáte jeho adresu IP pro správu, [připojte se ke konfiguračnímu nástroji clusteru](avere-vfxt-cluster-gui.md).

Pomocí konfiguračního rozhraní můžete cluster upravili, včetně těchto úloh nastavení:

* [Povolit podporu](avere-vfxt-enable-support.md)
* [Přidání úložiště](avere-vfxt-add-storage.md) (v případě potřeby)
