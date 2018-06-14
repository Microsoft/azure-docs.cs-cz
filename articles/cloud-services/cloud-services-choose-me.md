---
title: Možnosti výpočtů Azure - Azure Cloud Services | Microsoft Docs
description: 'Další informace o Azure výpočetní hostování možnosti a funkce: aplikační služby Azure Cloud Services a virtuálních počítačů'
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 2871a8c02db0ffc6d9033724e7c9f4a454afef8e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29120280"
---
# <a name="should-i-choose-azure-cloud-services-or-something-else"></a>Mám zvolit Azure Cloud Services nebo něco jiného?
Můžete je volba Azure Cloud Services? Azure poskytuje různé hostování modely pro spouštění aplikací. Každé z nich poskytuje jinou sadu služeb. Jeden, který zvolíte, závisí na přesně co se pokoušíte provést.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-azure-cloud-services"></a>Informace o Azure Cloud Services
Příkladem je Azure Cloud Services [platforma jako služba](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Jako [Azure App Service](../app-service/app-service-web-overview.md), tato technologie je navržen pro podporu aplikace, které jsou škálovatelný, spolehlivý a nenákladné pracovat. Stejným způsobem, služby App Service je proto hostována na virtuálních počítačích (VM), je příliš Azure Cloud Services. Ale máte větší kontrolu nad virtuálními počítači. Vlastní software můžete nainstalovat na virtuálních počítačích, které používají Azure Cloud Services a je můžete přistupovat vzdáleně.

![Diagram Azure Cloud Services](./media/cloud-services-choose-me/diagram.png)

Další ovládací prvek také znamená menší snadné použití. Pokud budete potřebovat možnosti Další ovládací prvek, je obvykle rychlejší a snadnější ke zprovoznění webové aplikace a spuštěna ve službě Web Apps funkce služby App Service ve srovnání s Azure Cloud Services.

Existují dva typy rolí Azure Cloud Services. Jediným rozdílem mezi těmito dvěma je, jak se vaše role hostuje na virtuálních počítačích:

* **Role webové**: automaticky nasadí a je hostitelem vaší aplikace pomocí služby IIS.

* **Role pracovního procesu**: nepoužívá službu IIS a spustí vaší samostatné aplikace.

Například jednoduchou aplikaci může použít pouze jeden webové role, obsluhující webu. Složitější aplikaci může zpracovávat příchozí požadavky od uživatelů pomocí webovou roli a pak předejte tyto požadavky na roli pracovního procesu pro zpracování. (Tato komunikace může použít [Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) nebo [Azure Queue storage](../storage/common/storage-introduction.md).)

Jak na předchozím obrázku naznačuje, všechny virtuální počítače v jedné aplikaci spustit v rámci stejné cloudové služby. Přístup uživatelů aplikace prostřednictvím jednu veřejnou IP adresu, s požadavky automaticky načíst rovnoměrně rozdělen mezi virtuálními počítači aplikace. Platforma [Škáluje a nasadí](cloud-services-how-to-scale-portal.md) virtuálních počítačů v aplikaci Azure Cloud Services způsobem, který zabraňuje jediný bod selhání hardwaru.

I když aplikace spustit ve virtuálních počítačích, je důležité si uvědomit, že Azure Cloud Services poskytuje PaaS, není infrastruktura jako služba (IaaS). Tady je jedním ze způsobů myslíte o něm. V modelu IaaS, jako je například virtuální počítače Azure nejprve vytvořit a nakonfigurovat, které vaše aplikace běží v prostředí. Pak můžete nasadit aplikace do tohoto prostředí. Jste zodpovědní za správu velkou část této world prvkem, jako je nasazení nové opravou verze operačního systému v jednotlivých virtuálních počítačů. V PaaS naopak je jako prostředí již existuje. Všechny, které musíte udělat, je nasazení aplikace. Správa platformy, na které běží na, včetně nasazení nové verze operačního systému, je zajistit sami.

## <a name="scaling-and-management"></a>Škálování a Správa
S Azure Cloud Services nevytvářejte virtuálních počítačů. Místo toho zadat konfigurační soubor informuje Azure, kolik jednotlivých chcete, jako je například "tři instance webové role" a "dva pracovní instancí rolí." Platforma potom vytvoří je pro vás. Stále zvolíte [jakou velikost](cloud-services-sizes-specs.md) by měla být ty zálohování virtuálních počítačů, ale nevytvoříte explicitně je sami. Pokud aplikace potřebuje ke zpracování větší zatížení, můžete požádat o další virtuální počítače a Azure vytvoří těchto instancí. Pokud snížení zatížení je možné vypnout těchto instancí a zastavit platícího pro ně.

Aplikace Azure Cloud Services obvykle je k dispozici uživatelům přes ve dvou krocích. Vývojář první [nahrávání aplikace](cloud-services-how-to-create-deploy-portal.md) do pracovní oblasti platformu. Když vývojář je připraven k zpřístupnění aplikace za provozu, použijí portál Azure se Prohodit pracovní s produkčním. To [přepínat mezi pracovní a provozní](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) lze provést bez výpadků, která umožní spuštěné aplikace upgradovat na novou verzi bez narušení svým uživatelům.

## <a name="monitoring"></a>Monitorování
Azure Cloud Services také poskytuje monitorování. Jako virtuální počítače zjistí selhání fyzického serveru a restartuje virtuální počítače, které byly spuštěné na daném serveru na nový počítač. Ale Azure Cloud Services také zjistí selhání virtuální počítače a aplikace, ne jenom selhání hardwaru. Na rozdíl od virtuálních počítačů má agenta v každé webové a pracovní role, a proto je možné spustit nové virtuální počítače a instance aplikací, když dojde k selhání.

PaaS povaha Azure Cloud Services má jiné důsledky příliš. Jedním z nejdůležitějších je, že aplikace založené na tuto technologii zasílány správně spustit, když všechny instance role web nebo pracovní proces se nezdaří. Toho dosáhnete tak, by neměl aplikaci Azure Cloud Services udržovat stav v systému souborů svůj vlastní virtuální počítače. Na rozdíl od virtuální počítače vytvořené pomocí virtuálních počítačů nejsou trvalé zápisy provedené na virtuálních počítačích Azure Cloud Services. Není nic jako datový disk virtuálního počítače. Místo toho aplikaci Azure Cloud Services musí explicitně zapisovat všechny stavy do Azure SQL Database, objekty BLOB, tabulek nebo jiné externí úložiště. Vytváření aplikací s tímto způsobem jejich jednodušší škálování a odolnější proti selhání, které jsou obě důležité cíle Azure Cloud Services.

## <a name="next-steps"></a>Další postup
* [Vytvoření aplikace cloudové služby v rozhraní .NET](cloud-services-dotnet-get-started.md) 
* [Vytvoření aplikace cloudové služby v Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Vytvoření cloudové služby aplikace v jazyce PHP](../cloud-services-php-create-web-role.md) 
* [Vytvoření aplikace cloudové služby v Pythonu](cloud-services-python-ptvs.md)



