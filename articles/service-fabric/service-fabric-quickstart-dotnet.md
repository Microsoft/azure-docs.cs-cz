---
title: Vytvoření aplikace Service Fabric v .NET v Azure | Dokumentace Microsoftu
description: V tomto rychlém startu vytvoříte aplikaci .NET pro Azure s využitím ukázkové aplikace Service Fabric Reliable Services.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: mikhegn
ms.custom: mvc, devcenter
ms.openlocfilehash: 001488a8c7e22db595cd9f929bc0f3d631da0715
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207202"
---
# <a name="quickstart-create-a-net-service-fabric-application-in-azure"></a>Rychlý start: Vytvoření aplikace Service Fabric v .NET v Azure
Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu škálovatelných a spolehlivých mikroslužeb a kontejnerů. 

Tento rychlý start ukazuje, jak nasadit první aplikaci .NET do Service Fabric. Až budete hotovi, budete mít hlasovací aplikaci s webovým front-endem v ASP.NET Core, která ukládá výsledky hlasování do stavové back-end služby v clusteru.

![Snímek obrazovky aplikace](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Pomocí této aplikace se naučíte:

* Vytvoření aplikace pomocí .NET a Service Fabric
* Použití ASP.NET jako webového front-endu
* Ukládání dat aplikace do stavové služby
* Místní ladění aplikace
* Nasazení aplikace do clusteru v Azure
* Škálování aplikace na více instancí napříč několika uzly
* Provedení upgradu aplikace se zajištěním provozu

## <a name="prerequisites"></a>Požadavky
K provedení kroků v tomto kurzu Rychlý start je potřeba:
1. [Nainstalovat sadu Visual Studio 2017](https://www.visualstudio.com/) se sadami funkcí **Vývoj pro Azure** a **Vývoj pro ASP.NET a web**.
2. [Nainstalovat Git](https://git-scm.com/).
3. [Nainstalovat sadu Microsoft Azure Service Fabric SDK](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK).
4. Spusťte následující příkaz a povolte sadě Visual Studio nasazování do místního clusteru Service Fabric:
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
    ```

>[!NOTE]
> Ukázková aplikace v tomto rychlém startu používá funkce, které nejsou dostupné v systému Windows 7.
>

## <a name="download-the-sample"></a>Stažení ukázky
V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.
```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>Místní spuštění aplikace
Klikněte pravým tlačítkem na ikonu sady Visual Studio v nabídce Start a zvolte **Spustit jako správce**. Abyste mohli ke svým službám připojit ladicí program, musíte sadu Visual Studio spustit jako správce.

Otevřete řešení sady Visual Studio **Voting.sln** z úložiště, které jste naklonovali.  

Ve výchozím nastavení je hlasovací aplikace nastavená k naslouchání na portu 8080.  Port aplikace je nastavený v souboru */VotingWeb/PackageRoot/ServiceManifest.xml*.  Port aplikace můžete změnit aktualizací atributu **Port** elementu **Endpoint**.  Pokud chcete aplikaci nasadit a spustit místně, port aplikace musí být otevřený a dostupný na vašem počítači.  Pokud změníte port aplikace, nahraďte hodnotu 8080 v celém tomto článku za novou hodnotu portu aplikace.

Pokud chcete aplikaci nasadit, stiskněte **F5**.

> [!NOTE]
> Při prvním spuštění a nasazení aplikace sada Visual Studio vytvoří místní cluster pro účely ladění. Tato operace může nějakou dobu trvat. Stav vytváření clusteru se zobrazí v okně výstupu sady Visual Studio.  Ve výstupu se zobrazí zpráva „The application URL is not set or is not an HTTP/HTTPS URL so the browser will not be opened to the application“ (Adresa URL aplikace není nastavená nebo se nejedná o adresu URL protokolu HTTP/HTTPS, takže se aplikace neotevře v prohlížeči).  Tato zpráva neznačí chybu, pouze informuje, že se prohlížeč nespustí automaticky.

Po dokončení nasazení spusťte prohlížeč a otevřete tuto stránku: `http://localhost:8080` – to je webový front-end aplikace.

![Front-end aplikace](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

Teď můžete přidat sadu možností hlasování a začít přijímat hlasy. Aplikace je spuštěná a ukládá veškerá data v clusteru Service Fabric, aniž by potřebovala samostatnou databázi.

## <a name="walk-through-the-voting-sample-application"></a>Prohlídka ukázkové hlasovací aplikace
Hlasovací aplikace se skládá ze dvou služeb:
- Webová front-end služba (VotingWeb) – Webová front-end služba v ASP.NET, která obsluhuje webovou stránku a zveřejňuje webová rozhraní API pro komunikaci s back-end službou.
- Back-end služba (VotingData) – Webová služba v ASP.NET Core, která zveřejňuje rozhraní API pro ukládání výsledků hlasování do spolehlivého slovníku trvale uloženého na disku.

![Diagram aplikace](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Při hlasování v aplikaci dojde k následujícím událostem:
1. JavaScript odešle požadavek s hlasem do webového rozhraní API ve webové front-end službě jako požadavek HTTP PUT.

2. Webová front-end služba pomocí proxy vyhledá požadavek HTTP PUT a přesměruje ho do back-end služby.

3. Back-end služba přijme příchozí požadavek a uloží aktualizované výsledky do spolehlivého slovníku, který se replikuje do několika uzlů v rámci clusteru a trvale se uloží na disku. Veškerá data aplikace se ukládají v clusteru, takže není potřeba žádná databáze.

## <a name="debug-in-visual-studio"></a>Ladění v sadě Visual Studio

Aplikace by měla být spuštěná bez problémů, ale pomocí ladicího programu se můžete podívat, jak fungují klíčové části aplikace. Při ladění aplikace v sadě Visual Studio používáte místní vývojový cluster Service Fabric. Možnosti ladění si můžete upravit tak, aby vyhovovaly vašemu scénáři. V této aplikaci se data ukládají v back-end službě s použitím spolehlivého slovníku. Sada Visual Studio ve výchozím nastavení odebere aplikaci při zastavení ladicího programu. Odebrání aplikace způsobí i odebrání dat v back-end službě. Pokud chcete zachovat data mezi ladicími relacemi, můžete změnit **Režim ladění aplikace** ve vlastnosti projektu **Voting** v sadě Visual Studio.

Pokud se chcete podívat, co se děje v kódu, proveďte následující kroky:
1. Otevřete soubor **/VotingWeb/Controllers/VotesController.cs** a nastavte zarážku v metodě **Put** webového rozhraní API (řádek 69) – Soubor můžete vyhledat v Průzkumníku řešení v sadě Visual Studio.

2. Otevřete soubor **/VotingData/Controllers/VoteDataController.cs** a nastavte zarážku v metodě **Put** tohoto webového rozhraní API (řádek 54).

3. Vraťte se do prohlížeče a klikněte na některou možnost hlasování nebo přidejte novou. Dostanete se k první zarážce v kontroleru rozhraní API webového front-endu.
    - Tady JavaScript v prohlížeči odesílá požadavek do kontroleru webového rozhraní API ve front-end službě.
    
    ![Front-end služba pro přidání hlasu](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

    - Nejprve se vytvoří adresa URL k ReverseProxy pro back-end službu **(1)**.
    - Pak se do ReverseProxy odešle požadavek HTTP PUT **(2)**.
    - Nakonec se do klienta vrátí odpověď z back-end služby **(3)**.

4. Pokračujte stisknutím **F5**.
    - Po zobrazení výzvy v prohlížeči udělte skupině ServiceFabricAllowedUsers oprávnění ke čtení a provádění v režimu ladění.
    - Nyní jste se dostali k zarážce v back-end službě.
    
    ![Back-end služba pro přidání hlasu](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

    - Na prvním řádku v metodě **(1)** `StateManager` načte nebo přidá spolehlivý slovník `counts`.
    - Všechny interakce s hodnotami ve spolehlivém slovníku vyžadují transakci. Tuto transakci vytvoří tento příkaz using **(2)**.
    - V transakci se pro možnost hlasování aktualizuje hodnota příslušného klíče a potvrdí se operace **(3)**. Jakmile se vrátí metoda potvrzení, data ve slovníku se aktualizují a replikují do dalších uzlů v clusteru. Data jsou teď bezpečně uložená v clusteru a v případě selhání back-end služby ji můžou převzít ostatní uzly, aby data byla i nadále dostupná.
5. Pokračujte stisknutím **F5**.

Pokud chcete zastavit ladicí relaci, stiskněte **Shift + F5**.

## <a name="deploy-the-application-to-azure"></a>Nasazení aplikace v Azure
Pokud chcete nasadit aplikaci do Azure, potřebujete cluster Service Fabric, ve kterém bude aplikace spuštěná. 

### <a name="join-a-party-cluster"></a>Připojení k Party clusteru
Party clustery jsou bezplatné, časově omezené clustery Service Fabric hostované v Azure a provozované týmem Service Fabric, na kterých může kdokoli nasazovat aplikace a seznamovat se s platformou. Cluster používá jediný certifikát podepsaný svým držitelem pro zabezpečení mezi uzly i mezi klientem a uzlem. 

Přihlaste se a [připojte se ke clusteru Windows](http://aka.ms/tryservicefabric). Stáhněte si certifikát PFX do počítače kliknutím na odkaz **PFX**. Klikněte na odkaz **How to connect to a secure Party cluster?** (Jak se připojit k zabezpečenému Party Clusteru?) a zkopírujte heslo certifikátu. Certifikát, heslo certifikátu a hodnotu **Koncový bod připojení** použijete v následujících krocích.

![PFX a koncový bod připojení](./media/service-fabric-quickstart-dotnet/party-cluster-cert.png)

> [!Note]
> Každou hodinu je k dispozici omezený počet Party Clusterů. Pokud se vám při pokusu o registraci Party Clusteru zobrazí chyba, můžete chvíli počkat a zkusit to znovu nebo můžete podle kroků v kurzu [Nasazení aplikace .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) vytvořit cluster Service Fabric ve svém předplatném Azure a nasadit aplikaci do něj. Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Po nasazení a ověření aplikace v clusteru můžete přeskočit k části [Škálování aplikací a služeb v clusteru](#scale-applications-and-services-in-a-cluster) v tomto rychlém startu.
>


Na svém počítači s Windows nainstalujte soubor PFX do úložiště certifikátů *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Zapamatujte si kryptografický otisk pro následující krok.

> [!Note]
> Webová front-end služba je ve výchozím nastavení nakonfigurovaná k naslouchání příchozímu provozu na portu 8080. Port 8080 je v Party clusteru otevřený.  Pokud potřebujete změnit port aplikace, změňte ho na některý z portů, které jsou v Party clusteru otevřené.
>

### <a name="deploy-the-application-using-visual-studio"></a>Nasazení aplikace pomocí sady Visual Studio
Aplikace je teď připravená a přímo ze sady Visual Studio ji můžete nasadit do clusteru.

1. V Průzkumníku řešení klikněte pravým tlačítkem na **Voting** a zvolte **Publikovat**. Zobrazí se dialogové okno Publikovat.


2. Do pole **Koncový bod připojení** zkopírujte **Koncový bod připojení** ze stránky Party clusteru. Například, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Klikněte na **Rozšířené parametry připojení** a ujistěte se, že hodnoty *FindValue* and *ServerCertThumbprint* odpovídají kryptografickému otisku certifikátu nainstalovaného v předchozím kroku. 

    ![Dialogové okno Publikovat](./media/service-fabric-quickstart-dotnet/publish-app.png)

    Každá aplikace v clusteru musí mít jedinečný název.  Party clustery jsou však veřejné a sdílené prostředí a může dojít ke konfliktu s již existující aplikací.  Pokud dojde ke konfliktu názvů, přejmenujte projekt sady Visual Studio a opakujte nasazení.

3. Klikněte na **Publikovat**.

4. Otevřete prohlížeč a přejděte do aplikace v clusteru zadáním adresy clusteru následované :8080, například `http://zwin7fh14scd.westus.cloudapp.azure.com:8080`. Nyní by se měla zobrazit aplikace spuštěná v clusteru v Azure.

    ![Front-end aplikace](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru
Služby Service Fabric je možné snadno škálovat napříč clusterem a vyřešit tak změny jejich zatížení. Služby se škálují změnou počtu instancí spuštěných v clusteru. Služby můžete škálovat několika způsoby – můžete použít skripty nebo příkazy v PowerShellu nebo Service Fabric CLI (sfctl). V tomto příkladu používáme Service Fabric Explorer.

Nástroj Service Fabric Explorer běží na všech clusterech Service Fabric a je přístupný z prohlížeče po přechodu na port HTTP pro správu clusteru (19080), například `https://zwin7fh14scd.westus.cloudapp.azure.com:19080`. 

Můžete obdržet upozornění prohlížeče, že umístění není důvěryhodné. To je způsobeno tím, že je certifikát podepsaný svým držitelem. Můžete se rozhodnout toto varování upozornění a pokračovat.
1. Po výzvě prohlížeče vyberte nainstalovaný certifikát pro připojení. Certifikát Party clusteru, který vyberete v seznamu, musí odpovídat Party clusteru, ke kterému se pokoušíte získat přístup. Příklad: win243uja6w62r.westus.cloudapp.azure.com.
2. Po zobrazení výzvy v prohlížeči udělte této relaci přístup ke svému privátnímu klíči rozhraní CryptoAPI.

Pokud chcete škálovat webovou front-end službu, proveďte následující kroky:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `https://zwin7fh14scd.westus.cloudapp.azure.com:19080`. 
2. Ve stromovém zobrazení rozbalte **Aplikace**->**VotingType**->**fabric:/Voting**. Ve stromovém zobrazení klikněte na tři tečky vedle uzlu **fabric:/Voting/VotingWeb** a zvolte **Škálovat službu**.

    ![Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scale.png)

    Nyní můžete škálovat počet instancí webové front-end služby.

3. Změňte počet na **2** a klikněte na **Škálovat službu**.
4. Ve stromovém zobrazení klikněte na uzel **fabric:/Voting/VotingWeb** a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID).

    ![Service Fabric Explorer – škálování služby](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scaled-service.png)

    Po prodlevě uvidíte, že má služba dvě instance.  Ve stromovém zobrazení vidíte, na kterých uzlech jsou instance spuštěné.

Touto jednoduchou úlohou správy se zdvojnásobily prostředky, které má front-end služba k dispozici pro zpracování uživatelské zátěže. Je důležité si uvědomit, že pro spolehlivý provoz služby nepotřebujete více jejích instancí. Pokud služba selže, Service Fabric zajistí v clusteru spuštění nové instance služby.

## <a name="perform-a-rolling-application-upgrade"></a>Provedení upgradu aplikace se zajištěním provozu
Když do aplikace nasazujete nové aktualizace, Service Fabric je bezpečným způsobem zavádí postupně. Upgrady se zajištěním provozu zajišťují upgradování bez výpadků a také automatické vrácení zpět v případě, že dojde k chybě.

Pokud chcete upgradovat aplikaci, postupujte následovně:

1. Otevřete v sadě Visual Studio soubor **/VotingWeb/Views/Home/Index.cshtml**.
2. Změňte nadpis na stránce přidáním nebo aktualizací textu. Nadpis můžete změnit například na Ukázková hlasovací aplikace Service Fabric v2.
3. Uložte soubor.
4. V Průzkumníku řešení klikněte pravým tlačítkem na **Voting** a zvolte **Publikovat**. Zobrazí se dialogové okno Publikovat.
5. Klikněte na tlačítko **Verze manifestu** a změňte verzi služby a aplikace.
6. Změňte například verzi elementu **Kód** v části **VotingWebPkg** na 2.0.0 a klikněte na **Uložit**.

    ![Dialogové okno pro změnu verze](./media/service-fabric-quickstart-dotnet/change-version.png)
7. V dialogovém okně **Publikovat aplikaci Service Fabric** zaškrtněte políčko Upgradovat aplikaci a klikněte na **Publikovat**.

    ![Dialogové okno pro publikování – nastavení upgradu](./media/service-fabric-quickstart-dotnet/upgrade-app.png)

    V průběhu upgradu můžete aplikaci dál používat. Vzhledem k tomu, že v clusteru máte spuštěné dvě instance služby, můžou některé požadavky přicházet do upgradované verze aplikace, zatímco jiné můžou stále přicházet do staré verze.

8. Otevřete prohlížeč a přejděte na adresu clusteru na portu 19080, například `http://zwin7fh14scd.westus.cloudapp.azure.com:19080`.
9. Ve stromovém zobrazení klikněte na uzel **Aplikace** a pak na **Probíhající upgrady** v pravém podokně. Zobrazí se postupné zavádění upgradu napříč upgradovacími doménami ve vašem clusteru. U každé domény se nejprve ověří, jestli je v pořádku, a pak se přejde k další. Po ověření stavu domény se upgradovací doména v indikátoru průběhu zobrazí zeleně.
    ![Zobrazení upgradu v Service Fabric Exploreru](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Service Fabric zajišťuje bezpečné upgrady tím, že po upgradu služby na každém uzlu v clusteru dvě minuty počká. Počítejte s tím, že úplná aktualizace bude trvat přibližně osm minut.


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste se naučili:

* Vytvoření aplikace pomocí .NET a Service Fabric
* Použití ASP.NET jako webového front-endu
* Ukládání dat aplikace do stavové služby
* Místní ladění aplikace
* Nasazení aplikace do clusteru v Azure
* Škálování aplikace na více instancí napříč několika uzly
* Provedení upgradu aplikace se zajištěním provozu

Další informace o Service Fabric a .NET najdete v tomto kurzu:
> [!div class="nextstepaction"]
> [Aplikace .NET v Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
