---
title: Informace o tom, jak auditovat obsah počítače
description: Přečtěte si, jak Azure Policy používá konfiguraci hosta k auditování nastavení v rámci počítače Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 06a767af71f457273e0e20d1248d64c22b3563e7
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274951"
---
# <a name="understand-azure-policys-guest-configuration"></a>Porozumět konfiguraci hosta Azure Policy

Kromě auditování a [Oprava](../how-to/remediate-resources.md) prostředků Azure může Azure Policy auditovat nastavení v rámci počítače. Ověření se provede tak, že rozšíření konfigurace hosta a klienta. Toto rozšíření prostřednictvím klienta, ověří nastavení jako konfigurace operačního systému, konfigurace aplikace nebo přítomnost, nastavení prostředí a další.

V současné době Azure Policy konfigurace hosta provede jenom audit nastavení v rámci počítače.
Konfigurace není ještě možné použít.

## <a name="extension-and-client"></a>Rozšíření a klienta

Pokud chcete auditovat nastavení v rámci počítače, je povolená [rozšíření virtuálního počítače](../../../virtual-machines/extensions/overview.md) . Rozšíření stahuje použitelné zásady přiřazení a odpovídající definici konfigurace.

### <a name="limits-set-on-the-extension"></a>Omezení nastavená pro rozšíření

Aby bylo rozšíření možné omezit proti ovlivnění aplikací spuštěných v počítači, může konfigurace hostů maximálně překročit 5% využití procesoru.
To platí jak pro konfigurace poskytované společností Microsoft jako "předdefinované", tak pro vlastní konfigurace vytvořené zákazníky.

## <a name="register-guest-configuration-resource-provider"></a>Registrace poskytovatele prostředků konfigurace hosta

Před použitím konfigurace hosta, zaregistrujte poskytovatele prostředků. Můžete zaregistrovat prostřednictvím portálu nebo pomocí Powershellu. Poskytovatel prostředků je zaregistrován automaticky, pokud je přiřazení zásady konfigurace hostů provedeno prostřednictvím portálu.

### <a name="registration---portal"></a>Registrace – portál

Registrace poskytovatele prostředků pro konfiguraci hostovaný na webu Azure portal, postupujte podle těchto kroků:

1. Spusťte na webu Azure portal a klikněte na **všechny služby**. Vyhledejte a vyberte **předplatná**.

1. Vyhledejte a klikněte na předplatné, které chcete povolit konfiguraci hosta pro.

1. V levé nabídce **předplatné** klikněte na **poskytovatelů prostředků**.

1. Filtrovat nebo můžete najít pomocí posuvníku **Microsoft.GuestConfiguration**, pak klikněte na tlačítko **zaregistrovat** na stejném řádku.

### <a name="registration---powershell"></a>Registrace – PowerShell

Zaregistrovat poskytovatele prostředků pro konfiguraci typu Host pomocí prostředí PowerShell, spusťte následující příkaz:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Nástroje pro ověření

V počítači používá klient konfigurace Host místní nástroje pro spuštění auditu.

V následující tabulce je seznam nástrojů pro místní použít na všech podporovaných operačních systémech:

|Operační systém|Nástroje pro ověření|Poznámky|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby a Python instaluje rozšíření konfigurace hosta. |

### <a name="validation-frequency"></a>Frekvence ověřování

Klient konfigurace hosta kontroluje nový obsah každých 5 minut. Po přijetí přiřazení hostů se nastavení kontroluje v intervalu 15 minut. Výsledky se odešlou do poskytovatele prostředků konfigurace hosta hned po dokončení auditu. Když dojde k [aktivaci vyhodnocení](../how-to/get-compliance-data.md#evaluation-triggers) zásad, stav počítače se zapíše do poskytovatele prostředků konfigurace hosta. To způsobí, Azure Policy vyhodnotit Azure Resource Manager vlastností. Vyhodnocení Azure Policy na vyžádání načte nejnovější hodnotu z poskytovatele prostředků konfigurace hosta. Neaktivuje ale nové auditování konfigurace v rámci počítače.

## <a name="supported-client-types"></a>Podporované klientské typy

Následující tabulka uvádí seznam podporovaný operační systém v imagích Azure:

|Vydavatel|Název|Verze|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Klient systému Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SuSE|SLES|12 SP3|

> [!IMPORTANT]
> Konfigurace hosta může auditovat uzly, na kterých běží podporovaný operační systém. Pokud chcete auditovat virtuální počítače, které používají vlastní image, je třeba duplikovat definici **DeployIfNotExists** a upravit část **if** tak, aby obsahovala vlastnosti obrázku.

### <a name="unsupported-client-types"></a>Nepodporované klientské typy

Windows Server nano Server se v žádné verzi nepodporuje.

## <a name="guest-configuration-extension-network-requirements"></a>Síťové požadavky rozšíření konfigurace hosta

Aby počítače komunikovaly s poskytovatelem prostředků konfigurace hosta v Azure, vyžadují odchozí přístup k datacentrům Azure na portu **443**. Pokud používáte privátní virtuální síť v Azure a nepovolujete odchozí přenosy, je nutné nakonfigurovat výjimky pomocí pravidel [skupiny zabezpečení sítě](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . V tuto chvíli neexistuje značka služby pro Azure Policy konfiguraci hostů.

U seznamů IP adres můžete stáhnout [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor se aktualizuje týdně a má aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres. V oblastech, ve kterých jsou nasazené vaše virtuální počítače, stačí jenom udělit odchozí přístup k IP adresám.

> [!NOTE]
> Soubor XML IP adresy datacentra Azure obsahuje seznam rozsahů IP adres, které se používají v datových centrech Microsoft Azure. Soubor zahrnuje výpočetní prostředky, SQL a rozsahy úložiště. Aktualizovaný soubor je zveřejněný týdně. Tento soubor odráží aktuálně nasazené rozsahy a všechny nadcházející změny v rozsahu IP adres. Nové rozsahy, které se zobrazí v souboru, se v datových centrech nepoužijí aspoň na jeden týden. Každý týden je vhodné stáhnout nový soubor XML. Pak aktualizujte svůj web tak, aby správně identifikoval služby běžící v Azure. Uživatelé Azure ExpressRoute by si měli všimnout, že se tento soubor používá k aktualizaci inzerce protokolu BGP (Border Gateway Protocol) v Azure Space v první týden v měsíci.

## <a name="guest-configuration-definition-requirements"></a>Požadavky na konfiguraci hosta definice

Každý audit spouštěný pomocí konfigurace hosta vyžaduje dvě definice zásad, definici **DeployIfNotExists** a definici **AuditIfNotExists** . Definice **DeployIfNotExists** slouží k přípravě počítače s agentem konfigurace hosta a dalšími komponentami pro podporu [ověřovacích nástrojů](#validation-tools).

**DeployIfNotExists** definici zásad ověří a řeší následující položky:

- Ověřte, že počítač má přiřazenou konfiguraci k vyhodnocení. Pokud aktuálně není k dispozici žádné přiřazení, načtěte přiřazení a připravte počítač podle:
  - Ověřování na počítači pomocí [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalace nejnovější verze **Microsoft.GuestConfiguration** rozšíření
  - Instalace [ověřovacích nástrojů](#validation-tools) a závislostí, v případě potřeby

Pokud přiřazení **DeployIfNotExists** nedodržuje předpisy, lze použít [úlohu nápravy](../how-to/remediate-resources.md#create-a-remediation-task) .

Jakmile je přiřazení **DeployIfNotExists** kompatibilní, přiřazení zásad **AuditIfNotExists** pomocí místních ověřovacích nástrojů určí, jestli je přiřazení konfigurace kompatibilní nebo nekompatibilní.
Nástroj ověření poskytuje výsledky klientovi Configuration hosta. Klient předává výsledky hosta rozšíření, které zpřístupní je prostřednictvím poskytovatele prostředků konfigurace hosta.

Služba Azure Policy používá poskytovatele prostředků hosta konfigurace **complianceStatus** vlastností na sestavu dodržování předpisů v **dodržování předpisů** uzlu. Další informace najdete v tématu [získávají data dodržování předpisů](../how-to/getting-compliance-data.md).

> [!NOTE]
> Zásady **DeployIfNotExists** se vyžadují, aby zásady **AuditIfNotExists** vracely výsledky.
> Bez **DeployIfNotExists**se v zásadách **AuditIfNotExists** zobrazuje "0 z 0" prostředků jako stav.

Všechny integrované zásady pro konfiguraci hosta jsou součástí iniciativy do definice pro použití v přiřazení skupiny. Integrovaná iniciativa s názvem *[Preview]: Auditování nastavení zabezpečení hesla v počítačích* se systémy Linux a Windows obsahuje 18 zásad. Obsahuje šest **DeployIfNotExists** a **AuditIfNotExists** dvojice pro Windows a tři páry pro Linux. V každém případě logika uvnitř definice ověří pouze cílový operační systém se vyhodnocuje na základě [pravidlo zásad](definition-structure.md#policy-rule) definice.

### <a name="multiple-assignments"></a>Více přiřazení

Zásady konfigurace hosta momentálně podporují přiřazování stejného přiřazení hostů jenom jednou pro každý počítač, a to i v případě, že přiřazení zásady používá jiné parametry.

## <a name="built-in-resource-modules"></a>Předdefinované moduly prostředků

Po instalaci rozšíření konfigurace hosta je modul PowerShellu GuestConfiguration zahrnutý v nejnovější verzi modulů prostředků DSC. Tento modul se dá stáhnout z Galerie prostředí PowerShell pomocí odkazu ruční stažení ze stránky modulu [GuestConfiguration/](https://www.powershellgallery.com/packages/GuestConfiguration/).
Formát souboru. nupkg se dá přejmenovat na. zip, aby se dal dekomprimovat a zkontrolovat.

## <a name="client-log-files"></a>Soubory protokolů klienta

Rozšíření konfigurace hosta zapisuje soubory protokolu do následujících umístění:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Kde `<version>` odkazuje na aktuální číslo verze.

### <a name="collecting-logs-remotely"></a>Vzdálené shromažďování protokolů

Prvním krokem při řešení potíží s konfiguracemi konfigurace hostů nebo moduly by měl být `Test-GuestConfigurationPackage` použití rutiny podle kroků v části [test konfiguračního balíčku hosta](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).  Pokud se to nepodaří, můžou se shromažďovat klientské protokoly, které vám pomůžou diagnostikovat problémy.

#### <a name="windows"></a>Windows

Pokud chcete k zachycení informací ze souborů protokolů v počítačích s Windows použít příkaz spuštění virtuálního počítače Azure, může být užitečné následující ukázkový skript PowerShellu. Podrobnosti o spuštění skriptu z webu Azure Portal nebo použití Azure PowerShell najdete v tématu [spuštění skriptů PowerShellu na virtuálním počítači s Windows pomocí příkazu Spustit](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Pokud chcete pro zachycení informací ze souborů protokolů na počítačích se systémem Linux použít funkci příkazu spuštění virtuálního počítače Azure, může být užitečný následující ukázkový skript bash. Podrobnosti o spuštění skriptu z webu Azure Portal nebo pomocí rozhraní příkazového řádku Azure najdete v tématu [spuštění skriptů prostředí na virtuálním počítači se systémem Linux pomocí příkazu Run](../../../virtual-machines/linux/run-command.md) .

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Ukázky konfigurace hosta

Ukázky konfigurace hosta zásad jsou k dispozici v následujících umístěních:

- [Rejstřík ukázek – konfigurace hostů](../samples/index.md#guest-configuration)
- [Azure Policy ukázky úložiště GitHubu](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Další postup

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/getting-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/index.md).
