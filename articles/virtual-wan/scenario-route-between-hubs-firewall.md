---
title: 'Scénář: Azure Firewall a směrování Interhub'
titleSuffix: Azure Virtual WAN
description: Scénáře směrování směrování mezi několika rozbočovači, které mají Azure Firewall
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568387"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>Scénář: Azure Firewall-Interhub (Preview)

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři je cílem směrování mezi několika rozbočovači, které obsahují Azure Firewall. Informace o směrování virtuálního rozbočovače najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architektura scénáře

V tomto scénáři předpokládáme následující konfiguraci:

* Máte několik Center s Azure Firewall v každém centru.
* Používáte zabezpečený virtuální rozbočovač.
* Byly nastaveny příslušné zásady pro přenos privátních přenosů (VNet), internetu a větví.
* V2I (VNet-to-Internet), V2B (VNet-to-VNet), (VNET-to-VNet), procházejí prostřednictvím Azure Firewall v každém centru.

Další rozhodnutí:

* Pro scénář mezi rozbočovači a Azure Firewall předpokládáme, že paprskový virtuální sítě není spojený s oddělenými směrovacími tabulkami. (např. **virtuální síť 1** přidružená ke **směrovací tabulce**a a **virtuální síť 2** přidružená ke **směrovací tabulce B**). Všechny virtuální sítě se přidruží ke stejné směrovací tabulce, ve které se nacházejí trasy pro Azure Firewall.
* Zabezpečení prostřednictvím Azure Firewall je v současné době omezené jenom na **větvení <-> VNet** a **internetovou** komunikaci. Tok mezi větvemi prostřednictvím Azure Firewall aktuálně není podporovaný.

**Obrázek 1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="Architektura":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Pracovní postup scénáře

Chcete-li nakonfigurovat tento scénář, proveďte následující kroky:

### <a name="step-1"></a><a name="step-1"></a>Step 1

Za předpokladu, že už máte zabezpečená připojení prostřednictvím správce Azure Firewall, je prvním krokem zajistit, aby všechna připojení větví a virtuálních sítí rozšířila **žádná**. To je nutné k zajištění toho, aby se provoz nastavil přes Azure Firewall.

1. Vyberte centrum a upravte tabulku směrování **none** .
1. Aktualizovat **šíření** pro výběr všech větví a všech virtuální sítě.

### <a name="step-2"></a><a name="step-2"></a>Krok 2

Nastavte vlastní směrovací tabulku pro každé centrum.

1. V případě **centra 1**vytvořte **RT_Hub1**směrovací tabulky.

    * Pokud jste použili správce Azure Firewall, automaticky vytvoří trasu pro 0.0.0.0/0 s dalším segmentem směrování **AZFW1** ve výchozí směrovací tabulce centra. Toto nastavení budeme upravovat v kroku 3. Pro **RT_Hub1**vytvořte položku pro 0.0.0.0/0 explicitně pomocí dalšího segmentu směrování **AZFW1**. Toto nastavení aktivuje V2V, B2V a V2I prostřednictvím AZFW1.
    * Vzhledem k tomu, že chcete, aby se větve **hub 2** a virtuální sítě dostaly prostřednictvím **AZFW2** z **centra 1** (místo přes AZFW1), musíte přidat další 2 agregované trasy pro větve **centra 2** (10.5.0.0/16->AZFW2) a virtuální sítě (10.2.0.0/16->AZFW2).

1. V případě **centra 2**vytvořte **RT_Hub2**směrovací tabulky.

    * Pokud jste použili správce Azure Firewall, automaticky vytvoří trasu pro 0.0.0.0/0 s dalším segmentem směrování **AZFW2** ve výchozí směrovací tabulce centra. Toto nastavení budeme upravovat v kroku 3. Pro **RT_Hub2**vytvořte položku pro 0.0.0.0/0 explicitně pomocí dalšího segmentu směrování **AZFW2**. Toto nastavení aktivuje V2V, B2V a V2I prostřednictvím **AZFW2**.
    * Vzhledem k tomu, že chcete provoz mezi rozbočovači chránit pomocí **AZFW2u**centra 2, nemusíte explicitně přidávat krok podobný druhé odrážce v předchozím kroku centra 1.

### <a name="step-3"></a><a name="step-3"></a>Krok 3

Upravte **výchozí směrovací tabulku** v každém z rozbočovačů, abyste přidali statickou trasu pro **virtuální síť** (B2V) a **větev do Internetu** (B2I) toků prostřednictvím Azure firewall. Větev do větve se v tuto chvíli nepodporuje prostřednictvím Azure Firewall.

1. Pro **výchozí směrovací tabulku centra 1**:

    * Větvení **do větví hub 2 přes AZFW2**: 10.5.0.0/16->AZFW2. Tato konfigurace nastavuje trasu pro bránu firewall centra 2.
    * **Větev do hub 2 virtuální sítě prostřednictvím AZFW2**: 10.2.0.0/16->AZFW2. Tato konfigurace nastavuje trasu pro bránu firewall centra 2.
    * **Větev do větve (místní)/větev k virtuální síti (místní)/větev na Internet**: 0.0.0.0/0->AZFW1.

2. Pro **výchozí směrovací tabulku centra 2**:

    * Větev do větve (místní a vzdálená)/větev do virtuální sítě (místní a vzdálená)/větev do Internetu: 0.0.0.0/0->AZFW2.

Výsledkem bude, že se změní konfigurace směrování, jak je vidět na následujícím obrázku.

**Obrázek 2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="pracovní postup":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
* Další informace o tom, jak nakonfigurovat směrování virtuálního rozbočovače, najdete v tématu [Jak konfigurovat směrování virtuálního rozbočovače](how-to-virtual-hub-routing.md).
