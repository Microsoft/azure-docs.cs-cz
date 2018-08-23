---
title: Správa aplikací Azure Statistika Fabric v sadě Visual Studio | Dokumentace Microsoftu
description: Pomocí sady Visual Studio vytvořit, vývoj, zabalení, nasazení a ladění aplikace Service Fabric v Azure a služby.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: 2e7b7d9b0a194b13de9bdf759f4f3be645ed7c2e
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42442165"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Jednodušší vytváření a Správa aplikací Service Fabric pomocí sady Visual Studio
Můžete spravovat vaše aplikace Azure Service Fabric a služby pomocí sady Visual Studio. Jakmile [nastavení vývojového prostředí](service-fabric-get-started.md), Visual Studio můžete vytvářet aplikace Service Fabric, přidat služby nebo balíček, registrace a nasazení aplikací v místním vývojovém clusteru.

## <a name="deploy-your-service-fabric-application"></a>Nasazení aplikace Service Fabric
Ve výchozím nastavení kombinuje nasazení aplikace do jedné jednoduché operace následující kroky:

1. Vytvoření balíčku aplikace
2. Nahrává se balíček aplikace do úložiště imagí
3. Registrace typu aplikace
4. Odebírá se všechny spuštěné instance aplikace
5. Vytvoření instance aplikace

V sadě Visual Studio stisknutím klávesy **F5** nasadí vaši aplikaci a připojit ladicí program pro všechny instance aplikace. Můžete použít **Ctrl + F5** k nasazení aplikace bez ladění nebo je můžete publikovat do místního nebo vzdáleného clusteru pomocí profilu publikování.

### <a name="application-debug-mode"></a>Režim ladění aplikace
Visual Studio poskytuje vlastnost s názvem **režim ladění aplikace**, který určuje způsob zpracování nasazení aplikace jako součást ladění Visual Studia.

#### <a name="to-set-the-application-debug-mode-property"></a>Chcete-li nastavit vlastnost režim ladění aplikace
1. V Service Fabric application projektu (*.sfproj) místní nabídce zvolte **vlastnosti** (nebo stiskněte klávesu **F4** klíč).
2. V **vlastnosti** okno, nastaveno **režim ladění aplikace** vlastnost.

![Nastavte vlastnost režim ladění aplikace][debugmodeproperty]

#### <a name="application-debug-modes"></a>Režim ladění aplikace

1. **Aktualizovat aplikaci** tento režim vám umožní rychle změnit a ladění vašeho kódu a podporuje úpravy statické webové soubory během ladění. Tento režim funguje jenom v případě místního vývojového clusteru je v [režimu uzlu 1]. Toto je výchozí režim ladění aplikace. (/ service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode).
2. **Odebrání aplikace** způsobí, že aplikace má být odebrán při ukončení relace ladění.
3. **Automatický Upgrade** aplikace i nadále běžel při ukončení relace ladění. Příští relaci ladění bude považovat za nasazení upgradu. Proces upgradu zachová všechna data, která jste zadali v předchozí ladicí relaci.
4. **Zachovat aplikaci** aplikace i nadále běží v clusteru při ukončení relace ladění. Na začátku příští relaci ladění, se odeberou aplikace.

Pro **automatický Upgrade** data se zachovají použitím možnosti upgradu aplikace Service Fabric. Další informace o upgradu aplikace a jak můžete provést upgrade ve skutečných prostředí, najdete v části [upgrade aplikace Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Přidat službu do aplikace Service Fabric
Nové služby můžete přidat do vaší aplikace k rozšíření svých funkcí. Aby bylo zajištěno, že služba je součástí vašeho balíčku aplikací, přidat službu prostřednictvím **novou službu prostředků infrastruktury...**  položky nabídky.

![Přidejte novou službu Service Fabric][newservice]

Vyberte typ projektu Service Fabric do vaší aplikace přidat a zadejte název pro službu.  Naleznete v tématu [zvolíte rozhraní pro vaši službu](service-fabric-choose-framework.md) vám pomohou rozhodnout typů služby, které se mají použít.

![Vyberte typ projektu služby Service Fabric do aplikace][addserviceproject]

Nová služba se přidá do vašeho řešení a existující balíček aplikace. Do manifestu aplikace způsobuje služby vytvořit a spustit ji při příštím nasazení aplikace se přidají odkazy na služby a výchozí instanci služby.

![Nová služba je přidán do manifestu aplikace][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Balíček aplikace Service Fabric
Pokud chcete nasadit aplikace a její služby do clusteru, musíte vytvořit balíček aplikace.  Balíček slouží k uspořádání manifestu aplikace, manifesty služby a další nezbytné soubory v specifické rozložení.  Visual Studio nastaví a spravuje balíček ve složce projektu aplikace v adresáři "pkg".  Kliknutím na **balíčku** z **aplikace** kontextové nabídky vytvoří nebo aktualizuje balíček aplikace.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Odebrat aplikace a typy aplikací pomocí Průzkumníka cloudu
Můžete provádět operace správy základní clusteru v rámci sady Visual Studio pomocí Průzkumníka cloudu, který můžete spustit z **zobrazení** nabídky. Můžete například odstranit aplikace a zrušit zřízení typy aplikací v clusterech místního nebo vzdáleného.

![Odebrání aplikace][removeapplication]

> [!TIP]
> Širší funkce správy clusteru najdete v části [vizualizace vašeho clusteru pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
* [Aplikační model Service Fabric](service-fabric-application-model.md)
* [Nasazení aplikace Service Fabric](service-fabric-deploy-remove-applications.md)
* [Správa parametrů aplikací pro víc prostředí](service-fabric-manage-multiple-environment-app-configuration.md)
* [Ladění aplikace Service Fabric](service-fabric-debugging-your-application.md)
* [Vizualizace clusteru pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png