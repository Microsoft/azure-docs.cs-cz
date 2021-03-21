---
title: Správa aplikací v sadě Visual Studio
description: Pomocí sady Visual Studio můžete vytvářet, vyvíjet, balit, nasazovat a ladit aplikace a služby Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 1fd9295a0dead180767febcc5339ef0a25cb1e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574543"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Použití sady Visual Studio k zjednodušení psaní a správy aplikací Service Fabric
Pomocí sady Visual Studio můžete spravovat své aplikace a služby Azure Service Fabric. Po [Nastavení vývojového prostředí](service-fabric-get-started.md)můžete použít Visual Studio k vytváření Service Fabric aplikací, přidávání služeb nebo balení, registraci a nasazení aplikací v místním vývojovém clusteru.

## <a name="deploy-your-service-fabric-application"></a>Nasazení aplikace Service Fabric
Nasazení aplikace ve výchozím nastavení kombinuje následující kroky do jedné jednoduché operace:

1. Vytváření balíčku aplikace
2. Nahrání balíčku aplikace do úložiště imagí
3. Registrace typu aplikace
4. Odebírají se všechny spuštěné instance aplikace.
5. Vytvoření instance aplikace

V aplikaci Visual Studio se po stisknutí klávesy **F5** nasadí aplikace a připojí se ladicí program ke všem instancím aplikace. Můžete použít **CTRL + F5** k nasazení aplikace bez ladění nebo můžete publikovat do místního nebo vzdáleného clusteru pomocí profilu publikování.

### <a name="application-debug-mode"></a>Režim ladění aplikace
Visual Studio poskytuje vlastnost nazvanou **režim ladění aplikace**, která určuje, jak má Visual studia zpracovávat nasazení aplikace jako součást ladění.

#### <a name="to-set-the-application-debug-mode-property"></a>Nastavení vlastnosti režimu ladění aplikace
1. V místní nabídce projektu aplikace Service Fabric aplikace (*. sfproj) zvolte **vlastnosti** (nebo stiskněte klávesu **F4** ).
2. V okně **vlastnosti** nastavte vlastnost **režim ladění aplikace** .

![Nastavit vlastnost režimu ladění aplikace][debugmodeproperty]

#### <a name="application-debug-modes"></a>Režimy ladění aplikace

1. **Aktualizovat aplikaci** Tento režim umožňuje rychlou změnu a ladění kódu a podporuje úpravy statických webových souborů při ladění. Tento režim funguje jenom v případě, že váš místní vývojový cluster je v režimu s jedním uzlem. Toto je výchozí režim ladění aplikace.
2. **Odebrání aplikace** způsobí, že aplikace bude odebrána po ukončení relace ladění.
3. **Automatický upgrade** Aplikace pokračuje v běhu i po ukončení relace ladění. Další relace ladění bude nasazení považovat za upgrade. Proces upgradu zachová všechna data, která jste zadali v předchozí relaci ladění.
4. **Zachovat aplikaci** Po ukončení relace ladění aplikace udržuje v clusteru spuštěný. Na začátku další relace ladění bude aplikace odebrána.

Pro data **automatického upgradu** se zachovají pomocí možností upgradu aplikace Service Fabric. Další informace o upgradu aplikací a o tom, jak můžete provést upgrade v reálném prostředí, najdete v tématu [Service Fabric upgrade aplikace](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Přidání služby do aplikace Service Fabric
Do své aplikace můžete přidat nové služby, abyste rozšířili její funkce. Aby bylo zajištěno, že je služba součástí balíčku aplikace, přidejte službu prostřednictvím nové položky nabídky **služby Fabric...** .

![Přidat novou Service Fabric službu][newservice]

Vyberte typ projektu Service Fabric, který chcete přidat do aplikace, a zadejte název služby.  Podívejte se na téma [Výběr architektury pro vaši službu](service-fabric-choose-framework.md) , která vám pomůžou určit, který typ služby se má použít.

![Vyberte typ projektu Service Fabric služby, který chcete přidat do aplikace.][addserviceproject]

Nová služba se přidá do vašeho řešení a existujícího balíčku aplikace. Odkazy na služby a výchozí instance služby budou přidány do manifestu aplikace, což způsobí, že se služba vytvoří a spustí při příštím nasazení aplikace.

![Nová služba se přidá do manifestu aplikace.][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Zabalení aplikace Service Fabric
Chcete-li nasadit aplikaci a její služby do clusteru, je nutné vytvořit balíček aplikace.  Balíček uspořádá manifest aplikace, manifesty služby a další nezbytné soubory do konkrétního rozložení.  Sada Visual Studio nastaví a spravuje balíček ve složce projektu aplikace v adresáři ' pkg '.  Kliknutím na **balíček** v kontextové nabídce **aplikace** se vytvoří nebo aktualizuje balíček aplikace.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Odebrání aplikací a typů aplikací pomocí Průzkumníka cloudu
Základní operace správy clusterů můžete provádět v rámci sady Visual Studio pomocí Průzkumníka cloudu, který můžete spustit z nabídky **Zobrazit** . Můžete například odstranit aplikace a zrušit zřízení typů aplikací v místních nebo vzdálených clusterech.

![Odebrání aplikace][removeapplication]

> [!TIP]
> Rozsáhlejší funkce správy clusterů najdete v tématu [Vizualizace clusteru pomocí Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další kroky
* [Service Fabric aplikační model](service-fabric-application-model.md)
* [Service Fabric nasazení aplikace](service-fabric-deploy-remove-applications.md)
* [Správa parametrů aplikace pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md)
* [Ladění aplikace Service Fabric](service-fabric-debugging-your-application.md)
* [Vizualizace clusteru pomocí Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
