---
title: Naučte se auditovat obsah virtuálních počítačů
description: Zjistěte, jak zásady Azure používají agenta konfigurace hosta k auditování nastavení uvnitř virtuálních počítačů.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4a2989badc099a199bf21f7e020ca8e6256ddaf0
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113429"
---
# <a name="understand-azure-policys-guest-configuration"></a>Principy konfigurace hosta zásad Azure

Kromě auditování a [nápravy](../how-to/remediate-resources.md) prostředků Azure můžou Zásady Azure auditovat nastavení uvnitř počítače. Ověřování se provádí pomocí rozšíření Konfigurace hosta a prostřednictvím klienta. Toto rozšíření prostřednictvím klienta ověřuje nastavení, jako například:

- Konfigurace operačního systému
- Konfigurace nebo přítomnost aplikací
- Nastavení prostředí

V tuto chvíli většina zásad Azure Zásady konfigurace konfigurace pouze nastavení auditování uvnitř počítače. Nepoužívají konfigurace. Výjimkou je jedna předdefinovaná [zásada uvedená níže](#applying-configurations-using-guest-configuration).

## <a name="extension-and-client"></a>Rozšíření a klient

Chcete-li auditovat nastavení uvnitř počítače, je povoleno [rozšíření virtuálního počítače.](../../../virtual-machines/extensions/overview.md) Rozšíření stáhne příslušné přiřazení zásad a odpovídající definici konfigurace.

### <a name="limits-set-on-the-extension"></a>Limity nastavené na rozšíření

Chcete-li omezit rozšíření z dopadu na aplikace spuštěné uvnitř počítače, konfigurace hosta není povoleno překročit více než 5 % procesoru. Toto omezení existuje pro předdefinované i vlastní definice.

## <a name="register-guest-configuration-resource-provider"></a>Registrace zprostředkovatele prostředků konfigurace hosta

Před použitím konfigurace hosta je nutné zaregistrovat poskytovatele prostředků. Zaregistrovat se můžete prostřednictvím [portálu](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShellu](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)nebo [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli). Poskytovatel prostředků je registrován automaticky, pokud se přiřazení zásadkonfigurace hosta provádí prostřednictvím portálu.

## <a name="validation-tools"></a>Nástroje pro ověřování pravosti

V rámci počítače klient konfigurace hosta používá místní nástroje ke spuštění auditu.

V následující tabulce je uveden seznam místních nástrojů používaných v každém podporovaném operačním systému:

|Operační systém|Nástroj pro ověření pravosti|Poznámky|
|-|-|-|
|Windows|[Konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Šéfkuchař InSpec](https://www.chef.io/inspec/)| Pokud Ruby a Python nejsou v počítači, jsou nainstalovány rozšířením Konfigurace hosta. |

### <a name="validation-frequency"></a>Frekvence ověřování

Klient konfigurace hosta kontroluje nový obsah každých 5 minut. Po přijetí přiřazení hosta jsou nastavení pro tuto konfiguraci znovu zkontrolována v intervalu 15 minut.
Výsledky jsou odesílány poskytovateli prostředků konfigurace hosta po dokončení auditu. Dojde-li k [aktivační události vyhodnocení](../how-to/get-compliance-data.md#evaluation-triggers) zásad, stav počítače je zapsán a zprostředkovatele prostředků konfigurace hosta. Tato aktualizace způsobí, že zásady Azure vyhodnotit vlastnosti Azure Resource Manager. Vyhodnocení zásad Azure na vyžádání načte nejnovější hodnotu od poskytovatele prostředků konfigurace hosta. Však neaktivuje nový audit konfigurace v rámci počítače.

## <a name="supported-client-types"></a>Podporované typy klientů

V následující tabulce je uveden seznam podporovaných operačních systémů v ibi Azure:

|Vydavatel|Name (Název)|Verze|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Kredativ|Debian|8, 9|
|Microsoft|Windows Server|Datacentrum 2012, Datacentrum 2012 R2, Datacentrum 2016, Datacentrum 2019|
|Microsoft|Klient Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5, 7.6, 7.7|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6, 7.7|
|Suse|SLES|12 AKTUALIZACE SP3|

### <a name="unsupported-client-types"></a>Nepodporované typy klientů

Windows Server Nano Server není v žádné verzi podporován.

## <a name="guest-configuration-extension-network-requirements"></a>Požadavky na síť rozšíření konfigurace hosta

Ke komunikaci s poskytovatelem prostředků konfigurace hosta v Azure vyžadují počítače odchozí přístup k datovým centrům Azure na portu **443**. Pokud síť v Azure neumožňuje odchozí provoz, nakonfigurujte výjimky pomocí pravidel [skupiny zabezpečení sítě.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
[Značku služby](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" lze použít k odkazování na službu konfigurace hosta.

## <a name="azure-managed-identity-requirements"></a>Požadavky na spravovanou identitu Azure

**Zásady DeployIfNotExists,** které přidávají rozšíření do virtuálních počítačů, také umožňují systémpřiřazenou spravovanou identitu, pokud neexistuje.

> [!WARNING]
> Vyhněte se povolení uživatele přiřazené spravované identity virtuálním počítačům v oboru pro zásady, které umožňují systémpřiřazenou spravovanou identitu. Identita přiřazená uživateli bude nahrazena a může dojít k tomu, že počítač přestane reagovat.

## <a name="guest-configuration-definition-requirements"></a>Požadavky na definici konfigurace hosta

Každý audit spuštěný konfigurací hosta vyžaduje dvě definice zásad, **definici DeployIfNotExists** a **definici AuditIfNotExists.** 

Definice zásad **DeployIfNotExists** ověřuje a opravuje následující položky:

- Ověřte, že počítači byla přiřazena konfigurace k vyhodnocení. Pokud není aktuálně k dispozici žádné přiřazení, získejte přiřazení a připravte stroj podle:
  - Ověřování počítače pomocí spravované [identity](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalace nejnovější verze rozšíření **Microsoft.GuestConfiguration**
  - Instalace [ověřovacích nástrojů](#validation-tools) a závislostí, v případě potřeby

Pokud **deployifNotExists** přiřazení je nekompatibilní, [nápravná úloha](../how-to/remediate-resources.md#create-a-remediation-task) lze použít.

Jakmile **deployifNotExists** přiřazení je kompatibilní, **AuditIfNotExists** přiřazení zásad určuje, zda je přiřazení hosta kompatibilní nebo nekompatibilní. Ověřovací nástroj poskytuje výsledky klientovi konfigurace hosta. Klient předá výsledky rozšíření hosta, což je zpřístupňuje prostřednictvím zprostředkovatele prostředků konfigurace hosta.

Zásady Azure používají vlastnost **complianceStatus** poskytovatelů konfigurace hosta k ohlášení dodržování předpisů v uzlu **Dodržování předpisů.** Další informace naleznete v [tématu získávání dat dodržování předpisů](../how-to/get-compliance-data.md).

> [!NOTE]
> **Zásada DeployIfNotExists** je vyžadována pro zásadu **AuditIfNotExists** k vrácení výsledků. Bez **DeployIfNotExists**, **AuditIfNotExists zásady** zobrazuje "0 z 0" prostředky jako stav.

Všechny předdefinované zásady pro konfiguraci hosta jsou zahrnuty v iniciativě pro seskupení definic pro použití v přiřazeních. Integrovaná iniciativa s názvem _ \[Náhled\]: Auditování zabezpečení hesla v počítačích s Linuxem a Windows_ obsahuje 18 zásad. Existuje šest **DeployIfNotExists** a **AuditIfNotExists** dvojice pro Windows a tři páry pro Linux. Logika [definice zásad](definition-structure.md#policy-rule) ověřuje, zda je vyhodnocen pouze cílový operační systém.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditování nastavení operačního systému podle směrných plánů odvětví

Jedna iniciativa v Azure Policy poskytuje možnost auditovat nastavení operačního systému podle "směrného plánu". Definice, _ \[náhled\]: Auditovat virtuální počítače se systémem Windows, které neodpovídají nastavení směrného plánu zabezpečení Azure,_ zahrnuje sadu pravidel založených na zásadách skupiny služby Active Directory.

Většina nastavení je k dispozici jako parametry. Parametry umožňují přizpůsobit auditování. Srovnejte zásady s vašimi požadavky nebo namapujte zásady na informace třetích stran, jako jsou oborové regulační standardy.

Některé parametry podporují rozsah hodnot celé číslo. Například nastavení Maximální stáří hesla může auditovat efektivní nastavení zásad skupiny. Rozsah "1,70" by potvrdil, že uživatelé jsou povinni měnit svá hesla alespoň každých 70 dní, ale ne méně než jeden den.

Pokud přiřadíte zásadu pomocí šablony nasazení Azure Resource Manager, použijte soubor parametrů ke správě výjimek. Vrácení souborů se změnami do systému správy verzí, jako je Git. Komentáře ke změnám souborů poskytují důkazy o tom, proč je přiřazení výjimkou z očekávané hodnoty.

#### <a name="applying-configurations-using-guest-configuration"></a>Použití konfigurací pomocí konfigurace hosta

Nejnovější funkce Azure Policy konfiguruje nastavení uvnitř počítačů. Definice _Konfigurace časového pásma na počítačích se systémem Windows_ provádí změny v počítači konfigurací časového pásma.

Při přiřazování definic začínajících na _konfigurovat_je nutné také přiřadit požadavky na _nasazení definice, aby bylo možné povolit zásady konfigurace hosta na virtuálních počítačích se systémem Windows_. Tyto definice můžete kombinovat v iniciativě, pokud se rozhodnete.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Přiřazení zásad počítačům mimo Azure

Zásady auditu dostupné pro konfiguraci hosta zahrnují typ prostředku **Microsoft.HybridCompute/machines.** Všechny počítače na palubě [Azure Arc pro servery,](../../../azure-arc/servers/overview.md) které jsou v rámci přiřazení zásad jsou automaticky zahrnuty.

### <a name="multiple-assignments"></a>Více přiřazení

Zásady konfigurace hosta v současné době podporují přiřazení stejného přiřazení hosta pouze jednou za počítač, a to i v případě, že přiřazení zásad používá jiné parametry.

## <a name="client-log-files"></a>Soubory protokolu klienta

Rozšíření Konfigurace hosta zapisuje soubory protokolu do následujících umístění:

Windows:`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux:`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Kde `<version>` odkazuje na aktuální číslo verze.

### <a name="collecting-logs-remotely"></a>Vzdálené shromažďování protokolů

Prvním krokem při řešení potíží s konfiguracemi nebo moduly konfigurace hosta by mělo být použití `Test-GuestConfigurationPackage` rutiny podle kroků, jak vytvořit vlastní [zásady auditu konfigurace hosta pro systém Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Pokud to není úspěšné, shromažďování protokolů klientů může pomoci diagnostikovat problémy.

#### <a name="windows"></a>Windows

Zachyťte informace ze souborů protokolu pomocí [příkazu Azure VM Run Command](../../../virtual-machines/windows/run-command.md), může být užitečný následující příklad skriptu PowerShellu.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Zachyťte informace ze souborů protokolu pomocí [příkazu Azure VM Run Command](../../../virtual-machines/linux/run-command.md), může být užitečný následující příklad skriptu Bash.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Ukázky konfigurace hosta

Předdefinované ukázky zásad konfigurace hosta jsou k dispozici v následujících umístěních:

- [Předdefinované definice zásad – konfigurace hosta](../samples/built-in-policies.md#guest-configuration)
- [Vestavěné iniciativy – Konfigurace hostů](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy ukázky úložiště GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak zobrazit podrobnosti o jednotlivých nastaveních v [zobrazení dodržování předpisů Konfigurace hosta](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Projděte si příklady na [ukázkách zásad Azure](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Pochopit, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](../how-to/remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
