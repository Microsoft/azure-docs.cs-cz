---
title: Nastavení přípravného prostředí v Azure jaře cloudu | Microsoft Docs
description: Naučte se používat nasazování s modrou zelenou službou Azure Pramenitého cloudu
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d36c185272b7b67ffb8a21a77cc3a64f22d0df5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888477"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Nastavení přípravného prostředí v Azure jaře cloudu

**Tento článek se týká:** ✔️ Java

Tento článek popisuje, jak nastavit pracovní nasazení pomocí modelu nasazení Blue-zelený ve jarním cloudu Azure. Modré/zelené nasazení je vzor průběžného doručování Azure DevOps, který spoléhá na zachování živé stávající (modré) verze, zatímco probíhá nasazení nové (zelené) verze. V tomto článku se dozvíte, jak umístit toto pracovní nasazení do produkčního prostředí beze změny produkčního nasazení přímo.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste už nasadili aplikaci PiggyMetrics z našeho [kurzu o spuštění aplikace pro jarní Cloud v Azure](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics zahrnuje tři aplikace: "brána", "Account-Service" a "auth-Service".  

Pokud chcete pro tento příklad použít jinou aplikaci, je nutné provést jednoduchou změnu ve veřejné části aplikace.  Tato změna rozlišuje vaše pracovní nasazení od výroby.

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění pokynů v tomto článku.  Má běžné, předem instalované nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni k předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com).  Další informace najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md).

Při vytváření přípravného prostředí v Azure jaře cloudu postupujte podle pokynů v dalších částech.

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Nainstalujte rozšíření Azure jaře Cloud pro Azure CLI pomocí následujícího příkazu:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Zobrazit všechna nasazení

V Azure Portal přejdete do své instance služby a výběrem možnosti **Správa nasazení** zobrazíte všechna nasazení. Chcete-li zobrazit další podrobnosti, můžete vybrat jednotlivá nasazení.

## <a name="create-a-staging-deployment"></a>Vytvoření pracovního nasazení

1. V místním vývojovém prostředí udělejte v aplikaci PiggyMetrics Gateway malou úpravu. Například změňte barvu v souboru *Gateway/src/Main/Resources/static/CSS/Launch. CSS* . Díky tomu můžete snadno odlišit tato dvě nasazení. Chcete-li sestavit balíček jar, spusťte následující příkaz: 

    ```console
    mvn clean package
    ```

1. V Azure CLI vytvořte nové nasazení a sdělte mu název pracovního nasazení "zelená".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Po úspěšném dokončení nasazení přejděte na stránku brány z **řídicího panelu aplikace**a zobrazte všechny vaše instance na kartě **instance aplikace** vlevo.
  
> [!NOTE]
> Stav zjišťování je *OUT_OF_SERVICE* , takže provoz nebude směrován do tohoto nasazení před dokončením ověření.

## <a name="verify-the-staging-deployment"></a>Ověření pracovního nasazení

1. Vraťte se na stránku **Správa nasazení** a vyberte nové nasazení. Stav nasazení by měl ukazovat na *spuštěno*. Tlačítko **přiřadit a zrušit přiřazení domény** by se mělo zobrazovat šedě, protože prostředí je přípravné prostředí.

1. V podokně **Přehled** by se měl zobrazit **testovací koncový bod**. Zkopírujte a vložte ho do nového okna prohlížeče a zobrazí se nová stránka PiggyMetrics.

>[!TIP]
> * Potvrďte, že koncový bod testu končí lomítkem (/), aby se zajistilo, že se soubor CSS správně načte.  
> * Pokud Váš prohlížeč vyžaduje, abyste zadali přihlašovací údaje pro zobrazení stránky, použijte k dekódování koncového bodu testu [adresu URL](https://www.urldecoder.org/) . Dekódování adresy URL vrátí adresu URL ve formátu "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green".  Pomocí tohoto formuláře můžete získat přístup ke koncovému bodu.

>[!NOTE]    
> Nastavení konfiguračního serveru se týká jak vašeho přípravného prostředí, tak i produkčního prostředí. Například pokud nastavíte cestu kontextu ( `server.servlet.context-path` ) pro bránu aplikace na serveru konfigurace jako *somepath*, cesta k zelenému nasazení se změní na "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/...".
 
 Pokud v tuto chvíli navštívíte svou veřejnou bránu aplikace, měli byste vidět starou stránku bez nové změny.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Nastavení zeleného nasazení jako produkčního prostředí

1. Jakmile ověříte změnu v přípravném prostředí, můžete ji vložit do produkčního prostředí. Vraťte se do **správy nasazení**a zaškrtněte políčko aplikace **brány** .

2. Vyberte **nastavit nasazení**.
3. V seznamu **nasazení výroby** vyberte **zelený**a pak vyberte **použít**.
4. Přejít na stránku **Přehled** aplikace brány. Pokud jste již přiřadili doménu pro aplikaci brány, adresa URL se zobrazí v podokně **Přehled** . Chcete-li zobrazit upravenou stránku PiggyMetrics, vyberte adresu URL a pak přejít na web.

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

* [Rychlý Start: nasazení první aplikace pro cloudovou službu Azure jaře](spring-cloud-quickstart.md)
