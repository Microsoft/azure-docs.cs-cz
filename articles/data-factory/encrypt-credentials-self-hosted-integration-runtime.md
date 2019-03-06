---
title: Šifrovat přihlašovací údaje ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak k šifrování a ukládat přihlašovací údaje pro vaše místní úložiště dat na počítači s místní prostředí integration runtime.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 65518e7515f9e233b12ae5406819c91e8e3f2a77
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453163"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Šifrovat přihlašovací údaje pro místní úložiště dat ve službě Azure Data Factory
Můžete šifrovat a uložení přihlašovacích údajů pro svá úložiště dat s místními (propojené služby s citlivými informacemi) na počítači s místní prostředí integration runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Předejte definiční soubor JSON s přihlašovacími údaji k <br/>[**Nové AzDataFactoryV2LinkedServiceEncryptedCredential** ](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) rutiny vytvořit výstupní soubor definice JSON se zašifrovanými přihlašovacími údaji. Potom použijte aktualizované definice JSON pro vytvoření propojené služby.

## <a name="author-sql-server-linked-service"></a>Vytvořit propojenou službu SQL serveru
Vytvořte soubor JSON s názvem **C:\adfv2tutorial** v jakékoli složce s následujícím obsahem:  

Nahraďte `<servername>`, `<databasename>`, `<username>`, a `<password>` s hodnotami pro váš SQL Server před uložením tohoto souboru. A nahraďte `<integration runtime name>` s názvem vašeho prostředí integration runtime. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Šifrovat přihlašovací údaje
K šifrování citlivých dat z datové části JSON v prostředí v místním prostředí integration runtime v místním, spusťte **New-AzDataFactoryV2LinkedServiceEncryptedCredential**a předat jí datovou část JSON. Tato rutina se zajistí, že přihlašovací údaje jsou šifrované pomocí rozhraní DPAPI a uložené na místně uzel modulu runtime integrace v místním prostředí. Výstupní datovou část je možné přesměrovat do jiného souboru JSON (v tomto případě "encryptedLinkedService.json"), který obsahuje zašifrované přihlašovací údaje.

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Pomocí zašifrované přihlašovací údaje ve formátu JSON
Teď pomocí výstupního souboru JSON z předchozího příkazu, který obsahuje zašifrované přihlašovací údaje, které pokud chcete nastavit **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Další postup
Informace o aspektech týkajících se zabezpečení v případě přesunu dat najdete v tématu [aspekty zabezpečení přesunu dat](data-movement-security-considerations.md).

