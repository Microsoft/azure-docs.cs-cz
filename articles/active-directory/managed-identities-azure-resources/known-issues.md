---
title: Nejčastější dotazy a známé problémy s spravovaných identit pro prostředky Azure
description: Známé problémy s spravovaných identit pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: a23bd99b05184316ee1547ab0917547b5d0da18f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421925"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Nejčastější dotazy a známé problémy s spravovaných identit pro prostředky Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Funguje spravovaných identit pro prostředky Azure s Azure Cloud Services?

Ne, nejsou žádné plány pro podporu spravovaných identit pro prostředky Azure ve službě Azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Funguje s Active Directory Authentication Library (ADAL) nebo Microsoft Authentication Library (MSAL) spravovaných identit pro prostředky Azure?

Ne, spravované identity pro prostředky Azure zatím není sadou ADAL nebo MSAL integrované. Podrobnosti o získání tokenu pro spravované identity pro prostředky Azure pomocí koncového bodu REST, naleznete v tématu [použití spravované identity pro prostředky Azure na Virtuálním počítači Azure získat přístupový token ](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Co je hranice zabezpečení spravovaných identit pro prostředky Azure?

Hranice zabezpečení identity je prostředek, ke kterému je připojený. Například hranice zabezpečení pro virtuální počítač se spravovaným identitám pro prostředky Azure povolena, je virtuální počítač. Jakýkoli kód spuštěn na tomto virtuálním počítači, je možné volat koncový bod a žádosti o tokeny spravovaných identit pro prostředky Azure. Je podobné prostředí s další prostředky, které podporují spravované identity pro prostředky Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jaké identity bude IMDS výchozí, pokud nechcete zadat identitu v požadavku?

- V případě spravovanou identitu přiřazenou systémem je povolen a je v požadavku je zadaná žádná identita, IMDS bude ve výchozím nastavení systém přiřadil spravovaná identita.
- Pokud spravovanou identitu přiřazenou systémem není povoleno a existuje jenom jeden spravovaný identity přiřazené uživateli, IMDS bude ve výchozím nastavení tento jeden spravované identity přiřazené uživateli. 
- Pokud není povoleno spravovanou identitu přiřazenou systémem a existuje více spravovaných identit přiřazených uživateli, zadáním spravovanou identitu v požadavku je povinný.

### <a name="should-i-use-the-managed-identities-for-azure-resources-vm-imds-endpoint-or-the-vm-extension-endpoint"></a>Použít spravované identity pro koncový bod virtuálního počítače IMDS prostředků Azure nebo koncového bodu virtuálního počítače rozšíření?

Při použití spravované identity pro prostředky Azure s virtuálními počítači, doporučujeme pomocí spravované identity pro koncový bod IMDS prostředků Azure. Služba Azure Instance Metadata je koncový bod REST, která je přístupná pro všechny virtuální počítače IaaS vytvořené prostřednictvím Azure Resource Manageru. Mezi výhody použití spravované identity pro prostředky Azure prostřednictvím IMDS patří:
    - Všechny operační systémy podporované IaaS s nástrojem Azure můžete použít spravovaných identit pro prostředky Azure prostřednictvím IMDS.
    - Už nebude potřeba nainstalovat rozšíření na vašem virtuálním počítači povolit spravovaných identit pro prostředky Azure. 
    - Certifikáty používané službou spravovaných identit pro prostředky Azure, už nejsou k dispozici ve virtuálním počítači.
    - Koncový bod IMDS je dobře známé nesměrovatelných IP adresu, k dispozici pouze z v rámci virtuálního počítače.

Spravované identity pro prostředky Azure, které je stále k dispozici pro použití dnes; rozšíření virtuálního počítače ale v budoucnu jsme se ve výchozím nastavení pomocí IMDS koncového bodu. Spravované identity pro prostředky Azure, které rozšíření virtuálního počítače se přestanou používat v lednu 2019. 

Další informace o Azure Instance Metadata Service, najdete v části [IMDS dokumentace](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Se spravovaným identitám znovu vytvoří automaticky když předplatné přesunu do jiného adresáře?

Ne. Pokud je předplatné přesunout do jiného adresáře, budete muset ručně je znovu vytvořit a udělit přiřazení role Azure RBAC znovu.
    - Pro systém přiřadil spravovaných identit: zakázat a znovu povolit.
    - Pro uživatele, přiřazenou spravovaných identit: odstranění, znovu vytvořte a připojte je znovu k potřebné prostředky (např. virtuální počítače)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Můžete použít spravovanou identitu pro přístup k prostředku v jiném tenantovi/adresáři?

Ne. Spravované identity aktuálně nepodporují adresář různé scénáře. 

### <a name="what-are-the-supported-linux-distributions"></a>Jaké jsou podporované distribuce systému Linux?

Všechny Linuxových distribucí, které podporuje Azure IaaS je možné pomocí spravované identity pro prostředky Azure prostřednictvím IMDS koncového bodu. 

Spravované identity pro prostředky Azure (plánovaná k převedení na zastaralého v lednu 2019) rozšíření virtuálního počítače podporuje pouze následující distribucí systému Linux:
- CoreOS Stable
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Ostatní Linuxové distribuce nejsou aktuálně podporovány a rozšíření může selhat v nepodporované distribucích.

Toto rozšíření funguje na CentOS 6.9. Nicméně z důvodu nedostatku systémové podpory v 6.9 rozšíření nespustí automaticky restartování Pokud došlo k chybě nebo zastavená. Restartuje po restartování virtuálního počítače. Rozšíření restartovat ručně, najdete v článku [jak je restartovat spravované identity pro rozšíření prostředků Azure?](#how-do-you-restart-the-managed-identities-for-Azure-resources-extension)

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Jak je restartovat spravované identity pro rozšíření prostředků Azure?
V některých verzích systému Linux a Windows Pokud rozšíření zastaví, následující rutiny můžou sloužit k ho restartovat ručně:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Kde: 
- Název rozšíření a typ pro Windows je: ManagedIdentityExtensionForWindows
- Název rozšíření a typ pro Linux je: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Známé problémy

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Automatizační skript" selhání při pokusu o export schématu pro spravované identity pro rozšíření prostředků Azure

Pokud spravovaných identit pro prostředky Azure je povoleno na virtuálním počítači, se zobrazí následující chyba při pokusu o použití funkce "Skript automatizace" pro virtuální počítač nebo jeho skupina prostředků:

![Chyba při exportování spravovaných identit pro prostředky Azure automatizační skript](./media/msi-known-issues/automation-script-export-error.png)

Spravované identity pro prostředky Azure, které rozšíření virtuálního počítače (plánovaná k převedení na zastaralého v lednu 2019) se aktuálně podporují možnost exportu schématem pro šablonu skupiny prostředků. Kvůli tomu že vygenerovaná šablona nezobrazuje parametry konfigurace, umožňuje spravovaným identitám pro prostředky Azure pro prostředek. Tyto části můžete přidat ručně podle příkladů v [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí šablony](qs-configure-template-windows-vm.md).

Když funkce exportu schématu je k dispozici pro spravované identity pro rozšíření virtuálního počítače prostředky Azure (plánovaná k převedení na zastaralého v lednu 2019), objeví se v [export skupiny prostředků, obsahující rozšíření virtuálních počítačů](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Na webu Azure Portal se nezobrazí okno Konfigurace

Pokud v okně Konfigurace virtuálního počítače není na vašem virtuálním počítači, pak spravovaných identit pro prostředky Azure není povolená na portálu ve vaší oblasti ještě.  Zkuste to znovu později.  Můžete také povolit spravovaných identit pro prostředky Azure pro virtuální počítač pomocí [PowerShell](qs-configure-powershell-windows-vm.md) nebo [rozhraní příkazového řádku Azure](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Nelze přiřadit přístup k virtuálním počítačům v okně přístupové ovládacího prvku (IAM)

Pokud **virtuálního počítače** nezobrazí na portálu Azure portal jako volba pro **přiřadit přístup k** v **řízení přístupu (IAM)** > **přidat roli přiřazení**, pak spravovaných identit pro prostředky Azure na portálu ve vaší oblasti ještě nepovolil. Zkuste to znovu později.  Stále můžete vybrat identitu pro přiřazení role virtuálního počítače tak, že spravovaných identit pro prostředky Azure instanční objekt služby.  Zadejte název virtuálního počítače v **vyberte** pole a objektu zabezpečení se zobrazí ve výsledcích hledání.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Virtuální počítač se nepodaří spustit po přesunutí ze skupiny prostředků nebo předplatného

Při přesunutí virtuálního počítače v běžícím stavu, pokračuje se spustí během přechodu. Ale po přesunutí, pokud je virtuální počítač zastavit, restartovat, dojde k selhání spuštění. Tento problém nastane, protože virtuální počítač není aktualizace odkazu na spravované identity pro identitu prostředků Azure a přejděte do původní skupiny prostředků i nadále.

**Alternativní řešení** 
 
Aktualizace na virtuálním počítači aktivujte, takže ho můžete získat správné hodnoty pro spravované identity pro prostředky Azure. Můžete provést změnu vlastnosti virtuálního počítače aktualizovat odkaz na spravované identity pro identitu prostředků Azure. Například můžete nastavit hodnotu nové značky na virtuálním počítači pomocí následujícího příkazu:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Tento příkaz nastaví nová značka "fixVM" s hodnotou 1 na virtuálním počítači. 
 
Nastavením této vlastnosti virtuálního počítače aktualizuje správné spravovaných identit pro identifikátor URI prostředku prostředků Azure a pak by měl být schopni spustit virtuální počítač. 
 
Po spuštění virtuálního počítače značku je možné odebrat pomocí následující příkaz:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Rozšíření virtuálního počítače se zřizování nezdaří

Zřizování rozšíření virtuálního počítače může selhat z důvodu chyby vyhledávání DNS. Restartujte virtuální počítač a zkuste to znovu.
 
> [!NOTE]
> Rozšíření virtuálního počítače je naplánovaná na vyřazení roku 2019. ledna. Doporučujeme že přejít na použití IMDS koncového bodu.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Přenos předplatného mezi adresářů služby Azure AD

Spravované identity není aktualizován, když se předplatné přesune/přenést do jiného adresáře. V důsledku toho systém přiřadil neexistuje ani uživatelsky přiřazené identity spravované se přeruší. 

Jako alternativní řešení po přesunutí předplatného můžete zakázat systém přiřadil spravovaných identit a je znovu povolit. Podobně můžete odstranit a znovu vytvořit všechny přiřazené uživatele spravované identity. 

## <a name="known-issues-with-user-assigned-managed-identities"></a>Známé problémy s uživatelsky přiřazené identity spravované

- Vytvoření uživatelsky přiřazené spravovanou identitu se speciálními znaky (třeba podtržítko) v názvu, není podporováno.
- Názvy uživatelsky přiřazené identity jsou omezeny na 24 znaků. Pokud název je delší než 24 znaků, identita nebude možné přiřadit k prostředku (tj. virtuální počítač.)
- Pokud používáte rozšíření virtuálních počítačů spravovanou identitu (plánovaná k převedení na zastaralého v lednu 2019), je podporovaný limit 32 spravované uživatelsky přiřazené identity. Bez přípon spravovanou identitu virtuálního počítače je podporovaný limit 512.  
- Přesunutí spravovanou identitu uživatele přiřadit k jiné skupině prostředků způsobí, že identita pro přerušení. V důsledku toho nebudete mít k žádosti o tokeny pro danou identitu. 
- Přenos předplatného do jiného adresáře budou přerušeny všechny existující uživatelsky přiřazené spravované identity. 