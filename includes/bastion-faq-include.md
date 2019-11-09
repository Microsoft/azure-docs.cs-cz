---
title: zahrnout soubor
description: zahrnout soubor
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b4e479405a9606a8353785828d0c9c94ef8c32ed
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850308"
---
### <a name="regions"></a>Které oblasti jsou k dispozici?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Potřebuji na svém virtuálním počítači veřejnou IP adresu?

Na virtuálním počítači Azure, ke kterému se připojujete pomocí služby Azure bastionu, nepotřebujete veřejnou IP adresu. Služba bastionu otevře relaci RDP/SSH nebo připojení k virtuálnímu počítači přes soukromou IP adresu vašeho virtuálního počítače v rámci vaší virtuální sítě.

### <a name="rdpssh"></a>Potřebuji klienta RDP nebo SSH?

K využití připojení RDP/SSH k virtuálnímu počítači Azure na webu Azure Portal nepotřebujete klienta SSH ani RDP. Pomocí [Azure Portal](https://portal.azure.com) můžete získat přístup RDP/SSH k virtuálnímu počítači přímo v prohlížeči.

### <a name="agent"></a>Potřebuji na virtuálním počítači Azure agenta, který je spuštěný?

Do virtuálních počítačů Azure a prohlížeče nemusíte instalovat žádné agenty ani jiný software. Služba Bastion je bez agentů a pro RDP/SSH nevyžaduje žádný další software.

### <a name="browsers"></a>Které prohlížeče se podporují?

Použijte prohlížeč Microsoft Edge nebo Google Chrome ve Windows. Na počítačích Apple Mac používejte prohlížeč Google Chrome. V systému Windows i Mac se také podporuje Microsoft Edge Chromium.

### <a name="roles"></a>Vyžadují se pro přístup k virtuálnímu počítači nějaké role?

Aby bylo možné vytvořit připojení, jsou vyžadovány následující role:

* Role čtenář na virtuálním počítači
* Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
* Role čtenář v prostředku Azure bastionu

### <a name="pricingpage"></a>Jaké jsou ceny?

Další informace najdete na [stránce s cenami](https://aka.ms/BastionHostPricing).

### <a name="session"></a>Proč se zobrazí chybová zpráva vypršela platnost vaší relace před spuštěním relace bastionu?

Relace by měla být iniciována pouze z Azure Portal. Přihlaste se k Azure Portal a znovu spusťte relaci. Pokud přejdete na adresu URL přímo z jiné relace nebo karty prohlížeče, tato chyba se očekává. Pomáhá zajistit, aby byla relace bezpečnější a aby k ní bylo možné přicházet pouze prostřednictvím Azure Portal.

### <a name="keyboard"></a>Jaká rozložení klávesnice jsou podporovaná během vzdálené relace bastionu?

Azure bastionu v současné době podporuje rozložení klávesnice en-US-QWERTY uvnitř virtuálního počítače.  Podpora pro jiné národní prostředí pro rozložení klávesnice probíhá v práci.

