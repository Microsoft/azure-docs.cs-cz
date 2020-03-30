---
title: Konfigurace připojení clusteru Azure Service Fabric
description: Zjistěte, jak pomocí sady Visual Studio nakonfigurovat zabezpečená připojení podporovaná clusterem Azure Service Fabric.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 11f76153726d3fc92118fb46cc61b4627ab6a1b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464097"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurace zabezpečených připojení ke clusteru Service Fabric z aplikace Visual Studio
Zjistěte, jak pomocí sady Visual Studio zabezpečit přístup ke clusteru Azure Service Fabric s nakonfigurovanými zásadami řízení přístupu.

## <a name="cluster-connection-types"></a>Typy připojení clusteru
Cluster Azure Service Fabric podporuje dva typy připojení: **nezabezpečená** připojení a zabezpečená připojení **založená na certifikátech x509.** (Pro clustery Service Fabric hostované místně jsou podporována také ověřování **systému Windows** a **dsts.)** Při vytváření clusteru je nutné nakonfigurovat typ připojení clusteru. Po vytvoření nelze změnit typ připojení.

Nástroje Visual Studio Service Fabric podporují všechny typy ověřování pro připojení ke clusteru pro publikování. Pokyny k nastavení [zabezpečeného clusteru](service-fabric-cluster-creation-via-portal.md) Service Fabric na webu Azure Portal najdete v tématu Nastavení clusteru Service Fabric.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurace připojení clusteru v profilech publikování
Pokud publikujete projekt Service Fabric z Visual Studia, použijte dialogové okno **Publikovat service fabric aplikace** pro výběr clusteru Azure Service Fabric. V části **Koncový bod připojení**vyberte existující cluster v rámci předplatného.

![Dialogové okno **Publish Service Fabric Application** se používá ke konfiguraci připojení Service Fabric.][publishdialog]

Dialogové okno **Publikovat aplikaci fabric služby** automaticky ověří připojení clusteru. Pokud se zobrazí výzva, přihlaste se ke svému účtu Azure. Pokud ověření projde, znamená to, že váš systém má nainstalovány správné certifikáty pro bezpečné připojení ke clusteru nebo cluster je nezabezpečený. Selhání ověření může být způsobeno problémy se sítí nebo tím, že systém není správně nakonfigurován pro připojení k zabezpečenému clusteru.

![Dialogové okno **Publish Service Fabric Application** ověřuje existující, správně nakonfigurované připojení clusteru Service Fabric.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Připojení k zabezpečenému clusteru
1. Ujistěte se, že máte přístup k jednomu z klientských certifikátů, kterým cílový cluster důvěřuje. Certifikát je obvykle sdílen jako soubor Výměny osobních informací (.pfx). Informace o tom, jak nakonfigurovat server tak, aby uděloval přístup klientovi, najdete v [tématu Nastavení clusteru Service Fabric z portálu Azure.](service-fabric-cluster-creation-via-portal.md)
2. Nainstalujte důvěryhodný certifikát. Chcete-li to provést, poklepejte na soubor .pfx nebo importujte certifikáty pomocí skriptu PowerShell Import-PfxCertificate. Nainstalujte certifikát do **aplikace Cert:\LocalMachine\My**. Při importu certifikátu je v pořádku přijmout všechna výchozí nastavení.
3. Zvolte **příkaz Publikovat...** v místní nabídce projektu, chcete-li otevřít dialogové okno **Publikovat aplikaci Azure** a pak vybrat cílový cluster. Nástroj automaticky vyřeší připojení a uloží parametry zabezpečeného připojení do profilu publikování.
4. Volitelné: Profil publikování můžete upravit a určit zabezpečené připojení clusteru.
   
   Vzhledem k tomu, že ručně upravujete soubor XML profilu publikování a zadejte informace o certifikátu, nezapomeňte si poznamenat název úložiště certifikátů, umístění úložiště a kryptografický otisk certifikátu. Budete muset zadat tyto hodnoty pro název úložiště certifikátu a umístění úložiště. Další informace naleznete [v tématu: Načtení kryptografického otisku certifikátu.](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx)
   
   Parametry *ClusterConnectionParameters* můžete použít k určení parametrů prostředí PowerShell, které se mají použít při připojování ke clusteru Service Fabric. Platné parametry jsou všechny, které jsou akceptovány rutinou Connect-ServiceFabricCluster. Seznam dostupných parametrů naleznete v tématu [Connect-ServiceFabricCluster.](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster)
   
   Pokud publikujete do vzdáleného clusteru, je třeba zadat příslušné parametry pro daný cluster. Následuje příklad připojení k nezabezpečenému clusteru:
   
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
5. Upravte všechna další potřebná nastavení, jako jsou parametry upgradu a umístění souboru parametru aplikace, a potom publikujte aplikaci z dialogového okna **Publikovat aplikaci aplikace service fabric** v sadě Visual Studio.

## <a name="next-steps"></a>Další kroky
Další informace o přístupu ke clusterům Service Fabric naleznete v [tématu Vizualizace clusteru pomocí aplikace Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
