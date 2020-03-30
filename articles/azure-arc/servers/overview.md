---
title: Přehled Azure Arc pro servery (preview)
description: Zjistěte, jak používat Azure Arc pro servery ke správě počítačů, které jsou hostované mimo Azure, jako by se jedná o prostředek Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, požadovaná konfigurace stavu, správa aktualizací, sledování změn, inventář, runbooky, python, grafické, hybridní
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 85a6f8dcb9f551d81a607adc9ae482f4025d804f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366361"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Co je Azure Arc pro servery (preview)

Azure Arc pro servery (preview) umožňuje spravovat vaše počítače s Windows a Linuxem hostované mimo Azure ve vaší podnikové síti nebo jiném poskytovateli cloudu, podobně jako spravujete nativní virtuální počítače Azure. Když je hybridní počítač připojený k Azure, stane se připojeným počítačem a bude v Azure považován za prostředek. Každý připojený počítač má ID prostředků, spravuje se jako součást skupiny prostředků v rámci předplatného a těží ze standardních konstrukcí Azure, jako jsou zásady Azure a použití značek.

Chcete-li poskytovat toto prostředí s hybridními počítači hostovanými mimo Azure, musí být agent Azure Connected Machine nainstalován na každém počítači, který plánujete připojit k Azure. Tento agent neposkytuje žádné další funkce a nenahrazuje [agenta](../../azure-monitor/platform/log-analytics-agent.md)Azure Log Analytics . Agent Log Analytics pro Windows a Linux je vyžadován, pokud chcete proaktivně sledovat operační systém a úlohy spuštěné na počítači, spravovat jej pomocí runbooků automation nebo řešení, jako je správa aktualizací, nebo používat jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Tato verze preview je určena pro účely vyhodnocení a doporučujeme nespravovat kritické výrobní stroje.
>

## <a name="supported-scenarios"></a>Podporované scénáře

Azure Arc pro servery (preview) podporuje následující scénáře s připojenými počítači:

- Přiřaďte [konfigurace hosta zásad Azure](../../governance/policy/concepts/guest-configuration.md) pomocí stejného prostředí jako přiřazení zásad pro virtuální počítače Azure.
- Data protokolu shromážděná agentem Analýzy protokolů a uložená v pracovním prostoru Log Analytics, ve kterém je počítač registrován, nyní obsahují vlastnosti specifické pro počítač, jako je například ID prostředku, které lze použít k podpoře přístupu k protokolu [kontextu prostředků.](../../azure-monitor/platform/design-logs-deployment.md#access-mode)

## <a name="supported-regions"></a>Podporované oblasti

S Azure Arc pro servery (preview) jsou podporované jenom určité oblasti:

- Západní US2
- WestEurope
- Západní Asie

Ve většině případů umístění, které vyberete při vytváření instalačního skriptu by měla být oblast Azure geograficky nejblíže k umístění vašeho počítače. Data v klidovém stavu se budou ukládat v rámci geografické oblasti Azure obsahující zadanou oblast, což může mít vliv také na výběr oblasti, pokud máte požadavky na rezidenci dat. Pokud oblast Azure, ke které je váš počítač připojený, je ovlivněna výpadkem, připojený počítač není ovlivněn, ale operace správy pomocí Azure nemusí být neúplné. Z důvodu odolnosti v případě regionálního výpadku, pokud máte více míst, které poskytují geograficky redundantní služby, je nejlepší připojit počítače v každé lokalitě do jiné oblasti Azure.

## <a name="prerequisites"></a>Požadavky

### <a name="supported-operating-systems"></a>Podporované operační systémy

Pro agenta Azure Connected Machine jsou oficiálně podporované následující verze operačního systému Windows a Linux: 

- Windows Server 2012 R2 a vyšší (včetně Windows Server Core)
- Ubuntu 16.04 a 18.04
- Centos Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 7

>[!NOTE]
>Tato předběžná verze agenta connected machine pro systém Windows podporuje pouze systém Windows Server nakonfigurovaný pro použití anglického jazyka.
>

### <a name="required-permissions"></a>Požadovaná oprávnění

- Pro palubní počítače jste členem role **Připojení k počítači Azure.**

- Chcete-li číst, upravovat, znovu napalubě a odstranit počítač, jste členem role **správce prostředků připojeného počítače Azure.** 

### <a name="azure-subscription-and-service-limits"></a>Limity předplatného a služeb Azure

Před konfigurací počítačů s Azure Arc pro servery (preview) byste měli zkontrolovat [limity předplatného](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) Azure Resource Manager a [omezení skupiny prostředků](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) pro plánování počtu počítačů, které mají být připojeny.

## <a name="tls-12-protocol"></a>Protokol TLS 1.2

Chcete-li zajistit zabezpečení dat při přenosu do Azure, důrazně doporučujeme nakonfigurovat počítač tak, aby používal zabezpečení transportní vrstvy (TLS) 1.2. Starší verze vrstvy TLS/SSockets Layer (SSL) byly shledány zranitelnými a zatímco v současné době pracují na povolení zpětné kompatibility, **nejsou doporučeny**. 

|Platforma/jazyk | Podpora | Další informace |
| --- | --- | --- |
|Linux | Linuxové distribuce mají tendenci spoléhat se na [OpenSSL](https://www.openssl.org) pro podporu TLS 1.2. | Zkontrolujte [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) a potvrďte, že je vaše verze OpenSSL podporována.|
| Windows Server 2012 R2 a vyšší | Podporováno a ve výchozím nastavení povoleno. | Chcete-li ověřit, zda stále používáte [výchozí nastavení](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Konfigurace sítě

Agent connected machine pro Linux a Windows bezpečně komunikuje odchozí do Azure Arc přes port TCP 443. Pokud se počítač připojuje přes bránu firewall nebo proxy server pro komunikaci přes Internet, zkontrolujte níže uvedené požadavky, abyste pochopili požadavky na konfiguraci sítě.

Pokud je odchozí připojení omezeno bránou firewall nebo proxy serverem, zkontrolujte, zda nejsou níže uvedené adresy URL blokovány. Pokud povolíte pouze rozsahy IP adres nebo názvy domén požadované pro agenta ke komunikaci se službou, musíte také povolit přístup k následujícím značkám služeb a adresám URL.

Značky služeb:

- AzureActiveDirectory
- AzureTrafficManager

Adresy url:

| Prostředek agenta | Popis |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Konfigurace hosta|
|*-agentservice-prod-1.azure-automation.net|Konfigurace hosta|
|*.his.hybridcompute.azure-automation.net|Hybridní služba identity|

Seznam IP adres pro každou značku služby nebo oblast najdete v souboru JSON – [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publikuje týdenní aktualizace obsahující všechny služby Azure a rozsahy IP adres, které používá. Další informace naleznete v recenzi [na service tagy](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Adresy URL v předchozí tabulce jsou vyžadovány kromě informací o rozsahu ip adresy výrobních značek, protože většina služeb aktuálně nemá registraci výrobního údaje. V důsledku toho se mohou změnit adresy IP. Pokud jsou pro konfiguraci brány firewall vyžadovány rozsahy IP adres, měla by se k povolení přístupu ke všem službám Azure použít výrobní číslo **AzureCloud.** Nezakazujte monitorování zabezpečení nebo kontrolu těchto adres URL, povolte je stejně jako ostatní internetové přenosy.

### <a name="register-azure-resource-providers"></a>Registrace poskytovatelů prostředků Azure

Azure Arc pro servery (preview) závisí na následujících zprostředkovatelích prostředků Azure ve vašem předplatném, aby bylo možné používat tuto službu:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Pokud nejsou registrovány, můžete je zaregistrovat pomocí následujících příkazů:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Cli Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Poskytovatele prostředků můžete zaregistrovat na webu Azure Portal podle kroků v části [Portál Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="connected-machine-agent"></a>Agent připojeného stroje

Balíček agenta Azure Connected Machine pro Windows a Linux si můžete stáhnout z níže uvedených umístění.

- [Balíček Instalační služby systému Windows pro systém Windows](https://aka.ms/AzureConnectedMachineAgent) ze služby Stažení softwaru.
- Balíček agenta Linuxu je distribuován z [úložiště balíčků](https://packages.microsoft.com/) společnosti Microsoft pomocí upřednostňovaného formátu balíčku pro distribuci (. rpm nebo . DEB).

>[!NOTE]
>Během tohoto náhledu byl vydán pouze jeden balíček, který je vhodný pro Ubuntu 16.04 nebo 18.04.

Agent azure connected machine pro Windows a Linux lze upgradovat na nejnovější verzi ručně nebo automaticky v závislosti na vašich požadavcích. Další informace naleznete [zde](manage-agent.md)

### <a name="agent-status"></a>Stav agenta

Agent připojeného počítače odesílá do služby každých 5 minut zprávu o pravidelném prezenčním signálu. Pokud jeden není přijat po dobu 15 minut, je počítač považován za offline a stav se automaticky změní na **Odpojeno** na portálu. Po přijetí následné zprávy prezenčního signálu od agenta připojeného počítače se jeho stav automaticky změní na **Připojeno**.

## <a name="install-and-configure-agent"></a>Instalace a konfigurace agenta

Připojení počítačů ve vašem hybridním prostředí přímo s Azure lze provést pomocí různých metod v závislosti na vašich požadavcích. V následující tabulce jsou uvedeny jednotlivé metody k určení, která je pro vaši organizaci nejvhodnější.

| Metoda | Popis |
|--------|-------------|
| Interaktivně | Ručně nainstalujte agenta na jeden nebo malý počet počítačů podle kroků v [části Připojit počítače z portálu Azure](onboard-portal.md).<br> Z portálu Azure můžete vygenerovat skript a spustit jej v počítači a automatizovat kroky instalace a konfigurace agenta.|
| Ve velkém měřítku | Nainstalujte a nakonfigurujte agenta pro více počítačů podle [počítačů Connect pomocí instančního objektu .](onboard-service-principal.md)<br> Tato metoda vytvoří instanční objekt pro připojení počítačů neinteraktivně.|
| Ve velkém měřítku | Nainstalujte a nakonfigurujte agenta pro více počítačů podle metody [Pomocí prostředí Windows PowerShell DSC](onboard-dsc.md).<br> Tato metoda používá instanční objekt pro připojení počítačů neinteraktivně s Prostředím PowerShell DSC. |

## <a name="next-steps"></a>Další kroky

- Chcete-li začít vyhodnocovat Azure Arc pro servery (preview), postupujte podle článku [Připojení hybridních počítačů k Azure z webu Azure Portal](onboard-portal.md). 