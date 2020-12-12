---
title: Použití privátního odkazu Azure k bezpečnému připojení sítí k Azure Automation
description: Použití privátního odkazu Azure k bezpečnému připojení sítí k Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.openlocfilehash: 26e7dbf3f5629d4691211b6c9b82446ba4035421
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347611"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Použití privátního odkazu Azure k bezpečnému připojení sítí k Azure Automation

Privátní koncový bod Azure je síťové rozhraní, které vás privátně a zabezpečeně připojí ke službám využívajícím službu Azure Private Link. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně přináší službu Automation do vaší virtuální sítě. Síťový provoz mezi počítači ve virtuální síti a účtem Automation prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu.

Máte například virtuální síť, ve které jste zakázali odchozí přístup k Internetu. Chcete ale přistoupit k účtu Automation soukromě a používat funkce automatizace, jako jsou Webhooky, konfigurace stavu a úlohy Runbooku na hybridních pracovních procesech Runbooku. Navíc chcete, aby uživatelé měli přístup k účtu Automation jenom přes virtuální síť.  Nasazení privátního koncového bodu dosahuje těchto cílů.

Tento článek popisuje, kdy použít a jak nastavit privátní koncový bod s vaším účtem Automation.

![Koncepční přehled privátního odkazu pro Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Podpora privátních propojení s Azure Automation je k dispozici pouze v cloudech Azure komerčních a Azure USA.

## <a name="advantages"></a>Výhody

Pomocí privátního odkazu můžete:

- Připojte soukromě k Azure Automation bez nutnosti otevírat přístup k veřejné síti.
- Připojte soukromě k Azure Monitor Log Analytics pracovním prostoru bez nutnosti otevírat přístup k veřejné síti.

    >[!NOTE]
    >Pokud je váš účet Automation propojený s pracovním prostorem Log Analytics, aby se data úloh předal, a když jste povolili funkce, jako je například Update Management, Change Tracking a inventář, konfigurace stavu nebo Start/Stop VMs during off-hours, je vyžadován samostatný privátní koncový bod pro váš Log Analytics pracovní prostor. Další informace o privátním odkazu pro Azure Monitor najdete v tématu [použití privátního odkazu Azure k bezpečnému připojení sítí k Azure monitor](../../azure-monitor/platform/private-link-security.md).

- Ujistěte se, že k datům automatizace máte jenom oprávnění prostřednictvím autorizovaných privátních sítí.
- Zajistěte, aby se data exfiltrace ze svých privátních sítí, a to tak, že definujete prostředek Azure Automation, který se připojuje přes
- Bezpečně připojte soukromou místní síť k Azure Automation pomocí ExpressRoute a privátního odkazu.
- Veškerý provoz se zachová v Microsoft Azure páteřní síti.

Další informace najdete v tématu  [klíčové výhody privátního propojení](../../private-link/private-link-overview.md#key-benefits).

## <a name="limitations"></a>Omezení

- V aktuální implementaci privátního propojení nemůžou cloudové úlohy účtu Automation získat přístup k prostředkům Azure zabezpečeným pomocí privátního koncového bodu. Například Azure Key Vault, Azure SQL, účet Azure Storage atd. K tomuto problému použijte místo toho [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) .
- Je nutné použít nejnovější verzi [agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) pro systém Windows nebo Linux.
- [Brána Log Analytics](../../azure-monitor/platform/gateway.md) nepodporuje privátní propojení.

## <a name="how-it-works"></a>Jak to funguje

Azure Automation privátní odkaz připojí jeden nebo více privátních koncových bodů (a proto virtuální sítě, které jsou součástí) k prostředku účtu Automation. Tyto koncové body jsou počítače, které používají Webhooky ke spuštění Runbooku, počítačů, které hostují roli Hybrid Runbook Worker a uzly Konfigurace požadovaného stavu (DSC).

Po vytvoření privátních koncových bodů pro automatizaci jsou všechny veřejné adresy URL pro automatizaci namapovány na jeden privátní koncový bod ve vaší virtuální síti. Vy nebo počítač můžete přímo kontaktovat adresy URL pro automatizaci.

### <a name="webhook-scenario"></a>Scénář Webhooku

Sady Runbook můžete spustit pomocí příspěvku na adrese URL Webhooku. Adresa URL vypadá například takto: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Scénář Hybrid Runbook Worker

Funkce Hybrid Runbook Worker uživatelů v Azure Automation umožňuje spouštět Runbooky přímo na počítačích Azure nebo mimo Azure, včetně serverů zaregistrovaných u serverů s podporou ARC Azure. Z počítače nebo serveru, který je hostitelem role, můžete spouštět Runbooky přímo na něm a s prostředky v prostředí pro správu těchto místních prostředků.

Koncový bod JRDS používá hybridní pracovní proces ke spouštění a zastavování runbooků, ke stažení runbooků do pracovního procesu a k odeslání streamu protokolu úlohy zpátky do služby Automation.Po povolení koncového bodu JRDS by adresa URL vypadala takto: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . To by zajistilo, že spuštění Runbooku na hybridním pracovním procesu připojeném k Azure Virtual Network může spouštět úlohy bez nutnosti otevřít odchozí připojení k Internetu.  

> [!NOTE]
>S aktuální implementací privátních odkazů pro Azure Automation podporuje pouze spouštění úloh v Hybrid Runbook Worker připojených ke službě Azure Virtual Network a nepodporuje cloudové úlohy.

## <a name="hybrid-worker-scenario-for-update-management"></a>Hybrid Worker scénář pro Update Management  

Systém Hybrid Runbook Worker podporuje sadu skrytých runbooků používaných funkcí Update Management, které jsou určeny k instalaci aktualizací určených uživatelem na počítačích se systémem Windows a Linux. Pokud je povolená Azure Automation Update Management, je počítač připojený k vašemu Log Analytics pracovnímu prostoru automaticky nakonfigurovaný jako Hybrid Runbook Worker systému.

[Informace o tom](../update-management/overview.md), & nakonfigurovat Update Management najdete v Update Management. Funkce Update Management má závislost na pracovním prostoru Log Analytics a proto vyžaduje propojení pracovního prostoru s účtem Automation. Pracovní prostor Log Analytics ukládá data shromážděná řešením a hostuje jeho prohledávání a zobrazení protokolů.

Pokud chcete, aby se počítače nakonfigurované pro správu aktualizací připojovaly ke službě Automation & Log Analytics pracovní prostor zabezpečeným způsobem prostřednictvím kanálu privátního propojení, je nutné povolit privátní propojení pro Log Analytics pracovní prostor propojený s účtem Automation nakonfigurovaným pomocí privátního odkazu.

Pomocí postupu popsaného v části [konfigurace Log Analytics](../../azure-monitor/platform/private-link-security.md#configure-log-analytics)můžete řídit, jak se dá k pracovnímu prostoru Log Analytics získat přístup mimo rozsah privátních odkazů. Pokud nastavíte možnost **povolí přístup k veřejné síti pro** ingestování na **ne**, počítače mimo připojené obory nemůžou do tohoto pracovního prostoru nahrávat data. Pokud nastavíte možnost **povolí přístup k veřejné síti pro dotazy** na **ne**, počítače mimo rozsah nebudou mít přístup k datům v tomto pracovním prostoru.

K povolení privátního odkazu pro uživatelské & systémových hybridních pracovních procesů použijte cílový dílčí prostředek **DSCAndHybridWorker** .

> [!NOTE]
> Počítače hostované mimo Azure, které jsou spravované pomocí Update Management a jsou připojené k virtuální síti Azure přes privátní partnerský vztah ExpressRoute, tunelové propojení VPN a partnerské virtuální sítě s použitím privátního odkazu na privátních koncových bodech.

### <a name="state-configuration-agentsvc-scenario"></a>Scénář konfigurace stavu (agentsvc)

Konfigurace stavu poskytuje službu Azure Configuration Management, která umožňuje zapisovat, spravovat a kompilovat konfigurace požadovaného stavu prostředí PowerShell pro uzly v jakémkoli cloudovém nebo místním datacentru.

Agent na počítači se registruje ve službě DSC a pak pomocí koncového bodu služby vyžádá konfiguraci DSC. Koncový bod služby agenta vypadá takto: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

Adresa URL veřejné & privátního koncového bodu by byla stejná, ale bude namapována na privátní IP adresu, pokud je povolené privátní propojení.

## <a name="planning-based-on-your-network"></a>Plánování na základě vaší sítě

Před nastavením prostředku účtu Automation zvažte požadavky na izolaci sítě. Vyhodnoťte přístup virtuálních sítí k veřejnému Internetu a omezení přístupu k vašemu účtu Automation (včetně nastavení oboru privátních odkazů na Azure Monitor protokolů, pokud jsou integrovány s vaším účtem Automation). V rámci vašeho plánu taky zahrňte revizi [záznamů DNS](./automation-region-dns-records.md) služby Automation, abyste zajistili, že podporované funkce budou fungovat bez problémů.

### <a name="connect-to-a-private-endpoint"></a>Připojení k privátnímu koncovému bodu

Vytvořte privátní koncový bod pro připojení k naší síti. Můžete ho vytvořit v [Azure Portal privátním centru odkazů](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Až budou změny publicNetworkAccess a privátního propojení uplatněny, může trvat až 35 minut, než se projeví.

V této části vytvoříte privátní koncový bod pro svůj účet Automation.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek > síť > soukromého centra odkazů**.

2. V části **centrum privátních odkazů – přehled** na možnost **vytvořit privátní připojení ke službě** vyberte možnost **Spustit**.

3. V části **vytvořit virtuální počítač základy** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název | Zadejte své *PrivateEndpoint*. |
    | Oblast | Vyberte **YourRegion**. |
    |||

4. Vyberte **Další: prostředek**.

5. V části **Vytvoření privátního koncového bodu – prostředek** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | V adresáři vyberte připojit k prostředku Azure.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Vyberte **Microsoft. Automation/automationAccounts**. |
    | Prostředek |Vybrat *myAutomationAccount*|
    |Cílový podprostředek |V závislosti na vašem scénáři vyberte *Webhook* nebo *DSCAndHybridWorker* .|
    |||

6. Vyberte **Další: Konfigurace**.

7. V části **Vytvoření privátního koncového bodu – konfigurace** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**SÍTĚ**| |
    | Virtuální síť| Vyberte *MyVirtualNetwork*. |
    | Podsíť | Vyberte *mySubnet*. |
    |**INTEGRACE S PRIVÁTNÍM DNS**||
    |Integrovat s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátního DNS |Select *(New) privatelink. Azure-Automation.NET* |
    |||

8. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci.

9. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

V **centru privátních odkazů** vyberte **soukromé koncové body** pro zobrazení prostředku privátního propojení.

![Privátní odkaz na prostředek služby Automation](./media/private-link-security/private-link-automation-resource.png)

Vyberte prostředek pro zobrazení všech podrobností. Tím se vytvoří nový privátní koncový bod pro váš účet Automation a přiřadí se mu privátní IP adresa z vaší virtuální sítě. **Stav připojení** se zobrazuje jako **schváleno**.

Podobně platí, že jedinečný plně kvalifikovaný název domény (FQDN) se vytvoří pro konfiguraci stavu (agentsvc) a pro modul runtime úloh Hybrid Runbook Worker (jrds). Každé z nich je přiřazená samostatná IP adresa z vaší virtuální sítě a **stav připojení** se zobrazí jako **schváleno**.

Pokud má příjemce služby oprávnění Azure RBAC na prostředku Automation, může zvolit metodu automatického schvalování. V takovém případě, když požadavek dosáhne prostředku zprostředkovatele automatizace, není od poskytovatele služeb vyžadována žádná akce a připojení je automaticky schváleno.

## <a name="set-public-network-access-flags"></a>Nastavit příznaky přístupu k veřejné síti

Můžete nakonfigurovat účet Automation pro odepření všech veřejných konfigurací a povolit jenom připojení prostřednictvím privátních koncových bodů, aby se zvýšilo zabezpečení sítě. Pokud chcete omezit přístup k účtu Automation jenom v rámci virtuální sítě a Nepovolit přístup z veřejného Internetu, můžete nastavit `publicNetworkAccess` vlastnost na `$false` .

Pokud je nastavení **přístup k veřejné síti** nastaveno na `$false` , jsou povolena pouze připojení prostřednictvím privátních koncových bodů a všechna připojení prostřednictvím veřejných koncových bodů budou odepřena s chybovou zprávou a stavem HTTP 401.

Následující skript prostředí PowerShell ukazuje, jak `Get` a `Set` vlastnost **přístup k veřejné síti** na úrovni účtu Automation:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

Vlastnost přístup k veřejné síti můžete také řídit z Azure Portal. Z účtu Automation vyberte v části **Nastavení účtu** možnost **izolace sítě** v levém podokně. Pokud je nastavení přístup k veřejné síti nastavené na **ne**, povolují se jenom připojení přes soukromé koncové body a všechna připojení přes veřejné koncové body jsou odepřená.

![Nastavení přístupu k veřejné síti](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>Konfigurace DNS

Při připojování k prostředku privátního propojení pomocí plně kvalifikovaného názvu domény (FQDN) jako součásti připojovacího řetězce je důležité správně nakonfigurovat nastavení DNS tak, aby se přeložilo na přidělenou privátní IP adresu. Existující služby Azure už můžou mít konfiguraci DNS, která se má použít při připojování přes Veřejný koncový bod. Vaše konfigurace DNS by se měla zkontrolovat a aktualizovat, aby se mohla připojit pomocí privátního koncového bodu.

Síťové rozhraní přidružené k privátnímu koncovému bodu obsahuje úplnou sadu informací potřebných ke konfiguraci DNS, včetně plně kvalifikovaného názvu domény a privátních IP adres přidělených pro daný prostředek privátního propojení.

Pomocí následujících možností můžete nakonfigurovat nastavení DNS privátních koncových bodů:

* Použijte soubor hostitele (doporučeno pouze pro testování). Soubor hostitele na virtuálním počítači můžete použít k přepsání prvního použití DNS k překladu názvů. Vaše položka DNS by měla vypadat jako v následujícím příkladu: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Použijte [privátní ZÓNU DNS](../../dns/private-dns-privatednszone.md). Privátní zóny DNS můžete použít k přepsání překladu DNS pro konkrétní soukromý koncový bod. Privátní zóna DNS se dá propojit s vaší virtuální sítí a vyřešit konkrétní domény. Pokud chcete, aby mohl Agent na virtuálním počítači komunikovat přes privátní koncový bod, vytvořte záznam Privátní DNS jako `privatelink.azure-automation.net` . Přidejte nové *mapování záznamů DNS na adresu IP* privátního koncového bodu.

* Použijte službu DNS pro přeposílání (volitelné). K přepsání překladu DNS pro konkrétní prostředek privátního propojení můžete použít server DNS pro přeposílání. Pokud je váš [Server DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) hostovaný ve virtuální síti, můžete vytvořit předávací pravidlo DNS pro použití privátní zóny DNS a zjednodušit tak konfiguraci všech prostředků privátního propojení.

Další informace najdete v tématu [Konfigurace DNS privátního koncového bodu Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Další kroky

Další informace o privátním koncovém bodu najdete v tématu [co je privátní koncový bod Azure](../../private-link/private-endpoint-overview.md).