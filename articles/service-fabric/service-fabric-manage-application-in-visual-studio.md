---
title: Správa aplikací v sadě Visual Studio
description: Visual Studio slouží k vytváření, vývoji, balení, nasazení a ladění aplikací a služeb Azure Service Fabric.
author: mikkelhegn
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: d6734f5da0fb7e5c9052b26b55b2d90b068bdbbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614328"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Zjednodušení psaní a správy aplikací Service Fabric pomocí sady Visual Studio
Aplikace a služby Azure Service Fabric můžete spravovat prostřednictvím sady Visual Studio. Po nastavení [vývojového prostředí](service-fabric-get-started.md)můžete pomocí sady Visual Studio vytvářet aplikace Service Fabric, přidávat služby nebo balit, registrovat a nasazovat aplikace v clusteru místního vývoje.

## <a name="deploy-your-service-fabric-application"></a>Nasazení aplikace Service Fabric
Ve výchozím nastavení nasazení aplikace kombinuje následující kroky do jedné jednoduché operace:

1. Vytvoření balíčku aplikace
2. Nahrání balíčku aplikace do úložiště obrázků
3. Registrace typu přihlášky
4. Odebrání všech spuštěných instancí aplikace
5. Vytvoření instance aplikace

V sadě Visual Studio stisknutím klávesy **F5** nasadíte vaši aplikaci a připojíte ladicí program ke všem instancím aplikace. **Kombinaci kláves Ctrl+F5** můžete použít k nasazení aplikace bez ladění, nebo můžete publikovat do místního nebo vzdáleného clusteru pomocí profilu publikování.

### <a name="application-debug-mode"></a>Režim ladění aplikací
Visual Studio poskytuje vlastnost s názvem **Režim ladění aplikací**, která určuje, jak chcete, aby Visual Studios zpracovávala nasazení aplikace jako součást ladění.

#### <a name="to-set-the-application-debug-mode-property"></a>Nastavení vlastnosti Režim ladění aplikací
1. V místní nabídce projektu aplikace Service Fabric (*.sfproj) zvolte **Vlastnosti** (nebo stiskněte klávesu **F4).**
2. V okně **Vlastnosti** nastavte vlastnost **Režim ladění aplikací.**

![Vlastnost Nastavení režimu ladění aplikace][debugmodeproperty]

#### <a name="application-debug-modes"></a>Režimy ladění aplikací

1. **Aktualizovat aplikaci** Tento režim umožňuje rychle změnit a ladit kód a podporuje úpravy statických webových souborů při ladění. Tento režim funguje pouze v případě, že je místní vývojový cluster v režimu 1 uzlu. Toto je výchozí režim ladění aplikací.
2. **Odebrat aplikaci** způsobí, že aplikace má být odebrána po ukončení relace ladění.
3. **Automatický upgrade** Aplikace pokračuje v běhu po ukončení relace ladění. Další relace ladění bude považovat nasazení jako upgrade. Proces upgradu zachová všechna data, která jste zadali v předchozí relaci ladění.
4. **Zachovat aplikaci** Aplikace je spuštěna v clusteru po ukončení relace ladění. Na začátku další relace ladění bude aplikace odebrána.

Pro **automatické inovace** data je zachována použitím možností upgradu aplikace Service Fabric. Další informace o inovaci aplikací a o tom, jak provést upgrade v reálném prostředí, naleznete v [tématu Service Fabric application upgrade](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Přidání služby do aplikace Service Fabric
Do aplikace můžete přidat nové služby a rozšířit tak její funkčnost. Chcete-li zajistit, že služba je součástí balíčku aplikace, přidejte službu prostřednictvím **nové služby Fabric ...** položky nabídky.

![Přidání nové služby Service Fabric][newservice]

Vyberte typ projektu Service Fabric, který chcete přidat do aplikace, a zadejte název služby.  Viz [Výběr rozhraní pro vaši službu,](service-fabric-choose-framework.md) které vám pomohou rozhodnout, který typ služby použít.

![Vyberte typ projektu služby Fabric, který chcete přidat do aplikace.][addserviceproject]

Nová služba je přidána do vašeho řešení a existujícího balíčku aplikace. Odkazy na službu a výchozí instance služby budou přidány do manifestu aplikace, což způsobí, že služba bude vytvořena a spuštěna při příštím nasazení aplikace.

![Nová služba je přidána do manifestu aplikace.][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Balení aplikace Service Fabric
Chcete-li nasadit aplikaci a její služby do clusteru, je třeba vytvořit balíček aplikace.  Balíček uspořádá manifest aplikace, manifesty služby a další potřebné soubory v určitém rozložení.  Visual Studio nastaví a spravuje balíček ve složce projektu aplikace, v adresáři 'pkg'.  Klepnutím na **balíček** z kontextové nabídky **aplikace** vytvoříte nebo aktualizujete balíček aplikace.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Odebrání aplikací a typů aplikací pomocí Průzkumníka Cloud
Základní operace správy clusteru můžete provádět v rámci sady Visual Studio pomocí Průzkumníka Cloud, které můžete spustit z nabídky **Zobrazení.** Můžete například odstranit aplikace a zrušit zřízení typů aplikací v místních nebo vzdálených clusterech.

![Odebrání aplikace][removeapplication]

> [!TIP]
> Podrobnější funkce správy clusteru naleznete v [tématu Vizualizace clusteru pomocí aplikace Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další kroky
* [Aplikační model Service Fabric](service-fabric-application-model.md)
* [Nasazení aplikace Service Fabric](service-fabric-deploy-remove-applications.md)
* [Správa parametrů aplikace pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md)
* [Ladění aplikace Service Fabric](service-fabric-debugging-your-application.md)
* [Vizualizace clusteru pomocí Průzkumníka prostředků Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
