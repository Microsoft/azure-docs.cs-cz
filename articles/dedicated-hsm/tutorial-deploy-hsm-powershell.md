---
title: Kurz se nasazuje do existující virtuální sítě pomocí PowerShellu – vyhrazený modul HARDWAROVÉho zabezpečení Azure | Microsoft Docs
description: Kurz ukazující, jak nasadit vyhrazený modul HARDWAROVÉho zabezpečení pomocí prostředí PowerShell do existující virtuální sítě
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 5ed5ac90f446f74c54488f6d0cf23adbd63a3e1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606874"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Kurz – nasazení HSM do existující virtuální sítě pomocí prostředí PowerShell

Vyhrazená služba HSM v Azure poskytuje fyzické zařízení pro použití výhradně pro zákazníky s úplnou správou a plnou zodpovědností na správu. Z důvodu poskytnutí fyzického hardwaru musí Microsoft určit, jak jsou tato zařízení přidělená, aby bylo zajištěno, že se kapacita bude efektivně spravovat. V důsledku toho se vyhrazená služba HSM v rámci předplatného Azure nebude normálně zobrazovat pro zřizování prostředků. Každý zákazník Azure, který vyžaduje přístup k vyhrazené službě HSM, musí nejdřív kontaktovat svého účet Microsoftho vedoucího, aby požádal o registraci vyhrazené služby HSM. Jenom po úspěšném dokončení tohoto procesu bude zřízení možné.
Tento kurz má za cíl zobrazit typický proces zřizování, kde:

- Zákazník už má virtuální síť.
- Mají virtuální počítač
- Musí do tohoto stávajícího prostředí přidávat prostředky HSM.

Typická, vysoká dostupnost, architektura nasazení ve více oblastech, může vypadat takto:

![nasazení ve více oblastech](media/tutorial-deploy-hsm-powershell/high-availability.png)

Tento kurz se zaměřuje na pár HSM a požadovanou bránu ExpressRoute (viz podsíť 1 výše), která se integruje do existující virtuální sítě (viz virtuální síť 1 výše).  Všechny ostatní prostředky jsou standardní prostředky Azure. Stejný proces integrace lze použít pro HSM v podsíti 4 ve virtuální síti 3 výše.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Vyhrazený modul HARDWAROVÉho zabezpečení Azure není v Azure Portal aktuálně k dispozici, takže veškerá interakce se službou bude prostřednictvím příkazového řádku nebo pomocí PowerShellu. V tomto kurzu použijete PowerShell v Azure Cloud Shell. Pokud prostředí PowerShell začínáte, postupujte podle pokynů v části Začínáme: [Azure PowerShell Začínáme](/powershell/azure/get-started-azureps).

Předpoklady:

- Procházeli jste prostřednictvím vyhrazeného procesu registrace HSM Azure a bylo schváleno pro používání služby. Pokud ne, požádejte o další informace svého zástupce účet Microsoft. 
- Vytvořili jste skupinu prostředků pro tyto prostředky a nově nasazená v tomto kurzu se připojí k této skupině.
- Už jste vytvořili potřebnou virtuální síť, podsíť a virtuální počítače podle diagramu výše a teď chcete do tohoto nasazení integrovat 2 HSM.

Všechny níže uvedené pokyny předpokládají, že jste již přešli na Azure Portal a že jste otevřeli Cloud Shell (vyberte " \> \_ " směrem nahoru v pravém horním rohu portálu).

## <a name="provisioning-a-dedicated-hsm"></a>Zřizování vyhrazeného modulu HSM

Zřizování HSM a integrace do existující virtuální sítě prostřednictvím brány ExpressRoute se ověří pomocí nástroje příkazového řádku SSH, aby se zajistila dostupnost a základní dostupnost zařízení HSM pro všechny další konfigurační aktivity. Následující příkazy použijí šablonu Správce prostředků k vytvoření prostředků HSM a přidružených síťových prostředků.

### <a name="validating-feature-registration"></a>Ověřování registrace funkce

Jak je uvedeno výše, všechny aktivity zřizování vyžadují, aby byla pro vaše předplatné zaregistrovaná vyhrazená služba HSM. Pokud to chcete ověřit, spusťte následující příkaz prostředí PowerShell ve službě Azure Portal Cloud Shell. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

Příkaz by měl vrátit stav "registrováno" (jak je vidět níže), než budete pokračovat dál.  Pokud jste pro tuto službu nezaregistrovali, obraťte se na svého zástupce účet Microsoft.

![stav předplatného](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>Vytváření prostředků HSM

Zařízení HSM se zřídí do virtuální sítě zákazníků. To předpokládá požadavek na podsíť. Závislost modulu HARDWAROVÉho zabezpečení, která umožňuje komunikaci mezi virtuální sítí a fyzickým zařízením, je bránou ExpressRoute a nakonec je pro přístup k zařízení HSM pomocí klientského softwaru Thales potřeba virtuální počítač. Tyto prostředky byly shromážděny do souboru šablony s odpovídajícím souborem parametrů pro snadné použití. Soubory jsou k dispozici kontaktováním společnosti Microsoft přímo na adrese HSMrequest@Microsoft.com .

Jakmile budete mít soubory, musíte upravit soubor parametrů a vložit tak preferované názvy prostředků. To znamená, že se upravují řádky s hodnotou:.

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

Přidružený soubor šablony Správce prostředků vytvoří 6 prostředků s těmito informacemi:

- Podsíť pro HSM v zadané virtuální síti
- Podsíť pro bránu virtuální sítě 
- Brána virtuální sítě, která připojuje virtuální síť k zařízením HSM
- Veřejná IP adresa brány
- HSM v razítku 1
- HSM na razítku 2

Po nastavení hodnot parametrů je potřeba odeslat soubory do Azure Portal sdílené složky Cloud Shell pro použití. V Azure Portal klikněte v \> \_ pravém horním rohu na symbol "Cloud Shell" a tím se v dolní části obrazovky zobrazí příkazové prostředí. Možnosti pro toto jsou BASH a PowerShell a pokud ještě nejsou nastavené, měli byste vybrat BASH.

Příkazové prostředí má na panelu nástrojů možnost odeslat/stáhnout a tuto šablonu byste měli vybrat k nahrání šablony a souborů parametrů do sdílené složky:

![sdílení souborů](media/tutorial-deploy-hsm-powershell/file-share.png)

Po nahrání souborů jste připraveni k vytváření prostředků.
Před vytvořením nových prostředků HSM jsou k dispozici některé nezbytné prostředky, které byste měli zajistit. Musíte mít virtuální síť s rozsahy podsítí pro výpočetní výkon, HSM a bránu. Následující příkazy slouží jako příklad toho, co by vytvořilo takovou virtuální síť.

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>Nejdůležitější konfigurace, která se má poznamenat pro virtuální síť, je, že podsíť pro zařízení HSM musí mít delegování nastavené na Microsoft. HardwareSecurityModules/dedicatedHSMs.  Zřizování modulu HSM nebude bez tohoto použití fungovat.

Až budou všechny požadavky splněné, spusťte následující příkaz, který bude používat šablonu Správce prostředků, a ujistěte se, že máte aktualizované hodnoty s jedinečnými názvy (aspoň název skupiny prostředků):

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Dokončení tohoto příkazu by mělo trvat přibližně 20 minut. Použije se možnost-verbose, aby se stav průběžně zobrazoval.

![stav zřizování](media/tutorial-deploy-hsm-powershell/progress-status.png)

Po úspěšném dokončení se zobrazí zpráva "provisioningState": "úspěch", můžete se přihlásit ke stávajícímu virtuálnímu počítači a použít SSH k zajištění dostupnosti zařízení HSM.

## <a name="verifying-the-deployment"></a>Ověřování nasazení

Pokud chcete ověřit zřízení zařízení a zobrazit atributy zařízení, spusťte následující sadu příkazů. Ujistěte se, že je správně nastavená skupina prostředků a že je název prostředku přesně stejný jako v souboru parametrů.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![stav zřizování](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Nyní budete moci zobrazit prostředky pomocí [Průzkumníka prostředků Azure](https://resources.azure.com/).   V Průzkumníkovi rozbalte na levé straně předplatná, rozbalte konkrétní předplatné pro vyhrazený modul HSM, rozbalte položku skupiny prostředků, rozbalte skupinu prostředků, kterou jste použili, a nakonec vyberte položku prostředky.

## <a name="testing-the-deployment"></a>Testování nasazení

Testování nasazení je případ, kdy se připojujete k virtuálnímu počítači, který má přístup k modulům HSM a pak se připojuje přímo k zařízení HSM. Tyto akce potvrdí, že modul HARDWAROVÉho zabezpečení bude dostupný.
Nástroj SSH se používá pro připojení k virtuálnímu počítači. Příkaz bude podobný následujícímu, ale s názvem správce a názvem DNS, který jste zadali v parametru.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

Heslo, které se má použít, je ten ze souboru parametrů.
Po přihlášení k virtuálnímu počítači se systémem Linux se můžete přihlásit k modulu HSM pomocí privátní IP adresy, kterou najdete na portálu pro \<prefix> hsm_vnic prostředku.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Po zadání IP adresy spusťte následující příkaz:

`ssh tenantadmin@<ip address of HSM>`

V případě úspěchu se zobrazí výzva k zadání hesla. Výchozí heslo je heslo. Modul HARDWAROVÉho zabezpečení vás vyzve, abyste změnili heslo, takže si nastavili silné heslo a použijete jakýkoliv mechanismus, který vaše organizace upřednostňuje k uložení hesla a zabránění ztrátám.  

>[!IMPORTANT]
>Pokud toto heslo ztratíte, bude nutné modul HARDWAROVÉho zabezpečení resetovat a to znamená ztrátu vašich klíčů.

Když jste se připojili k zařízení HSM pomocí SSH, spusťte následující příkaz, aby se zajistilo fungování modulu HARDWAROVÉho zabezpečení.

`hsm show`

Výstup by měl vypadat jako obrázek uvedený níže:

![Snímek obrazovky, který zobrazuje výstup z příkazu HSM show.](media/tutorial-deploy-hsm-powershell/output.png)

V tuto chvíli jste přidělili všechny prostředky pro vysoce dostupný, dva nasazení HSM a ověřený přístup a provozní stav. Jakákoli další konfigurace nebo testování zahrnuje více práce se samotným zařízením HSM. V takovém případě byste měli postupovat podle pokynů v příručce pro správu HSM Thales Luna 7 Kapitola 7 a inicializovat modul HSM a vytvořit oddíly. Veškerá dokumentace a software jsou k dispozici přímo z Thales ke stažení, jakmile se zaregistrujete na [portálu zákaznické podpory Thales](https://supportportal.thalesgroup.com/csm) a máte ID zákazníka. Stáhněte si klientský software verze 7,2, abyste získali všechny požadované součásti.

## <a name="delete-or-clean-up-resources"></a>Odstranění nebo vyčištění prostředků

Pokud jste hotovi s pouze zařízením HSM, pak ho můžete odstranit jako prostředek a vrátit se do bezplatného fondu. Zjevné obavy při tom, že se jedná o veškerá citlivá zákaznická data, která jsou na zařízení. Nejlepším způsobem, jak "zeroize", zařízení je získat heslo správce HSM nesprávně třikrát (Poznámka: Toto není správce zařízení, je to skutečný správce HSM). Jako bezpečnostní opatření k ochraně klíčových materiálů se zařízení nedá odstranit jako prostředek Azure, dokud nebude v nenulovém stavu.

> [!NOTE]
> Pokud máte problém s libovolnou konfigurací zařízení Thales, měli byste kontaktovat [zákaznickou podporu Thales](https://supportportal.thalesgroup.com/csm).

Pokud chcete odebrat prostředek HSM v Azure, můžete použít následující příkaz, který nahradí proměnné "$" pomocí jedinečných parametrů:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Další kroky

Po dokončení kroků v tomto kurzu jsou vyhrazené prostředky HSM zřízené a dostupné ve vaší virtuální síti. Nyní jste na pozici, abyste si toto nasazení vyžádali o další prostředky, jak je vyžaduje upřednostňovaná architektura nasazení. Další informace o pomoc při plánování nasazení najdete v tématu koncepty dokumentů. Návrh se dvěma Hsmy v primární oblasti, která adresuje dostupnost na úrovni racku, a dvě HSM v sekundární oblasti, která řeší dostupnost pro regionální účely, se doporučuje. Soubor šablony použitý v tomto kurzu se dá snadno použít jako základ pro dvě nasazení HSM, ale musí mít změněné své parametry, aby splňovaly vaše požadavky.

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Monitorování](monitoring.md)
* [Možnosti podpory](supportability.md)
