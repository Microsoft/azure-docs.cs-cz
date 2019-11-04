---
title: Přehled nasazení Preview Azure Firewall Manageru
description: Seznamte se s kroky nasazení na nejvyšší úrovni, které jsou nezbytné pro Azure Firewall Manager Preview
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502027"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Přehled nasazení Preview Azure Firewall Manageru

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Je k dispozici více než jeden způsob nasazení Azure Firewall Manager Preview, ale doporučuje se tento obecný postup.

## <a name="prerequisites"></a>Předpoklady

> [!IMPORTANT]
> Ukázková verze Azure Firewall Manageru musí být explicitně povolená pomocí příkazu `Register-AzProviderFeature` PowerShellu.
>Z příkazového řádku PowerShellu spusťte následující příkazy:
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>Dokončení registrace funkce trvá až 30 minut. Spusťte následující příkaz a ověřte > stav registrace:
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>Obecný proces nasazení

1. Vytvoření architektury hub a paprsků

   - Vytvořte zabezpečeného virtuálního rozbočovače pomocí správce Azure Firewall a přidejte připojení k virtuální síti.<br>*ani*<br>
   - Vytvořte virtuální síť WAN hub a přidejte připojení k virtuální síti.
2. Vybrat poskytovatele zabezpečení

   - Při vytváření zabezpečeného virtuálního rozbočovače se dokončila.<br>*ani*<br>
   - Převeďte stávající virtuální centrum sítě WAN na zabezpečené virtuální centrum.
3. Vytvoření zásady brány firewall a její přidružení ke svému centru

   - Platí pouze při použití Azure Firewall.
   - Zásady zabezpečení jako služby (SECaaS) třetích stran jsou konfigurovány prostřednictvím prostředí pro správu partnerů.
4. Konfigurace nastavení směrování pro směrování provozu do zabezpečeného centra

   - Snadná směrování provozu do zabezpečeného centra pro filtrování a protokolování bez uživatelsky definovaných tras (UDR) ve virtuálních sítích rozbočovače pomocí stránky nastavení trasy zabezpečeného virtuálního počítače.

> [!NOTE]
> - Pro každou virtuální síť WAN na jednu oblast nemůžete mít více než jedno rozbočovač. Můžete ale do této oblasti přidat víc virtuálních sítí WAN, abyste to dosáhli.
> - Pro rozbočovače v vWAN se nedají překrývat adresní prostory IP adres.
> - Připojení k virtuální síti rozbočovače musí být ve stejné oblasti jako centrum.

## <a name="next-steps"></a>Další kroky

- [Kurz: zabezpečení cloudové sítě pomocí nástroje Azure Firewall Manager ve verzi Preview pomocí Azure Portal](secure-cloud-network.md)