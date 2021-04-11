---
title: Připojení pomocí spravované identity – Azure Database for PostgreSQL – jeden server
description: Přečtěte si, jak se připojit a ověřit pomocí spravované identity pro ověřování pomocí Azure Database for PostgreSQL
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: d59b449a2dbf2ed3b1177db6e543de4c34a8ecb1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604154"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Připojení ke službě Azure Database for PostgreSQL s využitím spravované identity

V tomto článku se dozvíte, jak použít uživatelem přiřazenou identitu pro virtuální počítač Azure pro přístup k serveru Azure Database for PostgreSQL. Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověření přístupu ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. 

Získáte informace o těchto tématech:
- Udělení přístupu k serveru Azure Database for PostgreSQL k VIRTUÁLNÍmu počítači
- Vytvoření uživatele v databázi, který reprezentuje identitu virtuálního počítače přiřazenou uživatelem
- Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k dotazování serveru Azure Database for PostgreSQL
- Implementace Načtení tokenu v ukázkové aplikaci v jazyce C#

## <a name="prerequisites"></a>Požadavky

- Pokud ještě neznáte funkci spravovaných identit pro prostředky Azure, podívejte se na tento [přehled](../../articles/active-directory/managed-identities-azure-resources/overview.md). Pokud nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Aby bylo možné vytvořit požadovaný prostředek a správu rolí, váš účet potřebuje oprávnění "vlastník" v příslušném oboru (vaše předplatné nebo skupina prostředků). Pokud potřebujete pomoc s přiřazením role, přečtěte si téma [přiřazení rolí Azure ke správě přístupu k prostředkům předplatného Azure](../../articles/role-based-access-control/role-assignments-portal.md).
- Potřebujete virtuální počítač Azure (například se spuštěným Ubuntu Linux), který chcete použít pro přístup k databázi pomocí spravované identity.
- Potřebujete Azure Database for PostgreSQL databázový server s nakonfigurovaným [ověřováním Azure AD](howto-configure-sign-in-aad-authentication.md) .
- Chcete-li postupovat podle příkladu jazyka C#, nejprve dokončete průvodce, jak se [připojit pomocí jazyka c#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Vytvoření spravované identity přiřazené uživatelem pro váš virtuální počítač

Pomocí příkazu [AZ identity Create](/cli/azure/identity#az-identity-create) vytvořte v předplatném identitu. Můžete použít stejnou skupinu prostředků, ve které je váš virtuální počítač spuštěný, nebo jiný.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Pokud chcete nakonfigurovat identitu v následujících krocích, pomocí příkazu [AZ identity show](/cli/azure/identity#az-identity-show) uložte ID prostředku identity a ID klienta do proměnných.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

K virtuálnímu počítači teď můžeme přiřadit identitu přiřazenou uživatelem pomocí příkazu [AZ VM identity Assign](/cli/azure/vm/identity#az-vm-identity-assign) :

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Instalaci dokončíte tak, že zobrazíte hodnotu ID klienta, kterou budete potřebovat v následujících několika krocích:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Vytvoření uživatele PostgreSQL pro spravovanou identitu

Nyní se připojte jako uživatel s oprávněními správce Azure AD k databázi PostgreSQL a spusťte následující příkazy SQL:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

Spravovaná identita teď má přístup při ověřování s uživatelským jménem `myuser` (nahraďte názvem vaší volby).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Načítá se přístupový token ze služby metadat instance Azure.

Vaše aplikace teď může získat přístupový token ze služby metadat instance Azure a použít ho k ověřování s databází.

Toto Načtení tokenu se provádí provedením požadavku HTTP `http://169.254.169.254/metadata/identity/oauth2/token` a předáním následujících parametrů:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (které jste získali dříve)

Vrátíte výsledek JSON, který obsahuje `access_token` pole – tato dlouhá textová hodnota je přístupový token spravované identity, který byste měli použít jako heslo při připojování k databázi.

Pro účely testování můžete ve svém prostředí spustit následující příkazy. Všimněte si, že potřebujete `curl` , `jq` a `psql` nainstalovaného klienta.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Nyní jste připojeni k databázi, kterou jste nakonfigurovali dříve.

## <a name="connecting-using-managed-identity-in-c"></a>Připojení pomocí spravované identity v jazyce C #

V této části se dozvíte, jak získat přístupový token pomocí spravované identity přiřazené uživatelem na virtuálním počítači a použít ho k volání Azure Database for PostgreSQL. Azure Database for PostgreSQL nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané pomocí spravovaných identit pro prostředky Azure. Při vytváření připojení k PostgreSQL předáte přístupový token do pole heslo.

Tady je příklad kódu .NET pro otevření připojení k PostgreSQL pomocí přístupového tokenu. Tento kód musí být spuštěný na virtuálním počítači pro přístup ke koncovému bodu spravované identity přiřazené uživateli virtuálního počítače. K použití metody přístupového tokenu se vyžaduje .NET Framework 4,6 nebo vyšší nebo .NET Core 2,2 nebo vyšší. Nahraďte hodnoty položky hostitel, uživatel, databáze a CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader and extract access token.
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

Při spuštění tento příkaz nabídne výstup podobný tomuto:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Další kroky

* Projděte si obecné koncepty [ověřování Azure Active Directory s](concepts-aad-authentication.md) využitím Azure Database for PostgreSQL
