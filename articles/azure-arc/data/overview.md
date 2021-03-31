---
title: Co jsou datové služby s podporou služby Azure Arc
description: Zavádí datové služby s povoleným obloukem Azure
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: ef16a4c0b6ea40fb4934307916f1fe79ccea72f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97609135"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Co jsou datové služby s podporou služby Azure Arc (Preview)?

Azure ARC umožňuje spouštět místní datové služby Azure, na hraničních zařízeních a ve veřejných cloudech pomocí Kubernetes a infrastruktury podle vašeho výběru.

V současné době jsou ve verzi Preview k dispozici následující datové služby s podporou ARC Azure:

- Spravovaná instance SQL
- PostgreSQL škálování

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Vždy aktuální

Datové služby s podporou ARC Azure, jako je třeba spravovaná instance Azure ARC a Azure ARC s povoleným PostgreSQL, dostávají aktualizace na častou dobu, včetně servisních oprav a nových funkcí podobných prostředí v Azure. K dispozici jsou aktualizace z Microsoft Container Registryu a tempem nasazení, které jste nastavili v souladu s vašimi zásadami. V takovém případě mohou místní databáze zůstat stále aktuální a přitom zajistit zachování řízení. Vzhledem k tomu, že datové služby s podporou ARC Azure jsou služby předplatného, už nebudete mít k dispozici žádné situace na podporu pro vaše databáze.

## <a name="elastic-scale"></a>Elastické škálování

Cloudová flexibilita v místním prostředí umožňuje, aby se databáze v Azure nastavily dynamicky v podstatě stejně jako v Azure, a to na základě dostupné kapacity vaší infrastruktury. Tato schopnost může splňovat shlukové scénáře, které mají nestálé potřeby, včetně scénářů, které vyžadují ingestování a dotazování dat v reálném čase, a to v libovolném měřítku s dobou odezvy s dílčí sekundu. Kromě toho můžete také škálovat databázové instance pomocí jedinečné možnosti nasazení na úrovni technologie Hyper Azure Database for PostgreSQL škálování. Tato funkce poskytuje datovému zatížení další zvýšení optimalizace kapacity pomocí jedinečných čtení a zápisů *na více* instancích.

## <a name="self-service-provisioning"></a>Samoobslužné zřizování

Azure ARC také poskytuje další cloudové výhody, jako je rychlé nasazení a automatizace ve velkém měřítku. Díky orchestraci založené na Kubernetes můžete nasadit databázi v řádu sekund pomocí grafického rozhraní nebo nástrojů rozhraní příkazového řádku.

## <a name="unified-management"></a>Jednotná správa

Pomocí známých nástrojů, jako jsou Azure Portal, Azure Data Studio a [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , teď můžete získat jednotný přehled o všech datových assetech nasazených pomocí ARC Azure. Můžete nejen zobrazit a spravovat celou řadu relačních databází napříč vaším prostředím a Azure, ale taky získat protokoly a telemetrii z rozhraní API Kubernetes, abyste mohli analyzovat základní kapacitu infrastruktury a stav. Kromě toho, že máte lokalizovanou analýzu protokolů a výkonu, teď můžete využít Azure Monitor pro komplexní Operational Insights v celé nemovitosti.

## <a name="disconnected-scenario-support"></a>Podpora odpojeného scénáře

Mnohé ze služeb, jako je samoobslužné zřizování, automatizované zálohování a obnovování a monitorování, se můžou spouštět místně v infrastruktuře s přímým připojením k Azure nebo bez něj. Přímé připojení k Azure otevře další možnosti pro integraci s dalšími službami Azure, jako je Azure Monitor, a schopnost používat rozhraní API Azure Portal a Azure Resource Manager odkudkoli na světě ke správě datových služeb s podporou ARC Azure.

## <a name="next-steps"></a>Další kroky

> **Chcete něco vyzkoušet?**  
> Začněte rychle s využitím [Azure ARC rychlé zprovoznění](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) ve službě Azure Kubernetes Service (AKS), AWS elastické KUBERNETES (EKS), Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure.

[Instalace klientských nástrojů](install-client-tools.md)

[Vytvoření řadiče dat ARC Azure](create-data-controller.md) (nejprve vyžaduje instalaci nástrojů klienta)

[Vytvoření spravované instance Azure SQL v Arc Azure](create-sql-managed-instance.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)

[Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure](create-postgresql-hyperscale-server-group.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)
