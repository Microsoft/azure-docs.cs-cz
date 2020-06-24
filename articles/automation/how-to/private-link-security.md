---
title: Použití privátního odkazu Azure k bezpečnému připojení sítí k Azure Automation
description: Použití privátního odkazu Azure k bezpečnému připojení sítí k Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 06/22/2020
ms.subservice: ''
ms.openlocfilehash: fa473591355ef9e1ee582dd9c9b820dfa2f93f36
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85268972"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Použití privátního odkazu Azure k bezpečnému připojení sítí k Azure Automation

Privátní koncový bod Azure je síťové rozhraní, které vás privátně a zabezpečeně připojí ke službám využívajícím službu Azure Private Link. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně přináší službu Automation do vaší virtuální sítě. Síťový provoz mezi počítači ve virtuální síti a účtem Automation prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu.

Máte například virtuální síť, ve které jste zakázali odchozí přístup k Internetu. Chcete ale přistoupit k účtu Automation soukromě a používat funkce automatizace, jako jsou Webhooky, konfigurace stavu a úlohy Runbooku na hybridních pracovních procesech Runbooku. Navíc chcete, aby uživatelé měli přístup k účtu Automation jenom přes virtuální síť. To je možné dosáhnout nasazením privátních koncových bodů.

Tento článek popisuje, kdy použít a jak nastavit privátní koncový bod s vaším účtem Automation.

![Koncepční přehled privátního odkazu pro Azure Automation](./media/private-link-security/private-endpoints-automation.png)

## <a name="advantages"></a>Výhody

Pomocí privátního odkazu můžete:

- Připojte soukromě k Azure Automation bez nutnosti otevírat přístup k veřejné síti.
- Připojte soukromě k Azure Monitor Log Analytics pracovním prostoru bez nutnosti otevírat přístup k veřejné síti.

    >[!NOTE]
    >To je nutné v případě, že je váš účet Automation propojený s pracovním prostorem Log Analytics pro přeposílání dat úloh a když jste povolili funkce, jako jsou Update Management, Change Tracking a inventář, konfigurace stavu nebo Start/Stop VMs during off-hours. Další informace o privátním odkazu pro Azure Monitor najdete v tématu [použití privátního odkazu Azure k bezpečnému připojení sítí k Azure monitor](../../azure-monitor/platform/private-link-security.md).

- Ujistěte se, že k datům automatizace máte jenom oprávnění prostřednictvím autorizovaných privátních sítí.
- Zajistěte, aby se data exfiltrace ze svých privátních sítí, a to tak, že definujete prostředek Azure Automation, který se připojuje přes
- Bezpečně připojte soukromou místní síť k Azure Automation pomocí ExpressRoute a privátního odkazu.
- Veškerý provoz se zachová v Microsoft Azure páteřní síti.

Další informace najdete v tématu [klíčové výhody privátního propojení](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Jak to funguje

Azure Automation privátní odkaz připojí jeden nebo více privátních koncových bodů (a proto virtuální sítě, které jsou součástí) k prostředku účtu Automation. Tyto koncové body jsou počítače, které používají Webhooky ke spuštění Runbooku, počítačů, které hostují roli Hybrid Runbook Worker, a uzlů DSC.

Po vytvoření privátních koncových bodů pro automatizaci každá z veřejných adres URL automatizace, které vy nebo počítač můžete přímo kontaktovat, se namapuje na jeden privátní koncový bod ve vaší virtuální síti.

### <a name="webhook-scenario"></a>Scénář Webhooku

Sady Runbook můžete spustit pomocí příspěvku na adrese URL Webhooku. Například adresa URL vypadá takto:`https://<automationAccountId>.webhooks. <region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="state-configuration-agentsvc-scenario"></a>Scénář konfigurace stavu (agentsvc)

Konfigurace stavu poskytuje službu Azure Configuration Management, která umožňuje zapisovat, spravovat a kompilovat konfigurace požadovaného stavu prostředí PowerShell pro uzly v jakémkoli cloudovém nebo místním datacentru.

Agent na počítači se registruje ve službě DSC a pak pomocí koncového bodu služby vyžádá konfiguraci DSC. Koncový bod služby agenta vypadá takto: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

Adresa URL veřejné & privátního koncového bodu by byla stejná, ale bude namapována na privátní IP adresu, pokud je povolené privátní propojení.

## <a name="planning-based-on-your-network"></a>Plánování na základě vaší sítě

Před nastavením prostředku účtu Automation zvažte požadavky na izolaci sítě. Vyhodnoťte přístup virtuálních sítí k veřejnému Internetu a omezení přístupu k vašemu účtu Automation (včetně nastavení oboru privátních odkazů na Azure Monitor protokolů, pokud jsou integrovány s vaším účtem Automation).

### <a name="connect-to-a-private-endpoint"></a>Připojení k privátnímu koncovému bodu

Vytvořte privátní koncový bod pro připojení k naší síti. Tuto úlohu lze provést v [centru Azure Portal Private Link Center](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Až budou změny publicNetworkAccess a privátního propojení uplatněny, může trvat až 35 minut, než se projeví.

V této části vytvoříte privátní koncový bod pro svůj účet Automation.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek > síť > soukromého centra odkazů (Preview)**.

2. V části **centrum privátních odkazů – přehled**na možnost **vytvořit privátní připojení ke službě**vyberte možnost **Spustit**.

3. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Name | Zadejte své *PrivateEndpoint*. |
    | Oblast | Vyberte **YourRegion**. |
    |||

4. Vyberte **Další: prostředek**.

5. V **Vytvoření privátního koncového bodu – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | V adresáři vyberte připojit k prostředku Azure.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Vyberte **Microsoft. Automation/automationAccounts**. |
    | Prostředek |Vybrat *myAutomationAccount*|
    |Cílový dílčí prostředek |V závislosti na vašem scénáři vyberte *Webhook* nebo *DSCAndHybridWorker* .|
    |||

6. Vyberte **Další: Konfigurace**.

7. V **Vytvoření privátního koncového bodu – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**SÍTĚ**| |
    | Virtuální síť| Vyberte *MyVirtualNetwork*. |
    | Podsíť | Vyberte *mySubnet*. |
    |**INTEGRACE PRIVÁTNÍ DNS**||
    |Integrace s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátní DNS |Select *(New) privatelink. Azure-Automation.NET* |
    |||

8. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.

9. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

V **centru privátních odkazů (Preview)** vyberte **soukromé koncové body** pro zobrazení prostředku privátního propojení.

![Privátní odkaz na prostředek služby Automation](./media/private-link-security/private-link-automation-resource.png)

Vyberte prostředek pro zobrazení všech podrobností. Tím se vytvoří nový privátní koncový bod pro váš účet Automation a přiřadí se mu privátní IP adresa z vaší virtuální sítě. **Stav připojení** se zobrazuje jako **schváleno**.

Podobně platí, že jedinečný plně kvalifikovaný název domény (FQDN) se vytvoří pro konfiguraci stavu (agentsvc) a pro modul runtime úloh Hybrid Runbook Worker (jrds). Každé z nich je přiřazená samostatná IP adresa z vaší virtuální sítě a **stav připojení** se zobrazí jako **schváleno**.

Pokud má příjemce služby oprávnění RBAC pro prostředek automatizace, může zvolit metodu automatického schvalování. V takovém případě, když požadavek dosáhne prostředku zprostředkovatele automatizace, není od poskytovatele služeb vyžadována žádná akce a připojení je automaticky schváleno.

## <a name="set-public-network-access-flags"></a>Nastavit příznaky přístupu k veřejné síti

Můžete nakonfigurovat účet Automation pro odepření všech veřejných konfigurací a povolit jenom připojení prostřednictvím privátních koncových bodů, aby se zvýšilo zabezpečení sítě. Pokud chcete omezit přístup k účtu Automation jenom v rámci virtuální sítě a Nepovolit přístup z veřejného Internetu, můžete nastavit `publicNetworkAccess` vlastnost na `$true` .

Je-li nastavení **Odepřít přístup k veřejné síti** nastaveno na hodnotu `true` , jsou povolena pouze připojení prostřednictvím privátních koncových bodů a všechna připojení prostřednictvím veřejných koncových bodů budou odepřena s chybovou zprávou.

Následující skript prostředí PowerShell ukazuje, jak `Get` a `Set` vlastnost **přístup k veřejné síti** na úrovni účtu Automation:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $true
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

## <a name="dns-configuration"></a>Konfigurace DNS

Při připojování k prostředku privátního propojení pomocí plně kvalifikovaného názvu domény v rámci připojovacího řetězce je důležité správně nakonfigurovat nastavení DNS tak, aby se přeložilo na přidělenou privátní IP adresu. Existující služby Azure už můžou mít konfiguraci DNS, která se má použít při připojování přes Veřejný koncový bod. To je nutné přepsat pro připojení pomocí privátního koncového bodu.

Síťové rozhraní přidružené k privátnímu koncovému bodu obsahuje úplnou sadu informací potřebných ke konfiguraci DNS, včetně plně kvalifikovaného názvu domény a privátních IP adres přidělených pro daný prostředek privátního propojení.

Pomocí následujících možností můžete nakonfigurovat nastavení DNS privátních koncových bodů:

* Použijte soubor hostitele (doporučeno pouze pro testování). Soubor hostitele na virtuálním počítači můžete použít k přepsání prvního použití DNS k překladu názvů.

* Použijte [privátní ZÓNU DNS](../../dns/private-dns-privatednszone.md). Privátní zóny DNS můžete použít k přepsání překladu DNS pro konkrétní soukromý koncový bod. Privátní zóna DNS se dá propojit s vaší virtuální sítí a vyřešit konkrétní domény. Pokud chcete, aby mohl Agent na virtuálním počítači komunikovat přes privátní koncový bod, vytvořte záznam Privátní DNS jako `privatelink.azure-automation.net` . Přidejte nové *mapování záznamů DNS na adresu IP* privátního koncového bodu.

* Použijte službu DNS pro přeposílání (volitelné). K přepsání překladu DNS pro konkrétní prostředek privátního propojení můžete použít server DNS pro přeposílání. Pokud je váš [Server DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) hostovaný ve virtuální síti, můžete vytvořit předávací pravidlo DNS pro použití privátní zóny DNS a zjednodušit tak konfiguraci všech prostředků privátního propojení.

Další informace najdete v tématu [Konfigurace DNS privátního koncového bodu Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Další kroky

Další informace o privátním koncovém bodu najdete v tématu [co je privátní koncový bod Azure](../../private-link/private-endpoint-overview.md).