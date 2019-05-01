---
title: Migrace na granulární přístup na základě rolí pro konfigurace clusteru – Azure HDInsight
description: Další informace o změnách vyžaduje jako součást migrace granulární přístup na základě rolí pro konfigurace clusterů HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/26/2019
ms.openlocfilehash: 8bcb20ec5c85c3cfa2e481a4a5848f404a2fb4eb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685466"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrace na granulární řízení přístupu na základě rolí pro konfigurace clusteru

Představujeme některé důležité změny pro podporu více velice přesně kontrolovat přístup na základě rolí k získání citlivé informace. Jako část z nich změní některé **akce mohou být požadovány** Pokud používáte některou [vliv na entity a scénáře](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Co se mění?

Dříve, může získat tajných kódů přes rozhraní API HDInsight uživatelé clusteru má vlastník, Přispěvatel nebo Čtenář [role RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles).
Od této chvíle, těchto tajných kódů nebude přístupný pro uživatele k roli Čtenář. Tajné kódy, definovaná podle hodnoty, které by bylo možné získat přístup více se zvýšeným oprávněním než role uživatelů musí povolit. Patří mezi ně hodnoty, jako je například přihlašovací údaje clusteru brány protokolu HTTP, klíče účtu úložiště a přihlašovací údaje databáze.

Představujeme nový [operátor clusteru Hdinsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) role, která bude moct načíst tajné kódy bez udělení oprávnění správce, Přispěvatel nebo vlastník. Shrnutí:

| Role                                  | Dříve                                                                                       | Do budoucna       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Čtenář                                | -Přístup pro čtení, včetně tajných klíčů                                                                   | -Přístup pro čtení, **s výjimkou** tajných kódů |           |   |   |
| HDInsight Cluster – operátor<br>(Nová Role) | neuvedeno                                                                                              | – Čtení a zápis přístup, včetně tajných klíčů         |   |   |
| Přispěvatel                           | – Čtení a zápis přístup, včetně tajných klíčů<br>– Vytváření a Správa všech typů prostředků Azure.     | Beze změny |
| Vlastník                                 | -R/w přístup, včetně tajných klíčů<br>-Úplný přístup ke všem prostředkům<br>-Delegovat přístup ostatním uživatelům | Beze změny |

Informace o tom, jak přidat přiřazení role operátor clusteru HDInsight na uživatele a udělit jim přístup pro čtení/zápis k tajným kódům clusteru najdete v tématu níže uvedený oddíl, [přidat přiřazení role operátor clusteru HDInsight s uživatelem](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Jsem těmito změnami ovlivněny?

Jsou ovlivněny následující entity a scénáře:

- [ROZHRANÍ API](#api): Uživatele, kteří používají `/configurations` nebo `/configurations/{configurationName}` koncových bodů.
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) verze 1.1.1 nebo níže.
- [Sada Azure Toolkit pro IntelliJ](#azure-toolkit-for-intellij) verze 3.20.0 nebo níže.
- [Sada SDK pro .NET](#sdk-for-net)
    - [verze 1.x a 2.x](#versions-1x-and-2x): Uživatele, kteří používají `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` nebo `DisableHttp` metody ze třídy ConfigurationsOperationsExtensions.
    - [verze 3.x a až](#versions-3x-and-up): Uživatele, kteří používají `Get`, `Update`, `EnableHttp`, nebo `DisableHttp` metody ze `ConfigurationsOperationsExtensions` třídy.
- [Sada SDK pro Python](#sdk-for-python): Uživatele, kteří používají `get` nebo `update` metody ze `ConfigurationsOperations` třídy.
- [Sada SDK pro Javu](#sdk-for-java): Uživatele, kteří používají `update` nebo `get` metody ze `ConfigurationsInner` třídy.
- [Sada SDK for Go](#sdk-for-go): Uživatele, kteří používají `Get` nebo `Update` metody ze `ConfigurationsClient` struktury.

Kroky při migraci pro váš scénář najdete v následujících částech (nebo použijte odkazy uvedené výš).

### <a name="api"></a>Rozhraní API

Následující rozhraní API se změnil nebo zastaralé:

- [**/Configurations/ GET {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (odebrání citlivých informací)
    - Dříve se používá k získání individuální konfigurace typy (včetně tajné kódy).
    - Toto volání rozhraní API nyní vrátí typy individuální konfigurace s tajnými kódy vynechán. Pokud chcete získat všechny konfigurace, včetně tajných klíčů, použijte /configurations volání POST tak nové. Pokud chcete získat jenom nastavení brány, použijte nové /getGatewaySettings volání POST.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (zastaralé)
    - Dříve se používá k získání všech konfigurací (včetně tajné kódy)
    - Toto volání rozhraní API se už nebude podporovat. Pokud chcete získat všechny konfigurace do budoucna, použijte /configurations volání POST tak nové. K získání konfigurace s citlivé parametry tento parametr vynechán, použijte volání GET /configurations/ {configurationName}.
- [**PŘÍSPĚVEK /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (zastaralé)
    - Dříve se používá k aktualizaci přihlašovacích údajů brány.
    - Toto volání rozhraní API bude zastaralé a už není podporovaná. Místo toho použijte nové /updateGatewaySettings příspěvku.

Následující nahrazení, který jste přidali rozhraní API:</span>

- [**/Configurations příspěvku**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Pomocí tohoto rozhraní API k získání všechny konfigurace, včetně tajných klíčů.
- [**/GetGatewaySettings příspěvku**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Pomocí tohoto rozhraní API k získání nastavení brány.
- [**/UpdateGatewaySettings příspěvku**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Pomocí tohoto rozhraní API se aktualizovat nastavení brány (uživatelské jméno nebo heslo).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

Pokud používáte verze 1.1.1 nebo nižší, aktualizovat [nejnovější verzi Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) vyhnout se přerušením.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit pro IntelliJ

Pokud používáte verzi 3.20.0 nebo nižší, aktualizovat [nejnovější verzi sady Azure Toolkit pro IntelliJ plugin](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) vyhnout se přerušením.

### <a name="sdk-for-net"></a>SDK pro .NET

#### <a name="versions-1x-and-2x"></a>Verze 1.x a 2.x

Aktualizace na [verze 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) sady HDInsight SDK pro .NET. Změny jen minimum kódu mohou být vyžadovány, pokud používáte metodu těmito změnami ovlivněny:

- `ClusterOperationsExtensions.GetClusterConfigurations` bude **už nevracel citlivé parametry** , jako je úložiště klíčů (základní web) nebo přihlašovací údaje protokolu HTTP (brány).
    - Pokud chcete načíst všechny konfigurace, včetně citlivé parametrů, použijte `ClusterOperationsExtensions.ListConfigurations` do budoucna.  Všimněte si, že uživatelé s rolí 'Čtečky' nebude možné použít tuto metodu. Díky tomu umožňuje zajistit detailní kontrolu nad tím, které mohou uživatelé citlivých informací pro cluster.
    - Chcete-li načíst jenom přihlašovací údaje brány protokolu HTTP, použijte `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` je nyní zastaralá a nahradila ji `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` je nyní zastaralá a nahradila ji `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` a `DisableHttp` jsou nyní zastaralé. Je povolený protokol HTTP teď vždy, takže tyto metody jsou už je nepotřebujete.

#### <a name="versions-3x-and-up"></a>Verze 3.x a vyšší

Aktualizace na [verze 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) nebo novější sady HDInsight SDK pro .NET. Změny jen minimum kódu mohou být vyžadovány, pokud používáte metodu těmito změnami ovlivněny:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) bude **už nevracel citlivé parametry** , jako je úložiště klíčů (základní web) nebo přihlašovací údaje protokolu HTTP (brány).
    - Pokud chcete načíst všechny konfigurace, včetně citlivé parametrů, použijte [ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) do budoucna.  Všimněte si, že uživatelé s rolí 'Čtečky' nebude možné použít tuto metodu. Díky tomu umožňuje zajistit detailní kontrolu nad tím, které mohou uživatelé citlivých informací pro cluster. 
    - Chcete-li načíst jenom přihlašovací údaje brány protokolu HTTP, použijte [ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) je nyní zastaralá a nahradila ji [ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) a [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) jsou nyní zastaralé. Je povolený protokol HTTP teď vždy, takže tyto metody jsou už je nepotřebujete.

### <a name="sdk-for-python"></a>SDK pro Python

Aktualizace na [verze 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) nebo novější sady HDInsight SDK pro Python. Změny jen minimum kódu mohou být vyžadovány, pokud používáte metodu těmito změnami ovlivněny:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) bude **už nevracel citlivé parametry** , jako je úložiště klíčů (základní web) nebo přihlašovací údaje protokolu HTTP (brány).
    - Pokud chcete načíst všechny konfigurace, včetně citlivé parametrů, použijte [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) do budoucna.  Všimněte si, že uživatelé s rolí 'Čtečky' nebude možné použít tuto metodu. Díky tomu umožňuje zajistit detailní kontrolu nad tím, které mohou uživatelé citlivých informací pro cluster. 
    - Chcete-li načíst jenom přihlašovací údaje brány protokolu HTTP, použijte [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) je nyní zastaralá a nahradila ji [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>Sada SDK pro Javu

Aktualizace na [verze 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) nebo novější sady HDInsight SDK pro Javu. Změny jen minimum kódu mohou být vyžadovány, pokud používáte metodu těmito změnami ovlivněny:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) bude **už nevracel citlivé parametry** , jako je úložiště klíčů (základní web) nebo přihlašovací údaje protokolu HTTP (brány).
    - Pokud chcete načíst všechny konfigurace, včetně citlivé parametrů, použijte [ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) do budoucna.  Všimněte si, že uživatelé s rolí 'Čtečky' nebude možné použít tuto metodu. Díky tomu umožňuje zajistit detailní kontrolu nad tím, které mohou uživatelé citlivých informací pro cluster. 
    - Chcete-li načíst jenom přihlašovací údaje brány protokolu HTTP, použijte [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) je nyní zastaralá a nahradila ji [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>Sada SDK For Go

Aktualizace na [verze 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) nebo novější sady HDInsight SDK for Go. Změny jen minimum kódu mohou být vyžadovány, pokud používáte metodu těmito změnami ovlivněny:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) bude **už nevracel citlivé parametry** , jako je úložiště klíčů (základní web) nebo přihlašovací údaje protokolu HTTP (brány).
    - Pokud chcete načíst všechny konfigurace, včetně citlivé parametrů, použijte [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) do budoucna.  Všimněte si, že uživatelé s rolí 'Čtečky' nebude možné použít tuto metodu. Díky tomu umožňuje zajistit detailní kontrolu nad tím, které mohou uživatelé citlivých informací pro cluster. 
    - Chcete-li načíst jenom přihlašovací údaje brány protokolu HTTP, použijte [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) je nyní zastaralá a nahradila ji [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Přidání přiřazení role operátor clusteru HDInsight na uživatele

Uživatel s [Přispěvatel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) nebo [vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) můžete přiřadit role [operátor clusteru Hdinsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) role pro uživatele, které byste měli mít přístup pro čtení a zápis k citlivé Hodnoty konfigurace clusteru HDInsight (například přihlašovací údaje clusteru brány a klíče účtu úložiště).

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Nejjednodušší způsob, jak přidat tato přiřazení role je použít `az role assignemnt create` příkaz v rozhraní příkazového řádku Azure.

> [!NOTE]
> Tento příkaz musí spustit uživatel s rolí Přispěvatel nebo vlastník jako pouze se může udělit tato oprávnění. `--assignee` Je e-mailovou adresu uživatele, kterým chcete přiřadit roli operátora clusteru HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Udělení role na úrovni prostředků (cluster)

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

Na webu Azure portal můžete také použít k přidání přiřazení role operátor clusteru HDInsight s uživatelem. Naleznete v dokumentaci k [spravovat přístup k prostředkům Azure pomocí RBAC a webu Azure portal – přidání přiřazení role](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
