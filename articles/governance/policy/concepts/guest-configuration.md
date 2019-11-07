---
title: Informace o tom, jak auditovat obsah virtuálních počítačů
description: Přečtěte si, jak Azure Policy používá konfiguraci hosta k auditování nastavení v rámci počítače Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 0e5592f629646db3132ffd65fd56b1a0d5d5be39
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581427"
---
# <a name="understand-azure-policys-guest-configuration"></a>Vysvětlení konfigurace hosta Azure Policy

Kromě auditování a [Oprava](../how-to/remediate-resources.md) prostředků Azure může Azure Policy auditovat nastavení v rámci počítače. Ověřování se provádí pomocí rozšíření Konfigurace hosta a prostřednictvím klienta. Toto rozšíření prostřednictvím klienta ověřuje nastavení, jako například:

- Konfigurace operačního systému
- Konfigurace nebo přítomnost aplikací
- Nastavení prostředí

Konfigurace hosta Azure Policy momentálně jenom audituje nastavení uvnitř počítače. Neaplikuje konfigurace.

## <a name="extension-and-client"></a>Rozšíření a klient

Pokud chcete auditovat nastavení v rámci počítače, je povolená [rozšíření virtuálního počítače](../../../virtual-machines/extensions/overview.md) . Rozšíření stáhne příslušné přiřazení zásad a odpovídající definici konfigurace.

### <a name="limits-set-on-the-extension"></a>Omezení nastavená pro rozšíření

Chcete-li omezit rozšíření z vlivu na aplikace běžící v počítači, konfigurace hosta nemůže překročit více než 5% využití procesoru. Toto omezení existuje jak pro předdefinované, tak pro vlastní definice.

## <a name="register-guest-configuration-resource-provider"></a>Registrovat poskytovatele prostředků konfigurace hosta

Než budete moct použít konfiguraci hosta, musíte zaregistrovat poskytovatele prostředků. Můžete provést registraci prostřednictvím portálu nebo pomocí PowerShellu. Poskytovatel prostředků je zaregistrován automaticky, pokud je přiřazení zásady konfigurace hostů provedeno prostřednictvím portálu.

### <a name="registration---portal"></a>Registrace – portál

Chcete-li zaregistrovat poskytovatele prostředků pro konfiguraci hostů prostřednictvím Azure Portal, postupujte takto:

1. Spusťte Azure Portal a klikněte na **všechny služby**. Vyhledejte a vyberte **odběry**.

1. Vyhledejte a klikněte na předplatné, pro které chcete povolit konfiguraci hosta.

1. V levé nabídce stránky **předplatné** klikněte na **poskytovatelé prostředků**.

1. Vyfiltrujte nebo přejděte do umístění **Microsoft. GuestConfiguration**a potom klikněte na **zaregistrovat** na stejném řádku.

### <a name="registration---powershell"></a>Registrace – PowerShell

Pokud chcete zaregistrovat poskytovatele prostředků pro konfiguraci hosta prostřednictvím PowerShellu, spusťte následující příkaz:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Nástroje ověřování

V počítači používá klient konfigurace Host místní nástroje pro spuštění auditu.

V následující tabulce je uveden seznam místních nástrojů používaných pro každý podporovaný operační systém:

|Operační systém|Nástroj pro ověření|Poznámky|
|-|-|-|
|Windows|[Konfigurace požadovaného stavu společnosti Microsoft](/powershell/dsc) v2| |
|Linux|[Nespec](https://www.chef.io/inspec/)| Ruby a Python jsou nainstalovány pomocí rozšíření konfigurace hosta. |

### <a name="validation-frequency"></a>Frekvence ověřování

Klient konfigurace hosta kontroluje nový obsah každých 5 minut. Po přijetí přiřazení hostů se nastavení kontroluje v intervalu 15 minut. Výsledky se odešlou do poskytovatele prostředků konfigurace hosta hned po dokončení auditu. Když dojde k [aktivaci vyhodnocení](../how-to/get-compliance-data.md#evaluation-triggers) zásad, stav počítače se zapíše do poskytovatele prostředků konfigurace hosta. Tato aktualizace způsobí Azure Policy vyhodnocení vlastností Azure Resource Manager. Vyhodnocení Azure Policy na vyžádání načte nejnovější hodnotu z poskytovatele prostředků konfigurace hosta. Neaktivuje ale nové auditování konfigurace v rámci počítače.

## <a name="supported-client-types"></a>Podporované typy klientů

V následující tabulce je uveden seznam podporovaných operačních systémů na obrázcích Azure:

|Vydavatel|Název|Verze|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Klient Windows|Windows 10|
|OpenLogic|CentOS|7,3, 7,4, 7,5|
|Red Hat|Red Hat Enterprise Linux|7,4, 7,5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> Konfigurace hosta může auditovat uzly, na kterých běží podporovaný operační systém. Pokud chcete auditovat virtuální počítače, které používají vlastní image, je třeba duplikovat definici **DeployIfNotExists** a upravit část **if** tak, aby obsahovala vlastnosti obrázku.

### <a name="unsupported-client-types"></a>Nepodporované typy klientů

Windows Server nano Server se v žádné verzi nepodporuje.

## <a name="guest-configuration-extension-network-requirements"></a>Síťové požadavky rozšíření konfigurace hosta

Aby počítače komunikovaly s poskytovatelem prostředků konfigurace hosta v Azure, vyžadují odchozí přístup k datacentrům Azure na portu **443**. Pokud používáte privátní virtuální síť v Azure, která neumožňuje odchozí přenosy, nakonfigurujte výjimky s pravidly [skupiny zabezpečení sítě](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . Značka služby v tuto chvíli pro Azure Policy konfiguraci hostů neexistuje.

U seznamů IP adres můžete stáhnout [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor se aktualizuje týdně a má aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres. V oblastech, ve kterých jsou nasazené vaše virtuální počítače, stačí jenom udělit odchozí přístup k IP adresám.

> [!NOTE]
> Soubor XML IP adresy datacentra Azure obsahuje seznam rozsahů IP adres, které se používají v datových centrech Microsoft Azure. Soubor zahrnuje výpočetní prostředky, SQL a rozsahy úložiště. Aktualizovaný soubor je zveřejněný týdně. Tento soubor odráží aktuálně nasazené rozsahy a všechny nadcházející změny v rozsahu IP adres. Nové rozsahy, které se zobrazí v souboru, se v datových centrech nepoužijí aspoň na jeden týden. Každý týden je vhodné stáhnout nový soubor XML. Pak aktualizujte svůj web tak, aby správně identifikoval služby běžící v Azure. Uživatelé Azure ExpressRoute by si měli všimnout, že se tento soubor používá k aktualizaci inzerce protokolu BGP (Border Gateway Protocol) v Azure Space v první týden v měsíci.

## <a name="guest-configuration-definition-requirements"></a>Požadavky na definici konfigurace hosta

Každý audit spouštěný pomocí konfigurace hosta vyžaduje dvě definice zásad, definici **DeployIfNotExists** a definici **AuditIfNotExists** . Definice **DeployIfNotExists** slouží k přípravě počítače s agentem konfigurace hosta a dalšími komponentami pro podporu [ověřovacích nástrojů](#validation-tools).

Definice zásad **DeployIfNotExists** ověří a opraví následující položky:

- Ověřte, že počítač má přiřazenou konfiguraci k vyhodnocení. Pokud aktuálně není k dispozici žádné přiřazení, načtěte přiřazení a připravte počítač podle:
  - Ověřování na počítači pomocí [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalace nejnovější verze rozšíření **Microsoft. GuestConfiguration**
  - Instalace [ověřovacích nástrojů](#validation-tools) a závislostí, pokud je to potřeba

Pokud přiřazení **DeployIfNotExists** nedodržuje předpisy, lze použít [úlohu nápravy](../how-to/remediate-resources.md#create-a-remediation-task) .

Jakmile je přiřazení **DeployIfNotExists** kompatibilní, přiřazení zásad **AuditIfNotExists** pomocí místních ověřovacích nástrojů určí, jestli je přiřazení konfigurace kompatibilní nebo nekompatibilní. Nástroj pro ověření poskytuje výsledky pro klienta konfigurace hosta. Klient předává výsledky do rozšíření hosta, které je zpřístupní prostřednictvím poskytovatele prostředků konfigurace hosta.

Azure Policy používá k hlášení dodržování předpisů v uzlu **dodržování** předpisů vlastnost poskytovatelů prostředků konfigurace hosta ( **complianceStatus** ). Další informace najdete v tématu [získání dat o dodržování předpisů](../how-to/getting-compliance-data.md).

> [!NOTE]
> Zásady **DeployIfNotExists** se vyžadují, aby zásady **AuditIfNotExists** vracely výsledky. Bez **DeployIfNotExists**se v zásadách **AuditIfNotExists** zobrazuje "0 z 0" prostředků jako stav.

V iniciativě jsou zahrnuty všechny předdefinované zásady pro konfiguraci hosta, aby bylo možné seskupit definice pro použití v přiřazeních. Integrovaná iniciativa s názvem _\[Preview\]: Auditovat nastavení zabezpečení hesla v počítačích se systémy Linux a Windows_ obsahuje 18 zásad. Pro systém Linux existuje šest párů **DeployIfNotExists** a **AuditIfNotExists** pro Windows a tři páry. Logika [definice zásad](definition-structure.md#policy-rule) ověřuje, zda je vyhodnocen pouze cílový operační systém.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditování nastavení operačního systému po oborových plánech

Jedna z iniciativ, které jsou k dispozici v Azure Policy, poskytuje možnost auditování nastavení operačního systému v rámci virtuálních počítačů, které jsou uvedené na základě směrného plánu od Microsoftu. Definice, _\[Preview\]: Auditovat virtuální počítače s Windows, které neodpovídají nastavení základní hodnoty zabezpečení Azure,_ zahrnuje úplnou sadu pravidel auditu na základě nastavení ze služby Active Directory Zásady skupiny.

Většina nastavení je k dispozici jako parametry. Tato funkce umožňuje přizpůsobit, co je auditováno pro vyrovnání zásad podle požadavků vaší organizace, nebo k namapování zásad na informace třetích stran, jako jsou například oborové zákonné standardy.

Některé parametry podporují rozsah celočíselných hodnot. Například parametr maximální stáří hesla lze nastavit pomocí operátoru rozsahu, který umožní flexibilitu vlastníkům počítačů. Mohli byste auditovat, že platné Zásady skupiny nastavení, které vyžaduje, aby uživatelé změnili hesla, nesmí být delší než 70 dní, ale neměla by být kratší než jeden den. Jak je popsáno v části info-bubline pro parametr, pokud chcete, aby tyto obchodní zásady byly efektivní hodnotou auditu, nastavte hodnotu na 1, 70.

Pokud přiřadíte zásadu pomocí šablony nasazení Azure Resource Manager, můžete použít soubor parametrů ke správě těchto nastavení ze správy zdrojového kódu. Pomocí nástroje, jako je třeba Git, můžete spravovat změny zásad auditu s komentáři u jednotlivých dokumentů vrácení se změnami, protože přiřazení by mělo být výjimky na očekávanou hodnotu.

#### <a name="applying-configurations-using-guest-configuration"></a>Použití konfigurace pomocí konfigurace hosta

Nejnovější funkce Azure Policy konfiguruje nastavení v počítačích. Definice _nastaví časové pásmo na počítačích s Windows_ a provede změny v počítači konfigurací časového pásma.

Při přiřazování definic, které začínají na _Konfigurovat_, musíte také přiřadit _předpoklady nasazení definice a povolit zásadu konfigurace hosta na virtuálních počítačích s Windows_. V případě, že se rozhodnete, můžete tyto definice kombinovat v iniciativě.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Přiřazování zásad do počítačů mimo Azure

Zásady auditu, které jsou k dispozici pro konfiguraci hosta, zahrnují typ prostředku **Microsoft. HybridCompute/počítače** . Všechny počítače připojené ke [službě Azure ARC pro servery](../../../azure-arc/servers/overview.md) , které jsou v oboru přiřazení zásad, jsou automaticky zahrnuté.

### <a name="multiple-assignments"></a>Více přiřazení

Zásady konfigurace hosta momentálně podporují přiřazování stejného přiřazení hostů jenom jednou pro každý počítač, a to i v případě, že přiřazení zásady používá jiné parametry.

## <a name="built-in-resource-modules"></a>Předdefinované moduly prostředků

Při instalaci rozšíření konfigurace hosta je modul PowerShellu GuestConfiguration zahrnutý v nejnovější verzi modulů prostředků DSC. Tento modul se dá stáhnout z Galerie prostředí PowerShell pomocí odkazu ruční stažení ze stránky modulu [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). Formát souboru. nupkg se dá přejmenovat na. zip, aby se dal dekomprimovat a zkontrolovat.

## <a name="client-log-files"></a>Soubory protokolů klienta

Rozšíření konfigurace hosta zapisuje soubory protokolu do následujících umístění:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Kde `<version>` odkazuje na aktuální číslo verze.

### <a name="collecting-logs-remotely"></a>Vzdálené shromažďování protokolů

Prvním krokem při řešení potíží s konfiguracemi konfigurace hostů nebo moduly by měly být používat rutinu `Test-GuestConfigurationPackage` podle kroků v části [test konfiguračního balíčku hosta](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
Pokud to neproběhne úspěšně, může shromažďování protokolů klienta pomáhat s diagnostikou problémů.

#### <a name="windows"></a>Windows

Pokud chcete k zachycení informací ze souborů protokolů v počítačích s Windows použít funkci příkazu spuštění virtuálního počítače Azure, může být užitečné následující ukázkový skript PowerShellu. Další informace najdete v tématu [spuštění skriptů PowerShellu na virtuálním počítači s Windows pomocí příkazu Spustit](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Pokud chcete k zachycení informací ze souborů protokolů v počítačích se systémem Linux použít příkaz spuštění virtuálního počítače Azure, může být užitečný následující ukázkový skript bash. Další informace najdete v tématu [spuštění skriptů prostředí ve virtuálním počítači se systémem Linux pomocí příkazu Run](../../../virtual-machines/linux/run-command.md) .

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Ukázky konfigurace hosta

Ukázky konfigurace hosta zásad jsou k dispozici v následujících umístěních:

- [Rejstřík ukázek – konfigurace hostů](../samples/index.md#guest-configuration)
- [Azure Policy Samples – úložiště GitHub](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/getting-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
