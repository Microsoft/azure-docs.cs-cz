---
title: Přehled runtime funkcí Azure
description: Přehled náhledu runtime funkcí Azure
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: ab04aa4ca7f54e8de120d078a313c3096a350aa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226631"
---
# <a name="azure-functions-runtime-overview-preview"></a>Přehled runtime funkcí Azure (preview)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Azure Functions Runtime (preview) poskytuje nový způsob, jak využít jednoduchost a flexibilitu programovacího modelu Azure Functions místně. Azure Functions Runtime, postavený na stejných kořenech open source jako Azure Functions, se nasazuje místně, aby poskytoval téměř identické vývojové prostředí jako cloudová služba.

![Portál Preview runtime funkcí Azure][1]

Azure Functions Runtime poskytuje způsob, jak si před potvrzením do cloudu zažít Funkce Azure. Tímto způsobem prostředky kódu, které vytvoříte, pak mohou být při migraci přeneseny s sebou do cloudu.  Runtime také otevírá nové možnosti pro vás, jako je například použití náhradní výpočetní výkon místních počítačů ke spuštění dávkových procesů přes noc. Zařízení v rámci vaší organizace můžete také použít k podmíněnému odesílání dat do jiných systémů, a to jak v místním prostředí, tak v cloudu.

Azure Functions Runtime se skládá ze dvou částí:

* Role správy za běhu funkcí Azure
* Role pracovního procesu Azure Functions

## <a name="azure-functions-management-role"></a>Role správy funkcí Azure

Role správy funkcí Azure poskytuje hostitele pro správu vašich funkcí místně. Tato role provádí následující úkoly:

* Hostování portálu pro správu funkcí Azure, který je stejný jako ten, který vidíte na [portálu Azure](https://portal.azure.com). Portál poskytuje konzistentní prostředí, které vám umožní rozvíjet vaše funkce stejným způsobem jako na webu Azure Portal.
* Distribuce funkcí mezi více pracovníků funkce.
* Poskytnutí koncového bodu publikování, abyste mohli publikovat funkce přímo z aplikace Microsoft Visual Studio stažením a importem profilu publikování.

## <a name="azure-functions-worker-role"></a>Role pracovního procesu Azure Functions

Role pracovních míst Azure Functions se nasazují v kontejnerech Windows a jsou tam, kde se spustí kód vaší funkce.  Můžete nasadit více rolí pracovních sil v celé organizaci a tato možnost je klíčovým způsobem, ve kterém zákazníci mohou využít náhradní výpočetní výkon.  Jedním z příkladů, kde náhradní výpočetní prostředky existuje v mnoha organizacích je stroje neustále na zapnuté, ale nejsou používány pro velké časové období.

## <a name="minimum-requirements"></a>Minimální požadavky

Abyste mohli začít pracovat s prostředím Azure Functions Runtime, musíte mít počítač s aktualizací Windows Server 2016 nebo Windows 10 Creators Update s přístupem k instanci SQL Serveru.

## <a name="next-steps"></a>Další kroky

Instalace [náhledu runtime funkce Azure](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
