---
title: zahrnout soubor
description: zahrnout soubor
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94356640"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Můžu I nadále nasazovat víc hostitelů bastionu napříč partnerskými virtuálními sítěmi?

Ano. Ve výchozím nastavení uživatel vidí hostitele bastionu, který je nasazený ve stejné virtuální síti, ve které se nachází virtuální počítač. V nabídce **připojit** ale uživatel může zobrazit několik hostitelů bastionu zjištěných napříč partnerskými sítěmi. Můžou vybrat hostitele bastionu, který preferuje k použití pro připojení k virtuálnímu počítači nasazenému ve virtuální síti.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Pokud se virtuální sítě v různých předplatných nasazených partnerských uzlů, bude připojení prostřednictvím bastionu fungovat?

Ano, připojení přes bastionu bude fungovat i pro partnerský virtuální sítě v rámci jiného předplatného pro jednoho tenanta. Odběry ve dvou různých klientech nejsou podporovány. Pokud si chcete prohlédnout bastionu v rozevírací nabídce **připojit** , musí uživatel vybrat, ke kterým předplatným má mít přístup, **> globální předplatné**.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="Filtr globálních předplatných" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Mám přístup k virtuální síti s partnerským vztahem, ale nevidím tam nasazený virtuální počítač.

Ujistěte se, že má uživatel oprávnění **ke čtení** pro virtuální počítač i pro partnerský virtuální síť. Dále zaškrtněte v části IAM, že má uživatel oprávnění **ke čtení** pro následující prostředky:

* Role čtenáře na virtuálním počítači.
* Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
* Role čtecího modulu v prostředku Azure bastionu
* Role čtecího modulu na Virtual Network (není nutné, pokud není k dispozici žádná Partnerská virtuální síť).

|Oprávnění|Description|Typ oprávnění|
|---|---| ---|
|Microsoft. Network/bastionHosts/Read |Načte hostitele bastionu.|Akce|
|Microsoft. Network/virtualNetworks/BastionHosts/Action |Získá odkazy na hostitele bastionu v Virtual Network.|Akce|
|Microsoft. Network/virtualNetworks/bastionHosts/default/Action|Získá odkazy na hostitele bastionu v Virtual Network.|Akce|
|Microsoft. Network/networkInterfaces/Read|Načte definici síťového rozhraní.|Akce|
|Microsoft. Network/networkInterfaces/IPConfiguration/Read|Získá definici konfigurace IP adresy síťového rozhraní.|Akce|
|Microsoft. Network/virtualNetworks/Read|Získat definici virtuální sítě|Akce|
|Microsoft. Network/virtualNetworks/subnets/virtualMachines/Read|Získá odkazy na všechny virtuální počítače v podsíti virtuální sítě.|Akce|
|Microsoft. Network/virtualNetworks/virtualMachines/Read|Získá odkazy na všechny virtuální počítače ve virtuální síti.|Akce|