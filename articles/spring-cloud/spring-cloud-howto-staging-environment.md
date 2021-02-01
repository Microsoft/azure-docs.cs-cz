---
title: Nastavení přípravného prostředí v Azure jaře cloudu | Microsoft Docs
description: Naučte se používat nasazování s modrou zelenou službou Azure Pramenitého cloudu
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 991a335207fc29cef7b243d7e520dd5f62ff691f
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226100"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Nastavení přípravného prostředí v Azure jaře cloudu

**Tento článek se týká:** ✔️ Java

Tento článek popisuje, jak nastavit pracovní nasazení pomocí modelu nasazení Blue-zelený ve jarním cloudu Azure. Modré/zelené nasazení je vzor průběžného doručování Azure DevOps, který spoléhá na zachování živé stávající (modré) verze, zatímco probíhá nasazení nové (zelené) verze. V tomto článku se dozvíte, jak umístit toto pracovní nasazení do produkčního prostředí beze změny produkčního nasazení přímo.

## <a name="prerequisites"></a>Požadavky

* Instance Azure jarního cloudu s **cenovou úrovní** *Standard* .
* Běžící aplikace  Další informace najdete v tématu [rychlý Start: nasazení první aplikace pro cloudovou službu Azure jaře](spring-cloud-quickstart.md).
* Rozšíření Azure CLI [ASC](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

Pokud chcete pro tento příklad použít jinou aplikaci, je nutné provést jednoduchou změnu ve veřejné části aplikace.  Tato změna rozlišuje vaše pracovní nasazení od výroby.

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění pokynů v tomto článku.  Má běžné, předem instalované nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni k předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com).  Další informace najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md).

Při vytváření přípravného prostředí v Azure jaře cloudu postupujte podle pokynů v dalších částech.

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Nainstalujte rozšíření Azure jaře Cloud pro Azure CLI pomocí následujícího příkazu:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>Zobrazit aplikace a nasazení

Pomocí následujících postupů zobrazte nasazené aplikace.

1. V Azure Portal přejít na svou instanci cloudu Azure na jaře.

1. V levém navigačním podokně otevřete **nasazení**.

    [![Nasazení – zastaralé](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. Otevřete okno aplikace a zobrazte aplikace pro vaši instanci služby.

    [![Aplikace – Řídicí panel](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Můžete kliknout na aplikaci a zobrazit podrobnosti.

    [![Aplikace – přehled](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Otevřete okno **nasazení** , kde uvidíte všechna nasazení aplikace. V mřížce nasazení se zobrazuje, zda je nasazení v produkčním prostředí nebo v pracovním prostředí.

    [![Řídicí panel nasazení](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Kliknutím na název nasazení si můžete zobrazit přehled nasazení. V tomto případě se jediné nasazení nazývá *výchozí*.

    [![Přehled nasazení](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>Vytvoření pracovního nasazení

1. V místním vývojovém prostředí udělejte v aplikaci malou úpravu. Díky tomu můžete snadno odlišit tato dvě nasazení. Chcete-li sestavit balíček jar, spusťte následující příkaz: 

    ```console
    mvn clean package -DskipTests
    ```

1. V Azure CLI vytvořte nové nasazení a sdělte mu název pracovního nasazení "zelená".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app <appName> -n green --jar-path gateway/target/gateway.jar
    ```

1. Po úspěšném dokončení nasazení rozhraní příkazového řádku přejděte na stránku aplikace z **řídicího panelu aplikace** a zobrazte všechny vaše instance na kartě **nasazení** na levé straně.

   [![Řídicí panel nasazení po zeleném nasazení](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> Stav zjišťování je *OUT_OF_SERVICE* , takže provoz nebude směrován do tohoto nasazení před dokončením ověření.

## <a name="verify-the-staging-deployment"></a>Ověření pracovního nasazení

Chcete-li ověřit, že zelený pracovní vývoj funguje:
1. Přejděte do části **nasazení** a klikněte na `green` **pracovní nasazení**.
1. Na stránce **Přehled** klikněte na **koncový bod testu**.
1. Tím otevřete přípravný Build, ve kterém se zobrazí vaše změny.

>[!TIP]
> * Potvrďte, že koncový bod testu končí lomítkem (/), aby se zajistilo, že se soubor CSS správně načte.  
> * Pokud Váš prohlížeč vyžaduje, abyste zadali přihlašovací údaje pro zobrazení stránky, použijte k dekódování koncového bodu testu [adresu URL](https://www.urldecoder.org/) . Dekódování adresy URL vrátí adresu URL ve formátu "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green".  Pomocí tohoto formuláře můžete získat přístup ke koncovému bodu.

>[!NOTE]    
> Nastavení konfiguračního serveru se týká jak vašeho přípravného prostředí, tak i produkčního prostředí. Například pokud nastavíte cestu kontextu ( `server.servlet.context-path` ) pro bránu aplikace na serveru konfigurace jako *somepath*, cesta k zelenému nasazení se změní na "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/...".
 
 Pokud v tuto chvíli navštívíte svou veřejnou bránu aplikace, měli byste vidět starou stránku bez nové změny.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Nastavení zeleného nasazení jako produkčního prostředí

1. Jakmile ověříte změnu v přípravném prostředí, můžete ji vložit do produkčního prostředí. Vraťte se do **správy nasazení** a vyberte aplikaci, která je aktuálně v `Production` .

1. Klikněte na elipsy po **stavu registrace** a nastavte produkční sestavení na `staging` .

   [![Nasazení nastavení pracovního nasazení](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Vraťte se na stránku **správy nasazení** . Nastavte `green` nasazení na `production` . Až se nastavení dokončí, váš `green` stav nasazení by se měl zobrazit. Nyní se jedná o běžící výrobní sestavení.

   [![Nasazení nastaví výsledek pracovního nasazení.](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. Adresa URL aplikace by měla zobrazit vaše změny.

>[!NOTE]
> Po nastavení zeleného nasazení v produkčním prostředí se předchozí nasazení pokusí o pracovní nasazení.

## <a name="modify-the-staging-deployment"></a>Úprava pracovního nasazení

Pokud nejste spokojeni s vaší změnou, můžete upravit kód aplikace, sestavit nový balíček jar a nahrát ho do zeleného nasazení pomocí Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Odstraní pracovní nasazení.

Pokud chcete odstranit pracovní nasazení z portu Azure, klikněte na stránku pracovní nasazení a pak vyberte tlačítko **Odstranit** .

Případně odstraňte pracovní nasazení z Azure CLI spuštěním následujícího příkazu:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Další kroky

* [CI/CD pro jarní cloud Azure](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)
