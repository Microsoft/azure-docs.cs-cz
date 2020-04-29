---
title: Informace o tom, jak auditovat obsah virtuálních počítačů
description: Přečtěte si, jak Azure Policy používá agenta konfigurace hosta k auditování nastavení v rámci virtuálních počítačů.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 89f7cc3931971d70b441490f77b67ace89434c2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025216"
---
# <a name="understand-azure-policys-guest-configuration"></a>Vysvětlení konfigurace hosta Azure Policy

Kromě auditování a [Oprava](../how-to/remediate-resources.md) prostředků Azure může Azure Policy auditovat nastavení v rámci počítače. Ověřování se provádí pomocí rozšíření Konfigurace hosta a prostřednictvím klienta. Toto rozšíření prostřednictvím klienta ověřuje nastavení, jako například:

- Konfigurace operačního systému
- Konfigurace nebo přítomnost aplikací
- Nastavení prostředí

V tuto chvíli většina zásad konfigurace hosta Azure Policy jenom auditovat nastavení v rámci počítače. Nepoužívají konfigurace. Výjimkou je jedna integrovaná zásada, na [kterou se odkazuje níže](#applying-configurations-using-guest-configuration).

## <a name="resource-provider"></a>Poskytovatel prostředků

Než budete moct použít konfiguraci hosta, musíte zaregistrovat poskytovatele prostředků. Poskytovatel prostředků je zaregistrován automaticky, pokud je přiřazení zásady konfigurace hostů provedeno prostřednictvím portálu. Můžete provést ruční registraci prostřednictvím [portálu](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)nebo rozhraní příkazového [řádku Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="extension-and-client"></a>Rozšíření a klient

Pokud chcete auditovat nastavení v rámci počítače, je povolená [rozšíření virtuálního počítače](../../../virtual-machines/extensions/overview.md) . Rozšíření stáhne příslušné přiřazení zásad a odpovídající definici konfigurace.

> [!Important]
> K provádění auditů na virtuálních počítačích Azure se vyžaduje rozšíření konfigurace hosta.
> Pokud chcete nasadit rozšíření ve velkém měřítku, přiřaďte následující definice zásad:
>   - [Nasaďte požadavky pro povolení zásad konfigurace hostů na virtuálních počítačích s Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
>   - [Nasaďte požadavky pro povolení zásad konfigurace hostů na virtuálních počítačích se systémem Linux.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

### <a name="limits-set-on-the-extension"></a>Omezení nastavená pro rozšíření

Pokud chcete omezit rozšíření proti ovlivnění aplikací spuštěných v počítači, konfigurace hosta nemůže překročit více než 5% procesoru. Toto omezení existuje jak pro předdefinované, tak pro vlastní definice.

### <a name="validation-tools"></a>Nástroje ověřování

V počítači používá klient konfigurace Host místní nástroje pro spuštění auditu.

V následující tabulce je uveden seznam místních nástrojů používaných pro každý podporovaný operační systém:

|Operační systém|Nástroj pro ověření|Poznámky|
|-|-|-|
|Windows|[Konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Nespec](https://www.chef.io/inspec/)| Pokud Ruby a Python nejsou v počítači, jsou nainstalovány pomocí rozšíření konfigurace hosta. |

### <a name="validation-frequency"></a>Frekvence ověřování

Klient konfigurace hosta kontroluje nový obsah každých 5 minut. Po přijetí přiřazení hostů se nastavení této konfigurace znovu zkontrolují v intervalu 15 minut.
Výsledky se odešlou do poskytovatele prostředků konfigurace hosta, až se audit dokončí. Když dojde k [aktivaci vyhodnocení](../how-to/get-compliance-data.md#evaluation-triggers) zásad, stav počítače se zapíše do poskytovatele prostředků konfigurace hosta. Tato aktualizace způsobí Azure Policy vyhodnocení vlastností Azure Resource Manager. Vyhodnocení Azure Policy na vyžádání načte nejnovější hodnotu z poskytovatele prostředků konfigurace hosta. Neaktivuje ale nové auditování konfigurace v rámci počítače.

## <a name="supported-client-types"></a>Podporované typy klientů

Zásady konfigurace hosta jsou zahrnuté do nových verzí. Starší verze operačních systémů, které jsou k dispozici na webu Azure Marketplace, jsou vyloučené, pokud není agent konfigurace hosta kompatibilní. Následující tabulka obsahuje seznam podporovaných operačních systémů pro Image Azure:

|Vydavatel|Název|Verze|
|-|-|-|
|Canonical|Ubuntu Server|14,04 a novější|
|Credativ|Debian|8 a novější|
|Microsoft|Windows Server|2012 a novější|
|Microsoft|Klient Windows|Windows 10|
|OpenLogic|CentOS|7,3 a novější|
|Red Hat|Red Hat Enterprise Linux|7,4 a novější|
|SUSE|SLES|12 SP3 a novější|

Vlastní image virtuálních počítačů jsou podporovány zásadami konfigurace hosta, pokud se jedná o jeden z operačních systémů uvedených v tabulce výše.

### <a name="unsupported-client-types"></a>Nepodporované typy klientů

Windows Server nano Server se v žádné verzi nepodporuje.

## <a name="guest-configuration-extension-network-requirements"></a>Síťové požadavky rozšíření konfigurace hosta

Aby počítače komunikovaly s poskytovatelem prostředků konfigurace hosta v Azure, vyžadují odchozí přístup k datacentrům Azure na portu **443**. Pokud síť v Azure nepovoluje odchozí přenosy, nakonfigurujte výjimky s pravidly [skupiny zabezpečení sítě](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) .
[Označení služby](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" lze použít k odkazování na službu konfigurace hosta.

## <a name="azure-managed-identity-requirements"></a>Požadavky na spravovanou identitu Azure

Zásady **DeployIfNotExists** , které přidávají rozšíření do virtuálních počítačů, také povolují spravovanou identitu přiřazenou systémem, pokud taková neexistuje.

> [!WARNING]
> Vyhněte se povolení spravované identity přiřazené uživateli pro virtuální počítače v oboru pro zásady, které povolují spravovanou identitu přiřazenou systémem. Identita přiřazená uživatelem se nahradí a může způsobit, že počítač přestane reagovat.

## <a name="guest-configuration-definition-requirements"></a>Požadavky na definici konfigurace hosta

Každý audit spouštěný pomocí konfigurace hosta vyžaduje dvě definice zásad, definici **DeployIfNotExists** a definici **AuditIfNotExists** . 

Definice zásad **DeployIfNotExists** ověří a opraví následující položky:

- Ověřte, že počítač má přiřazenou konfiguraci k vyhodnocení. Pokud aktuálně není k dispozici žádné přiřazení, načtěte přiřazení a připravte počítač podle:
  - Ověřování na počítači pomocí [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalace nejnovější verze rozšíření **Microsoft. GuestConfiguration**
  - Instalace [ověřovacích nástrojů](#validation-tools) a závislostí, pokud je to potřeba

Pokud přiřazení **DeployIfNotExists** nedodržuje předpisy, lze použít [úlohu nápravy](../how-to/remediate-resources.md#create-a-remediation-task) .

Jakmile je přiřazení **DeployIfNotExists** kompatibilní, přiřazení zásady **AuditIfNotExists** určí, jestli je přiřazení hostů kompatibilní nebo nekompatibilní. Nástroj pro ověření poskytuje výsledky pro klienta konfigurace hosta. Klient předává výsledky do rozšíření hosta, které je zpřístupní prostřednictvím poskytovatele prostředků konfigurace hosta.

Azure Policy používá k hlášení dodržování předpisů v uzlu **dodržování** předpisů vlastnost poskytovatelů prostředků konfigurace hosta ( **complianceStatus** ). Další informace najdete v tématu [získání dat o dodržování předpisů](../how-to/get-compliance-data.md).

> [!NOTE]
> Zásady **DeployIfNotExists** se vyžadují, aby zásady **AuditIfNotExists** vracely výsledky. Bez **DeployIfNotExists**se v zásadách **AuditIfNotExists** zobrazuje "0 z 0" prostředků jako stav.

V iniciativě jsou zahrnuty všechny předdefinované zásady pro konfiguraci hosta, aby bylo možné seskupit definice pro použití v přiřazeních. Integrovaná iniciativa s názvem _ \[Preview\]: audit zabezpečení hesel uvnitř počítačů se systémy Linux a Windows_ obsahuje 18 zásad. Pro systém Linux existuje šest párů **DeployIfNotExists** a **AuditIfNotExists** pro Windows a tři páry. Logika [definice zásad](definition-structure.md#policy-rule) ověřuje, zda je vyhodnocen pouze cílový operační systém.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditování nastavení operačního systému po oborových plánech

Jedna z iniciativ v Azure Policy poskytuje možnost auditu nastavení operačního systému podle směrného plánu. Definice, _ \[verze Preview\]: Auditovat virtuální počítače s Windows, které neodpovídají nastavení základní hodnoty zabezpečení Azure,_ zahrnuje sadu pravidel založených na zásady skupiny Active Directory.

Většina nastavení je k dispozici jako parametry. Parametry umožňují přizpůsobit, co je auditováno. Zarovnejte zásady s vašimi požadavky nebo namapujte zásady na informace třetích stran, jako jsou například oborový zákon o standardech.

Některé parametry podporují rozsah celočíselných hodnot. Například nastavení maximálního stáří hesla může auditovat platné nastavení Zásady skupiny. Rozsah "1; 70" by potvrdil, že uživatelé musí měnit hesla alespoň každých 70 dní, ale ne méně než jeden den.

Pokud přiřadíte zásadu pomocí šablony nasazení Azure Resource Manager, použijte k řízení výjimek soubor parametrů. Soubory vraťte se změnami do systému správy verzí, jako je třeba Git. Komentáře k změnám souborů poskytují důkaz o tom, proč je přiřazení výjimkou očekávané hodnoty.

#### <a name="applying-configurations-using-guest-configuration"></a>Použití konfigurace pomocí konfigurace hosta

Nejnovější funkce Azure Policy konfiguruje nastavení v počítačích. Definice _nastaví časové pásmo na počítačích s Windows_ a provede změny v počítači konfigurací časového pásma.

Při přiřazování definic, které začínají na _Konfigurovat_, musíte také přiřadit _předpoklady nasazení definice a povolit zásadu konfigurace hosta na virtuálních počítačích s Windows_. V případě, že se rozhodnete, můžete tyto definice kombinovat v iniciativě.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Přiřazování zásad do počítačů mimo Azure

Zásady auditu, které jsou k dispozici pro konfiguraci hosta, zahrnují typ prostředku **Microsoft. HybridCompute/počítače** . Všechny počítače připojené ke [službě Azure ARC pro servery](../../../azure-arc/servers/overview.md) , které jsou v oboru přiřazení zásad, jsou automaticky zahrnuté.

### <a name="multiple-assignments"></a>Více přiřazení

Zásady konfigurace hosta momentálně podporují přiřazování stejného přiřazení hostů jenom jednou pro každý počítač, a to i v případě, že přiřazení zásady používá jiné parametry.

## <a name="client-log-files"></a>Soubory protokolů klienta

Rozšíření konfigurace hosta zapisuje soubory protokolu do následujících umístění:

Systému`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Kde `<version>` odkazuje na aktuální číslo verze.

### <a name="collecting-logs-remotely"></a>Vzdálené shromažďování protokolů

Prvním krokem při řešení potíží s konfiguracemi konfigurace hosta nebo moduly by se `Test-GuestConfigurationPackage` měly používat rutiny podle kroků, jak [vytvořit vlastní zásady auditu konfigurace hostů pro Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Pokud to neproběhne úspěšně, může shromažďování protokolů klienta pomáhat s diagnostikou problémů.

#### <a name="windows"></a>Windows

Pomocí [příkazu spustit pro virtuální počítače Azure](../../../virtual-machines/windows/run-command.md)Zachyťte informace ze souborů protokolů, což může být užitečné v následujícím ukázkovém skriptu PowerShellu.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Pomocí [příkazu spustit pro virtuální počítače Azure](../../../virtual-machines/linux/run-command.md)Zachyťte informace ze souborů protokolů, což může být užitečné v následujícím ukázkovém skriptu bash.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Ukázky konfigurace hosta

Ukázky integrovaných zásad konfigurace hosta jsou k dispozici v následujících umístěních:

- [Předdefinované definice zásad – konfigurace hostů](../samples/built-in-policies.md#guest-configuration)
- [Integrované iniciativy – konfigurace hostů](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy Samples – úložiště GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Další kroky

- Podívejte se, jak zobrazit podrobnosti jednotlivých nastavení ze [zobrazení dodržování předpisů konfigurace hostů.](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
