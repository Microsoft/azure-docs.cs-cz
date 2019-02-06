---
title: Technické informace pro nabídku aplikace Power BI – Azure Marketplace | Dokumentace Microsoftu
description: Nakonfigurujte pole technické informace pro aplikace Power BI nabídky webu Microsoft Marketplace AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: d96ef2fd318d6164e1b7dfc5c4b72d6957af0f3e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744430"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI Apps technické informace o kartě

**Technické informace o** karty **nová nabídka** je stránka, kde se adresa URL balíčku a žádné další informace požadované pro ověření nová nabídka poskytuje instalační služby Power BI.  Počáteční verze všechny aplikace Power BI jsou zdarma, k dispozici ke stažení z AppSource bez dalších poplatků. V důsledku toho nebudete mít k definování skladové jednotky (SKU) pro tento typ nabídky.

![Technické informace o kartě](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Technické informace o polích 

V **technické informace o** kartu, je nutné zadat následující pole.  Připojený hvězdička (*) na popisek pole označuje, že je povinný.

|        Pole          |  Popis                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Adresa URL instalační program**     | Adresa vytvořené pomocí Power BI po publikování aplikace a jeho přesun do produkčního prostředí.  Další informace o tom, jak generovat adresu URL, najdete v tématu [publikování aplikací služby v Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Pokyny k ověřování**  |  Volitelný text, který pokyny (max 3000 znaků) pro ověřovací tým Microsoft pomáhat při konfiguraci, připojení a testování vaší aplikace, včetně: obvyklý konfigurační nastavení, účty testovacích nebo parametry, které můžete použít k testování možnost "Připojit dat." atd. Tyto informace pouze budou viditelné pro ověřovací tým a používá se pouze pro účely ověření.  |
| **Tuto aplikaci vytvořili jako balíček obsahu Power BI?** | V současné době se interně používá pole. Ponechte nastavenou na výchozí hodnotu, `No`; v opačném případě toto pole a změna `Yes` by mohl bránit publikování.  |  
|  |  |


## <a name="next-steps"></a>Další postup

V dalším [podrobnosti z prodejních míst](./cpp-storefront-details-tab.md) kartu, bude poskytovat uvádění na trh a právní informace pro vaši aplikaci.

