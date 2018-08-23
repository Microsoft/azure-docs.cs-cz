---
title: Konfigurace zabezpečených připojení clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat zabezpečená připojení, které jsou podporovány v clusteru Azure Service Fabric pomocí sady Visual Studio.
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
ms.openlocfilehash: 8d76a2144234591792359ed8dd4a0779e6a2fc5c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "42054822"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurace zabezpečených připojení ke clusteru Service Fabric v sadě Visual Studio
Další informace o použití sady Visual Studio zabezpečený přístup k clusteru Azure Service Fabric pomocí zásad řízení přístupu nakonfigurované.

## <a name="cluster-connection-types"></a>Typy připojení clusteru
Cluster Azure Service Fabric podporuje dva typy připojení: **nezabezpečené** připojení a **x509 certifikátů** zabezpečená připojení. (Pro clustery Service Fabric hostované v místním **Windows** a **dSTS** ověření jsou také podporovány.) Budete muset nakonfigurovat typ připojení clusteru při vytvoření clusteru. Po vytvoření ho nelze změnit typ připojení.

Nástroje Visual Studio Service Fabric podporují všechny typy ověřování pro připojení ke clusteru pro publikování. Zobrazit [nastavení clusteru Service Fabric z portálu Azure portal](service-fabric-cluster-creation-via-portal.md) pokyny o tom, jak nastavit zabezpečený cluster Service Fabric.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurace připojení clusteru v publikační profily
Pokud publikujete projekt Service Fabric v sadě Visual Studio, použijte **publikovat aplikaci Service Fabric** dialogové okno Zvolit cluster Azure Service Fabric. V části **koncový bod připojení**, vyberte existující cluster v rámci vašeho předplatného.

![** Publikovat Service Fabric aplikace ** dialogové okno se používá ke konfiguraci připojení k Service Fabric.][publishdialog]

**Publikovat aplikaci Service Fabric** dialogové okno automaticky ověří připojení clusteru. Pokud se zobrazí výzva, přihlaste se ke svému účtu Azure. V případě úspěšného ověření, znamená to, že má systém správné certifikátů nainstalovaných se navázat zabezpečené připojení ke clusteru, nebo je váš cluster není zabezpečený. Chyb při ověřování může být způsobeno problémy se sítí nebo tím, že váš systém správně nakonfigurovaný pro připojení k zabezpečenému clusteru.

![** Publikovat Service Fabric aplikace ** dialogové okno ověří existující, správně nakonfigurovaná připojení clusteru Service Fabric.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Pro připojení k zabezpečenému clusteru
1. Ujistěte se, že umožňuje přístup k jedné klientské certifikáty, které důvěřuje cílový cluster. Certifikát se obvykle sdílí jako soubor Personal Information Exchange (.pfx). Zobrazit [nastavení clusteru Service Fabric z portálu Azure portal](service-fabric-cluster-creation-via-portal.md) pro postup konfigurace serveru pro udělení přístupu ke klientovi.
2. Nainstalujte tento důvěryhodný certifikát. K tomu, poklikejte na soubor .pfx, nebo pomocí skriptu prostředí PowerShell Import PfxCertificate pro import certifikátů. Nainstalujte certifikát do **Cert: \LocalMachine\My**. Je možné přijměte všechna výchozí nastavení při importu certifikátu.
3. Zvolte **publikování...**  příkazu v místní nabídce projektu otevřete **publikování aplikaci Azure** dialogové okno a potom vyberte cílový cluster. Nástroj automaticky vyřeší, připojení a uloží parametry zabezpečených připojení v profilu publikování.
4. Volitelné: Můžete upravit profilu publikování k určení připojení k zabezpečenému clusteru.
   
   Protože jste ruční úpravy souboru XML profilu publikování chcete zadat informace o certifikátu, nezapomeňte si poznamenat název úložiště certifikátu, uložení umístění a kryptografický otisk certifikátu. Budete muset zadat tyto hodnoty pro úložiště certifikátu název a umístění úložiště. Zobrazit [postupy: načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) Další informace.
   
   Můžete použít *ClusterConnectionParameters* parametry se mají nastavit parametry PowerShell, který mají používat při připojování ke clusteru Service Fabric. Platné parametry jsou všechny, které akceptuje rutina Connect-ServiceFabricCluster. Zobrazit [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) seznam dostupných parametrů.
   
   Pokud publikujete do vzdáleného clusteru, musíte zadat příslušné parametry pro tento konkrétní cluster. Následuje příklad připojení ke clusteru nezabezpečené:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Tady je příklad pro připojení k x x509 zabezpečený cluster na základě certifikátů:
   
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
5. Upravit další nastavení nezbytná, jako jsou parametry upgradu a umístění souboru parametrů aplikace a potom publikovat aplikaci **publikovat aplikaci Service Fabric** dialogové okno v sadě Visual Studio.

## <a name="next-steps"></a>Další postup
Další informace o přístupu k clustery Service Fabric najdete v tématu [vizualizace vašeho clusteru pomocí Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
