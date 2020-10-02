---
title: Konfigurace časového limitu nečinnosti nástroje pro vyrovnávání zatížení TCP v Azure
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat Azure Load Balancer časový limit nečinnosti protokolu TCP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 374ec9daf6255a0a05ed9b2f03cc01b90785493c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628158"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Nakonfigurujte nastavení časového limitu nečinnosti protokolu TCP pro Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="tcp-idle-timeout"></a>Časový limit nečinnosti protokolu TCP
Azure Load Balancer má nastavení časového limitu nečinnosti po dobu 4 minuty až 30 minut. Ve výchozím nastavení je nastaveno na 4 minuty. Pokud je období neaktivity delší než hodnota časového limitu, není nijak zaručeno, že relace TCP nebo HTTP mezi klientem a vaší cloudovou službou bude zachovaná.

Po zavření připojení může klientská aplikace zobrazit následující chybovou zprávu: "základní připojení bylo zavřeno: připojení, které bylo očekáváno jako aktivní, bylo zavřeno serverem."

Běžným postupem je používání udržování připojení TCP. Tento postup zachovává aktivní připojení po delší dobu. Další informace najdete v těchto [příkladech rozhraní .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Když je povolená možnost Keep-Alive, pakety se odešlou během období nečinnosti na připojení. Pakety Keep-Alive zajišťují, že hodnota časového limitu nečinnosti není dosažena a připojení je udržováno po dlouhou dobu.

Nastavení funguje jenom pro příchozí připojení. Aby nedošlo ke ztrátě připojení, nakonfigurujte udržování připojení TCP pomocí intervalu kratšího, než je nastavení časového limitu nečinnosti, nebo zvyšte hodnotu časového limitu nečinnosti. Pro podporu těchto scénářů byla přidána podpora konfigurovatelného časového limitu nečinnosti.

Udržování připojení TCP funguje ve scénářích, kdy životnost baterie není omezením. Nedoporučuje se pro mobilní aplikace. Při použití udržování připojení TCP v mobilní aplikaci můžete baterii zařízení rychleji vyprázdnit.

![Časový limit TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Následující části popisují, jak změnit nastavení časového limitu nečinnosti pro prostředky veřejné IP adresy a nástroje pro vyrovnávání zatížení.

>[!NOTE]
> Časový limit nečinnosti protokolu TCP nemá vliv na pravidla vyrovnávání zatížení na protokolu UDP.


## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Nakonfigurujte časový limit TCP pro veřejnou IP adresu na úrovni instance na 15 minut.

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

Parametr `IdleTimeoutInMinutes` je volitelný. Pokud není nastavené, výchozí časový limit je 4 minuty. Přípustný rozsah časového limitu je 4 až 120 minut.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Nastavte časový limit TCP pro pravidlo vyrovnávání zatížení na 15 minut.

Chcete-li nastavit časový limit nečinnosti pro nástroj pro vyrovnávání zatížení, je v pravidle vyrovnávání zatížení nastaven atribut ' IdleTimeoutInMinutes '. Například:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Další kroky

[Interní přehled nástroje pro vyrovnávání zatížení](load-balancer-internal-overview.md)

[Začínáme s konfigurací internetového nástroje pro vyrovnávání zatížení](quickstart-load-balancer-standard-public-powershell.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)
