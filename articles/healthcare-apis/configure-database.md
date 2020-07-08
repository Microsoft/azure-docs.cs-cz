---
title: Konfigurace nastavení databáze v rozhraní Azure API pro FHIR
description: Tento článek popisuje, jak nakonfigurovat nastavení databáze v rozhraní Azure API pro FHIR.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: adc6fdf144927d10f811a00aa33f244cfdc25042
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871761"
---
# <a name="configure-database-settings"></a>Konfigurovat nastavení databáze 

Azure API pro FHIR používá k ukládání dat databázi. Výkon podkladové databáze závisí na počtu jednotek žádosti (RU) vybraných během zřizování služby nebo v nastavení databáze po zřízení služby.

Rozhraní Azure API pro FHIR vypůjčuje koncept ru z Cosmos DB (v části [žádosti o přijetí v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/request-units)) se při nastavování výkonu podkladové databáze. 

Aby bylo zajištěno, že pro vaši databázi jsou neustále k dispozici dostatečné systémové prostředky, musí být zajištěna propustnost. Kolik ru potřebujete pro vaši aplikaci, závisí na operacích, které provádíte. Operace mohou být v rozsahu od jednoduchých čtení a zápisu až po složitější dotazy. 

> [!NOTE]
> Jelikož různé operace spotřebovávají jiný počet RU, vrátíme v hlavičce odpovědi skutečný počet ru spotřebovaných při každém volání rozhraní API. Tímto způsobem můžete profilovat počet ru spotřebovaných vaší aplikací.

## <a name="update-throughput"></a>Aktualizace propustnosti
Pokud chcete toto nastavení v Azure Portal změnit, přejděte do rozhraní Azure API pro FHIR a otevřete okno databáze. Dále v závislosti na vašich požadavcích na výkon změňte zřízenou propustnost na požadovanou hodnotu. Hodnotu můžete změnit na maximálně 10 000 RU/s. Pokud potřebujete vyšší hodnotu, obraťte se na podporu Azure.

> [!NOTE] 
> Vyšší hodnota znamená vyšší propustnost FHIR a vyšší náklady na službu Azure API.

![Cosmos DB konfigurace](media/database/database-settings.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak aktualizovat ru pro Azure API pro FHIR. Dále nasaďte plně spravované rozhraní Azure API pro FHIR:
 
>[!div class="nextstepaction"]
>[Nasazení Azure API for FHIR](fhir-paas-portal-quickstart.md)