---
title: Kurz nasazení do existující virtuální sítě pomocí azure CLI – Azure Dedicated HSM | Dokumenty společnosti Microsoft
description: Kurz, který ukazuje, jak nasadit vyhrazený modul hardwarového zabezpečení pomocí zaúmocení uživatele do existující virtuální sítě
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 76b7a97a5be5e7952b0ac11d93bd68656ff8f1ec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79454308"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Kurz: Nasazení objektových síťových serverů do existující virtuální sítě pomocí cli

Vyhrazený modul hardwarového zabezpečení Azure poskytuje fyzické zařízení pro použití jediným zákazníkem s úplnou kontrolou správy a plnou odpovědností za správu. Použití fyzických zařízení vytváří potřebu společnosti Microsoft řídit přidělování zařízení, aby bylo zajištěno efektivní správě kapacity. V důsledku toho v rámci předplatného Azure vyhrazené služby hardwarového zabezpečení obvykle nebude viditelné pro zřizování prostředků. Každý zákazník Azure, který vyžaduje přístup ke službě vyhrazeného modulu hardwarového zabezpečení, musí nejprve kontaktovat svého vedoucího účtu Microsoft a požádat o registraci vyhrazené služby hardwarového zabezpečení. Pouze po úspěšném dokončení tohoto procesu bude možné zřizování. 

Tento kurz ukazuje typický proces zřizování, kde:

- Zákazník už má virtuální síť.
- Mají virtuální stroj
- Je třeba přidat prostředky hsm do existujícího prostředí.

Typická architektura nasazení s vysokou dostupností ve více oblastech může vypadat takto:

![Nasazení ve více oblastech](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Tento kurz se zaměřuje na dvojici hsmů a požadované ExpressRoute Gateway (viz Podsíť 1 výše) jsou integrovány do existující virtuální sítě (viz VNET 1 výše).  Všechny ostatní prostředky jsou standardní prostředky Azure. Stejný proces integrace lze použít pro hmenové soubory v podsíti 4 na virtuální síti 3 výše.

## <a name="prerequisites"></a>Požadavky

Vyhrazený modul hardwarového zabezpečení Azure není momentálně dostupný na webu Azure Portal. Veškerá interakce se službou bude prostřednictvím příkazového řádku nebo pomocí prostředí PowerShell. Tento kurz bude používat rozhraní příkazového řádku (CLI) v prostředí Azure Cloud Shell. Pokud s azure CLI tečujete novým způsobem, postupujte podle pokynů začínáme tady: [Azure CLI 2.0 Začínáme](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Předpoklady:

- Dokončili jste proces registrace modulu hardwarového zabezpečení Azure Dedicated
- Byli jste schváleni pro použití služby. Pokud ne, obraťte se na zástupce účtu Microsoft pro podrobnosti.
- Vytvořili jste skupinu prostředků pro tyto prostředky a nové nasazené v tomto kurzu se k této skupině připojí.
- Už jste vytvořili potřebnou virtuální síť, podsíť a virtuální počítače podle výše uvedeného diagramu a nyní chcete integrovat 2 soubory hesm do tohoto nasazení.

Všechny níže uvedené pokyny předpokládají, že jste již přešli na portál\>\_Azure a otevřeli jste cloudové prostředí (vyberte " " směrem k pravému hornímu rohu portálu).

## <a name="provisioning-a-dedicated-hsm"></a>Zřizování vyhrazeného modulu hardwarového zabezpečení

Zřizování hsmů a jejich integrace do existující virtuální sítě prostřednictvím brány ExpressRoute bude ověřena pomocí ssh. Toto ověření pomáhá zajistit dosažitelnost a základní dostupnost zařízení HSM pro další aktivity konfigurace. Následující příkazy budou používat šablonu Azure Resource Manager k vytvoření prostředků hsm a přidružených síťových prostředků.

### <a name="validating-feature-registration"></a>Ověření registrace funkce

Jak bylo uvedeno výše, jakákoli zřizovací činnost vyžaduje, aby byla vyhrazená služba hardwarového zabezpečení zaregistrována pro vaše předplatné. Chcete-li to ověřit, spusťte následující příkazy v cloudovém prostředí portálu Azure Portal.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Následující příkaz ověřuje síťové funkce požadované pro vyhrazené služby modulu hardwarového zabezpečení.

```azurecli
az feature show \
   --namespace Microsoft.Network \
   --name AllowBaremetalServers
```

Oba příkazy by měly vrátit stav "Registrováno" (jak je znázorněno níže). Pokud příkazy nevrátí "Registrováno", musíte se zaregistrovat pro tuto službu, obraťte se na zástupce účtu Microsoft.

![stav předplatného](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Vytváření prostředků hsm

Objekt zabezpečení je zřízen do virtuální sítě zákazníků, takže jsou vyžadovány virtuální sítě a podsítě. Závislost pro hsm povolit komunikaci mezi virtuální sítí a fyzické zařízení je ExpressRoute gateway a nakonec virtuální počítač je nutné pro přístup k zařízení HSM pomocí klientského softwaru Gemalto. Tyto prostředky byly shromážděny do souboru šablony s odpovídajícím souborem parametrů pro snadné použití. Soubory jsou k dispozici tak, HSMrequest@Microsoft.comže kontaktujete společnost Microsoft přímo jako .

Jakmile budete mít soubory, musíte upravit soubor parametrů, chcete-li vložit upřednostňované názvy prostředků. Upravit řádky s "hodnotou": "".

- `namingInfix`Předpona pro názvy prostředků hsm
- `ExistingVirtualNetworkName`Název virtuální sítě používané pro servery zabezpečení hesel
- `DedicatedHsmResourceName1`Název prostředku HSM v razítku datového centra 1
- `DedicatedHsmResourceName2`Název prostředku HSM v razítku datového centra 2
- `hsmSubnetRange`Rozsah adres IP podsítě pro hmenové položky
- `ERSubnetRange`Rozsah ip adres podsítě pro bránu virtuální sítě

Příklad těchto změn je následující:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

Přidružený soubor šablony Azure Resource Manager vytvoří 6 prostředků s těmito informacemi:

- Podsíť pro hsm v zadané virtuální síti
- Podsíť pro bránu virtuální sítě
- Brána virtuální sítě, která připojuje virtuální síť k zařízením hsm
- Veřejná IP adresa brány
- HSM v razítku 1
- HSM na razítku 2

Jakmile jsou nastaveny hodnoty parametrů, je potřeba soubory nahrát do sdílené složky cloudového prostředí Azure Portal pro použití. Na webu Azure Portal\>\_klikněte na symbol cloudshellu vpravo nahoře a tím se spodní část obrazovky stane prostředím příkazů. Možnosti pro toto jsou BASH a PowerShell a měli byste vybrat BASH, pokud již není nastavena.

Příkaz shell má upload / download možnost na panelu nástrojů a měli byste vybrat to nahrát šablony a parametr soubory do sdílené složky:

![sdílení souborů](media/tutorial-deploy-hsm-cli/file-share.png)

Po nahrání souborů jste připraveni vytvořit prostředky. Před vytvořením nových prostředků hsm existují některé nezbytné prostředky, které byste měli zajistit, aby byly na místě. Musíte mít virtuální síť s rozsahy podsítí pro výpočetní prostředky, hsm a bránu. Následující příkazy slouží jako příklad toho, co by takové virtuální sítě vytvořilo.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>Nejdůležitější konfigurace na vědomí pro virtuální síť, je, že podsíť pro zařízení hardwarového zabezpečení musí mít delegování nastavena na "Microsoft.HardwareSecurityModules/dedicatedHSMs".  Zřizování hsm nebude fungovat bez této možnosti je nastavena.

Jakmile budou všechny předpoklady na místě, spusťte následující příkaz a použijte šablonu Správce prostředků Azure a ujistěte se, že jste aktualizovali hodnoty s jedinečnými názvy (alespoň název skupiny prostředků):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Toto nasazení by mělo trvat přibližně 25 až 30 minut, než se převážná část této doby bude spořit o zařízení hesm

![zřizování stavu](media/tutorial-deploy-hsm-cli/progress-status.png)

Po dokončení nasazení úspěšně "provisioningState": "Úspěšné" se zobrazí. Můžete se připojit ke svému stávajícímu virtuálnímu počítači a použít SSH k zajištění dostupnosti zařízení HSM.

## <a name="verifying-the-deployment"></a>Ověření nasazení

Chcete-li ověřit, že zařízení byla zřízena a zobrazit atributy zařízení, spusťte následující sadu příkazů. Ujistěte se, že skupina prostředků je správně nastavena a název prostředku je přesně tak, jak jste měli v souboru parametrů.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![tvorba opravných položek](media/tutorial-deploy-hsm-cli/progress-status2.png)

Teď budete taky moct zobrazit prostředky pomocí [Průzkumníka prostředků Azure](https://resources.azure.com/).   Jakmile v průzkumníku rozbalte "odběry" na levé straně, rozbalte konkrétní předplatné pro vyhrazený modul hardwarového zabezpečení, rozbalte "skupiny prostředků", rozbalte skupinu prostředků, kterou jste použili, a nakonec vyberte položku "prostředky".

## <a name="testing-the-deployment"></a>Testování nasazení

Testování nasazení je případ připojení k virtuálnímu počítači, který může přistupovat k hme(s) a pak připojení přímo k zařízení hsm. Tyto akce potvrdí, že je přístupný přístup k hsm.
Nástroj ssh se používá pro připojení k virtuálnímu počítači. Příkaz bude podobný následujícímu, ale s názvem správce a názvem DNS, který jste zadali v parametru.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

IP adresa virtuálního_ virtuálního počítačů může být také použita místo názvu DNS ve výše uvedeném příkazu. Pokud je příkaz úspěšný, vyzve k zadání hesla a měli byste jej zadat. Po přihlášení k virtuálnímu počítači se můžete přihlásit k modulu zabezpečení pomocí privátní IP adresy nalezené na portálu pro prostředek síťového rozhraní přidruženého k modulu zabezpečení zabezpečení.

![seznam součástí](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Všimněte si zaškrtávacího políčka Zobrazit skryté typy, které se při zaškrtnutí zobrazí prostředky hsm.

Na obrázku výše, kliknutím na "HSM1_HSMnic" nebo "HSM2_HSMnic" by se zobrazí příslušná soukromá IP adresa. V opačném `az resource show` případě je výše použitý příkaz způsobem, jak identifikovat správnou adresu IP. 

Pokud máte správnou adresu IP, spusťte následující příkaz nahrazující tuto adresu:

`ssh tenantadmin@10.0.2.4`

V případě úspěchu budete vyzváni k zadání hesla. Výchozí heslo je heslo a hsm vás nejprve požádá o změnu hesla, takže nastavte silné heslo a použijte jakýkoli mechanismus, který vaše organizace upřednostňuje k uložení hesla a zabránění ztrátě.

>[!IMPORTANT]
>Pokud toto heslo ztratíte, bude muset být soubor zabezpečení resetován a to znamená ztrátu klíčů.

Pokud jste připojeni k hsm pomocí ssh, spusťte následující příkaz, abyste zajistili, že je funkční.

`hsm show`

Výstup by měl vypadat tak, jak je znázorněno na obrázku níže:

![seznam součástí](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

V tomto okamžiku jste přidělili všechny prostředky pro vysoce dostupné, dvě nasazení vzdáleného serveru hesm a ověřený přístup a provozní stav. Jakákoli další konfigurace nebo testování zahrnuje více práce se samotným zařízením s hsm. Za tímto účelem byste měli postupovat podle pokynů v Gemalto Luna Network HSM 7 Administration Guide kapitola 7 inicializovat HSM a vytvořit oddíly. Veškerá dokumentace a software jsou k dispozici přímo od společnosti Gemalto ke stažení, jakmile se zaregistrujete na portálu zákaznické podpory Gemalto a budete mít ID zákazníka. Stáhněte si klientský software verze 7.2 a získejte všechny požadované součásti.

## <a name="delete-or-clean-up-resources"></a>Odstranění nebo vyčištění prostředků

Pokud jste dokončili pouze zařízení hsm, pak jej lze odstranit jako prostředek a vrátit do fondu volného. Zřejmé obavy, když to dělá, jsou citlivá zákaznická data, která jsou v zařízení. Nejlepší způsob, jak "zeroize" zařízení je získat heslo správce HSM špatně 3 krát (poznámka: to není zařízení admin, je to skutečný Správce HSM). Jako bezpečnostní opatření k ochraně materiálu klíče zařízení nelze odstranit jako prostředek Azure, dokud není v nule stavu.

> [!NOTE]
> Pokud máte problém s konfigurací zařízení Gemalto, měli byste kontaktovat [zákaznickou podporu Gemalto](https://safenet.gemalto.com/technical-support/).


Pokud jste dokončili všechny prostředky v této skupině prostředků, můžete je všechny odebrat pomocí následujícího příkazu:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Další kroky

Po dokončení kroků v kurzu jsou zřízeny vyhrazené prostředky modulu hardwarového zabezpečení a máte virtuální síť s nezbytnými moduly hardwarového zabezpečení a dalšísíťové součásti, které umožňují komunikaci s modulhardwarově ohez.  Nyní jste v pozici, abyste toto nasazení doplnili o další prostředky, jak vyžaduje upřednostňovaná architektura nasazení. Další informace o tom, jak pomoci s plánováním nasazení, najdete v dokumentech koncepty.
Doporučuje se návrh se dvěma soubory hmenážmi v primární oblasti, které řeší dostupnost na úrovni racku, a dvěma soubory hsm v sekundární oblasti, které řeší regionální dostupnost. Soubor šablony použitý v tomto kurzu lze snadno použít jako základ pro dva nasazení hsm, ale musí mít jeho parametry upravené tak, aby vyhovovalvašim požadavkům.

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Síťové služby](networking.md)
* [Možnosti podpory](supportability.md)
* [Sledování](monitoring.md)
