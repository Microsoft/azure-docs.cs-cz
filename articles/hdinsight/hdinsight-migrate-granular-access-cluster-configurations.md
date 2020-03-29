---
title: Granulární přístup založený na rolích Konfigurace clusteru Azure HDInsight
description: Přečtěte si o změnách požadovaných v rámci migrace na podrobný přístup založený na rolích pro konfigurace clusteru HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: f1fdb9dffbe06430ea7e3eb9339e23f5239e4e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310828"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrace na granulární řízení přístupu na základě rolí pro konfigurace clusteru

Zavádíme některé důležité změny na podporu více jemně odstupňovaný přístup založený na rolích získat citlivé informace. V rámci těchto změn může být některé akce **vyžadovány do 3 září 2019,** pokud používáte některou z [ovlivněných entit nebo scénářů](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Co se mění?

Dříve bylo možné získat tajné klíče prostřednictvím rozhraní HDInsight API uživateli clusteru, kteří mají role `*/read` Vlastník, Přispěvatel nebo Reader [RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles), protože byly k dispozici všem uživatelům s oprávněním. Tajné klíče jsou definovány jako hodnoty, které by mohly být použity k získání větší hod, než by měla povolit role uživatele. Patří mezi ně hodnoty, jako jsou přihlašovací údaje http brány clusteru, klíče účtů úložiště a pověření databáze.

září 2019 bude přístup k těmto tajným `Microsoft.HDInsight/clusters/configurations/action` kódům vyžadovat oprávnění, což znamená, že již nemohou mít přístup k uživatelům s rolí Čtečka. Role, které mají toto oprávnění jsou přispěvatel, vlastník a nové hdinsight clusteru operátor role (více o tom níže).

Zavádíme také novou roli [operátora clusteru HDInsight,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) která bude moci načíst tajné klíče, aniž by jim byla udělena oprávnění správce přispěvatele nebo vlastníka. Shrnutí:

| Role                                  | Dříve                                                                                       | Do budoucna       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Čtenář                                | - Přístup ke čtení, včetně tajemství                                                                   | - Přístup ke čtení, **s výjimkou tajných** kódů |           |   |   |
| Operátor clusteru HDInsight<br>(Nová role) | Není dostupné.                                                                                              | - Přístup pro čtení/zápis, včetně tajných kódů         |   |   |
| Přispěvatel                           | - Přístup pro čtení/zápis, včetně tajných kódů<br>- Vytvořte a spravujte všechny typy prostředků Azure.     | Beze změny |
| Vlastník                                 | - Přístup pro čtení /zápis včetně tajných kódů<br>- Plný přístup ke všem zdrojům<br>- Delegovat přístup k ostatním | Beze změny |

Informace o tom, jak přidat přiřazení role operátora clusteru HDInsight uživateli, aby mu byl udělit přístup pro čtení a zápis k tajným kódům clusteru, naleznete v následující části [Přidání přiřazení role operátora clusteru HDInsight uživateli](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Týká se mě těchto změn?

Ovlivněny jsou následující entity a scénáře:

- [ROZHRANÍ API](#api): `/configurations` `/configurations/{configurationName}` Uživatelé používající koncové body nebo.
- [Nástroje Azure HDInsight pro kód Visual Studia verze](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1 nebo nižší.
- [Azure Toolkit pro IntelliJ](#azure-toolkit-for-intellij) verze 3.20.0 nebo nižší.
- [Nástroje Azure Data Lake a Stream Analytics pro Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) pod verzí 2.3.9000.1.
- [Azure Toolkit pro Eclipse](#azure-toolkit-for-eclipse) verze 3.15.0 nebo nižší.
- [SDK pro .NET](#sdk-for-net)
    - [verze 1.x nebo 2.x](#versions-1x-and-2x): `GetClusterConfigurations` `GetConnectivitySettings`Uživatelé používající metodu , , `ConfigureHttpSettings` `EnableHttp` nebo `DisableHttp` metody z třídy ConfigurationsOperationsExtensions.
    - [verze 3.x a vyšší](#versions-3x-and-up): `Get` `Update`Uživatelé používající , , `EnableHttp`nebo `DisableHttp` metody z `ConfigurationsOperationsExtensions` třídy.
- [SDK pro Python](#sdk-for-python): `get` `update` Uživatelé `ConfigurationsOperations` používající metody or z třídy.
- [SDK pro Javu](#sdk-for-java): Uživatelé používající `update` metody or `get` z `ConfigurationsInner` třídy.
- [SDK for Go](#sdk-for-go): `Get` `Update` Uživatelé `ConfigurationsClient` používající metody nebo z struktury.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) pod verzí 2.0.0.
Postup migrace pro váš scénář najdete v následujících částech (nebo použijte výše uvedené odkazy).

### <a name="api"></a>rozhraní API

Následující api budou změněna nebo zastaralá:

- [**GET /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (citlivé informace odstraněny)
    - Dříve používané k získání jednotlivých typů konfigurace (včetně tajných kódů).
    - září 2019 toto volání rozhraní API nyní vrátí jednotlivé typy konfigurace s vynecháním tajných kódů. Chcete-li získat všechny konfigurace, včetně tajných kódů, použijte nové volání POST /configurations. Chcete-li získat pouze nastavení brány, použijte nové volání POST /getGatewaySettings.
- [**GET /konfigurace**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (zastaralé)
    - Dříve používané k získání všech konfigurací (včetně tajných kódů)
    - září 2019 bude toto volání rozhraní API zastaralé a už nebude podporováno. Chcete-li získat všechny konfigurace do budoucna, použijte nové volání POST /configurations. Chcete-li získat konfigurace s vynechánými citlivými parametry, použijte volání GET /configurations/{configurationName}.
- [**POST /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (zastaralé)
    - Dříve se používá k aktualizaci přihlašovacích údajů brány.
    - září 2019 bude toto volání rozhraní API zastaralé a již nebude podporováno. Místo toho použijte nové nastavení POST /updateGatewaySettings.

Byla přidána následující náhradní prostředí API:</span>

- [**POST /konfigurace**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Toto rozhraní API slouží k získání všech konfigurací, včetně tajných kódů.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Toto rozhraní API slouží k získání nastavení brány.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Toto rozhraní API slouží k aktualizaci nastavení brány (uživatelské jméno a/nebo heslo).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Nástroje Azure HDInsight pro kód Visual Studia

Pokud používáte verzi 1.1.1 nebo nižší, aktualizujte na [nejnovější verzi nástroje Azure HDInsight tools for Visual Studio Code,](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) abyste předešli přerušení.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Pokud používáte verzi 3.20.0 nebo nižší, aktualizujte na [nejnovější verzi modulu plug-in IntelliJ Azure Toolkit for IntelliJ,](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) abyste předešli přerušení.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Nástroje Azure Data Lake a Stream Analytics pro Visual Studio

Aktualizujte na verzi 2.3.9000.1 nebo novější z [Nástrojů Azure Data Lake a Stream Analytics pro Visual Studio,](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) abyste předešli přerušení.  Nápovědu k aktualizaci najdete v naší dokumentaci [Aktualizace nástrojů data lake pro Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Pokud používáte verzi 3.15.0 nebo nižší, aktualizujte na [nejnovější verzi sady Azure Toolkit for Eclipse,](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) abyste předešli přerušením.

### <a name="sdk-for-net"></a>SDK pro .NET

#### <a name="versions-1x-and-2x"></a>Verze 1.x a 2.x

Aktualizujte na [verzi 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) sady HDInsight SDK pro rozhraní .NET. Minimální změny kódu může být vyžadováno, pokud používáte metodu ovlivněnou těmito změnami:

- `ClusterOperationsExtensions.GetClusterConfigurations`**již vrátí citlivé parametry,** jako jsou klíče úložiště (core-site) nebo http pověření (brána).
    - Chcete-li načíst všechny konfigurace, `ClusterOperationsExtensions.ListConfigurations` včetně citlivých parametrů, použijte do budoucna.  Všimněte si, že uživatelé s rolí "Reader" nebudou moci tuto metodu použít. To umožňuje podrobnou kontrolu nad tím, kteří uživatelé mají přístup k citlivým informacím pro cluster.
    - Chcete-li načíst pouze `ClusterOperationsExtensions.GetGatewaySettings`pověření brány HTTP, použijte .

- `ClusterOperationsExtensions.GetConnectivitySettings`je nyní zastaralá a byla `ClusterOperationsExtensions.GetGatewaySettings`nahrazena .

- `ClusterOperationsExtensions.ConfigureHttpSettings`je nyní zastaralá a byla `ClusterOperationsExtensions.UpdateGatewaySettings`nahrazena .

- `ConfigurationsOperationsExtensions.EnableHttp`a `DisableHttp` jsou nyní zastaralé. Protokol HTTP je nyní vždy povolen, takže tyto metody již nejsou potřeba.

#### <a name="versions-3x-and-up"></a>Verze 3.x a novější

Aktualizujte na [verzi 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) nebo novější sady HDInsight SDK pro rozhraní .NET. Minimální změny kódu může být vyžadováno, pokud používáte metodu ovlivněnou těmito změnami:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**již vrátí citlivé parametry,** jako jsou klíče úložiště (core-site) nebo http pověření (brána).
    - Chcete-li načíst všechny konfigurace, [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) včetně citlivých parametrů, použijte do budoucna.Všimněte si, že uživatelé s rolí "Reader" nebudou moci tuto metodu použít. To umožňuje podrobnou kontrolu nad tím, kteří uživatelé mají přístup k citlivým informacím pro cluster. 
    - Chcete-li načíst pouze [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)pověření brány HTTP, použijte . 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)je nyní zastaralá a byla [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)nahrazena . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)a [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) jsou nyní zastaralé. Protokol HTTP je nyní vždy povolen, takže tyto metody již nejsou potřeba.

### <a name="sdk-for-python"></a>SDK pro Python

Aktualizujte na [verzi 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) nebo novější sady HDInsight SDK pro Python. Minimální změny kódu může být vyžadováno, pokud používáte metodu ovlivněnou těmito změnami:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**již vrátí citlivé parametry,** jako jsou klíče úložiště (core-site) nebo http pověření (brána).
    - Chcete-li načíst všechny konfigurace, [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) včetně citlivých parametrů, použijte do budoucna.Všimněte si, že uživatelé s rolí "Reader" nebudou moci tuto metodu použít. To umožňuje podrobnou kontrolu nad tím, kteří uživatelé mají přístup k citlivým informacím pro cluster. 
    - Chcete-li načíst pouze [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)pověření brány HTTP, použijte .
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)je nyní zastaralá a byla [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)nahrazena .

### <a name="sdk-for-java"></a>Sada SDK pro jazyk Java

Aktualizujte na [verzi 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) nebo novější sady HDInsight SDK pro Jazyk Java. Minimální změny kódu může být vyžadováno, pokud používáte metodu ovlivněnou těmito změnami:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)**již vrátí citlivé parametry,** jako jsou klíče úložiště (core-site) nebo http pověření (brána).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)nyní zastaralá.

### <a name="sdk-for-go"></a>Sada SDK pro

Aktualizujte na [verzi 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) nebo novější sady HDInsight SDK for Go. Minimální změny kódu může být vyžadováno, pokud používáte metodu ovlivněnou těmito změnami:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**již vrátí citlivé parametry,** jako jsou klíče úložiště (core-site) nebo http pověření (brána).
    - Chcete-li načíst všechny konfigurace, [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) včetně citlivých parametrů, použijte do budoucna.Všimněte si, že uživatelé s rolí "Reader" nebudou moci tuto metodu použít. To umožňuje podrobnou kontrolu nad tím, kteří uživatelé mají přístup k citlivým informacím pro cluster. 
    - Chcete-li načíst pouze [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)pověření brány HTTP, použijte .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)je nyní zastaralá a byla [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)nahrazena .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Aktualizujte na [Az PowerShell verze 2.0.0](https://www.powershellgallery.com/packages/Az) nebo novější, abyste předešli přerušení.  Minimální změny kódu může být vyžadováno, pokud používáte metodu ovlivněny těmito změnami.
- `Grant-AzHDInsightHttpServicesAccess`je nyní zastaralé a byl nahrazen novou `Set-AzHDInsightGatewayCredential` rutinou.
- `Get-AzHDInsightJobOutput`byl aktualizován, aby podporoval přístup k klíči úložiště na základě granulární role.
    - Uživatelů s rolemi Vlastník, Přispěvatel a Operátor clusteru HDInsight se tato změna nedotkne.
    - Uživatelé s pouze roli Čtečka `DefaultStorageAccountKey` bude muset zadat parametr explicitně.
- `Revoke-AzHDInsightHttpServicesAccess`nyní zastaralá. Http je nyní vždy povolena, takže tato rutina již není potřeba.
 Podívejte se na [az. HdInsight migrace průvodce](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) pro více informací.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Přidání přiřazení role operátora clusteru HDInsight uživateli

Uživatel s rolí [Vlastník](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) může přiřadit roli [operátora clusteru HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) uživatelům, kterým byste chtěli mít přístup pro čtení a zápis k citlivým hodnotám konfigurace clusteru HDInsight (například pověření brány clusteru a klíče účtu úložiště).

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Nejjednodušší způsob, jak přidat toto přiřazení `az role assignment create` role je pomocí příkazu v Azure CLI.

> [!NOTE]
> Tento příkaz musí být spuštěn uživatelem s rolí Vlastník, protože pouze oni mohou udělit tato oprávnění. Jedná `--assignee` se o název instančního objektu nebo e-mailovou adresu uživatele, kterému chcete přiřadit roli operátora clusteru HDInsight. Pokud se zobrazí chyba nedostatečných oprávnění, přečtěte si níže uvedené nejčastější dotazy.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Grantová role na úrovni prostředků (clusteru)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Role grantu na úrovni skupiny prostředků

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Grantová role na úrovni předplatného

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Můžete také použít portál Azure k přidání přiřazení role operátora clusteru HDInsight uživateli. Podívejte se na [dokumentaci, Správa přístupu k prostředkům Azure pomocí RBAC a portálazure – přidání přiřazení role](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>Nejčastější dotazy

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Proč se po aktualizaci požadavků rozhraní API nebo nástroje zobrazuje odpověď 403 (Zakázáno)?

Konfigurace clusteru jsou nyní za podrobným řízením `Microsoft.HDInsight/clusters/configurations/*` přístupu založeným na rolích a vyžadují oprávnění k přístupu k nim. Chcete-li získat toto oprávnění, přiřaďte roli operátora clusteru HDInsight, přispěvatele nebo vlastníka uživateli nebo instančnímu objektu služby, který se pokouší o přístup ke konfiguracím.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Proč se při spuštění příkazu Azure CLI ke přiřazení role operátora clusteru HDInsight jinému uživateli nebo instančnímu objektu zobrazí "Nedostatečná oprávnění k dokončení operace"?

Kromě role vlastníka musí mít uživatel nebo instanční objekt provádějící příkaz dostatečná oprávnění AAD, aby mohl vyhledat ID objektu postupníka. Tato zpráva označuje nedostatečná oprávnění add. Zkuste nahradit `-–assignee` argument `–assignee-object-id` a zadejte ID objektu postupníka jako parametr namísto názvu (nebo ID jistiny v případě spravované identity). Další informace naleznete v části volitelné parametry [přiřazení role az vytvořit dokumentaci.](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)

Pokud to stále nefunguje, obraťte se na správce AAD získat správná oprávnění.

### <a name="what-will-happen-if-i-take-no-action"></a>Co se stane, když nepodniknu žádnou akci?

září 2019 `GET /configurations` a `POST /configurations/gateway` volání již nevrátí žádné informace a `GET /configurations/{configurationName}` volání již nebude vracet citlivé parametry, jako jsou klíče účtu úložiště nebo heslo clusteru. Totéž platí pro odpovídající metody sady SDK a rutiny prostředí PowerShell.

Pokud používáte starší verzi jednoho z nástrojů pro Visual Studio, VSCode, IntelliJ nebo Eclipse uvedené výše, budou již fungovat, dokud aktualizovat.

Podrobnější informace naleznete v odpovídající části tohoto dokumentu pro váš scénář.
