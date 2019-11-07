---
title: Přehled služby Azure ARC pro servery
description: Naučte se používat Azure ARC pro servery k automatizaci životního cyklu infrastruktury a aplikací.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Automatizace Azure, DSC, PowerShell, konfigurace požadovaného stavu, Správa aktualizací, sledování změn, inventarizace, Runbooky, Python, grafický, hybridní
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 57dbfa0c2063c2059955294ccc1337bb76b72614
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606090"
---
# <a name="what-is-azure-arc-for-servers"></a>Co je Azure ARC pro servery

Azure ARC pro servery umožňuje spravovat počítače, které jsou mimo Azure.
Když je počítač, který není Azure, připojený k Azure, bude se jednat o **připojený počítač** , který se považuje za prostředek v Azure. Každý **připojený počítač** má ID prostředku, je spravovaný jako součást skupiny prostředků v rámci předplatného a přináší výhody standardních konstrukcí Azure, jako je Azure Policy a označování.

Balíček agenta musí být nainstalovaný na každém počítači, aby ho bylo možné připojit k Azure. Zbytek tohoto dokumentu podrobněji vysvětluje proces.

Počítače budou mít stav **připojeno** nebo **Odpojeno** v závislosti na tom, jak nedávno se agent vrátil. Každé vrácení se změnami se nazývá prezenční signál. Pokud se počítač v posledních 5 minutách nevrátil se změnami, zobrazí se v režimu offline, dokud se neobnoví připojení.  <!-- For more information on troubleshooting agent connectivity, see [Troubleshooting Azure Arc for servers](troubleshoot/arc-for-servers.md). -->

![Připojené servery](./media/overview/arc-for-servers-onboarded-servers.png)

## <a name="clients"></a>Klienti

### <a name="supported-operating-systems"></a>Podporované operační systémy

V Public Preview podporujeme:

- Windows Server 2012 R2 a novější
- Ubuntu 16,04 a 18,04

Verze Public Preview je navržená pro účely vyhodnocení a neměla by se používat ke správě kritických produkčních prostředků.

## <a name="azure-subscription-and-service-limits"></a>Omezení předplatného a služeb Azure

Ujistěte se prosím, že jste si přečetli omezení Azure Resource Manager a naplánujete počet počítačů, které se mají připojit, podle pokynů uvedených pro [předplatné](../../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)a pro [skupiny prostředků](../../azure-subscription-service-limits.md#resource-group-limits). Ve výchozím nastavení je pro každou skupinu prostředků povolený limit 800 serverů.

## <a name="networking-configuration"></a>Konfigurace sítě

Během instalace a modulu runtime vyžaduje agent připojení ke **koncovým bodům služby Azure ARC**. Pokud je odchozí připojení blokováno branami firewall, ujistěte se, že následující adresy URL nejsou ve výchozím nastavení blokované. Všechna připojení jsou odchozí od agenta do Azure a jsou zabezpečená pomocí **protokolu SSL**. Veškerý provoz se dá směrovat přes proxy **https** . Pokud povolíte rozsahy IP adres nebo názvy domén, ke kterým se můžou servery připojit, musíte povolit port 443 přístup k následujícím značkám služby a názvům DNS.

Značky služby:

* Azureactivedirectory selhala
* AzureTrafficManager

Seznam IP adres pro jednotlivé značky nebo oblasti služby najdete v souboru JSON – [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publikuje týdenní aktualizace obsahující každou službu Azure a rozsahy IP adres, které používá. Další podrobnosti najdete v tématu [značky služby](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Tyto názvy DNS jsou k dispozici kromě informací o rozsahu IP adres značky služby, protože většina služeb aktuálně nemá registraci značky služby a je tak možné, že se tyto IP adresy mohou změnit. Pokud se pro konfiguraci brány firewall vyžadují rozsahy IP adres, měla by se tato značka služby **AzureCloud** použít k povolení přístupu ke všem službám Azure. Nepovolujte monitorování zabezpečení ani kontrolu těchto adres URL, ale povolte je stejně jako ostatní přenosy v Internetu.

| Doménové prostředí | Požadované koncové body služby Azure |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Konfigurace hosta|
|\* – agentservice-prod-1.azure-automation.net|Konfigurace hosta|
|*. his.hybridcompute.azure-automation.net|Služba hybridní identity|

### <a name="installation-network-requirements"></a>Požadavky na instalaci sítě

Stáhněte si [balíček agenta připojeného počítače Azure](https://aka.ms/AzureConnectedMachineAgent) z našich oficiálních distribučních serverů, které níže musí být dostupné z vašeho prostředí. Můžete si stáhnout balíček do sdílené složky a nechat z něj nainstalovaného agenta. V takovém případě může být potřeba upravit skript zprovoznění generovaný z Azure Portal.

Windows:

* `aka.ms`
* `download.microsoft.com`

Linux:

* `aka.ms`
* `packages.microsoft.com`

Informace o tom, jak nakonfigurovat agenta pro používání proxy serveru, najdete v části [konfigurace proxy serveru](quickstart-onboard-powershell.md#proxy-server-configuration).

## <a name="register-the-required-resource-providers"></a>Registrovat požadované poskytovatele prostředků

Aby bylo možné použít ARC Azure pro servery, musíte zaregistrovat požadované poskytovatele prostředků.

* **Microsoft.HybridCompute**
* **Microsoft. GuestConfiguration**

Poskytovatele prostředků můžete zaregistrovat pomocí následujících příkazů:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Rozhraní příkazového řádku Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Poskytovatele prostředků můžete také zaregistrovat pomocí portálu podle kroků v části [Azure Portal](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="supported-scenarios"></a>Podporované scénáře

Po registraci uzlu můžete začít spravovat uzly pomocí jiných služeb Azure.

V Public Preview jsou pro **připojené počítače**podporovány následující scénáře.

## <a name="guest-configuration"></a>Konfigurace hosta

Po připojení počítače k Azure můžete k **připojeným počítačům** přiřadit zásady Azure pomocí stejného prostředí, jako je přiřazení zásad k virtuálním počítačům Azure.

Další informace najdete v tématu [vysvětlení konfigurace hosta Azure Policy](../../governance/policy/concepts/guest-configuration.md).

Protokoly agenta konfigurace hosta pro **připojený počítač** jsou v následujících umístěních:

* Windows – `%ProgramFiles%\AzureConnectedMachineAgent\logs\dsc.log`
* Linux:-`/opt/logs/dsc.log`

## <a name="log-analytics"></a>Log Analytics

Data protokolu shromažďovaná [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) a uložená v pracovním prostoru Log Analytics nyní obsahují vlastnosti specifické pro počítač, jako je například **ResourceID**, které lze použít pro přístup k protokolu orientovaného na prostředky.

- V počítačích, které už mají nainstalovaného agenta MMA, bude mít funkce **ARC Azure** povolené prostřednictvím aktualizovaných sad Management Pack.
- Pro integraci Azure ARC pro servery se vyžaduje [Agent MMA verze 10.20.18011 nebo vyšší](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows#agent-and-vm-extension-version) .
- Při dotazování na data protokolu v [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#log-queries)bude vracené schéma dat obsahovat Hybrid **ResourceId** ve formuláři `/subscriptions/<SubscriptionId/resourceGroups/<ResourceGroup>/providers/Microsoft.HybridCompute/machines/<MachineName>`.

Další informace najdete v tématu [Začínáme s Log Analytics v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

<!-- MMA agent version 10.20.18011 and later -->

## <a name="next-steps"></a>Další kroky

Existují dva způsoby, jak připojit počítače pomocí ARC Azure pro servery.

* **Interaktivně** – pomocí rychlého startu na [portálu](quickstart-onboard-portal.md) vygenerujte skript z portálu a spustí se v počítači. Tato možnost je nejlepší, pokud se připojujete k jednomu počítači v jednom okamžiku.
* **Škálování** – postupujte podle pokynů pro [rychlý Start PowerShellu](quickstart-onboard-powershell.md) a vytvořte instanční objekt, který neinteraktivně připojí počítače.
