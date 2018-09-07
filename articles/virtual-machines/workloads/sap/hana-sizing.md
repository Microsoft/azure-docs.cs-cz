---
title: Změna velikosti SAP Hana v Azure (velké instance) | Dokumentace Microsoftu
description: K určení velikosti SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d0d72a9ef592579b21d082111ab8ad00f8995de2
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028061"
---
# <a name="sizing"></a>Velikost

Nastavení velikosti pro velké Instance HANA se nijak neliší od změny velikosti pro HANA obecně. U stávajících a nasadit systémy, které chcete přesunout z jiných relační databázový systém k HANA, SAP poskytuje řadu sestav, které běží na vašich stávajících systémů SAP. Pokud databáze byla převedena do platformy HANA, tyto sestavy zkontrolujte data a výpočet požadavků na paměť pro instanci HANA. Další informace o tom, jak spustit tyto sestavy a získat jeho nejnovější opravy nebo verze si přečtěte následující poznámky SAP:

- [Poznámka SAP #1793345 - změny velikosti pro SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Poznámka: #1872170 - Suite on HANA a s/4 HANA zpráva o velikosti SAP](https://launchpad.support.sap.com/#/notes/1872170)
- [Poznámka SAP #2121330 – nejčastější dotazy: SAP BW on HANA velikosti sestav](https://launchpad.support.sap.com/#/notes/2121330)
- [Poznámka: #1736976 – Sestava nastavení velikosti pro BW on HANA SAP](https://launchpad.support.sap.com/#/notes/1736976)
- [Poznámka: #2296290 – nové velikosti sestavy pro BW on HANA SAP](https://launchpad.support.sap.com/#/notes/2296290)

Pro implementace zelené louce je možné vypočítat požadavky na paměť implementace SAP software nad HANA SAP rychlé přizpůsobení velikosti symbolů.

Požadavky na paměť pro HANA zvýšit objem dat roste. Mějte na paměti z aktuální využití paměti můžete odhadnout, co se děje bude v budoucnu. Podle požadavků na vysoce paměťově, pak můžete mapovat vaši vyžadují do jedné skladové položky velké Instance HANA.

**Další kroky**
- Přečtěte si [požadavků na připojení](hana-onboarding-requirements.md)