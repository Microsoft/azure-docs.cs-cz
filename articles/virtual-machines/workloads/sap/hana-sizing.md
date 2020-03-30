---
title: Dimenzování SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Dimenzování SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616912"
---
# <a name="sizing"></a>Velikosti

Změna velikosti pro velké instance HANA se neliší od velikosti pro HANA obecně. Pro stávající a nasazené systémy, které chcete přesunout z jiných RDBMS na HANA, SAP poskytuje řadu sestav, které běží na stávajících systémech SAP. Pokud je databáze přesunuta na HANA, tyto sestavy zkontrolovat data a vypočítat požadavky na paměť pro instanci HANA. Další informace o tom, jak spustit tyto sestavy a získat jejich nejnovější opravy nebo verze, přečtěte si následující poznámky SAP:

- [SAP Note #1793345 - Velikost pro SAP Suite na HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Sada na HANA a S/4 HANA sestavy velikosti](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 – nejčastější dotazy: SAP BW na sestavě velikosti HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - sestava velikosti pro BW na HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - Nová sestava velikosti pro BW na HANA](https://launchpad.support.sap.com/#/notes/2296290)

Pro zelené pole implementace SAP Quick Sizer je k dispozici pro výpočet požadavků na paměť implementace softwaru SAP nad HANA.

Požadavky na paměť pro HANA zvýšit s růstem objemu dat. Uvědomte si aktuální spotřebu paměti, která vám pomůže předpovědět, co to bude v budoucnu. Na základě požadavků na paměť pak můžete mapovat poptávku do jednoho z velkých sad Itu HANA velké instance.

**Další kroky**
- Odkazovat [na požadavky na připojení](hana-onboarding-requirements.md)