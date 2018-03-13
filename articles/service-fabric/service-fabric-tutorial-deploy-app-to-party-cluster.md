---
title: "Nasazení aplikace Azure Service Fabric do clusteru ze sady Visual Studio | Microsoft Docs"
description: "Zjistěte, jak nasadit aplikaci do clusteru ze sady Visual Studio."
services: service-fabric
documentationcenter: .net
-author: mikkelhegn
-manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2018
ms.author: mikkelhegn
ms.custom: mvc
ms.openlocfilehash: 21c991a4e3f9ae19a4ad4a96427fdc1c91c55a1c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>Kurz: Nasazení aplikace do clusteru Service Fabric v Azure
V tomto kurzu, který je druhou částí série, se dozvíte, jak nasadit aplikaci Azure Service Fabric do nového clusteru v Azure přímo ze sady Visual Studio.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření clusteru v sadě Visual Studio
> * Nasadit aplikaci do vzdáleného clusteru pomocí sady Visual Studio


V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavit aplikaci .NET pro Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Nasadit aplikaci do vzdáleného clusteru
> * [Nakonfigurovat CI/CD pomocí Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>Požadavky
Než začnete s tímto kurzem:
- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Nainstalujte sadu Visual Studio 2017](https://www.visualstudio.com/) se sadami funkcí **Vývoj pro Azure** a **Vývoj pro ASP.NET a web**.
- [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové hlasovací aplikace
Pokud jste nesestavili ukázkovou hlasovací aplikaci v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md), můžete si ji stáhnout. V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="deploy-the-sample-application"></a>Nasazení ukázkové aplikace

### <a name="select-a-service-fabric-cluster-to-which-to-publish"></a>Výběr clusteru Service Fabric, na který se má publikovat
Aplikace je teď připravená a přímo ze sady Visual Studio ji můžete nasadit do clusteru.

Máte dvě možnosti nasazení:
- Vytvořit cluster v sadě Visual Studio. Tato možnost umožňuje vytvořit zabezpečený cluster přímo ze sady Visual Studio s použitím upřednostňované konfigurace. Tento typ clusteru je ideální pro testovací scénáře, kdy můžete vytvořit cluster a pak do něj publikovat přímo ze sady Visual Studio.
- Publikovat do existujícího clusteru ve vašem předplatném.

V tomto kurzu je popsaný postup pro vytvoření clusteru v sadě Visual Studio. V případě jiných možností můžete zkopírovat a vložit svůj koncový bod připojení nebo ho zvolit ze svého předplatného.
> [!NOTE]
> Řada služeb ke komunikaci mezi sebou používá reverzní proxy server. Clustery vytvořené v sadě Visual Studio a Party Clustery mají ve výchozím nastavení reverzní proxy server povolený.  Pokud používáte existující cluster, musíte [v clusteru povolit reverzní proxy server](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Nasazení aplikace do clusteru Service Fabric

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace a zvolte **Publikovat**.

2. Přihlaste se pomocí svého účtu Azure, abyste získali přístup ke svým předplatným. Tento krok je volitelný, pokud používáte Party Cluster.

3. Vyberte rozevírací seznam **Koncový bod připojení** a v něm vyberte možnost <Create New Cluster...>.
    
    ![Dialogové okno Publikovat](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
4. V dialogovém okně Vytvořit cluster upravte následující nastavení:

    1. Do pole Název clusteru zadejte název vašeho clusteru a zadejte také předplatné a umístění, které chcete použít.
    2. Volitelné: Můžete upravit počet uzlů. Ve výchozím nastavení máte tři uzly, což je požadované minimum pro testovací scénáře pro Service Fabric.
    3. Vyberte kartu Certifikát. Na této kartě zadejte heslo, které se použije k zabezpečení certifikátu vašeho clusteru. Tento certifikát pomáhá zabezpečit váš cluster. Můžete také upravit cestu, kam chcete certifikát uložit. Sada Visual Studio může také importovat certifikát za vás, protože se jedná o požadovaný krok pro publikování aplikace do clusteru.
    4. Vyberte kartu Podrobnosti virtuálního počítače. Zadejte heslo, které chcete použít pro virtuální počítače, které tvoří cluster. Pomocí uživatelského jména a hesla je možné se vzdáleně připojit k virtuálním počítačům. Musíte také vybrat velikost virtuálních počítačů a v případě potřeby změnit image virtuálního počítače.
    5. Volitelné: Na kartě Rozšířené můžete upravit seznam portů, které chcete otevřít v nástroji pro vyrovnávání zatížení, který se vytvoří společně s clusterem. Můžete také přidat existující klíč Application Insights, který se použije ke směrování souborů aplikačních protokolů.
    6. Jakmile budete hotovi s úpravami nastavení, vyberte tlačítko Vytvořit. Vytvoření trvá několik minut. Po úplném vytvoření clusteru se zobrazí oznámení v okně výstupu.
    
    ![Dialogové okno Vytvořit cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

4. Jakmile bude připravený cluster, který chcete použít, klikněte pravým tlačítkem na projekt aplikace a zvolte **Publikovat**.

    Po dokončení publikování by mělo být možné odeslat do aplikace požadavek z prohlížeče.

5. Otevřete svůj upřednostňovaný prohlížeč a zadejte adresu clusteru (koncový bod připojení bez informací o portu, třeba win1kw5649s.westus.cloudapp.azure.com).

    Teď byste měli vidět stejný výsledek, jako když jste aplikaci spustili v místním prostředí.

    ![Odpověď rozhraní API z clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření clusteru v sadě Visual Studio
> * Nasadit aplikaci do vzdáleného clusteru pomocí sady Visual Studio

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Nastavení průběžné integrace s Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
