---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d14cfb82ae74f85425dbd3e8a365e8b99969641d
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226745"
---
## <a name="using-vm-extensions"></a>Pomocí rozšíření virtuálních počítačů
Rozšíření Azure VM implementovat chování nebo funkce, které buď pomáhají další programy, které fungují na virtuálních počítačích Azure (třeba **WebDeployForVSDevTest** rozšíření umožňuje sadě Visual Studio do řešení pro nasazení webu na vašem virtuálním počítači Azure) nebo zadejte možnost interakce s virtuálním Počítačem pro podporu některých chování (například vám pomůže rozšíření přístup k virtuálním počítačům z prostředí PowerShell, rozhraní příkazového řádku Azure a ostatní klienti resetovat nebo změnit hodnoty vzdáleného přístupu na vašem virtuálním počítači Azure).

> [!IMPORTANT]
> Úplný seznam rozšíření podle funkce podporují, najdete v části [rozšíření virtuálních počítačů Azure a funkce](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Protože každý rozšíření virtuálního počítače podporuje konkrétní funkce, přesně co lze a nelze provést s příponou závisí na rozšíření. Proto se před změnou váš virtuální počítač, ujistěte se, že jste si přečetli dokumentaci pro rozšíření virtuálního počítače, který chcete použít. Odebrání některých rozšíření virtuálních počítačů není podporována. ostatní uživatelé mají vlastnosti, které je možné nastavit, které výrazně mění chování virtuálního počítače.
> 
> 

Běžné úkoly, jsou:

1. Zjištění dostupných rozšíření
2. Aktualizuje se načíst rozšíření
3. Přidání rozšíření
4. Odebírá se rozšíření

## <a name="find-available-extensions"></a>Vyhledání dostupných rozšíření
Můžete najít rozšíření a používání rozšířených informací:

* PowerShell
* Rozhraní příkazového řádku Azure Cross-Platform (Azure CLI)
* Rozhraní REST API pro správu služeb

### <a name="azure-powershell"></a>Azure PowerShell
Některá rozšíření mají rutin prostředí PowerShell, které jsou specifické pro jejich, který může zjednodušit jejich konfigurace z prostředí PowerShell; ale tyto rutiny fungují všechna rozšíření virtuálních počítačů.

Chcete-li získat informace o dostupných rozšíření můžete použít následující rutiny:

* Pro instance webové role nebo role pracovního procesu, můžete použít [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) rutiny.
* Pro instance virtuálních počítačů, můžete použít [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) rutiny.
  
   Například následující příklad kódu ukazuje, jak uvádí informace k **IaaSDiagnostics** rozšíření pomocí Powershellu.
  
      PS C:\> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
  
      Publisher                   : Microsoft.Azure.Diagnostics
      ExtensionName               : IaaSDiagnostics
      Version                     : 1.2
      Label                       : Microsoft Monitoring Agent Diagnostics
      Description                 : Microsoft Monitoring Agent Extension
      PublicConfigurationSchema   :
      PrivateConfigurationSchema  :
      IsInternalExtension         : False
      SampleConfig                :
      ReplicationCompleted        : True
      Eula                        :
      PrivacyUri                  :
      HomepageUri                 :
      IsJsonExtension             : True
      DisallowMajorVersionUpgrade : False
      SupportedOS                 :
      PublishedDate               :
      CompanyName                 :

### <a name="azure-command-line-interface-azure-cli"></a>Rozhraní příkazového řádku Azure (Azure CLI)
Některá rozšíření mají příkazy rozhraní příkazového řádku Azure, které jsou specifické pro jejich (rozšíření Docker VM je jedním z příkladů), který může zjednodušit vlastní konfiguraci; ale následující příkazy lze použít pro všechna rozšíření virtuálních počítačů.

Můžete použít **seznamu rozšíření azure vm** příkazu získat informace o dostupných rozšíření a použijte **–-json** možnosti se zobrazí všechny dostupné informace o jedno nebo více rozšíření. Pokud nepoužijete příponu, příkaz vrátí JSON popis všech dostupných rozšíření.

Například následující příklad kódu ukazuje, jak uvádí informace k **IaaSDiagnostics** rozšíření pomocí Azure CLI **seznamu rozšíření azure vm** příkazu a použití **–-json**  možnost vrátit kompletní informace.

    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



### <a name="service-management-rest-apis"></a>Rozhraní REST API pro správu služeb
Následující rozhraní REST API můžete použít k získání informací o dostupných rozšíření:

* Pro instance webové role nebo role pracovního procesu, můžete použít [seznam dostupných rozšíření](https://msdn.microsoft.com/library/dn169559.aspx) operace. Seznam verzí rozšíření k dispozici, můžete použít [verze rozšíření seznamu](https://msdn.microsoft.com/library/dn495437.aspx).
* Pro instance virtuálních počítačů, můžete použít [rozšíření prostředků seznamu](https://msdn.microsoft.com/library/dn495441.aspx) operace. Seznam verzí rozšíření k dispozici, můžete použít [verze rozšíření prostředků seznamu](https://msdn.microsoft.com/library/dn495440.aspx).

## <a name="add-update-or-disable-extensions"></a>Přidání, aktualizace nebo zakázat rozšíření
Rozšíření můžete přidat, když je vytvořena instance nebo mohou být přidány do běžící instance. Rozšíření můžete aktualizovat, zablokováno nebo odstraněno. Tyto akce můžete provádět pomocí rutin prostředí Azure PowerShell nebo pomocí operací REST API pro správu služeb. Parametry jsou nutné k instalaci a nastavení některých rozšíření. Veřejné a soukromé parametry jsou podporovány pro rozšíření.

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutin prostředí Azure PowerShell je nejjednodušší způsob, jak přidávat a aktualizovat rozšíření. Při použití rutiny rozšíření většinu položek konfigurace rozšíření je provede za vás. V některých případech budete muset programově přidat rozšíření. Pokud potřebujete udělat, je nutné zadat konfiguraci rozšíření.

Vědět, zda rozšíření vyžaduje konfiguraci veřejného a privátního parametrů můžete použít následující rutiny:

* Pro instance webové role nebo role pracovního procesu, můžete použít **Get-AzureServiceAvailableExtension** rutiny.
* Pro instance virtuálních počítačů, můžete použít **Get-AzureVMAvailableExtension** rutiny.

### <a name="service-management-rest-apis"></a>Rozhraní REST API pro správu služeb
Když načtete seznam dostupných rozšíření pomocí rozhraní REST API, zobrazí se informace o tom, jak rozšíření je potřeba nakonfigurovat. Vrácené informace se můžou zobrazovat informace o parametrech reprezentována schéma veřejné a privátní schématu. Veřejné parametr hodnoty jsou vráceny dotazy o instance. Hodnoty parametrů privátní nebudou zobrazeny.

Následující rozhraní REST API můžete vědět, zda rozšíření vyžaduje konfiguraci veřejného a privátního parametrů:

* Pro instance webové role nebo role pracovních procesů **PublicConfigurationSchema** a **PrivateConfigurationSchema** informacemi o prvky v odpovědi z [k dispozici seznam Rozšíření](https://msdn.microsoft.com/library/dn169559.aspx) operace.
* Pro instance virtuálních počítačů **PublicConfigurationSchema** a **PrivateConfigurationSchema** informacemi o prvky v odpovědi z [seznam prostředků Rozšíření](https://msdn.microsoft.com/library/dn495441.aspx) operace.

> [!NOTE]
> Rozšíření můžete také použít konfigurace, které jsou definovány pomocí formátu JSON. Když tyto typy rozšíření se používají, jenom **SampleConfig** element se používá.
> 
> 

