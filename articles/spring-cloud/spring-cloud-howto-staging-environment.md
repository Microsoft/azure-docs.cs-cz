---
title: Nastavení přípravného prostředí v Azure Spring Cloud | Dokumenty společnosti Microsoft
description: Přečtěte si, jak používat modrozelené nasazení s Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471026"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Nastavení přípravného prostředí v Azure Spring Cloudu

Tento článek popisuje, jak nastavit pracovní nasazení pomocí modrozeleného vzoru nasazení v Azure Spring Cloud. Modré/zelené nasazení je vzor průběžného doručování Azure DevOps, který spoléhá na zachování živé stávající (modré) verze, zatímco probíhá nasazení nové (zelené) verze. Tento článek ukazuje, jak umístit toto pracovní nasazení do produkčního prostředí bez přímé změny produkčního nasazení.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste už nasadili aplikaci PiggyMetrics z našeho [kurzu o spuštění aplikace Azure Spring Cloud](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics se skládá ze tří aplikací: "brána", "account-service" a "auth-service".  

Pokud chcete použít jinou aplikaci pro tento příklad, je třeba provést jednoduchou změnu ve veřejné části aplikace.  Tato změna odlišuje pracovní nasazení od produkčního prostředí.

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění pokynů v tomto článku.  Má běžné předinstalované nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášení k předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com).  Další informace najdete v [tématu Přehled Azure Cloud Shell](../cloud-shell/overview.md).

Pokud chcete nastavit pracovní prostředí v Azure Spring Cloudu, postupujte podle pokynů v dalších částech.

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Nainstalujte rozšíření Azure Spring Cloud pro azure cli pomocí následujícího příkazu:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Zobrazit všechna nasazení

Přejděte na instanci služby na webu Azure portal a vyberte **správa nasazení,** chcete-li zobrazit všechna nasazení. Chcete-li zobrazit další podrobnosti, můžete vybrat každé nasazení.

## <a name="create-a-staging-deployment"></a>Vytvoření přípravného nasazení

1. V místním vývojovém prostředí proveďte malé změny aplikace brány PiggyMetrics. Můžete například změnit barvu v souboru *gateway/src/main/resources/static/css/launch.css.* To vám umožní snadno odlišit dvě nasazení. Chcete-li sestavit balíček jar, spusťte následující příkaz: 

    ```console
    mvn clean package
    ```

1. V nastavení náměku příkazového odpojování od Azure vytvořte nové nasazení a přiřazujte mu název pracovního nasazení "zelený".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Po úspěšném dokončení nasazení přejděte na stránku brány z **řídicího panelu aplikace**a zobrazte všechny instance na kartě **Instance aplikací** vlevo.
  
> [!NOTE]
> Stav zjišťování je *OUT_OF_SERVICE,* aby provoz nebyl směrován do tohoto nasazení před dokončením ověření.

## <a name="verify-the-staging-deployment"></a>Ověření testovacího nasazení

1. Vraťte se na stránku **správy nasazení** a vyberte nové nasazení. Stav nasazení by měl zobrazovat *spuštěný*. Tlačítko **Přiřadit nebo zrušit přiřazení domény** by mělo vypadat šedě, protože prostředí je pracovní prostředí.

1. V podokně **Přehled** byste měli vidět **koncový bod testu**. Zkopírujte a vložte jej do nového okna prohlížeče a měla by se zobrazit nová stránka PiggyMetrics.

>[!TIP]
> * Zkontrolujte, zda koncový bod testu končí lomítkem (/), abyste zajistili, že je soubor CSS načten správně.  
> * Pokud váš prohlížeč vyžaduje zadání přihlašovacích údajů pro zobrazení stránky, použijte [dekódování adresy URL](https://www.urldecoder.org/) k dekódování testovacího koncového bodu. Dekódování adresy URL vrátí adresu\<URL ve\<tvaru "https:// uživatelské jméno>: heslo>@\<název clusteru>.test.azureapps.io/gateway/green".  Tento formulář slouží k přístupu ke koncovému bodu.

>[!NOTE]    
> Nastavení konfiguračního serveru platí jak pro pracovní prostředí, tak pro produkční prostředí. Pokud například nastavíte cestu`server.servlet.context-path`k kontextu ( ) pro bránu aplikace na konfiguračním\<serveru jako\<nějakou\< *cestu*, změní se cesta k zelenému nasazení na "https:// uživatelské jméno>: heslo>@ název clusteru>.test.azureapps.io/gateway/green/somepath/...".
 
 Pokud v tomto okamžiku navštívíte veřejnou bránu aplikací, měla byste vidět starou stránku bez nové změny.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Nastavení zeleného nasazení jako produkčního prostředí

1. Po ověření změny v pracovním prostředí ji můžete posunout do produkčního prostředí. Vraťte se ke **správě nasazení**a zaškrtněte políčko aplikace **brány.**

2. Vyberte **nastavit nasazení**.
3. V seznamu **Nasazení výroby** vyberte **Zelenou**a pak vyberte **Použít**.
4. Přejděte na stránku **Přehled** aplikace brány. Pokud jste již aplikaci brány přiřadili doménu, adresa URL se zobrazí v podokně **Přehled.** Chcete-li zobrazit upravenou stránku PiggyMetrics, vyberte adresu URL a přejděte na web.

>[!NOTE]
> Po nastavení zelené nasazení jako produkční prostředí, předchozí nasazení se stane pracovní nasazení.

## <a name="modify-the-staging-deployment"></a>Úprava pracovního nasazení

Pokud nejste spokojeni s vaší změnou, můžete upravit kód aplikace, vytvořit nový balíček jar a nahrát ho do zeleného nasazení pomocí příkazového příkazu k řešení Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Odstranění testovacího nasazení

Pokud chcete odstranit pracovní nasazení z portu Azure, přejděte na stránku pracovního nasazení a vyberte tlačítko **Odstranit.**

Případně odstraňte pracovní nasazení z azure cli spuštěním následujícího příkazu:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
