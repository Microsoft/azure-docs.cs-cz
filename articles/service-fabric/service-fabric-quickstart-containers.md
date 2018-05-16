---
title: Vytvoření aplikace Azure Service Fabric typu kontejner pro Windows | Dokumentace Microsoftu
description: V tomto rychlém startu vytvoříte svou první aplikaci typu kontejner pro Windows na platformě Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: d78dbc9a32e804e37eb76047edcc050482df5761
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-deploy-a-service-fabric-windows-container-application-on-azure"></a>Rychlý start: Nasazení aplikace Service Fabric typu kontejner pro Windows v Azure
Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu škálovatelných a spolehlivých mikroslužeb a kontejnerů. 

Spuštění existující aplikace v kontejneru Windows v clusteru Service Fabric nevyžaduje žádné změny aplikace. Tento rychlý start ukazuje, jak nasadit předem připravenou image kontejneru Dockeru v aplikaci Service Fabric. Až budete hotovi, budete mít funkční kontejner Windows Server 2016 Nano Serveru a služby IIS. Tento rychlý start popisuje nasazení kontejneru Windows. Pokud chcete nasadit kontejner Linuxu, přečtěte si [tento rychlý start](service-fabric-quickstart-containers-linux.md).

![Výchozí webová stránka služby IIS][iis-default]

V tomto rychlém startu se naučíte:

* Zabalení kontejneru image Dockeru
* Konfigurace komunikace
* Sestavení a zabalení aplikace Service Fabric
* Nasazení aplikace typu kontejner do Azure

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure (můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Vývojový počítač s:
  * Visual Studio 2015 nebo Visual Studio 2017.
  * [Sada Service Fabric SDK a nástroje](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Zabalení kontejneru image Dockeru pomocí sady Visual Studio
Sada Service Fabric SDK a nástroje poskytují šablonu služby, která vám pomůže s nasazením kontejneru do clusteru Service Fabric.

Spusťte sadu Visual Studio jako správce.  Vyberte **Soubor** > **Nový** > **Projekt**.

Vyberte **Aplikace Service Fabric**, pojmenujte ji MyFirstContainer a klikněte na **OK**.

Z šablon **Hostované kontejnery a aplikace** vyberte **Kontejner**.

Do pole **Název image** zadejte microsoft/iis:nanoserver, což je [základní image Windows Server Nano Serveru a služby IIS](https://hub.docker.com/r/microsoft/iis/). 

Nakonfigurujte mapování portů kontejneru na porty hostitele tak, aby se příchozí požadavky na službu na portu 80 mapovaly na port 80 v kontejneru.  Nastavte **Port kontejneru** na 80 a **Port hostitele** také na 80.  

Pojmenujte službu MyContainerService a klikněte na **OK**.

![Dialogové okno Nová služba][new-service]

## <a name="create-a-cluster"></a>Vytvoření clusteru
Pokud chcete nasadit aplikaci do clusteru v Azure, můžete se připojit k Party Clusteru. Party clustery jsou bezplatné, časově omezené clustery Service Fabric hostované v Azure a provozované týmem Service Fabric, na kterých může kdokoli nasazovat aplikace a seznamovat se s platformou.  Cluster používá jediný certifikát podepsaný svým držitelem (self-signed certificate) pro zabezpečení mezi uzly i mezi klientem a uzlem. Party Clustery podporují kontejnery. Pokud se rozhodnete nastavit a používat vlastní cluster, musí tento cluster využívat skladovou položku, která podporuje kontejnery (například Windows Server 2016 Datacenter s kontejnery).

Přihlaste se a [připojte se ke clusteru Windows](http://aka.ms/tryservicefabric). Stáhněte si certifikát PFX do počítače kliknutím na odkaz **PFX**. Klikněte na odkaz **How to connect to a secure Party cluster?** (Jak se připojit k zabezpečenému Party Clusteru?) a zkopírujte heslo certifikátu. Certifikát, heslo certifikátu a hodnotu **Koncový bod připojení** použijete v následujících krocích.

![PFX a koncový bod připojení](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> Každou hodinu je k dispozici omezený počet Party Clusterů. Pokud se vám při pokusu o registraci Party Clusteru zobrazí chyba, můžete chvíli počkat a zkusit to znovu nebo můžete podle kroků v kurzu [Nasazení aplikace .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) vytvořit cluster Service Fabric ve svém předplatném Azure a nasadit aplikaci do něj. Cluster vytvořený prostřednictvím sady Visual Studio podporuje kontejnery. Po nasazení a ověření aplikace v clusteru můžete přeskočit k části [Kompletní příklad manifestů služby a aplikace Service Fabric](#complete-example-service-fabric-application-and-service-manifests) v tomto rychlém startu. 
>

Na počítači s Windows nainstalujte PFX do úložiště certifikátů *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
``` 

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Nasazení aplikace do Azure pomocí sady Visual Studio
Aplikace je teď připravená a přímo ze sady Visual Studio ji můžete nasadit do clusteru.

V Průzkumníku řešení klikněte pravým tlačítkem na **MyFirstContainer** a zvolte **Publikovat**. Zobrazí se dialogové okno Publikovat.

Do pole **Koncový bod připojení** zkopírujte **Koncový bod připojení** ze stránky Party clusteru. Například, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. 

Klikněte na **Publikovat**.

Každá aplikace v clusteru musí mít jedinečný název.  Party clustery jsou však veřejné a sdílené prostředí a může dojít ke konfliktu s již existující aplikací.  Pokud dojde ke konfliktu názvů, přejmenujte projekt sady Visual Studio a opakujte nasazení.

Otevřete prohlížeč a přejděte na **Koncový bod připojení** uvedený na stránce Party Clusteru. Volitelně můžete před adresu URL přidat identifikátor schématu `http://` a připojit za ní port `:80`. Například, http://zwin7fh14scd.westus.cloudapp.azure.com:80. Měla by se zobrazit výchozí webová stránka služby IIS: ![Výchozí webová stránka služby IIS][iis-default]

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste se naučili:

* Zabalení kontejneru image Dockeru
* Konfigurace komunikace
* Sestavení a zabalení aplikace Service Fabric
* Nasazení aplikace typu kontejner do Azure

Další informace o práci s kontejnery Windows v Service Fabric najdete v kurzu věnovaném aplikacím typu kontejner pro Windows.

> [!div class="nextstepaction"]
> [Vytvoření aplikace typu kontejner pro Windows](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
