---
title: Vytvoření aplikace .NET neinteraktivního ověřování v Azure HDInsight | Microsoft Docs
description: Informace o vytváření aplikací rozhraní Microsoft .NET neinteraktivního ověřování v Azure HDInsight.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 497c67132b6d5ebc1301f56fde72031ca3e7b976
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199696"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Vytvoření neinteraktivního ověřování aplikace .NET HDInsight
Můžete spustit aplikace Microsoft .NET Azure HDInsight s identitou aplikace vlastní (neinteraktivní) nebo v části identita přihlášeného uživatele (interaktivní) aplikace. Tento článek ukazuje, jak vytvořit neinteraktivního ověřování aplikace .NET pro připojení k Azure a spravovat HDInsight. Ukázka interaktivní aplikace, najdete v části [připojit k Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

Ze neinteraktivní aplikace .NET potřebujete:

* ID klienta vašeho předplatného Azure (také nazývané *ID adresáře*). V tématu [získání ID tenanta](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* ID klienta aplikace Azure Active Directory (Azure AD). V tématu [vytvořit aplikaci Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) a [získání ID aplikace](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* Aplikace Azure AD tajný klíč. V tématu [Get aplikace ověřovací klíč](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Požadavky
* Cluster služby HDInsight. Najdete v článku [kurz Začínáme](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Přiřazení role aplikace Azure AD
Přiřaďte aplikaci Azure AD [role](../role-based-access-control/built-in-roles.md), jí udělit oprávnění k provádění akcí. Rozsah můžete nastavit na úrovni předplatné, skupinu prostředků nebo prostředek. Oprávnění jsou děděné na nižších úrovních oboru. (Například přidání aplikace do role Čtenář pro skupinu prostředků znamená, že aplikace může číst skupina prostředků a na něm žádné prostředky.) V tomto kurzu nastavit obor na úrovni skupiny prostředků. Další informace najdete v tématu [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).

**Chcete-li přidat roli vlastníka do aplikace Azure AD**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **skupiny prostředků**.
3. Vyberte skupinu prostředků, který má cluster HDInsight, na kterém budete spouštět dotaz Hive později v tomto kurzu. Pokud máte velký počet skupin prostředků, můžete použít filtr je ta, kterou chcete najít.
4. V nabídce skupiny prostředků, vyberte **přístup k ovládacímu prvku (IAM)**.
5. V části **uživatelé**, vyberte **přidat**.
6. Postupujte podle pokynů a přidejte roli vlastníka do aplikace Azure AD. Po úspěšném přidání role, aplikace je uveden v části **uživatelé**, s roli vlastníka. 

## <a name="develop-an-hdinsight-client-application"></a>Vývoj aplikace HDInsight klienta

1. Vytvořte konzolovou aplikaci C#.
2. Přidejte následující balíčky NuGet:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Spusťte následující kód:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```


## <a name="next-steps"></a>Další postup
* [Vytvořit Azure Active Directory objekt aplikací a služeb na portálu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).
* Zjistěte, jak [ověřit hlavní název služby pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* Další informace o [řízení přístupu Azure na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md).
