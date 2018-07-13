---
title: Co je Azure Cloud Services | Dokumentace Microsoftu
description: Další informace o tom, co je Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 6af4533a4ab374071b904cc4b03ca239a3e83364
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008962"
---
# <a name="overview-of-azure-cloud-services"></a>Přehled služby Azure Cloud Services
Azure Cloud Services je příkladem [platforma jako služba](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Stejně jako [služby Azure App Service](../app-service/app-service-web-overview.md), tato technologie je navržen pro podporu aplikací, které jsou škálovatelné, spolehlivé a mají levný provoz. Stejným způsobem, App Service je proto hostována na virtuálních počítačích (VM), je příliš Azure Cloud Services. Máte však větší kontrolu nad virtuálními počítači. Svůj vlastní software můžete nainstalovat na virtuálních počítačích, které používají Azure Cloud Services a které můžete přistupovat vzdáleně.

![Azure Cloud Services diagramu](./media/cloud-services-choose-me/diagram.png)

Další ovládací prvek také znamená méně snadnost použití. Pokud potřebujete větší kontrolu možnosti, je obvykle rychlejší a snadnější ke zprovoznění webové aplikace a spuštěná ve službě Web Apps funkce služby App Service ve srovnání s Azure Cloud Services.

Existují dva typy rolí Azure Cloud Services. Jediným rozdílem mezi těmito dvěma je, jak je vaše role hostovaných na virtuálních počítačích:

* **Webová role**: automaticky nasadí a je hostitelem vaší aplikace prostřednictvím služby IIS.

* **Role pracovního procesu**: nepoužívají služby IIS, a spouští vaše samostatné aplikace.

Jednoduchá aplikace může například použít právě jednu webovou roli, obsluhující Web. Složitější aplikaci může používat webové role ke zpracování příchozích požadavků od uživatelů a pak předejte tyto žádosti do role pracovního procesu pro zpracování. (Tato komunikace může použít [Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) nebo [Azure Queue storage](../storage/common/storage-introduction.md).)

Na předchozím obrázku navrhuje, všechny virtuální počítače v jedné aplikaci jsou spuštěny v rámci stejné cloudové služby. Přístup uživatelů aplikace prostřednictvím jednu veřejnou IP adresu, s žádostmi o automaticky načíst vyvažují mezi virtuálních počítačů aplikaci. Platforma [dá škálovat a nasadí](cloud-services-how-to-scale-portal.md) virtuálních počítačů v aplikaci Azure Cloud Services tak, aby se vyhnete jediný bod selhání hardwaru.

I když aplikace běží na virtuálních počítačích, je důležité pochopit, že poskytuje cloudové služby Azure PaaS, ne na infrastrukturu jako službu (IaaS). Tady je jeden ze způsobů zamyslete o něm. S modelem IaaS, jako jsou Azure Virtual Machines nejprve vytvořte a nakonfigurujte prostředí, ve kterém běží aplikace v. Potom nasaďte svoji aplikaci do tohoto prostředí. Zodpovídáte za správu velkou část tento svět věci, jako je nasazení nové verze verzi operačního systému do každého virtuálního počítače. V modelu PaaS naopak je jako prostředí už existuje. Všechno, co musíte udělat, je při nasazování aplikace. Správa platformy, na kterých se spouští, včetně nasazování nových verzí operačního systému se postará služba za vás.

## <a name="scaling-and-management"></a>Škálování a Správa
S Azure Cloud Services nemusíte vytvářet virtuální počítače. Místo toho zadejte konfigurační soubor, který dává Azure pokyn, kolik jednotlivých by rádi používáte, jako je například "tři instance webové role" a "dvou instancí rolí pracovního procesu." Platformu je pak vytvoří za vás. Dál možnost [jakou velikost](cloud-services-sizes-specs.md) by měl být ty zálohování virtuálních počítačů, ale není explicitně je sami vytvoříte. Pokud vaše aplikace potřebuje ke zpracování větší zátěže, můžete požádat o další virtuální počítače a Azure vytvoří tyto instance. Pokud snížení zatížení, můžete vypnout tyto instance a zastavit platit za ně.

Aplikace Azure Cloud Services je obvykle k dispozici uživatelům přes dvoustupňový proces. Vývojář první [odešle aplikace](cloud-services-how-to-create-deploy-portal.md) do pracovní oblasti platformy. Když vývojář je připraven ke zpřístupnění aplikace live, používají na webu Azure portal se Prohodit pracovní s produkčním prostředí. To [přepínat mezi přípravným a produkčním prostředím](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) lze provést bez výpadku provozu, které vám umožní spuštěné aplikaci bez narušení jeho uživatelé upgradovat na novou verzi.

## <a name="monitoring"></a>Monitorování
Azure Cloud Services také poskytuje monitorování. Jako jsou virtuální počítače zjistí selhání fyzického serveru a restartuje virtuální počítače, které byly spuštěny na tomto serveru na nový počítač. Ale Azure Cloud Services také detekuje selhání virtuálních počítačů a aplikací, ne jenom selhání hardwaru. Na rozdíl od virtuálních počítačů má agenta uvnitř jednotlivých webových a pracovních rolí a proto je možné spustit nové virtuální počítače a instance aplikace, když dojde k selhání.

PaaS povaha cloudových služeb Azure má příliš ostatní důsledky. Jednou z vašich nejdůležitějších je, že aplikace založené na tuto technologii by měly být zapsány správně spustit, když selže všechny instance webové nebo pracovní role. Za tím účelem aplikaci Azure Cloud Services by neměl uchování stavu v systému souborů své vlastní virtuální počítače. Na rozdíl od virtuální počítače vytvořené pomocí virtuálních počítačů nejsou trvalé zápis provedené na virtuální počítače Azure Cloud Services. Neexistuje nic jako datového disku virtuálního počítače. Místo toho aplikace Azure Cloud Services by měl explicitně zápis všech stavů do Azure SQL Database, objekty BLOB, tabulek nebo jiné externí úložiště. Sestavování aplikací díky tomu je mezi nimi vlastně snáze škálovatelné a odolnější vůči selhání, které jsou obě důležité cíle cloudových služeb Azure.

## <a name="next-steps"></a>Další postup
* [Vytvoření aplikace cloudové služby v .NET](cloud-services-dotnet-get-started.md) 
* [Vytvoření aplikace cloudové služby v Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Vytvoření aplikace cloudové služby v PHP](../cloud-services-php-create-web-role.md) 
* [Vytvoření aplikace cloudové služby v Pythonu](cloud-services-python-ptvs.md)



