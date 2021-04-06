---
title: Nejde odstranit virtuální síť v Azure | Microsoft Docs
description: Naučte se řešit potíže, při kterých nelze odstranit virtuální síť v Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b974af343907c98ebd7a318bc60a0e553a07a233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219347"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Řešení potíží: Nepodařilo se odstranit virtuální síť v Azure.

Při pokusu o odstranění virtuální sítě v Microsoft Azure může docházet k chybám. Tento článek popisuje kroky pro řešení potíží, které vám pomůžou tento problém vyřešit.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Pokyny při řešení potíží 

1. [Ověřte, jestli je ve virtuální síti spuštěná Brána virtuální sítě](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Ověřte, zda je ve virtuální síti spuštěna Aplikační brána](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Ověřte, jestli ve virtuální síti stále existují instance kontejnerů Azure](#check-whether-azure-container-instances-still-exist-in-the-virtual-network).
4. [Ověřte, jestli je ve virtuální síti povolená Azure Active Directory Doménová služba](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
5. [Ověřte, jestli je virtuální síť připojená k jinému prostředku](#check-whether-the-virtual-network-is-connected-to-other-resource).
6. [Ověřte, jestli virtuální počítač pořád běží ve virtuální síti](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
7. [Ověřte, jestli je virtuální síť zablokovaná při migraci](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Ověřte, jestli je ve virtuální síti spuštěná Brána virtuální sítě.

Pokud chcete odebrat virtuální síť, musíte nejdřív odebrat bránu virtuální sítě.

V případě klasických virtuálních sítí přejdete na Azure Portal na stránce **Přehled** klasické virtuální sítě. Pokud je brána ve virtuální síti v části **připojení VPN** spuštěná, zobrazí se IP adresa brány. 

![Ověřte, jestli je brána spuštěná.](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Pro virtuální sítě navštivte stránku **Přehled** virtuální sítě. Ověřte **připojená zařízení** pro bránu virtuální sítě.

![Snímek obrazovky se seznamem připojených zařízení pro virtuální síť v Azure Portal. Brána virtuální sítě je v seznamu zvýrazněná.](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Předtím, než budete moci bránu odebrat, odeberte nejprve všechny objekty **připojení** v bráně. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Ověřte, jestli je ve virtuální síti spuštěná Aplikační brána.

Přejít na stránku **Přehled** virtuální sítě. Ověřte **připojená zařízení** pro aplikační bránu.

![Snímek obrazovky se seznamem připojených zařízení pro virtuální síť v Azure Portal. Application Gateway je v seznamu zvýrazněná.](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Pokud je k dispozici brána Application Gateway, je nutné ji odebrat předtím, než budete moci odstranit virtuální síť.

### <a name="check-whether-azure-container-instances-still-exist-in-the-virtual-network"></a>Ověřte, jestli ve virtuální síti pořád existují instance kontejnerů Azure.

1. V Azure Portal přejdete na stránku **Přehled** skupiny prostředků.
1. V záhlaví seznamu prostředků skupiny prostředků vyberte **Zobrazit skryté typy**. Typ profilu sítě je ve výchozím nastavení skrytý Azure Portal.
1. Vyberte profil sítě, který souvisí se skupinami kontejnerů.
1. Vyberte **Odstranit**.

   ![Snímek obrazovky se seznamem skrytých profilů sítě](media/virtual-network-troubleshoot-cannot-delete-vnet/container-instances.png)

1. Znovu odstraňte podsíť nebo virtuální síť.

Pokud tyto kroky problém nevyřeší, pomocí těchto [příkazů Azure CLI](../container-instances/container-instances-vnet.md#clean-up-resources) vyčistěte prostředky. 

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Ověřte, jestli je ve virtuální síti povolená Azure Active Directory Doménová služba.

Pokud je služba Doména služby Active Directory povolená a připojená k virtuální síti, nemůžete tuto virtuální síť odstranit. 

![Obrazovka obrazovky Azure AD Domain Services v Azure Portal Je zvýrazněno pole k dispozici v Virtual Network/podsíti.](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Pokud chcete službu zakázat, přečtěte si téma [zakázání Azure Active Directory Domain Services pomocí Azure Portal](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Ověřte, jestli je virtuální síť připojená k jinému prostředku.

Kontrolovat propojení okruhů, připojení a partnerské vztahy virtuálních sítí. Některé z těchto příčin můžou způsobit selhání odstranění virtuální sítě. 

Doporučené pořadí odstranění je následující:

1. Připojení brány
2. Brány
3. IP
4. Partnerské vztahy virtuálních sítí
5. App Service Environment (pomocného mechanismu)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Ověřte, jestli virtuální počítač pořád běží ve virtuální síti.

Ujistěte se, že virtuální počítač není ve virtuální síti.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Ověřte, jestli je virtuální síť zablokovaná při migraci.

Pokud je virtuální síť zablokovaná v rámci stavu migrace, nedá se odstranit. Spusťte následující příkaz k přerušení migrace a pak virtuální síť odstraňte.

```azurepowershell
Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort
```

## <a name="next-steps"></a>Další kroky

- [Azure Virtual Network](virtual-networks-overview.md)
- [Nejčastější dotazy ke službě Azure Virtual Network](virtual-networks-faq.md)