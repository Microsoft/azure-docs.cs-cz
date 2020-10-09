---
title: Kurz nasazení do existující virtuální sítě pomocí Azure CLI – vyhrazený modul HARDWAROVÉho zabezpečení Azure | Microsoft Docs
description: Kurz ukazující, jak nasadit vyhrazený modul HARDWAROVÉho zabezpečení pomocí rozhraní příkazového řádku do existující virtuální sítě
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 63cdb27663cb1a2d8de1a97a2f352b05ff57a3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89489880"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Kurz: nasazení HSM do existující virtuální sítě pomocí rozhraní příkazového řádku

Vyhrazený modul HARDWAROVÉho zabezpečení Azure poskytuje fyzické zařízení pro použití výhradně pro zákazníky s úplnou správou a plnou zodpovědností na správu. Použití fyzických zařízení umožňuje, aby Microsoft mohl řídit přidělování zařízení, aby se zajistila efektivita správy kapacity. V důsledku toho se vyhrazená služba HSM v rámci předplatného Azure nebude normálně zobrazovat pro zřizování prostředků. Každý zákazník Azure, který vyžaduje přístup k vyhrazené službě HSM, musí nejdřív kontaktovat svého účet Microsoftho vedoucího, aby požádal o registraci vyhrazené služby HSM. Jenom po úspěšném dokončení tohoto procesu bude zřízení možné. 

V tomto kurzu se zobrazuje typický proces zřizování, kde:

- Zákazník už má virtuální síť.
- Mají virtuální počítač
- Musí do tohoto stávajícího prostředí přidávat prostředky HSM.

Typická, vysoká dostupnost, architektura nasazení ve více oblastech, může vypadat takto:

![nasazení ve více oblastech](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Tento kurz se zaměřuje na pár HSM a požadovanou bránu ExpressRoute (viz podsíť 1 výše), která se integruje do existující virtuální sítě (viz virtuální síť 1 výše).  Všechny ostatní prostředky jsou standardní prostředky Azure. Stejný proces integrace lze použít pro HSM v podsíti 4 ve virtuální síti 3 výše.

## <a name="prerequisites"></a>Požadavky

Vyhrazený modul HARDWAROVÉho zabezpečení Azure není v současnosti dostupný v Azure Portal. Veškerá interakce se službou bude prostřednictvím příkazového řádku nebo pomocí PowerShellu. V tomto kurzu použijete rozhraní příkazového řádku (CLI) v Azure Cloud Shell. Pokud začínáte s rozhraním příkazového řádku Azure CLI, postupujte podle pokynů v části Začínáme: [Azure CLI 2,0](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)Začínáme.

Předpoklady:

- Dokončili jste proces registrace HSM vyhrazený pro Azure.
- Byli jste schváleni pro použití služby. Pokud ne, požádejte o další informace svého zástupce účet Microsoft.
- Vytvořili jste skupinu prostředků pro tyto prostředky a nově nasazená v tomto kurzu se připojí k této skupině.
- Už jste vytvořili potřebnou virtuální síť, podsíť a virtuální počítače podle diagramu výše a teď chcete do tohoto nasazení integrovat 2 HSM.

Všechny níže uvedené pokyny předpokládají, že jste již přešli na Azure Portal a že jste otevřeli Cloud Shell (vyberte " \> \_ " směrem nahoru v pravém horním rohu portálu).

## <a name="provisioning-a-dedicated-hsm"></a>Zřizování vyhrazeného modulu HSM

Zřizování HSM a jejich integrování do existující virtuální sítě prostřednictvím brány ExpressRoute se ověří pomocí SSH. Toto ověření zajišťuje dostupnost a základní dostupnost zařízení HSM pro všechny další konfigurační aktivity. Následující příkazy použijí šablonu Azure Resource Manager k vytvoření prostředků HSM a přidružených síťových prostředků.

### <a name="validating-feature-registration"></a>Ověřování registrace funkce

Jak je uvedeno výše, všechny aktivity zřizování vyžadují, aby byla pro vaše předplatné zaregistrovaná vyhrazená služba HSM. Pokud to chcete ověřit, spusťte následující příkazy ve službě Azure Portal Cloud Shell.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Příkazy by měly vracet stav "registrováno" (jak je vidět níže). Pokud příkazy nevrátí "zaregistrované", budete se muset zaregistrovat pro tuto službu tím, že se obrátíte na zástupce účet Microsoft.

![stav předplatného](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Vytváření prostředků HSM

Modul HARDWAROVÉho zabezpečení se zřídí do virtuální sítě zákazníků, takže se vyžaduje virtuální síť a podsíť. Závislost modulu HARDWAROVÉho zabezpečení, která umožňuje komunikaci mezi virtuální sítí a fyzickým zařízením, je bránou ExpressRoute a nakonec je pro přístup k zařízení HSM pomocí klientského softwaru identita Gemalto potřeba virtuální počítač. Tyto prostředky byly shromážděny do souboru šablony s odpovídajícím souborem parametrů pro snadné použití. Soubory jsou k dispozici na základě kontaktování Microsoftu přímo jako HSMrequest@Microsoft.com .

Jakmile budete mít soubory, musíte upravit soubor parametrů a vložit tak preferované názvy prostředků. Upravit řádky pomocí "value": "".

- `namingInfix` Předpona názvů prostředků HSM
- `ExistingVirtualNetworkName` Název virtuální sítě použité pro HSM
- `DedicatedHsmResourceName1` Název prostředku HSM v razítku Datacenter 1
- `DedicatedHsmResourceName2` Název prostředku HSM v razítku Datacenter 2
- `hsmSubnetRange` Rozsah IP adres podsítě pro HSM
- `ERSubnetRange` Rozsah IP adres podsítě pro bránu virtuální sítě

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

- Podsíť pro HSM v zadané virtuální síti
- Podsíť pro bránu virtuální sítě
- Brána virtuální sítě, která připojuje virtuální síť k zařízením HSM
- Veřejná IP adresa brány
- HSM v razítku 1
- HSM na razítku 2

Po nastavení hodnot parametrů je potřeba odeslat soubory do Azure Portal sdílené složky Cloud Shell pro použití. V Azure Portal klikněte v \> \_ pravém horním rohu na symbol "Cloud Shell" a tím se v dolní části obrazovky zobrazí příkazové prostředí. Možnosti pro toto jsou BASH a PowerShell a pokud ještě nejsou nastavené, měli byste vybrat BASH.

Příkazové prostředí má na panelu nástrojů možnost odeslat/stáhnout a tuto šablonu byste měli vybrat k nahrání šablony a souborů parametrů do sdílené složky:

![sdílení souborů](media/tutorial-deploy-hsm-cli/file-share.png)

Po nahrání souborů jste připraveni k vytváření prostředků. Před vytvořením nových prostředků HSM jsou k dispozici některé nezbytné prostředky, které byste měli zajistit. Musíte mít virtuální síť s rozsahy podsítí pro výpočetní výkon, HSM a bránu. Následující příkazy slouží jako příklad toho, co by vytvořilo takovou virtuální síť.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
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
>Nejdůležitější konfigurace, která se má poznamenat pro virtuální síť, je, že podsíť pro zařízení HSM musí mít delegování nastavené na Microsoft. HardwareSecurityModules/dedicatedHSMs.  Zřizování modulu HSM nebude fungovat bez nastavené této možnosti.

Až budou všechny požadavky splněné, spusťte následující příkaz, který bude používat šablonu Azure Resource Manager, a ujistěte se, že máte aktualizované hodnoty s jedinečnými názvy (aspoň název skupiny prostředků):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Toto nasazení by mělo trvat přibližně 25 až 30 minut, než se dokončí hromadně v době, kdy se jedná o zařízení HSM.

![stav zřizování](media/tutorial-deploy-hsm-cli/progress-status.png)

Po úspěšném dokončení nasazení se zobrazí "provisioningState": "úspěch". Ke stávajícímu virtuálnímu počítači se můžete připojit a použít SSH k zajištění dostupnosti zařízení HSM.

## <a name="verifying-the-deployment"></a>Ověřování nasazení

Pokud chcete ověřit zřízení zařízení a zobrazit atributy zařízení, spusťte následující sadu příkazů. Ujistěte se, že je správně nastavená skupina prostředků a že je název prostředku přesně stejný jako v souboru parametrů.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![výstup zřizování](media/tutorial-deploy-hsm-cli/progress-status2.png)

Nyní budete moci zobrazit prostředky pomocí [Průzkumníka prostředků Azure](https://resources.azure.com/).   V Průzkumníkovi rozbalte na levé straně předplatná, rozbalte konkrétní předplatné pro vyhrazený modul HSM, rozbalte položku skupiny prostředků, rozbalte skupinu prostředků, kterou jste použili, a nakonec vyberte položku prostředky.

## <a name="testing-the-deployment"></a>Testování nasazení

Testování nasazení je případ, kdy se připojujete k virtuálnímu počítači, který má přístup k modulům HSM a pak se připojuje přímo k zařízení HSM. Tyto akce potvrdí, že modul HARDWAROVÉho zabezpečení bude dostupný.
Nástroj SSH se používá pro připojení k virtuálnímu počítači. Příkaz bude podobný následujícímu, ale s názvem správce a názvem DNS, který jste zadali v parametru.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

Ve výše uvedeném příkazu by se mohla místo názvu DNS použít taky IP adresa virtuálního počítače. Pokud se příkaz nezdaří, zobrazí se výzva k zadání hesla a měli byste ho zadat. Po přihlášení k virtuálnímu počítači se můžete přihlásit k modulu HSM pomocí privátní IP adresy, která se nachází na portálu pro prostředek síťového rozhraní, který je přidružený k modulu HSM.

![Seznam součástí](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Všimněte si políčka "Zobrazit skryté typy", které při výběru zobrazí prostředky HSM.

Na snímku obrazovky výše klikněte na "HSM1_HSMnic" nebo "HSM2_HSMnic" by se zobrazila příslušná privátní IP adresa. V opačném případě `az resource show` výše uvedený příkaz slouží jako způsob identifikace správné IP adresy. 

Pokud máte správnou IP adresu, spusťte následující příkaz, který nahradí tuto adresu:

`ssh tenantadmin@10.0.2.4`

V případě úspěchu se zobrazí výzva k zadání hesla. Výchozí heslo je heslo a modul hardwarového zabezpečení (HSM) nejprve požádá o změnu hesla, aby bylo nastaveno silné heslo, a použijte jakýkoliv mechanismus, který vaše organizace upřednostňuje k uložení hesla a zabránění ztrátě.

>[!IMPORTANT]
>Pokud toto heslo ztratíte, bude nutné modul HARDWAROVÉho zabezpečení resetovat a to znamená ztrátu vašich klíčů.

Když jste k modulu HSM připojeni pomocí SSH, spusťte následující příkaz, aby se zajistilo fungování modulu HARDWAROVÉho zabezpečení.

`hsm show`

Výstup by měl vypadat, jak je znázorněno na obrázku níže:

![Seznam součástí](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

V tuto chvíli jste přidělili všechny prostředky pro vysoce dostupný, dva nasazení HSM a ověřený přístup a provozní stav. Jakákoli další konfigurace nebo testování zahrnuje více práce se samotným zařízením HSM. V takovém případě byste měli postupovat podle pokynů v tématu Identita Gemalto Luna Network HSM 7 Kapitola 7 a inicializovat modul HSM a vytvořit oddíly. Veškerá dokumentace a software jsou k dispozici přímo z identita Gemalto ke stažení, jakmile se zaregistrujete na portálu zákaznické podpory identita Gemalto a máte ID zákazníka. Stáhněte si klientský software verze 7,2, abyste získali všechny požadované součásti.

## <a name="delete-or-clean-up-resources"></a>Odstranění nebo vyčištění prostředků

Pokud jste hotovi s pouze zařízením HSM, pak ho můžete odstranit jako prostředek a vrátit se do bezplatného fondu. Zjevné obavy při tom, že se jedná o veškerá citlivá zákaznická data, která jsou na zařízení. Nejlepším způsobem, jak "zeroize", zařízení je získat heslo správce HSM nesprávně třikrát (Poznámka: Toto není správce zařízení, je to skutečný správce HSM). Jako bezpečnostní opatření k ochraně klíčových materiálů se zařízení nedá odstranit jako prostředek Azure, dokud nebude v nenulovém stavu.

> [!NOTE]
> Pokud máte problém s libovolnou konfigurací zařízení identita Gemalto, měli byste kontaktovat [zákaznickou podporu identita Gemalto](https://safenet.gemalto.com/technical-support/).


Pokud jste dokončili všechny prostředky v této skupině prostředků, můžete je odebrat pomocí následujícího příkazu:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Další kroky

Po dokončení kroků v tomto kurzu se zřídí vyhrazené prostředky HSM a máte virtuální síť s nezbytným HSM a dalšími síťovými součástmi, které umožňují komunikaci s modulem HSM.  Nyní jste na pozici, abyste si toto nasazení vyžádali o další prostředky, jak je vyžaduje upřednostňovaná architektura nasazení. Další informace o pomoc při plánování nasazení najdete v tématu koncepty dokumentů.
Návrh se dvěma Hsmy v primární oblasti, která adresuje dostupnost na úrovni racku, a dvě HSM v sekundární oblasti, která řeší dostupnost pro regionální účely, se doporučuje. Soubor šablony použitý v tomto kurzu se dá snadno použít jako základ pro dvě nasazení HSM, ale musí mít změněné své parametry, aby splňovaly vaše požadavky.

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)
