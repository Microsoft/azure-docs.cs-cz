---
title: Aplikace .NET bez interaktivního ověřování – Azure HDInsight
description: Naučte se vytvářet neinteraktivní ověřování aplikací Microsoft .NET v Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/23/2019
ms.openlocfilehash: 121c5850ef47999f54d206b95b69e10775d3e5c9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946095"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Vytvoření aplikace .NET HDInsight bez interaktivního ověřování

Spusťte aplikaci Microsoft .NET Azure HDInsight buď v rámci vlastní identity aplikace (bez interaktivního), nebo pod identitou přihlášeného uživatele aplikace (interaktivní). V tomto článku se dozvíte, jak vytvořit aplikaci .NET, která není interaktivní pro ověřování, aby se mohla připojit k Azure a spravovat HDInsight. Ukázku interaktivní aplikace najdete v tématu [připojení k Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

Z aplikace bez interaktivního rozhraní .NET potřebujete:

* ID tenanta předplatného Azure (označuje se také jako *ID adresáře*). Viz [získat ID tenanta](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* ID klienta aplikace Azure Active Directory (Azure AD). Viz [Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) a [získání ID aplikace](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* Tajný klíč aplikace služby Azure AD. Viz [získat klíč pro ověření aplikace](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="prerequisites"></a>Požadavky

Cluster An HDInsight. Podívejte se na [Úvodní kurz](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Přiřazení role k aplikaci Azure AD

Přiřaďte aplikaci Azure AD [roli](../role-based-access-control/built-in-roles.md)a udělte jí oprávnění k provádění akcí. Rozsah můžete nastavit na úrovni předplatného, skupiny prostředků nebo prostředku. Oprávnění jsou děděna do nižších úrovní rozsahu. Například přidání aplikace do role čtenář pro skupinu prostředků znamená, že aplikace může číst skupinu prostředků a všechny prostředky v ní. V tomto článku nastavíte obor na úrovni skupiny prostředků. Další informace najdete v tématu [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).

**Přidání role vlastníka do aplikace Azure AD**

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte do skupiny prostředků, která obsahuje cluster HDInsight, na kterém spustíte dotaz na podregistr později v tomto článku. Pokud máte velký počet skupin prostředků, můžete použít filtr a vyhledat ten, který chcete.
1. V nabídce skupina prostředků vyberte **řízení přístupu (IAM)**.
1. Kliknutím na kartu **přiřazení rolí** zobrazíte aktuální přiřazení rolí.
1. V horní části stránky vyberte **+ Přidat**.
1. Podle pokynů přidejte do vaší aplikace Azure AD roli vlastníka. Po úspěšném přidání role se aplikace zobrazí v části role vlastníka.

## <a name="develop-an-hdinsight-client-application"></a>Vývoj klientské aplikace HDInsight

1. Vytvořte konzolovou aplikaci C#.
2. Přidejte následující balíčky [NuGet](https://www.nuget.org/) :

    * `Install-Package Microsoft.Azure.Common.Authentication -Pre`
    * `Install-Package Microsoft.Azure.Management.HDInsight -Pre`
    * `Install-Package Microsoft.Azure.Management.Resources -Pre`

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

## <a name="next-steps"></a>Další kroky

* [Vytvořte Azure Active Directory aplikace a instanční objekt v Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).
* Přečtěte si, jak [ověřit instanční objekt pomocí služby Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Přečtěte si o [řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md).
