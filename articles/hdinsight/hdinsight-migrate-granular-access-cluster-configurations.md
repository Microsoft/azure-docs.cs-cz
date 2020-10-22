---
title: Podrobné přístupy ke konfiguraci clusteru Azure HDInsight na základě rolí
description: Přečtěte si o změnách vyžadovaných v rámci migrace až na podrobný přístup založený na rolích pro konfigurace clusteru HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 23811f379f8738e3fe9f162e23627d0c3c457621
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367495"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrace na granulární řízení přístupu na základě rolí pro konfigurace clusteru

Zavádíme některé důležité změny pro podporu přístupu k citlivým informacím na základě přesnější role. V rámci těchto změn mohou být některé akce vyžadovány **3. září 2019,** Pokud používáte některou z [ovlivněných entit a scénářů](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Co se mění?

Dříve mohli tajné klíče získat prostřednictvím rozhraní API HDInsight uživatelů clusteru, kteří mají [role Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)vlastník, přispěvatel nebo čtenář, protože byly k dispozici pro kohokoli s `*/read` oprávněním. Tajné kódy se definují jako hodnoty, které by se daly použít k získání vyšší úrovně přístupu, než jakou má uživatel role. Mezi ně patří hodnoty jako například přihlašovací údaje protokolu HTTP brány clusteru, klíče účtu úložiště a přihlašovací údaje databáze.

Od 3. září 2019 budou přístup k těmto tajným klíčům vyžadovat `Microsoft.HDInsight/clusters/configurations/action` oprávnění, což znamená, že je uživatelé s rolí čtenář již nebudou mít přístup. Role, které mají toto oprávnění, jsou přispěvatel, vlastník a nová role operátora clusteru HDInsight (Další informace najdete níže).

Zavádíme taky novou roli [operátora clusteru HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) , která bude moct načíst tajné kódy bez udělení oprávnění pro správu přispěvatele nebo vlastníka. Shrnutí:

| Role                                  | Měsíci                                                                                       | Po přechodu dál       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Čtenář                                | – Přístup pro čtení, včetně tajných klíčů.                                                                   | – Přístup pro čtení, **s výjimkou** tajných klíčů |           |   |   |
| Operátor clusteru HDInsight<br>(Nová role) | –                                                                                              | – Přístup pro čtení a zápis, včetně tajných klíčů         |   |   |
| Přispěvatel                           | – Přístup pro čtení a zápis, včetně tajných klíčů.<br>– Vytvářejte a spravujte všechny typy prostředků Azure.<br>-Spouštění akcí skriptu.     | Beze změny |
| Vlastník                                 | – Přístup pro čtení a zápis včetně tajných klíčů.<br>– Úplný přístup ke všem prostředkům<br>– Delegovat přístup jiným uživatelům.<br>-Spouštění akcí skriptu. | Beze změny |

Informace o tom, jak přidat přiřazení role operátora clusteru HDInsight uživateli, aby jim udělil přístup pro čtení a zápis k tajným klíčům clusteru, najdete v níže uvedené části [Přidání přiřazení role operátora clusteru HDInsight uživateli](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Ovlivněné tyto změny?

Ovlivněny jsou následující entity a scénáře:

- [Rozhraní API](#api): uživatelé používají `/configurations` `/configurations/{configurationName}` koncové body nebo.
- [Nástroje Azure HDInsight pro Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) verze 1.1.1 nebo nižší.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) verze 3.20.0 nebo nižší.
- [Azure Data Lake a Stream Analytics Tools for Visual Studio, které](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) následují 2.3.9000.1 verze.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) verze 3.15.0 nebo nižší.
- [SDK pro .NET](#sdk-for-net)
    - [verze 1. x nebo 2. x](#versions-1x-and-2x): uživatelé používají `GetClusterConfigurations` metody, `GetConnectivitySettings` , `ConfigureHttpSettings` , `EnableHttp` nebo `DisableHttp` z třídy ConfigurationsOperationsExtensions.
    - [verze 3. x a vyšší](#versions-3x-and-up): uživatelé používají `Get` metody, `Update` , `EnableHttp` , nebo `DisableHttp` z `ConfigurationsOperationsExtensions` třídy.
- [SDK pro Python](#sdk-for-python): uživatelé používají `get` metody nebo `update` z `ConfigurationsOperations` třídy.
- [Sada SDK pro jazyk Java](#sdk-for-java): uživatelé `update` používají `get` metody nebo z `ConfigurationsInner` třídy.
- [Sada SDK for přejít](#sdk-for-go): uživatelé používají `Get` `Update` metody nebo z `ConfigurationsClient` struktury.
- [AZ. HDInsight PowerShell](#azhdinsight-powershell) pod 2.0.0 verze
Postup migrace pro váš scénář najdete v následujících částech (nebo použijte odkazy výše).

### <a name="api"></a>Rozhraní API

Následující rozhraní API se změní nebo zastaralé:

- [**Získat/Configurations/{ConfigurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (odstraněné citlivé informace)
    - Dřív se použila k získání individuálních typů konfigurace (včetně tajných klíčů).
    - Od 3. září 2019 toto volání rozhraní API nyní vrátí jednotlivé typy konfigurace s tajnými klíči vynechány. Chcete-li získat všechny konfigurace, včetně tajných klíčů, použijte nové volání POST/configurations. Chcete-li získat pouze nastavení brány, použijte nové volání POST/getGatewaySettings.
- [**Získat/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (zastaralé)
    - Dřív se použilo k získání všech konfigurací (včetně tajných klíčů).
    - Od 3. září 2019 bude toto volání rozhraní API zastaralé a už se nepodporuje. Chcete-li získat všechny konfigurace před tím, použijte nové volání POST/configurations. Chcete-li získat konfigurace s citlivými parametry, použijte volání metody GET/configurations/{configurationName}.
- [**Post/Configurations/{ConfigurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (zastaralé)
    - Dřív se použila k aktualizaci přihlašovacích údajů brány.
    - Od 3. září 2019 bude toto volání rozhraní API zastaralé a už se nepodporuje. Místo toho použijte nový příspěvek/updateGatewaySettings.

Byla přidána následující náhradní rozhraní API:</span>

- [**PŘÍSPĚVEK/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Pomocí tohoto rozhraní API můžete získat všechny konfigurace, včetně tajných klíčů.
- [**PŘÍSPĚVEK/getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Pomocí tohoto rozhraní API můžete získat nastavení brány.
- [**PŘÍSPĚVEK/updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Pomocí tohoto rozhraní API můžete aktualizovat nastavení brány (uživatelské jméno a/nebo heslo).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Nástroje Azure HDInsight pro Visual Studio Code

Pokud používáte verzi 1.1.1 nebo nižší, aktualizujte na [nejnovější verzi nástrojů Azure HDInsight pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) , abyste se vyhnuli přerušení.

### <a name="azure-toolkit-for-intellij"></a>Sada Azure Toolkit for IntelliJ

Pokud používáte verzi 3.20.0 nebo nižší, aktualizujte na [nejnovější verzi modulu plug-in Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) , abyste se vyhnuli přerušení.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Nástroje pro Azure Data Lake a Stream Analytics pro Visual Studio

Aktualizujte na verzi 2.3.9000.1 nebo novější z [Azure Data Lake a Stream Analyticsch nástrojů pro Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) , abyste se vyhnuli přerušení.  Nápovědu k aktualizaci najdete v naší dokumentaci [Data Lake nástroje pro aktualizaci sady Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Pokud používáte verzi 3.15.0 nebo nižší, aktualizujte na [nejnovější verzi Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) , abyste se vyhnuli přerušení.

### <a name="sdk-for-net"></a>SDK pro .NET

#### <a name="versions-1x-and-2x"></a>Verze 1. x a 2. x

Aktualizace [verze 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) sady HDInsight SDK pro .NET Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu:

- `ClusterOperationsExtensions.GetClusterConfigurations`**už nebude vracet citlivé parametry** , jako jsou klíče úložiště (Core-site) nebo přihlašovací údaje http (brána).
    - Pokud chcete načíst všechny konfigurace, včetně citlivých parametrů, použijte příkaz `ClusterOperationsExtensions.ListConfigurations` pokračovat dál.  Všimněte si, že uživatelé s rolí čtenář nebudou moct tuto metodu použít. To umožňuje detailní kontrolu nad tím, kteří uživatelé budou mít přístup k citlivým informacím v clusteru.
    - Pokud chcete načíst jenom přihlašovací údaje brány HTTP, použijte `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.GetConnectivitySettings` je nyní zastaralý a byl nahrazen `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.ConfigureHttpSettings` je nyní zastaralý a byl nahrazen `ClusterOperationsExtensions.UpdateGatewaySettings` .

- `ConfigurationsOperationsExtensions.EnableHttp` a `DisableHttp` jsou nyní zastaralé. Protokol HTTP je nyní vždy povolen, takže tyto metody již nejsou potřeba.

#### <a name="versions-3x-and-up"></a>Verze 3. x a vyšší

Aktualizujte na [verzi 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) nebo novější v sadě HDInsight SDK pro .NET. Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**už nebude vracet citlivé parametry** , jako jsou klíče úložiště (Core-site) nebo přihlašovací údaje http (brána).
    - Pokud chcete načíst všechny konfigurace, včetně citlivých parametrů, použijte příkaz [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) pokračovat dál.Všimněte si, že uživatelé s rolí čtenář nebudou moct tuto metodu použít. To umožňuje detailní kontrolu nad tím, kteří uživatelé budou mít přístup k citlivým informacím v clusteru. 
    - Pokud chcete načíst jenom přihlašovací údaje brány HTTP, použijte [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet) . 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) je nyní zastaralý a byl nahrazen [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet) . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) a [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) jsou nyní zastaralé. Protokol HTTP je nyní vždy povolen, takže tyto metody již nejsou potřeba.

### <a name="sdk-for-python"></a>SDK pro Python

Aktualizujte na [verzi 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) nebo novější v sadě HDInsight SDK pro Python. Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**už nebude vracet citlivé parametry** , jako jsou klíče úložiště (Core-site) nebo přihlašovací údaje http (brána).
    - Pokud chcete načíst všechny konfigurace, včetně citlivých parametrů, použijte příkaz [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) pokračovat dál.Všimněte si, že uživatelé s rolí čtenář nebudou moct tuto metodu použít. To umožňuje detailní kontrolu nad tím, kteří uživatelé budou mít přístup k citlivým informacím v clusteru. 
    - Pokud chcete načíst jenom přihlašovací údaje brány HTTP, použijte [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) je nyní zastaralý a byl nahrazen [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) .

### <a name="sdk-for-java"></a>Sada SDK pro jazyk Java

Aktualizujte na [verzi 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) nebo novější v sadě HDInsight SDK pro jazyk Java. Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu:

- `ConfigurationsInner.get`**už nebude vracet citlivé parametry** , jako jsou klíče úložiště (Core-site) nebo přihlašovací údaje http (brána).
- `ConfigurationsInner.update` je nyní zastaralé.

### <a name="sdk-for-go"></a>Sada SDK pro přejít

Aktualizujte na [verzi 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) nebo novější v sadě HDInsight SDK pro přejít. Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get)**už nebude vracet citlivé parametry** , jako jsou klíče úložiště (Core-site) nebo přihlašovací údaje http (brána).
    - Pokud chcete načíst všechny konfigurace, včetně citlivých parametrů, použijte příkaz [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) pokračovat dál.Všimněte si, že uživatelé s rolí čtenář nebudou moct tuto metodu použít. To umožňuje detailní kontrolu nad tím, kteří uživatelé budou mít přístup k citlivým informacím v clusteru. 
    - Pokud chcete načíst jenom přihlašovací údaje brány HTTP, použijte [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) je nyní zastaralý a byl nahrazen [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) .

### <a name="azhdinsight-powershell"></a>AZ. HDInsight PowerShell
Aktualizujte [rutinu AZ PowerShell verze 2.0.0](https://www.powershellgallery.com/packages/Az) nebo novější, abyste se vyhnuli přerušení.  Pokud používáte metodu ovlivněnou těmito změnami, může být nutné provést minimální změny kódu.
- `Grant-AzHDInsightHttpServicesAccess` je nyní zastaralá a byla nahrazena novou `Set-AzHDInsightGatewayCredential` rutinou.
- `Get-AzHDInsightJobOutput` byla aktualizována pro podporu podrobného přístupu založeného na rolích k klíči úložiště.
    - Uživatelů s rolemi Vlastník, Přispěvatel a Operátor clusteru HDInsight se tato změna nedotkne.
    - Jenom uživatelé, kteří mají roli Čtenář, budou muset `DefaultStorageAccountKey` parametr explicitně zadat.
- `Revoke-AzHDInsightHttpServicesAccess` je nyní zastaralé. Protokol HTTP je teď vždycky povolený, takže tato rutina už není potřeba.
 Viz [AZ. ](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) Další podrobnosti najdete v příručce k migraci HDInsight.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Přidání přiřazení role operátora clusteru HDInsight uživateli

Uživatel s rolí [vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) může přiřadit roli [operátora clusteru HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) uživatelům, kteří by měli mít přístup pro čtení a zápis k citlivým hodnotám konfigurace clusteru HDInsight (například přihlašovací údaje brány clusteru a klíče účtu úložiště).

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Nejjednodušší způsob, jak přidat toto přiřazení role, je použití `az role assignment create` příkazu v Azure CLI.

> [!NOTE]
> Tento příkaz musí spustit uživatel s rolí vlastníka, protože mu můžou udělit tato oprávnění. `--assignee`Je název objektu služby nebo e-mailové adresy uživatele, kterému chcete přiřadit roli operátora clusteru HDInsight. Pokud se zobrazí chyba nedostatečná oprávnění, přečtěte si níže uvedené Nejčastější dotazy.

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

Alternativně můžete použít Azure Portal k přidání přiřazení role operátora clusteru HDInsight uživateli. Projděte si dokumentaci, [přidejte nebo odeberte přiřazení rolí Azure pomocí Azure Portal – přidejte přiřazení role](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>Nejčastější dotazy

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Proč se po aktualizaci požadavků rozhraní API nebo nástroje zobrazuje odpověď 403 (Zakázáno)?

Konfigurace clusteru jsou nyní za detailní řízení přístupu založené na rolích a vyžadují `Microsoft.HDInsight/clusters/configurations/*` oprávnění k přístupu k nim. Pokud chcete získat toto oprávnění, přiřaďte k uživateli nebo instančnímu objektu, který se snaží získat přístup ke konfiguraci, roli operátora clusteru HDInsight, přispěvatele nebo vlastníka.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Proč se zobrazuje "nedostatečná oprávnění k dokončení operace" při spuštění příkazu rozhraní příkazového řádku Azure pro přiřazení role operátora clusteru HDInsight jinému uživateli nebo instančnímu objektu?

Uživatel nebo instanční objekt, který spouští příkaz, musí kromě role vlastníka mít k dispozici dostatečná oprávnění služby Azure AD, aby mohl vyhledat ID objektu pověřené nabyvatelem. Tato zpráva indikuje nedostatečná oprávnění služby Azure AD. Zkuste nahradit `-–assignee` argument `–assignee-object-id` parametrem a místo názvu (nebo ID objektu zabezpečení v případě spravované identity) zadejte ID objektu zmocnění. Další informace najdete v části nepovinných parametrů v [dokumentaci AZ role Assignment Create](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) .

Pokud to pořád nefunguje, požádejte správce Azure AD, aby získal správná oprávnění.

### <a name="what-will-happen-if-i-take-no-action"></a>Co se stane, když neprovedem žádnou akci?

Od 3. září 2019 `GET /configurations` a `POST /configurations/gateway` volání již nebudou vracet žádné informace a `GET /configurations/{configurationName}` volání již nebude vracet citlivé parametry, jako jsou klíče účtu úložiště nebo heslo clusteru. Totéž platí pro odpovídající metody SDK a rutiny PowerShellu.

Pokud používáte starší verzi některého z nástrojů pro sadu Visual Studio, VSCode, IntelliJ nebo zatmění uvedenou výše, nebudou již nadále fungovat, dokud ji neaktualizujete.

Podrobnější informace najdete v odpovídající části tohoto dokumentu pro váš scénář.
