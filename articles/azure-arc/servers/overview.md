---
title: Přehled služby Azure ARC pro servery (Preview)
description: Naučte se používat Azure ARC pro servery ke správě počítačů, které jsou hostované mimo Azure, jako by se jedná o prostředek Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Automatizace Azure, DSC, PowerShell, konfigurace požadovaného stavu, Správa aktualizací, sledování změn, inventarizace, Runbooky, Python, grafický, hybridní
ms.date: 02/12/2020
ms.topic: overview
ms.openlocfilehash: 33681d5c9e296d7c292dabbd64560e3d95c45af2
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190311"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Co je Azure ARC pro servery (Preview)

Azure ARC pro servery (Preview) umožňuje spravovat počítače se systémem Windows a Linux hostované mimo Azure v podnikové síti nebo jiném poskytovateli cloudu, podobně jako při správě nativních virtuálních počítačů Azure. Když je hybridní počítač připojený k Azure, bude se jednat o připojený počítač, který se považuje za prostředek v Azure. Každý připojený počítač má ID prostředku, je spravován jako součást skupiny prostředků v rámci předplatného a přináší výhody standardních konstrukcí Azure, jako je například Azure Policy a použití značek.

Aby bylo možné doručovat toto prostředí do vašich hybridních počítačů hostovaných mimo Azure, musí být na každém počítači, který plánujete připojit k Azure, nainstalovaný agent připojeného počítače Azure. Tento agent nedoručuje žádné jiné funkce a nenahrazuje [agenta Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je nutný, pokud chcete aktivně monitorovat operační systém a úlohy běžící v počítači, spravovat je pomocí runbooků nebo řešení automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Tato verze Preview je určená pro účely vyhodnocení a nedoporučujeme spravovat důležité provozní počítače.
>

## <a name="supported-scenarios"></a>Podporované scénáře

Azure ARC pro servery (verze Preview) podporuje následující scénáře s připojenými počítači:

- Přiřaďte [Azure Policy konfigurace hostů](../../governance/policy/concepts/guest-configuration.md) stejným způsobem jako přiřazení zásad pro virtuální počítače Azure.
- Data protokolu shromážděná agentem Log Analytics a uložená v pracovním prostoru Log Analytics je počítač zaregistrovaný. nyní obsahuje vlastnosti specifické pro počítač, například ID prostředku, které lze použít k podpoře přístupu k protokolu [kontextu prostředků](../../azure-monitor/platform/design-logs-deployment.md#access-mode) .

## <a name="supported-regions"></a>Podporované oblasti

U Azure ARC pro servery (Preview) jsou podporované jenom některé oblasti:

- WestUS2
- WestEurope
- WestAsia

Ve většině případů umístění, které vyberete při vytváření instalačního skriptu, by mělo být oblast Azure geograficky nejblíže umístění vašeho počítače. Uložená data se uloží v rámci geografické oblasti Azure obsahující oblast, kterou zadáte, což může mít vliv i na výběr oblasti, pokud máte požadavky na umístění dat. Pokud je oblast Azure, ke které je počítač připojený, ovlivněná výpadkem, připojený počítač to neovlivní, ale operace správy používající Azure možná nebude možné dokončit. V případě pružnosti v případě výpadku v oblasti regionu, pokud máte více umístění, která poskytují geograficky redundantní službu, je nejlepší připojit počítače v každém umístění do jiné oblasti Azure.

## <a name="prerequisites"></a>Předpoklady

### <a name="supported-operating-systems"></a>Podporované operační systémy

Pro agenta připojeného počítače Azure jsou oficiálně podporované následující verze operačního systému Windows a Linux: 

- Windows Server 2012 R2 a vyšší
- Ubuntu 16,04 a 18,04

>[!NOTE]
>Tato verze Preview agenta připojeného počítače pro Windows podporuje jenom Windows Server nakonfigurovaný pro použití v anglickém jazyce.
>

### <a name="required-permissions"></a>Požadovaná oprávnění

- Pokud chcete připojit počítače, jste členem role **připojení počítače připojeného k Azure** .

- Pro čtení, úpravy, opětovné zprovoznění a odstranění počítače jste členem role **Správce prostředků počítače připojeného k Azure** . 

### <a name="azure-subscription-and-service-limits"></a>Omezení předplatného a služeb Azure

Před konfigurací počítačů pomocí Azure ARC pro servery (Preview) byste měli zkontrolovat [omezení Azure Resource Manager předplatného](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) a [skupiny prostředků](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) pro plánování počtu počítačů, které se mají připojit.

### <a name="networking-configuration"></a>Konfigurace sítě

Agent připojeného počítače pro systémy Linux a Windows komunikuje zabezpečeným způsobem přes Azure ARC přes port TCP 443. Pokud se počítač připojí prostřednictvím brány firewall nebo proxy server komunikovat přes Internet, Projděte si níže uvedené požadavky, abyste pochopili požadavky na konfiguraci sítě.

Pokud je odchozí připojení omezeno bránou firewall nebo proxy server, ujistěte se, že níže uvedené adresy URL nejsou blokované. Pokud povolíte pouze rozsahy IP adres nebo názvy domén, které musí agent komunikovat se službou, musíte taky dovolit přístup k následujícím značkám služby a adresám URL.

Značky služby:

- AzureActiveDirectory
- AzureTrafficManager

Adrese

| Prostředek agenta | Popis |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Konfigurace hosta|
|\* – agentservice-prod-1.azure-automation.net|Konfigurace hosta|
|*. his.hybridcompute.azure-automation.net|Služba hybridní identity|

Seznam IP adres pro jednotlivé značky nebo oblasti služby najdete v souboru JSON – [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publikuje týdenní aktualizace obsahující každou službu Azure a rozsahy IP adres, které používá. Další informace najdete v přehledu [značek služeb](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Adresy URL v předchozí tabulce jsou nutné kromě informací o rozsahu IP adres značky služby, protože většina služeb aktuálně nemá registraci značky služby. V takovém případě se tyto IP adresy mohou změnit. Pokud se pro konfiguraci brány firewall vyžadují rozsahy IP adres, měla by se tato značka služby **AzureCloud** použít k povolení přístupu ke všem službám Azure. Nepovolujte monitorování zabezpečení ani kontrolu těchto adres URL. Povolte je stejně jako jiný internetový provoz.

### <a name="register-azure-resource-providers"></a>Registrovat poskytovatele prostředků Azure

Azure ARC pro servery (verze Preview) závisí na následujících poskytovatelích prostředků Azure ve vašem předplatném, aby bylo možné tuto službu používat:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Pokud nejsou registrovány, můžete je zaregistrovat pomocí následujících příkazů:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Rozhraní příkazového řádku Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Poskytovatele prostředků můžete také zaregistrovat v Azure Portal podle kroků v části [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="connected-machine-agent"></a>Agent připojeného počítače

Balíček agenta připojeného počítače Azure pro Windows a Linux si můžete stáhnout z níže uvedených umístění.

- [Balíček Windows agent Instalační služba systému Windows](https://aka.ms/AzureConnectedMachineAgent) z webu Microsoft Download Center.
- Balíček agenta pro Linux se distribuuje z [úložiště balíčků](https://packages.microsoft.com/) Microsoftu pomocí preferovaného formátu balíčku pro distribuci (. Ot./min. nebo. DEB).

>[!NOTE]
>V této verzi Preview byl vydán pouze jeden balíček, který je vhodný pro Ubuntu 16,04 nebo 18,04.

## <a name="install-and-configure-agent"></a>Instalace a konfigurace agenta

Propojení počítačů ve vašem hybridním prostředí s Azure je možné dosáhnout pomocí různých metod v závislosti na vašich požadavcích. Následující tabulka zvýrazňuje jednotlivé metody, abyste zjistili, které funkce jsou pro vaši organizaci nejvhodnější.

| Metoda | Popis |
|--------|-------------|
| Interaktivně | Ručně nainstalujte agenta na jeden nebo malý počet počítačů podle postupu v části [připojení počítačů od Azure Portal](onboard-portal.md).<br> Z Azure Portal můžete vygenerovat skript a spustit ho na počítači, abyste mohli automatizovat kroky instalace a konfigurace agenta.|
| Ve velkém měřítku | Nainstalujte a nakonfigurujte agenta pro více počítačů, které následují po [připojení počítačů pomocí instančního objektu](onboard-service-principal.md).<br> Tato metoda vytvoří instanční objekt pro připojení počítačů, které nejsou interaktivně.|


## <a name="next-steps"></a>Další kroky

- Pokud chcete začít vyhodnocovat Azure ARC pro servery (Preview), postupujte podle článku [připojení Hybrid Machines k Azure z Azure Portal](onboard-portal.md). 