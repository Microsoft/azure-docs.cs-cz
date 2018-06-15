---
title: Přehled modulu Runtime Azure Functions | Microsoft Docs
description: Přehled Azure Functions Runtime Preview
services: functions
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 557f071e2cd8d4f639c881274e6e74a8fb745859
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2017
ms.locfileid: "26290220"
---
# <a name="azure-functions-runtime-overview"></a>Přehled Azure Functions modulu Runtime

Azure Functions Runtime nabízí nový způsob můžete využívat jednoduchost a flexibilitu Azure Functions programovací model na místě. Postavené na stejném kořeny s otevřeným zdrojem jako funkce Azure, Azure Functions Runtime je nasadit místně a poskytuje prostředí téměř shodná vývoj jako cloudová služba.

![Modul Runtime Azure Functions na portálu Preview][1]

Azure Functions Runtime poskytuje způsob, jak jste prostředí Azure Functions před potvrzením do cloudu. Tímto způsobem kód prostředků, které vytvoříte pak děláte s vámi do cloudu při migraci.  Modul runtime také otevře nové možnosti pro vás, například pomocí k výměně za chodu výpočetní výkon vašich počítačů místní spuštění dávky procesy přes noc. Zařízení můžete také použít v rámci vaší organizace podmíněně odesílání dat do jiných systémů, jak místně a v cloudu.

Azure Functions Runtime se skládá ze dvou částí:

* Role správy Runtime Azure Functions
* Role pracovního procesu modulu Runtime Azure Functions

## <a name="azure-functions-management-role"></a>Role správy Azure Functions

Role Azure funkce správy poskytuje pro správu funkce místní hostitel. Tato role se provádí tyto úlohy:

* Hostování portálu Azure funkce správy, což je stejný jako ten, můžete vidět v [portál Azure](https://portal.azure.com). Na portálu zajišťuje konzistentní prostředí, které vám umožní vyvíjet funkcí stejným způsobem, jako byste na portálu Azure.
* Distribuci funkcí mezi více pracovníků funkce.
* Koncový bod publikování poskytuje tak, aby vaše funkce přímo ze sady Microsoft Visual Studio můžete publikovat pomocí stahování a import profilu publikování.

## <a name="azure-functions-worker-role"></a>Role pracovního procesu Azure Functions

Role pracovního procesu Azure funkce nasazených v kontejnerech Windows a jsou, kde se provádí funkce kódu.  Více rolí pracovního procesu můžete nasadit ve vaší organizaci a tato možnost je klíče způsob, ve kterém zákazníci měli použít k výměně za chodu výpočetní výkon.  Jedna je například kde k výměně za chodu výpočetní existuje v mnoha organizacích počítače neustále zapnutý, ale nepoužívala pro velké období.

## <a name="minimum-requirements"></a>Minimální požadavky

Chcete-li začít pracovat s modulem Runtime funkce Azure, musí mít počítač s Windows Server 2016 nebo Windows 10 Creators Update přístup k instanci systému SQL Server.

## <a name="next-steps"></a>Další kroky

Nainstalujte [Runtime funkce Azure preview](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
