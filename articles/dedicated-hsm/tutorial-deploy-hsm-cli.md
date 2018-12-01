---
title: Kurz – Azure vyhrazené HSM nasazení HSM do existující virtuální sítě pomocí rozhraní příkazového řádku | Dokumentace Microsoftu
description: Kurz ukazuje, jak nasadit vyhrazený HSM pomocí rozhraní příkazového řádku
services: dedicated-hsm
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: barclayn
ms.openlocfilehash: ca30dc9d86db8faabfdd3791b74b9f86c9480ea5
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679643"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Kurz – moduly hardwarového zabezpečení nasazení do existující virtuální sítě pomocí rozhraní příkazového řádku

Azure vyhrazené HSM nabízí fyzické zařízení pro použití výhradně zákazník, s kompletní správu kontroly a odpovědnosti úplné řízení. Použití fyzického zařízení vytvoří Microsoft k řízení zařízení přidělení potřeba zajistit, že je efektivně spravovat kapacity. V důsledku toho v rámci předplatného Azure vyhrazené HSM nebude služba obvykle viditelná jenom pro zřizování prostředků. Žádné zákazníků Azure, které vyžadují přístup ke službě vyhrazené HSM nutné kontaktovat příslušného manažera účtu Microsoft k žádosti o registraci pro službu vyhrazené modulu hardwarového zabezpečení. Pouze po úspěšném dokončení tohoto procesu bude zřizování možné. 

Tento kurz ukazuje typické procesu zřizování, kde:

- Zákazník má virtuální síť už
- Mají virtuálního počítače
- Je třeba přidat prostředky modulu HSM do existujícího prostředí.

Typická, vysokou dostupnost, architektura nasazení ve více oblastech může vypadat takto:

![Nasazení s více oblastmi](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Tento kurz se zaměřuje na pár moduly hardwarového zabezpečení a vyžaduje bránu ExpressRoute (viz výše 1 podsítě) je integrované do existující virtuální síť (viz výše 1 virtuální síť).  Všechny ostatní prostředky jsou standardní prostředky Azure. Stejný postup integrace lze použít pro moduly hardwarového zabezpečení v podsíti 4 na virtuální síť 3 výše.

## <a name="prerequisites"></a>Požadavky

Azure vyhrazené HSM není aktuálně k dispozici na webu Azure Portal. Všechny interakce s služba bude prostřednictvím příkazového řádku nebo pomocí prostředí PowerShell. V tomto kurzu se pomocí rozhraní příkazového řádku (CLI) ve službě Azure Cloud Shell. Pokud jste ještě na rozhraní příkazového řádku Azure, postupujte podle Začínáme pokynů tady: [Azure CLI 2.0 – Začínáme](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Předpoklady:

- Dokončit proces registrace vyhrazené modulu hardwarového zabezpečení Azure
- Můžete mít schválený pro použití služby. Pokud ne, obraťte se na obchodního zástupce společnosti Microsoft pro podrobnosti.
- Vytvoří skupinu prostředků pro tyto prostředky a nové nasazení v tomto kurzu se připojí k této skupině.
- Už vytvořili nezbytné virtuální síť, podsíť a virtuální počítače v závislosti na výše uvedeném diagramu a teď chcete integrovat nasazení 2 moduly hardwarového zabezpečení.

Všechny následující pokyny předpokládají, že už jste přešli na webu Azure portal a máte otevřený službě Cloud Shell (vyberte "\>\_" směrem k horním pravém rohu portálu).

## <a name="provisioning-a-dedicated-hsm"></a>Zřizování vyhrazené HSM

Zřizování moduly hardwarového zabezpečení a integraci do existující virtuální sítě přes bránu ExpressRoute se ověří pomocí ssh. Ověřování pomáhá zajistit připojení a základní dostupnost modulu hardwarového zabezpečení zařízení pro jakékoli další konfigurace aktivity. Následující příkazy používají šablony Azure Resource Manageru k vytvoření prostředků modulu hardwarového zabezpečení a přidružené síťové prostředky.

### <a name="validating-feature-registration"></a>Ověřuje se registrace funkce

Jak je uvedeno výše, žádné zřizovací aktivita vyžaduje, aby služba Dedicated modulu hardwarového zabezpečení je registrována pro vaše předplatné. Chcete-li ověřit, zda, spusťte následující příkazy v portálu Azure cloud shell.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Následující příkaz ověří síťové funkce potřebné pro službu vyhrazené modulu hardwarového zabezpečení.

```azurecli
az feature show \
   --namespace Microsoft.Network \
   --name AllowBaremetalServers
```

Oba příkazy by měl vrátit ve stavu "Registrováno" (jak je vidět níže). Pokud příkazy nevracejte "Registrováno", budete muset zaregistrovat pro tuto službu, obraťte se na obchodního zástupce společnosti Microsoft.

![stav odběru](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Vytváří se prostředky modulu hardwarového zabezpečení

Modul hardwarového zabezpečení se zřídí virtuální sítě Zákazníci tak, aby byly požadované virtuální síť a podsíť. Závislost pro modul hardwarového zabezpečení k umožnění komunikace mezi virtuální sítí a fyzickým zařízením je bránu ExpressRoute a nakonec virtuální počítač vyžaduje přístup k modulu hardwarového zabezpečení zařízení pomocí klientského softwaru Gemalto. Tyto prostředky byly shromážděny do souboru šablony, s odpovídající soubor parametrů pro snadné použití. Soubory jsou k dispozici kontaktujete společnost Microsoft přímo jako HSMrequest@Microsoft.com.

Jakmile budete mít soubory, musíte upravit soubor parametrů pro vložení upřednostňované názvy pro prostředky. Upravit řádky s "value": "".

- `namingInfix` Předpona pro názvy prostředků modulu hardwarového zabezpečení
- `ExistingVirtualNetworkName` Název virtuální sítě pro moduly hardwarového zabezpečení
- `DedicatedHsmResourceName1` Název prostředku modulu hardwarového zabezpečení v datovém centru razítko 1
- `DedicatedHsmResourceName2` Název prostředku modulu hardwarového zabezpečení v datovém centru razítko 2
- `hsmSubnetRange` Rozsah IP adres podsítě pro moduly hardwarového zabezpečení
- `ERSubnetRange` Rozsah IP adres podsítě brány virtuální sítě

Příkladem těchto změn je následujícím způsobem:

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

Přidružený soubor šablony Azure Resource Manageru vytvoříte 6 prostředky s těmito informacemi:

- Podsíť pro moduly hardwarového zabezpečení v zadané virtuální sítě
- Podsíť pro bránu virtuální sítě
- Brány virtuální sítě, která se připojuje virtuální sítě do modulu hardwarového zabezpečení zařízení
- Veřejnou IP adresu pro bránu
- Modul hardwarového zabezpečení v razítku 1
- Modul hardwarového zabezpečení v razítku 2

Po nastavení hodnoty parametrů jsou soubory budete muset nahrát do sdílené složky prostředí cloudu Azure portal k použití. Na webu Azure Portal, klikněte "\>\_" cloud shell symbol pravé horní části a to způsobí, že dolní části obrazovky příkazového prostředí. Možnosti pro to jsou BASH a PowerShell a jste by měl vybrat BASH, pokud nebude již nastaven.

Příkazové okno má možnost nahrávání a stahování na panelu nástrojů a vyberte tento postup nahrání souborů šablonu a parametry do sdílené složky:

![stav odběru](media/tutorial-deploy-hsm-cli/file-share.png)

Jakmile soubory odešlete, jste připraveni k vytváření prostředků. Před vytvořením nového modulu hardwarového zabezpečení jsou prostředky, které existují některé požadovaného prostředky, by měl zajistit na místě. Musí mít virtuální síť s rozsahy adres podsítě pro výpočty, moduly hardwarového zabezpečení a brány. Následující příkazy slouží jako příklad co byste vytvořit virtuální síť.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
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
>Nejdůležitější konfigurace mějte na paměti pro virtuální síť je, že podsíť pro zařízení HSM musí mít delegování nastavena na "Microsoft.HardwareSecurityModules/dedicatedHSMs".  Zřizování modulu hardwarového zabezpečení nebude fungovat bez tato možnost nastavena.

Jakmile jsou všechny požadavky na místě, spusťte následující příkaz pro použití šablony Azure Resource Manageru zajistit aktualizaci hodnoty s jedinečnými názvy (nejméně název skupiny prostředků):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Toto nasazení zabere přibližně 25 – 30 minut pomocí hromadné této doby se zařízení HSM

![Stav zřizování](media/tutorial-deploy-hsm-cli/progress-status.png)

Po dokončení nasazení úspěšně "provisioningState": "Bylo dokončeno" se zobrazí. Můžete připojit k existující virtuální počítač a pomocí SSH k zajištění dostupnosti zařízení HSM.

## <a name="verifying-the-deployment"></a>Ověření nasazení

Pokud chcete ověřit zařízení se zřizují a naleznete v tématu atributy zařízení, spusťte následující sady příkazů. Ujistěte se nastaví skupinu prostředků a název prostředku je přesně tak, jak máte v souboru parametrů.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![Stav zřizování](media/tutorial-deploy-hsm-cli/progress-status2.png)

Nyní také moct zobrazit prostředky pomocí [Azure resource Exploreru](https://resources.azure.com/).   Jednou v Průzkumníku rozbalte "předplatné" na levé straně, rozšířit vaše konkrétní předplatné pro vyhrazené modulu hardwarového zabezpečení, rozbalte položku "skupiny prostředků", rozbalte skupinu prostředků, které jste použili a nakonec vyberte položku "resources".

## <a name="testing-the-deployment"></a>Testování nasazení

Testování nasazení se nestane, připojení k virtuálnímu počítači s přístupem HSM(s) a následným připojením přímo do modulu hardwarového zabezpečení zařízení. Tyto akce bude potvrďte, že modul hardwarového zabezpečení je dostupný.
Ssh nástroj se používá k připojení k virtuálnímu počítači. Bude podobný následujícímu příkazu, ale pomocí jména správce a název dns, který jste zadali v parametru.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

IP adresa virtuálního počítače může být zastoupen název DNS ve výše uvedeném příkazu. Pokud příkaz je úspěšná, zobrazí výzvu k zadání hesla a měli byste zadat, která. Po přihlášení k virtuálnímu počítači, můžete přihlásit k modulu hardwarového zabezpečení pomocí privátní IP adresy nalézt v portálu pro prostředek síťové rozhraní přidružené k modulu HSM.

![seznam součástí](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Všimněte si, že políčko "Zobrazit skryté typy", který pokud vybrané se zobrazit prostředky modulu hardwarového zabezpečení.

Na snímku obrazovky výše kliknutím na "HSM1_HSMnic" nebo "HSM2_HSMnic" zobrazí odpovídající privátní IP adresa. V opačném případě `az resource show` příkaz využité nad je způsob, jak určit správné IP adresy. 

Až budete mít správnou IP adresu, spusťte následující příkaz, kde nahraďte tuto adresu:

`ssh tenantadmin@10.0.2.4`

V případě úspěchu zobrazí výzva k zadání hesla. Výchozí heslo je heslo a modul hardwarového zabezpečení se zeptá, nejprve budete muset změnit heslo proto nastavte silné heslo a použít libovolné mechanismus, vaše organizace preferuje k uložení hesla a zabránit ztrátě.

>[!IMPORTANT]
>Pokud ztratíte heslo, bude nutné resetovat modul hardwarového zabezpečení a to znamená, že došlo ke ztrátě klíče.

Pokud jste připojeni k modulu hardwarového zabezpečení pomocí ssh, spusťte následující příkaz k zajištění, že modul hardwarového zabezpečení je funkční.

`hsm show`

Výstup by měl vypadat, jak je znázorněno na následujícím obrázku:

![seznam součástí](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

V tomto okamžiku jste přidělili všechny prostředky s vysokou dostupností, dvě nasazení modulu hardwarového zabezpečení a ověřeného přístupu a provozní stav. Další konfigurace nebo testování zahrnuje další práci samotného zařízení HSM. V takovém případě postupujte podle pokynů v kapitole Gemalto Luna sítě HSM 7 příručka věnovaná 7 k inicializaci modulu HSM a vytvoření oddílů. Všechny dokumentaci a softwarové jsou k dispozici přímo z Gemalto ke stažení, jakmile jsou registrované na portálu Gemalto Zákaznická podpora a mají ID zákazníka. Stáhněte klientský Software verze 7.2 zobrazíte všechny požadované součásti.

## <a name="delete-or-clean-up-resources"></a>Odstranit nebo vyčištění prostředků

Pokud budete hotovi s zařízení HSM, potom může být jako prostředek odstranit a vrátit do volného fondu. Ze zřejmých problém při tomto postupu je citlivá zákaznická data, která je na zařízení. K odebrání citlivých zákazníka by měl být data zařízení pomocí klienta Gemalto obnovit tovární nastavení. V příručce správce Gemalto 7 Luna SafeNet síťových zařízení a zvažte následující příkazy v pořadí.

1. `hsm factoryReset -f`
2. `sysconf config factoryReset -f -service all`
3. `network interface delete -device eth0`
4. `network interface delete -device eth1`
5. `network interface delete -device eth2`
6. `network interface delete -device eth3`
7. `my file clear -f`
8. `my public-key clear -f`
9. `syslog rotate`


>[!NOTE]
Pokud máte potíže s konfigurací zařízení jakékoli Gemalto měli byste požádat [Gemalto zákaznickou podporu](https://safenet.gemalto.com/technical-support/).


Pokud budete hotovi s prostředky v této skupině prostředků, můžete ho odebrat pomocí následujícího příkazu:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Další postup

Po dokončení kroků v tomto kurzu, jsou zřízené prostředky vyhrazené HSM a máte virtuální síť s nezbytné moduly hardwarového zabezpečení a další komponenty sítě k umožnění komunikace s modul hardwarového zabezpečení.  Teď jste na pozici pro toto nasazení s více prostředky podle potřeby podle preferované nasazení architektury návrzích. Další informace pomáhající při plánování nasazení najdete v dokumentech koncepty.
Návrh s použitím dva moduly hardwarového zabezpečení v primární oblasti adresování dostupnost na úrovni racku a dva moduly hardwarového zabezpečení v sekundární oblasti adresování regionální dostupnosti se doporučuje. Soubor šablony použité v tomto kurzu můžete jednoduše použít jako základ pro dvě nasazení modulu hardwarového zabezpečení, ale musí mít jeho parametry upravit tak, aby splňovaly vaše požadavky.

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)
