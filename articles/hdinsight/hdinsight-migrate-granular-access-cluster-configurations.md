---
title: Migrace na podrobný přístup založený na rolích pro konfigurace clusteru – Azure HDInsight
description: Přečtěte si o změnách vyžadovaných v rámci migrace až na podrobný přístup založený na rolích pro konfigurace clusteru HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ebb1723a9a2b2d069a1766d4f78151f2b684c5b9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464662"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrace na granulární řízení přístupu na základě rolí pro konfigurace clusteru

Zavádíme některé důležité změny pro podporu přístupu k citlivým informacím na základě přesnější role. V rámci těchto změn **může být nutné provést určitou akci** , pokud používáte některou z [ovlivněných entit nebo scénářů](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Co se mění?

Dříve mohli tajné klíče získat prostřednictvím rozhraní API HDInsight uživatelů clusteru, kteří mají [role RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)vlastník, přispěvatel nebo čtenář, protože byly k dispozici pro kohokoli s `*/read` oprávněním.
Když budete pokračovat, bude přístup k těmto tajným klíčům vyžadovat `Microsoft.HDInsight/clusters/configurations/*` oprávnění, což znamená, že je uživatelé s rolí čtenář už nebudou mít přístup. Tajné kódy se definují jako hodnoty, které by se daly použít k získání vyšší úrovně přístupu, než jakou má uživatel role. Mezi ně patří hodnoty jako například přihlašovací údaje protokolu HTTP brány clusteru, klíče účtu úložiště a přihlašovací údaje databáze.

Zavádíme taky novou roli [operátora clusteru HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) , která bude moct načíst tajné kódy bez udělení oprávnění pro správu přispěvatele nebo vlastníka. Sumarizace:

| Role                                  | Měsíci                                                                                       | Po přechodu dál       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Čtenář                                | – Přístup pro čtení, včetně tajných klíčů                                                                   | – Přístup pro čtení, **s výjimkou** tajných klíčů |           |   |   |
| Operátor clusteru HDInsight<br>(Nová role) | Není k dispozici                                                                                              | – Přístup pro čtení a zápis, včetně tajných klíčů         |   |   |
| Přispěvatel                           | – Přístup pro čtení a zápis, včetně tajných klíčů<br>– Vytvářejte a spravujte všechny typy prostředků Azure.     | Žádné změny |
| Owner                                 | – Přístup pro čtení a zápis včetně tajných klíčů<br>– Úplný přístup ke všem prostředkům<br>– Delegovat přístup jiným uživatelům | Žádné změny |

Informace o tom, jak přidat přiřazení role operátora clusteru HDInsight uživateli, aby jim udělil přístup pro čtení a zápis k tajným klíčům clusteru, najdete v níže uvedené části [Přidání přiřazení role operátora clusteru HDInsight uživateli](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Ovlivněné tyto změny?

Ovlivněny jsou následující entity a scénáře:

- [ROZHRANÍ API](#api): Uživatelé používají `/configurations` koncové body `/configurations/{configurationName}` nebo.
- [Nástroje Azure HDInsight pro Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) verze 1.1.1 nebo nižší.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) verze 3.20.0 nebo nižší.
- [Azure Data Lake a Stream Analytics Tools for Visual Studio, které](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) následují 2.3.9000.1 verze.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) verze 3.15.0 nebo nižší.
- [Sada SDK pro .NET](#sdk-for-net)
    - [verze 1. x nebo 2. x](#versions-1x-and-2x): Uživatelé `GetClusterConfigurations`používají metody, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` nebo ztřídyConfigurationsOperationsExtensions.`DisableHttp`
    - [verze 3. x a vyšší](#versions-3x-and-up): `Get`Uživatelé používají metody, `Update`, `EnableHttp`, nebo `DisableHttp` z `ConfigurationsOperationsExtensions` třídy.
- [Sada SDK pro Python](#sdk-for-python): Uživatelé používají `get` metody nebo `update` z `ConfigurationsOperations` třídy.
- [Sada SDK pro jazyk Java](#sdk-for-java): Uživatelé používají `update` metody nebo `get` z `ConfigurationsInner` třídy.
- [Sada SDK pro přejít](#sdk-for-go): Uživatelé používají `Get` metody nebo `Update` z `ConfigurationsClient` struktury.
- [AZ. HDInsight PowerShell](#azhdinsight-powershell) pod 2.0.0 verze
Kroky při migraci pro váš scénář najdete v následujících částech (nebo použijte odkazy uvedené výš).

### <a name="api"></a>rozhraní API

Následující rozhraní API se změní nebo zastaralé:

- [**Získat/Configurations/{ConfigurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (odstraněné citlivé informace)
    - Dřív se použila k získání individuálních typů konfigurace (včetně tajných klíčů).
    - Toto volání rozhraní API nyní vrátí jednotlivé typy konfigurace s tajnými klíči vynechány. Chcete-li získat všechny konfigurace, včetně tajných klíčů, použijte nové volání POST/configurations. Chcete-li získat pouze nastavení brány, použijte nové volání POST/getGatewaySettings.
- [**Získat/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) zastaralé
    - Dřív se použilo k získání všech konfigurací (včetně tajných klíčů).
    - Toto volání rozhraní API již nebude podporováno. Chcete-li získat všechny konfigurace před tím, použijte nové volání POST/configurations. Chcete-li získat konfigurace s citlivými parametry, použijte volání metody GET/configurations/{configurationName}.
- [**Příspěvek/Configurations/{ConfigurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) zastaralé
    - Dřív se použila k aktualizaci přihlašovacích údajů brány.
    - Toto volání rozhraní API bude zastaralé a už se nepodporuje. Místo toho použijte nový příspěvek/updateGatewaySettings.

Byla přidána následující náhradní rozhraní API:</span>

- [**PŘÍSPĚVEK/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Pomocí tohoto rozhraní API můžete získat všechny konfigurace, včetně tajných klíčů.
- [**PŘÍSPĚVEK/getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Pomocí tohoto rozhraní API můžete získat nastavení brány.
- [**PŘÍSPĚVEK/updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Pomocí tohoto rozhraní API můžete aktualizovat nastavení brány (uživatelské jméno a/nebo heslo).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Nástroje Azure HDInsight pro Visual Studio Code

Pokud používáte verzi 1.1.1 nebo nižší, aktualizujte na [nejnovější verzi nástrojů Azure HDInsight pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) , abyste se vyhnuli přerušení.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit pro IntelliJ

Pokud používáte verzi 3.20.0 nebo nižší, aktualizujte na [nejnovější verzi modulu plug-in Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) , abyste se vyhnuli přerušení.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Nástroje pro Azure Data Lake a Stream Analytics pro Visual Studio

Aktualizujte na verzi 2.3.9000.1 nebo novější z [Azure Data Lake a Stream Analyticsch nástrojů pro Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) , abyste se vyhnuli přerušení.  Nápovědu k aktualizaci najdete v naší dokumentaci [Data Lake nástroje pro aktualizaci sady Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit pro Eclipse

Pokud používáte verzi 3.15.0 nebo nižší, aktualizujte na [nejnovější verzi Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) , abyste se vyhnuli přerušení.

### <a name="sdk-for-net"></a>SDK pro .NET

#### <a name="versions-1x-and-2x"></a>Verze 1. x a 2. x

Aktualizace [verze 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) sady HDInsight SDK pro .NET Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu:

- `ClusterOperationsExtensions.GetClusterConfigurations`**už nebude vracet citlivé parametry** , jako jsou klíče úložiště (Core-site) nebo přihlašovací údaje http (brána).
    - Pokud chcete načíst všechny konfigurace, včetně citlivých parametrů `ClusterOperationsExtensions.ListConfigurations` , použijte příkaz pokračovat dál.  Všimněte si, že uživatelé s rolí čtenář nebudou moct tuto metodu použít. To umožňuje detailní kontrolu nad tím, kteří uživatelé budou mít přístup k citlivým informacím v clusteru.
    - Pokud chcete načíst jenom přihlašovací údaje brány HTTP `ClusterOperationsExtensions.GetGatewaySettings`, použijte.

- `ClusterOperationsExtensions.GetConnectivitySettings`je nyní zastaralý a byl nahrazen `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings`je nyní zastaralý a byl nahrazen `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp`a `DisableHttp` jsou nyní zastaralé. Protokol HTTP je nyní vždy povolen, takže tyto metody již nejsou potřeba.

#### <a name="versions-3x-and-up"></a>Verze 3. x a vyšší

Aktualizujte na [verzi 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) nebo novější v sadě HDInsight SDK pro .NET. Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**už nebude vracet citlivé parametry** , jako jsou klíče úložiště (Core-site) nebo přihlašovací údaje http (brána).
    - Pokud chcete načíst všechny konfigurace, včetně citlivých parametrů [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) , použijte příkaz pokračovat dál.  Všimněte si, že uživatelé s rolí čtenář nebudou moct tuto metodu použít. To umožňuje detailní kontrolu nad tím, kteří uživatelé budou mít přístup k citlivým informacím v clusteru. 
    - Pokud chcete načíst jenom přihlašovací údaje brány HTTP [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet), použijte. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)je nyní zastaralý a byl nahrazen [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)a [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) jsou nyní zastaralé. Protokol HTTP je nyní vždy povolen, takže tyto metody již nejsou potřeba.

### <a name="sdk-for-python"></a>SDK pro Python

Aktualizujte na [verzi 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) nebo novější v sadě HDInsight SDK pro Python. Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**už nebude vracet citlivé parametry** , jako jsou klíče úložiště (Core-site) nebo přihlašovací údaje http (brána).
    - Pokud chcete načíst všechny konfigurace, včetně citlivých parametrů [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) , použijte příkaz pokračovat dál.  Všimněte si, že uživatelé s rolí čtenář nebudou moct tuto metodu použít. To umožňuje detailní kontrolu nad tím, kteří uživatelé budou mít přístup k citlivým informacím v clusteru. 
    - Pokud chcete načíst jenom přihlašovací údaje brány HTTP [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-), použijte.
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)je nyní zastaralý a byl nahrazen [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>Sada SDK pro jazyk Java

Aktualizujte na [verzi 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) nebo novější v sadě HDInsight SDK pro jazyk Java. Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)**už nebude vracet citlivé parametry** , jako jsou klíče úložiště (Core-site) nebo přihlašovací údaje http (brána).
    - Pokud chcete načíst všechny konfigurace, včetně citlivých parametrů [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) , použijte příkaz pokračovat dál.  Všimněte si, že uživatelé s rolí čtenář nebudou moct tuto metodu použít. To umožňuje detailní kontrolu nad tím, kteří uživatelé budou mít přístup k citlivým informacím v clusteru. 
    - Pokud chcete načíst jenom přihlašovací údaje brány HTTP [`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable), použijte.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)je nyní zastaralý a byl nahrazen [`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>Sada SDK pro přejít

Aktualizujte na [verzi 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) nebo novější v sadě HDInsight SDK pro přejít. Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**už nebude vracet citlivé parametry** , jako jsou klíče úložiště (Core-site) nebo přihlašovací údaje http (brána).
    - Pokud chcete načíst všechny konfigurace, včetně citlivých parametrů [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) , použijte příkaz pokračovat dál.  Všimněte si, že uživatelé s rolí čtenář nebudou moct tuto metodu použít. To umožňuje detailní kontrolu nad tím, kteří uživatelé budou mít přístup k citlivým informacím v clusteru. 
    - Pokud chcete načíst jenom přihlašovací údaje brány HTTP [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings), použijte.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)je nyní zastaralý a byl nahrazen [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>AZ. HDInsight PowerShell
Aktualizujte [rutinu AZ PowerShell verze 2.0.0](https://www.powershellgallery.com/packages/Az) nebo novější, abyste se vyhnuli přerušení.  Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu.
- `Grant-AzHDInsightHttpServicesAccess`je nyní zastaralá a byla nahrazena novou `Set-AzHDInsightGatewayCredential` rutinou.
- `Get-AzHDInsightJobOutput`byla aktualizována pro podporu podrobného přístupu založeného na rolích k klíči úložiště.
    - Uživatelů s rolemi Vlastník, Přispěvatel a Operátor clusteru HDInsight se tato změna nedotkne.
    - Jenom uživatelé, kteří mají roli Čtenář, budou muset `DefaultStorageAccountKey` parametr explicitně zadat.
- `Revoke-AzHDInsightHttpServicesAccess`je nyní zastaralé. Protokol HTTP je teď vždycky povolený, takže tato rutina už není potřeba.
 Viz [AZ. ](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)Další podrobnosti najdete v příručce k migraci HDInsight.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Přidání přiřazení role operátora clusteru HDInsight uživateli

Uživatel s rolí [Přispěvatel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) nebo [vlastník](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) může přiřadit roli operátora [clusteru HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) uživatelům, kteří by měli mít přístup pro čtení a zápis k citlivým hodnotám konfigurace clusteru HDInsight (například přihlašovací údaje brány clusteru. a klíče účtu úložiště).

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Nejjednodušší způsob, jak přidat toto přiřazení role, je použití `az role assignment create` příkazu v Azure CLI.

> [!NOTE]
> Tento příkaz musí spustit uživatel s rolemi Přispěvatel nebo Owner, protože jim můžou udělit tato oprávnění. `--assignee` Je e-mailová adresa uživatele, kterému chcete přiřadit roli operátora clusteru HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Udělení role na úrovni prostředku (clusteru)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Udělení role na úrovni skupiny prostředků

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Udělení role na úrovni předplatného

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Alternativně můžete použít Azure Portal k přidání přiřazení role operátora clusteru HDInsight uživateli. Projděte si dokumentaci, [spravujte přístup k prostředkům Azure pomocí RBAC a Azure Portal – přidejte přiřazení role](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
