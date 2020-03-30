---
title: Neinteraktivní ověřovací aplikace .NET – Azure HDInsight
description: Přečtěte si, jak ve službě Azure HDInsight vytvářet neinteraktivní ověřovací aplikace Microsoft .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 5e6a0586bc750f8972586920c15dbb297295aa20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371269"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Vytvoření neinteraktivní aplikace HDInsight s neinteraktivním ověřováním

Spusťte aplikaci Microsoft .NET Azure HDInsight buď pod vlastní identitou aplikace (neinteraktivní), nebo pod identitou přihlášeného uživatele aplikace (interaktivní). Tento článek ukazuje, jak vytvořit neinteraktivní ověřovací .NET aplikace pro připojení k Azure a spravovat HDInsight. Ukázku interaktivní aplikace najdete v tématu [Připojení k Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

Z neinteraktivní aplikace .NET potřebujete:

* ID klienta předplatného Azure (nazývané také *ID adresáře).* Viz [Získání ID klienta](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* ID klienta aplikace Azure Active Directory (Azure AD). Viz [Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) a získání [ID aplikace](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Tajný klíč aplikace Azure AD. Viz [Získání ověřovacího klíče aplikace](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Požadavky

Cluster HDInsight. Podívejte se na [výuku Začínáme](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Přiřazení role k aplikaci Azure AD

Přiřaďte aplikaci Azure AD [roli](../role-based-access-control/built-in-roles.md), abyste jí udělili oprávnění k provádění akcí. Obor můžete nastavit na úrovni předplatného, skupiny prostředků nebo prostředku. Oprávnění jsou zděděna do nižších úrovní oboru. Například přidání aplikace do role Čtečka pro skupinu prostředků znamená, že aplikace může číst skupinu prostředků a všechny prostředky v ní. V tomto článku nastavíte obor na úrovni skupiny prostředků. Další informace najdete [v tématu Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).

**Přidání role vlastníka do aplikace Azure AD**

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte do skupiny prostředků, která má cluster HDInsight, ve kterém spustíte dotaz Hive dále v tomto článku. Pokud máte velký počet skupin prostředků, můžete pomocí filtru najít požadovanou.
1. V nabídce skupiny prostředků vyberte **Ovládací prvek přístupu (IAM)**.
1. Výběrem karty **Přiřazení rolí** zobrazíte aktuální přiřazení rolí.
1. V horní části stránky vyberte **+ Přidat**.
1. Podle pokynů přidejte roli vlastníka do aplikace Azure AD. Po úspěšném přidání role je aplikace uvedena v roli Vlastník.

## <a name="develop-an-hdinsight-client-application"></a>Vývoj klientské aplikace HDInsight

1. Vytvořte konzolovou aplikaci C#.
2. Přidejte následující balíčky [NuGet:](https://www.nuget.org/)

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

## <a name="next-steps"></a>Další kroky

* [Vytvořte na webu Azure Portal instanční objekt a instanční objekt Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).
* Zjistěte, jak [ověřit instanční objekt pomocí Správce prostředků Azure](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Další informace o [řízení přístupu na základě rolí Azure (RBAC)](../role-based-access-control/role-assignments-portal.md).
