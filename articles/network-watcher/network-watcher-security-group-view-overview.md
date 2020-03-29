---
title: Úvod do zobrazení skupiny zabezpečení v Azure Network Watcher | Dokumenty společnosti Microsoft
description: Tato stránka obsahuje přehled funkce zobrazení zabezpečení Sledování sítě
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 18619cc0f7a21a39d0802575c484c68c5fa1a624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840753"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Úvod k zobrazení efektivních pravidel zabezpečení v Azure Network Watcher

Skupiny zabezpečení sítě jsou přidruženy na úrovni podsítě nebo na úrovni síťové sítě. Pokud je přidružena na úrovni podsítě, vztahuje se na všechny instance virtuálních virtuálních mitv v podsíti. Zobrazení efektivních pravidel zabezpečení vrátí všechny nakonfigurované skupiny zabezpečení sítě a pravidla, která jsou přidružena na úrovni síťové karty a podsítě pro virtuální počítač poskytující přehled o konfiguraci. Kromě toho jsou vrácena účinná pravidla zabezpečení pro každý z nic ve virtuálním počítači. Pomocí zobrazení efektivní pravidla zabezpečení můžete posoudit virtuální ho virtuálního virtuálního papíru pro zabezpečení sítě, jako jsou otevřené porty. Můžete také ověřit, zda skupina zabezpečení sítě pracuje podle očekávání na základě [porovnání mezi nakonfigurovanými a schválenými pravidly zabezpečení](network-watcher-nsg-auditing-powershell.md).

Delší případ použití je v dodržování předpisů zabezpečení a auditování. Jako model pro zásady správného řízení zabezpečení ve vaší organizaci můžete definovat normativní sadu pravidel zabezpečení. Pravidelný audit dodržování předpisů lze implementovat programovým způsobem porovnáním normativních pravidel s platnými pravidly pro každý z virtuálních počítačů ve vaší síti.

V portálu pravidla jsou zobrazeny pro každé síťové rozhraní a seskupeny podle příchozí vs odchozí. To poskytuje jednoduché zobrazení do pravidel používaných pro virtuální počítač. Tlačítko pro stažení je k dispozici pro snadné stažení všech bezpečnostních pravidel bez ohledu na kartu do souboru CSV.

![zobrazení skupiny zabezpečení][1]

Pravidla lze vybrat a otevře se nové okno, které zobrazí skupinu zabezpečení sítě a předpony zdroje a cíle. Z tohoto okna můžete přejít přímo na prostředek skupiny zabezpečení sítě.

![Procházení][2]

### <a name="next-steps"></a>Další kroky

Informace o auditování nastavení skupiny zabezpečení sítě nastráncením [na stránce Audit Network Security Group pomocí prostředí PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









