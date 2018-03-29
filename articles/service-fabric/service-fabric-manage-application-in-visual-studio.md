---
title: Spravovat vaše aplikace Azure Statistika Fabric v sadě Visual Studio | Microsoft Docs
description: Pomocí sady Visual Studio k vytvoření, vývoji, balíčku, nasazení a ladění aplikace Azure Service Fabric a služeb.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: 5c5c3dc20996fbbdee9f9ed61eb3b09e4b274d92
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Pomocí sady Visual Studio zjednodušují zápis a správu aplikací Service Fabric
Můžete spravovat vaše aplikace Azure Service Fabric a služby pomocí sady Visual Studio. Jakmile jste [nastavení vývojového prostředí](service-fabric-get-started.md), Visual Studio můžete použít k vytvoření aplikací Service Fabric, přidání služby nebo balíček, registrace a nasazení aplikací v místní vývojový cluster.

## <a name="deploy-your-service-fabric-application"></a>Nasazení aplikace Service Fabric
Ve výchozím nastavení nasazení aplikace kombinuje následující kroky do jednoho jednoduché operace:

1. Vytvoření balíčku aplikace
2. Nahrávání balíčku aplikace do úložiště bitové kopie
3. Registrace typu aplikace
4. Odebrat všechny spuštěné instance aplikace
5. Vytvoření instance aplikace

Ve Visual Studiu stisknutím **F5** nasadí aplikaci a připojit ladicí program na všechny instance aplikace. Můžete použít **Ctrl + F5** k nasazení aplikace bez ladění, nebo můžete publikovat do místního nebo vzdáleného clusteru pomocí profilu publikování.

### <a name="application-debug-mode"></a>Režim ladění aplikací
Visual Studio poskytují vlastnost s názvem **režim ladění aplikací**, který určuje, jakým způsobem chcete Visual Studia pro zpracování nasazení aplikace v rámci ladění.

#### <a name="to-set-the-application-debug-mode-property"></a>Chcete-li nastavit vlastnost režim ladění aplikací
1. V Service Fabric aplikace projektu (*.sfproj) místní nabídky zvolte **vlastnosti** (nebo stiskněte klávesu **F4** klíč).
2. V **vlastnosti** nastavte **režim ladění aplikací** vlastnost.

![Nastavte vlastnost režimu ladění aplikace][debugmodeproperty]

#### <a name="application-debug-modes"></a>Režim ladění aplikace

1. **Aktualizovat aplikaci** tento režim umožňuje rychle změnit a ladění kódu a podporuje úpravy statických webových souborů při ladění. Tento režim funguje jenom v případě místního vývojového clusteru je v [režimu uzlem 1]. Toto je výchozí režim ladění aplikací. (nebo service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode).
2. **Odebrání aplikace** způsobí, že aplikace má být odebrán při ukončení relace ladění.
3. **Automaticky Upgrade** aplikace stále běží při ukončení relace ladění. Další relaci ladění bude považovat za nasazení upgradu. Proces upgradu zachová všechna data, která jste zadali v předchozí relaci ladění.
4. **Zachovat aplikace** aplikace neustále běží v clusteru při ukončení relace ladění. Na začátku další relaci ladění, se odeberou aplikace.

Pro **automatického upgradu** uchování dat s použitím možnosti upgradu aplikace Service Fabric. Další informace o upgrade aplikace a jak můžete provést upgrade v prostředí skutečné najdete v tématu [upgradu aplikace Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Přidání služby do aplikace Service Fabric
Nové služby je možné přidat do vaší aplikace pro rozšíření její funkce. Aby se zajistilo, že služba je součástí balíčku aplikace, přidejte služby pomocí **nové služby technologie Fabric...**  položku nabídky.

![Přidejte novou službu Service Fabric][newservice]

Vyberte typ projektu Service Fabric přidat do vaší aplikace a zadejte název pro službu.  V tématu [výběr rozhraní služby](service-fabric-choose-framework.md) vám pomohou rozhodnout typů služby, které se mají použít.

![Vyberte typ projektu služby Service Fabric k přidání do aplikace][addserviceproject]

Nové služby se přidá do vašeho řešení a stávající balíček aplikace. Odkazy na službu a výchozí instance služby se zařadí do manifestu aplikace, která způsobila služba vytvořena a spuštěna při příštím nasazení aplikace.

![Nové služby se přidá do manifestu aplikace][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Balíček aplikace Service Fabric
Pokud chcete nasadit aplikace a služby do clusteru, musíte vytvořit balíček aplikace.  Balíček organizuje manifest aplikace, manifesty služby a jiné potřebné soubory v konkrétní rozložení.  Visual Studio nastavuje a spravuje balíček v projektu aplikace složky v adresáři 'pkg'.  Kliknutím na tlačítko **balíček** z **aplikace** kontextovou nabídku vytvoří nebo aktualizuje balíček aplikace.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Odebrat aplikace a typy aplikací pomocí Průzkumníku cloudu
Můžete provádět operace správy základní cluster z v sadě Visual Studio pomocí Průzkumníku cloudu, který můžete spustit z **zobrazení** nabídky. Můžete například odstranit aplikace a zrušit zřízení typy aplikací v clusterech místního nebo vzdáleného.

![Odebrání aplikace][removeapplication]

> [!TIP]
> Širší funkce správy clusteru najdete v části [vizualizace vašeho clusteru pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
* [Model aplikace Service Fabric](service-fabric-application-model.md)
* [Nasazení aplikace Service Fabric](service-fabric-deploy-remove-applications.md)
* [Správa aplikací parametry pro prostředí s více](service-fabric-manage-multiple-environment-app-configuration.md)
* [Ladění aplikace Service Fabric](service-fabric-debugging-your-application.md)
* [Vizualizace vašeho clusteru pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png