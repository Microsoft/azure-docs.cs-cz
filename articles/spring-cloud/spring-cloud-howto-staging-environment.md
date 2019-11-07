---
title: Nastavení přípravného prostředí v Azure jaře cloudu | Microsoft Docs
description: Naučte se používat nasazování s modrou zelenou službou Azure Pramenitého cloudu
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 24ce4dee04e4daf3eaee4144f8dc56de5867bbca
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607209"
---
# <a name="how-to-set-up-a-staging-environment"></a>Jak nastavit přípravné prostředí

V tomto článku se dozvíte, jak pomocí modelu nasazení Blue-zelená ve jarním cloudu Azure využít pracovní nasazení. Také se dozvíte, jak umístit toto pracovní nasazení do produkčního prostředí beze změny produkčního nasazení přímo.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste už nasadili aplikaci PiggyMetrics z našeho [kurzu spuštění aplikace](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics zahrnuje tři aplikace: "brána", "Account-Service" a "auth-Service".  

Máte-li jinou aplikaci, kterou chcete použít v tomto příkladu, bude nutné provést jednoduchou změnu ve veřejné části aplikace.  Tato změna rozlišuje vaše pracovní nasazení od výroby.

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku.  Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

Postup dokončení tohoto článku:


## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Pomocí následujícího příkazu nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure.

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Zobrazit všechna nasazení

V Azure Portal přejdete do své instance služby a výběrem možnosti **Správa nasazení** zobrazíte všechna nasazení. Kliknutím na každé nasazení si můžete zobrazit další podrobnosti.

## <a name="create-a-staging-deployment"></a>Vytvoření pracovního nasazení

1. V místním vývojovém prostředí udělejte malou úpravu aplikace brány Piggy metriky. Změňte například barvu v `gateway/src/main/resources/static/css/launch.css`. To vám umožní snadno odlišit tato dvě nasazení. Spusťte následující příkaz, který sestaví balíček JAR: 

    ```azurecli
    mvn clean package
    ```

1. Vytvoří nové nasazení pomocí Azure CLI a pojmenuje ho jako název pracovního nasazení "zelený".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Po úspěšném dokončení nasazení přejděte na stránku brány z **řídicího panelu aplikace** a na kartě **instance aplikace** na levé straně uvidíte všechny vaše instance.
  
> [!NOTE]
> Stav zjišťování je "OUT_OF_SERVICE", takže před dokončením ověřování nebude provoz směrován do tohoto nasazení.

## <a name="verify-the-staging-deployment"></a>Ověření pracovního nasazení

1. Vraťte se na stránku **správy nasazení** a vyberte nové nasazení. Stav nasazení by měl ukazovat na **spuštěno**. Tlačítko "přiřadit/zrušit přiřazení domény" bude zakázáno, protože se jedná o přípravné prostředí.

1. Na stránce **Přehled** byste měli vidět **testovací koncový bod**. Zkopírujte ho a vložte na novou stránku prohlížeče a měli byste vidět novou stránku metriky Piggy.

>[!TIP]
> * Ověřte, zda koncový bod testu končí znakem "/", aby bylo zajištěno, že CSS bude správně načteno.  
> * Pokud Váš prohlížeč vyžaduje, abyste zadali přihlašovací údaje pro zobrazení stránky, použijte k dekódování koncového bodu testu [adresu URL](https://www.urldecoder.org/) . Dekódování adresy URL vrátí adresu URL ve formátu https://\<username >:\<heslo > @\<clusteru-Name >. test. azureapps. IO/Gateway/zelená.  Pro přístup ke koncovému bodu použijte tento postup.

>[!NOTE]    
> Nastavení konfiguračního serveru se vztahuje na vaše pracovní prostředí i na produkci. Pokud jste například nastavili cestu kontextu (`server.servlet.context-path`) pro bránu aplikace na serveru konfigurace jako *somepath*, cesta ke zeleně změnám nasazení: "https://\<username >:\<Password > @\<cluster-Name >. test.azureapps.io/gateway/green/somepath/... "
 
 Pokud v tuto chvíli navštívíte svou veřejnou bránu aplikace, měli byste vidět starou stránku bez nové změny.
    
## <a name="set-the-green-as-production-deployment"></a>Nastavení zeleného nasazení v produkčním prostředí

1. Když jste ověřili změnu v přípravném prostředí, můžete ji vložit do produkčního prostředí. Vraťte se do **správy nasazení** a zaškrtněte políčko před aplikací Gateway.
2. Vyberte nastavit nasazení.
3. V nabídce "nasazení v PRODUKČNÍm prostředí" vyberte "zelená" a vyberte **použít**
4. Přejít na stránku **Přehled** aplikace brány. Pokud jste již přiřadili doménu pro aplikaci brány, adresa URL se zobrazí na stránce **Přehled** . Přejděte na adresu URL a zobrazí se upravená stránka Piggy metriky.

>[!NOTE]
> Po nastavení zeleného nasazení v produkčním prostředí se předchozí nasazení stalo pracovním nasazením.

## <a name="modify-the-staging-deployment"></a>Úprava pracovního nasazení

Pokud nejste s vaší změnou spokojeni, můžete upravit kód aplikace, sestavit nový balíček jar a nahrát ho do zeleného nasazení pomocí Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>Odstranění pracovního nasazení

Odstraňte pracovní nasazení z portu Azure tak, že přejdete na stránku pracovního nasazení a vyberete tlačítko **Odstranit** .

Případně odstraňte pracovní nasazení z Azure CLI pomocí následujícího příkazu:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
