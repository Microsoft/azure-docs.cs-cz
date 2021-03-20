---
title: Konfigurace zabezpečených připojení clusteru Azure Service Fabric
description: Naučte se, jak pomocí sady Visual Studio nakonfigurovat zabezpečená připojení podporovaná clusterem Azure Service Fabric.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: d4d6b781d97d481793e69cf2ca97cca5b93ce432
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008527"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurace zabezpečených připojení k Service Fabricmu clusteru ze sady Visual Studio
Naučte se používat Visual Studio k zabezpečenému přístupu ke clusteru Azure Service Fabric s nakonfigurovanými zásadami řízení přístupu.

## <a name="cluster-connection-types"></a>Typy připojení clusteru
Cluster Azure Service Fabric podporuje dva typy připojení: **nezabezpečená** připojení a zabezpečená připojení **založená na certifikátech x509** . (U Service Fabricch hostovaných clusterů se také podporují ověřování **systému Windows** a **dSTS** .) Při vytváření clusteru musíte nakonfigurovat typ připojení clusteru. Po vytvoření se typ připojení nedá změnit.

Nástroje sady Visual Studio Service Fabric podporují všechny typy ověřování pro připojení ke clusteru pro publikování. Pokyny, jak nastavit cluster zabezpečeného Service Fabric, najdete v tématu [Nastavení clusteru Service Fabric v Azure Portal](service-fabric-cluster-creation-via-portal.md) .

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurace připojení clusteru v profilech publikování
Pokud publikujete Service Fabric projekt ze sady Visual Studio, použijte dialogové okno **publikovat Service Fabric aplikaci** k výběru clusteru Azure Service Fabric. V části **koncový bod připojení** vyberte v rámci svého předplatného existující cluster.

![Dialogové okno * * Publikovat Service Fabric aplikace * * se používá ke konfiguraci připojení Service Fabric.][publishdialog]

Dialogové okno **publikovat Service Fabric aplikaci** automaticky ověří připojení clusteru. Pokud se zobrazí výzva, přihlaste se ke svému účtu Azure. Pokud ověření projde, znamená to, že váš systém má správně nainstalované správné certifikáty pro připojení ke clusteru, nebo že váš cluster není zabezpečený. Selhání ověřování může být způsobeno problémy se sítí nebo tím, že není váš systém správně nakonfigurován pro připojení k zabezpečenému clusteru.

![Dialogové okno * * Publikovat Service Fabric aplikace * * ověří existující, správně nakonfigurovanou Service Fabric připojení clusteru.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Připojení k zabezpečenému clusteru
1. Ujistěte se, že máte přístup k jednomu z klientských certifikátů, které cílový cluster důvěřuje. Certifikát je obvykle sdílen jako soubor. pfx (Personal Information Exchange). Informace o tom, jak nakonfigurovat server tak, aby udělil přístup klientovi, najdete v tématu [Nastavení clusteru Service Fabric z Azure Portal](service-fabric-cluster-creation-via-portal.md) .
2. Nainstalujte důvěryhodný certifikát. Provedete to tak, že dvakrát kliknete na soubor. pfx nebo pomocí skriptu PowerShellu Import-PfxCertificate naimportujete certifikáty. Nainstalujte certifikát do **certifikátu: \ úložišti LocalMachine\MY**. Pro přijetí všech výchozích nastavení je v pořádku při importu certifikátu.
3. Zvolte příkaz **publikovat...** v místní nabídce projektu pro otevření dialogového okna **publikovat aplikaci Azure** a pak vyberte cílový cluster. Nástroj automaticky vyřeší připojení a uloží parametry zabezpečeného připojení v profilu publikování.
4. Volitelné: profil publikování můžete upravit a zadat zabezpečené připojení k clusteru.
   
   Vzhledem k tomu, že ručně upravujete soubor XML profilu publikování, abyste určili informace o certifikátu, nezapomeňte si poznamenat název úložiště certifikátů, umístění úložiště a kryptografický otisk certifikátu. Tyto hodnoty budete muset zadat pro název úložiště certifikátu a umístění úložiště. Další informace naleznete v tématu [How to: Načtení kryptografického otisku certifikátu](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) .
   
   Pomocí parametrů *ClusterConnectionParameters* můžete zadat parametry PowerShellu, které se použijí při připojování ke clusteru Service Fabric. Platné parametry jsou všechny, které jsou přijímány rutinou Connect-ServiceFabricCluster. Seznam dostupných parametrů najdete v tématu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .
   
   Pokud publikujete do vzdáleného clusteru, je nutné zadat příslušné parametry pro konkrétní cluster. Následuje příklad připojení k nezabezpečenému clusteru:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Tady je příklad pro připojení k zabezpečenému clusteru založenému na certifikátech x509:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Upravte všechna další potřebná nastavení, jako jsou parametry upgradu a umístění souboru parametrů aplikace, a potom publikujte aplikaci v dialogovém okně **publikovat Service Fabric aplikaci** v aplikaci Visual Studio.

## <a name="next-steps"></a>Další kroky
Další informace o přístupu k clusterům Service Fabric najdete v tématu [Vizualizace clusteru pomocí Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
