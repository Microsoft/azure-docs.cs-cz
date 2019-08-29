---
title: Určení velikosti SAP HANA v Azure (velké instance) | Microsoft Docs
description: Určení velikosti SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2540a0f9ea702ff620ccd4b68208cbb2e70aafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101147"
---
# <a name="sizing"></a>Velikost

Velikost pro velkou instanci HANA se neliší od obecné velikosti pro HANA. Pro existující a nasazené systémy, které chcete přesunout z jiných RDBMS na HANA, poskytuje SAP řadu sestav, které se spouštějí ve stávajících systémech SAP. Pokud se databáze přesune na HANA, tyto sestavy kontrolují data a počítají požadavky na paměť pro instanci HANA. Další informace o tom, jak tyto sestavy spustit a jak získat nejnovější opravy nebo verze, najdete v následujících komentářích ke službě SAP:

- [Poznámky SAP #1793345 – určení velikosti pro SAP Suite na HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Zpráva SAP Poznámka #1872170 – sada v HANA a S/4 HANA – sestava velikosti](https://launchpad.support.sap.com/#/notes/1872170)
- [Poznámka ke SAP #2121330 – Nejčastější dotazy: Sestava velikosti SAP BW v HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [Poznámka SAP Poznámka #1736976 – sestava velikosti pro ČERNOBÍLé nasazení v HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [Poznámka SAP #2296290 – Nová sestava velikosti pro šířku v HANA](https://launchpad.support.sap.com/#/notes/2296290)

Pro implementace zelených polí je k dispozici Sizer SAP pro výpočet požadavků na paměť pro implementaci softwaru SAP nad HANA.

Požadavky na paměť pro HANA zvýšit objem dat roste. Zamyslete se nad aktuální spotřebou paměti, abyste mohli předpovědět, co bude v budoucnosti. Na základě požadavků na paměť můžete namapovat svou žádost na jednu z SKU velkých instancí HANA.

**Další postup**
- Odkazy na [požadavky na registraci](hana-onboarding-requirements.md)