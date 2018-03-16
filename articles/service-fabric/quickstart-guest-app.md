---
title: "Rychlé nasazení existující aplikace do clusteru Azure Service Fabric"
description: "Použijte cluster Azure Service Fabric k hostování existující aplikace Node.js pomocí sady Visual Studio."
services: service-fabric
documentationcenter: nodejs
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: adegeo
ms.openlocfilehash: 65185a2d594a4a251cf87ece6f758ab3db223d7d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Hostování aplikace Node.js na platformě Azure Service Fabric

Tento rychlý start vám pomůže s nasazením existující aplikace (v tomto příkladu Node.js) do clusteru Service Fabric spuštěného v Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte [nastavené vývojové prostředí](service-fabric-get-started.md). To zahrnuje instalaci sady Service Fabric SDK a sady Visual Studio 2017 nebo 2015.

Také musíte mít existující aplikaci Node.js k nasazení. V tomto rychlém startu se používá jednoduchý web v Node.js, který je ke stažení [zde][download-sample]. Po vytvoření projektu v dalším kroku extrahujte tento soubor do složky `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\`.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][create-account].

## <a name="create-the-service"></a>Vytvoření služby

Spusťte sadu Visual Studio jako **správce**.

Vytvořte projekt pomocí klávesové zkratky `CTRL`+`SHIFT`+`N`.

V dialogovém okně **Nový projekt** zvolte **Cloud > Aplikace Service Fabric**.

Pojmenujte aplikaci **MyGuestApp** a stiskněte **OK**.

>[!IMPORTANT]
>Node.js může snadno překročit omezení 260 znaků pro cesty v systému Windows. Pro samotný projekt použijte krátkou cestu, například **c:\code\svc1**. Volitelně můžete postupovat podle **[těchto pokynů](https://stackoverflow.com/a/41687101/1664231)** a povolit dlouhé cesty k souborům ve Windows 10.
   
![Dialogové okno Nový projekt ve Visual Studiu][new-project]

V dalším dialogovém okně můžete vytvořit jakýkoli typ služby Service Fabric. Pro účely tohoto Rychlého startu zvolte **Spustitelný soubor typu Host**.

Pojmenujte službu **MyGuestService** a nastavte možnosti na pravé straně na následující hodnoty:

| Nastavení                   | Hodnota |
| ------------------------- | ------ |
| Složka balíčku kódu       | _&lt;složka s vaší aplikací Node.js&gt;_ |
| Chování balíčku kódu     | Zkopírujte obsah složky do projektu |
| Program                   | node.exe |
| Argumenty                 | server.js |
| Pracovní složka            | CodePackage |

Stiskněte **OK**.

![Dialogové okno Nová služba ve Visual Studiu][new-service]

Sada Visual Studio vytvoří projekt aplikace a projekt služby objektu actor a zobrazí je v Průzkumníku řešení.

Projekt aplikace (**MyGuestApp**) jako takový neobsahuje žádný kód. Odkazuje ale na sadu projektů služeb. Kromě toho obsahuje další tři typy obsahu:

* **Profily publikování**  
Předvolby nástrojů pro různá prostředí.

* **Skripty**  
Skript PowerShellu pro nasazení/upgrade aplikace.

* **Definice aplikace**  
Obsahuje manifest aplikace ve složce *ApplicationPackageRoot*. Přidružené soubory parametrů aplikace, které se nachází ve složce *ApplicationParameters*, definují aplikaci a umožňují vám ji nakonfigurovat speciálně pro určité prostředí.
    
Přehled obsahu projektu služby najdete v tématu [Začínáme se službami Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="set-up-networking"></a>Nastavení síťových služeb

Příklad aplikace Node.js, který nasazujeme, používá port **80** a platformě Service Fabric potřebujeme říct, že tento port potřebujeme zpřístupnit.

Otevřete v projektu soubor **ServiceManifest.xml**. V dolní části manifestu je část `<Resources> \ <Endpoints>` s již definovanou položkou. Upravte tuto položku a přidejte `Port`, `Protocol` a `Type`. 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>Nasazení do Azure

Pokud stisknete klávesu **F5** a spustíte projekt, nasadí se do místního clusteru. My jej ale místo toho nasadíme do Azure.

Klikněte na projekt pravým tlačítkem a zvolte **Publikovat...**, tím se otevře dialogové okno pro publikování do Azure.

![Dialogové okno pro publikování služby Service Fabric do Azure][publish]

Vyberte cílový profil **PublishProfiles\Cloud.xml**.

Pokud jste to neudělali dříve, zvolte účet Azure, do kterého se má nasazení provést. Pokud ještě žádný nemáte, [zaregistrujte si bezplatný účet][create-account].

V části **Koncový bod připojení** vyberte cluster Service Fabric, do kterého se má nasazení provést. Pokud žádný nemáte, vyberte **&lt;Vytvořit nový cluster...&gt;**, tím se otevře okno webového prohlížeče s webem Azure Portal. Další informace najdete v tématu popisujícím [vytvoření clusteru na portálu](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal). 

Při vytváření clusteru Service Fabric nezapomeňte nastavit nastavení **Vlastní koncové body** na hodnotu **80**.

![Konfigurace typu uzlu Service Fabric s vlastním koncovým bodem][custom-endpoint]

Dokončení vytvoření nového clusteru Service Fabric nějakou dobu trvá. Jakmile bude vytvořený, vraťte se do dialogového okna pro publikování a vyberte **&lt;Aktualizovat&gt;**. Nový cluster bude uveden v rozevíracím seznamu, vyberte ho.

Stiskněte **Publikovat** a počkejte na dokončení nasazení.

Může to trvat několik minut. Po dokončení nasazení může ještě několik minut trvat, než bude aplikace plně dostupná.

## <a name="test-the-website"></a>Testování webu

Jakmile bude vaše služba publikována, otestujte ji ve webovém prohlížeči. 

Nejprve otevřete web Azure Portal a vyhledejte vaši službu Service Fabric.

Zkontrolujte okno přehledu adresy služby. Použijte název domény z vlastnosti _Koncový bod pro připojení klienta_. Například, `http://mysvcfab1.westus2.cloudapp.azure.com`.

![Okno přehledu Service Fabric na webu Azure Portal][overview]

Přejděte na tuto adresu, kde se zobrazí odezva `HELLO WORLD`.

## <a name="delete-the-cluster"></a>Odstranění clusteru

Nezapomeňte odstranit všechny prostředky, které jste vytvořili pro účely tohoto rychlého startu – tyto prostředky se vám účtují.

## <a name="next-steps"></a>Další postup
Další informace o [spustitelných souborech typu Host](service-fabric-guest-executables-introduction.md).

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
