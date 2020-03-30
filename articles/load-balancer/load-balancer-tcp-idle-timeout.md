---
title: Konfigurace časového limitu nečinnosti tcp protokolu TCP v Azure
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat časový limit nečinnosti tcp vykladače zatížení azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456824"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, musíte taky `Connect-AzAccount` spustit k vytvoření připojení s Azure.

## <a name="tcp-idle-timeout"></a>Časový limit nečinnosti protokolu TCP
Azure Load Balancer má nastavení časového limitu nečinnosti 4 minuty až 30 minut. Ve výchozím nastavení je nastavena na 4 minuty. Pokud je doba nečinnosti delší než hodnota časového času, neexistuje žádná záruka, že relace TCP nebo HTTP je udržována mezi klientem a cloudovou službou.

Po zavření připojení se může zobrazit následující chybová zpráva: "Základní připojení bylo ukončeno: Server uzavřel připojení, u kterého bylo očekáváno, že bude udržováno naživu."

Běžnou praxí je použití tcp keep-alive. Tento postup udržuje připojení aktivní po delší dobu. Další informace naleznete v těchto [příkladech rozhraní .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). S keep-alive povoleno pakety jsou odesílány během období nečinnosti na připojení. Pakety keep-alive zajišťují, že není dosaženo hodnoty časového limitu nečinnosti a připojení je udržováno po dlouhou dobu.

Nastavení funguje pouze pro příchozí připojení. Chcete-li zabránit ztrátě připojení, nakonfigurujte udržování protokolu TCP s intervalem menším, než je nastavení časového limitu nečinnosti, nebo zvyšte hodnotu časového limitu nečinnosti. Pro podporu těchto scénářů byla přidána podpora konfigurovatelného časového limitu nečinnosti.

Tcp keep-alive funguje pro scénáře, kde životnost baterie není omezení. Nedoporučuje se pro mobilní aplikace. Použití tcp keep-alive v mobilní aplikaci může vybít baterii zařízení rychleji.

![Časový čas protokolu TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Následující části popisují, jak změnit nastavení časového limitu nečinnosti pro veřejné prostředky IP a vyrovnávání zatížení.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurace časového ochazu protokolu TCP pro veřejnou IP adresu na úrovni instance na 15 minut

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

Parametr `IdleTimeoutInMinutes` je volitelný. Pokud není nastavena, výchozí časový limit je 4 minuty. Přijatelný časový rozsah je 4 až 30 minut.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Nastavení časového omezení protokolu TCP u pravidla s vyrovnáváním zatížení na 15 minut

Chcete-li nastavit časový limit nečinnosti pro vyrovnávání zatížení, je na pravidle s vyrovnáváním zatížení nastaveno "IdleTimeoutInMinutes". Například:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Další kroky

[Přehled interního nástroje pro vyrovnávání zatížení](load-balancer-internal-overview.md)

[Začínáme s konfigurací internetového zařízení pro vyrovnávání zatížení](quickstart-create-standard-load-balancer-powershell.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)
