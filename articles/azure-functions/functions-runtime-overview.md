---
title: Přehled Modul runtime služby Azure Functions
description: Přehled Modul runtime služby Azure Functions Preview
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: ab04aa4ca7f54e8de120d078a313c3096a350aa5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74226631"
---
# <a name="azure-functions-runtime-overview-preview"></a>Přehled Modul runtime služby Azure Functions (Preview)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Modul runtime služby Azure Functions (Preview) poskytuje nový způsob, jak využít jednoduchost a flexibilitu Azure Functions programovacího modelu v místním prostředí. Modul runtime služby Azure Functions je postavená na stejných kořenech Open Source jako Azure Functions, je nasazená místně, aby poskytovala skoro stejné vývojové prostředí jako cloudová služba.

![Portál Preview Modul runtime služby Azure Functions][1]

Modul runtime služby Azure Functions poskytuje způsob, jak se Azure Functions před potvrzením cloudu. Tímto způsobem můžete při migraci do cloudu považovat prostředky kódu, které vytvoříte.  Modul runtime také otevírá nové možnosti, jako je například použití náhradní výpočetní síly vašich místních počítačů ke spouštění dávkových procesů v noci. Zařízení v rámci vaší organizace taky můžete použít k podmíněnému posílání dat do jiných systémů, místních i cloudových.

Modul runtime služby Azure Functions se skládá ze dvou částí:

* Role správy Modul runtime služby Azure Functions
* Modul runtime služby Azure Functions role pracovního procesu

## <a name="azure-functions-management-role"></a>Role správy Azure Functions

Role správy Azure Functions poskytuje hostitele pro správu vašich funkcí v místním prostředí. Tato role provádí následující úlohy:

* Hostování Azure Functions Portál pro správu, který je stejný jako ten, který vidíte v [Azure Portal](https://portal.azure.com). Portál nabízí konzistentní prostředí, které umožňuje vyvíjet funkce stejným způsobem jako v Azure Portal.
* Distribuce funkcí mezi více pracovníků funkcí.
* Poskytnutím koncového bodu publikování, abyste mohli publikovat své funkce přímo z Microsoft Visual Studio stažením a importem profilu publikování.

## <a name="azure-functions-worker-role"></a>Azure Functions role pracovního procesu

Role pracovního procesu Azure Functions jsou nasazeny v kontejnerech systému Windows a jsou v místě, kde je spuštěn kód funkce.  V rámci vaší organizace můžete nasadit více rolí pracovního procesu a tato možnost je klíčovým způsobem, ve kterém můžou zákazníci využít náhradní výpočetní výkon.  Jeden z příkladů, kde v mnoha organizacích existují náhradní výpočetní prostředky, jsou počítače s nepřetržitým chodem, ale nepoužívají se po velkých časových obdobích.

## <a name="minimum-requirements"></a>Minimální požadavky

Pokud chcete začít s Modul runtime služby Azure Functions, musíte mít počítač s Windows serverem 2016 nebo Windows 10 Creators Update s přístupem k instanci SQL Server.

## <a name="next-steps"></a>Další kroky

Nainstalovat [modul runtime služby Azure Functions Preview](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
