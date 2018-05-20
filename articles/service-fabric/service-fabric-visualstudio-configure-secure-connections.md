---
title: Konfigurace zabezpečeného připojení clusteru Azure Service Fabric | Microsoft Docs
description: Zjistěte, jak nakonfigurovat zabezpečené připojení, které jsou podporovány v clusteru Azure Service Fabric pomocí sady Visual Studio.
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 80501867-dd7a-4648-8bd6-d4f26b68402d
ms.service: multiple
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: e2772cc2c59b93c7e523eaa0127dcf4ea0bc589e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurace zabezpečeného připojení ke clusteru Service Fabric ze sady Visual Studio
Další informace o použití sady Visual Studio bezpečný přístup k clusteru služby Azure Service Fabric s nakonfigurované zásady řízení přístupu.

## <a name="cluster-connection-types"></a>Typy připojení clusteru
Cluster Azure Service Fabric podporuje dva typy připojení: **nezabezpečené** připojení a **x509 založené na certifikátech** zabezpečené připojení. (Pro clustery infrastruktury služby hostované na místních počítačích **Windows** a **dSTS** ověřování jsou také podporovány.) Je nutné konfigurovat typ připojení clusteru při vytvoření clusteru. Jakmile je vytvořen, nelze změnit typ připojení.

Nástroje sady Visual Studio Service Fabric podporovat všechny typy ověřování pro připojení ke clusteru s podporou pro publikování. V tématu [nastavení cluster Service Fabric na portálu Azure](service-fabric-cluster-creation-via-portal.md) pokyny, jak nastavit zabezpečení clusteru Service Fabric.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurace připojení clusteru v publikační profily
Pokud publikujete projektu Service Fabric v sadě Visual Studio, použijte **publikovat aplikace Service Fabric** dialogové okno Vybrat clusteru služby Azure Service Fabric. V části **koncového bodu připojení**, vyberte existující cluster v rámci svého předplatného.

![** Publikování Service Fabric aplikace ** dialogové okno slouží ke konfiguraci připojení Service Fabric.][publishdialog]

**Publikovat aplikace Service Fabric** dialogové okno automaticky ověří připojení clusteru. Pokud budete vyzváni, přihlaste se k účtu Azure. V případě úspěšného ověření, znamená to, že má váš systém správné certifikáty pro připojení ke clusteru bezpečně nebo clusteru je nezabezpečené. Selhání ověření může být způsobeno problémy se síťovým nebo tak, že nejsou správně konfigurovány pro připojení ke clusteru s podporou zabezpečení systému.

![** Publikování Service Fabric aplikace ** dialogové okno ověří stávající správně nakonfigurované připojení clusteru Service Fabric.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Připojit k zabezpečení clusteru
1. Ujistěte se, že vám přístup mezi klientské certifikáty, které důvěřuje cílový cluster. Certifikát se obvykle sdílí jako soubor Personal Information Exchange (.pfx). V tématu [nastavení cluster Service Fabric na portálu Azure](service-fabric-cluster-creation-via-portal.md) pro konfiguraci serveru k udělení přístupu ke klientovi.
2. Nainstalujte důvěryhodný certifikát. Chcete-li to provést, poklikejte na soubor .pfx nebo pomocí skriptu prostředí PowerShell Import PfxCertificate pro import certifikátů. Nainstalujte certifikát, který chcete **Cert: \LocalMachine\My**. Je OK přijměte všechna výchozí nastavení při importu certifikátu.
3. Vyberte **publikování...**  příkaz v místní nabídce projektu otevřete **publikování aplikaci Azure** dialogové okno a potom vyberte cílový cluster. Nástroj automaticky vyřeší připojení a uloží parametry zabezpečené připojení v profilu publikování.
4. Volitelné: Můžete upravit profilu publikování k určení připojení zabezpečení clusteru.
   
   Vzhledem k tomu, že jste ruční úpravy souboru XML profilu publikování k určení informací o certifikátu, poznamenejte si název úložiště certifikátu, uložení, umístění a kryptografický otisk certifikátu. Budete muset zadat tyto hodnoty pro úložiště certifikátu, název a umístění úložiště. V tématu [postupy: načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) Další informace.
   
   Můžete použít *ClusterConnectionParameters* parametry zadat parametry prostředí PowerShell, které chcete použít při připojení ke clusteru Service Fabric. Platné parametry jsou všechny, které přijímají Connect-ServiceFabricCluster rutinou. V tématu [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) seznam dostupných parametrů.
   
   Pokud publikujete na vzdálený cluster, je třeba zadat příslušné parametry pro tento konkrétní cluster. Následuje příklad připojování ke clusteru nezabezpečené:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Tady je příklad pro připojení k x509 na základě certifikátu zabezpečeného clusteru:
   
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
5. Upravit další potřebné nastavení, například upgradu parametry a umístění souboru aplikace parametr a pak publikujte aplikaci z **publikovat aplikace Service Fabric** dialogové okno v sadě Visual Studio.

## <a name="next-steps"></a>Další postup
Další informace o přístupu k clusterů Service Fabric najdete v tématu [vizualizace vašeho clusteru pomocí Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
