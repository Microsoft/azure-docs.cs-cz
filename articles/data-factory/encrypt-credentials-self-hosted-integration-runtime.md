---
title: Šifrování přihlašovacích údajů v Azure Data Factory | Microsoft Docs
description: Zjistěte, jak k šifrování a ukládat přihlašovací údaje pro vaše místní úložiště dat na počítači s vlastním hostováním integrace modulu runtime.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: b577c276627c3a187215cd0da551428fbb32791f
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050902"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Šifrovat přihlašovací údaje pro místní úložiště dat v Azure Data Factory
Můžete šifrovat a ukládat přihlašovací údaje pro vaše místní data úložiště (propojené služby s citlivými informacemi) na počítači s vlastním hostováním integrace modulu runtime. 

Předat definice soubor JSON s přihlašovacími údaji na <br/>[**Nové AzureRmDataFactoryV2LinkedServiceEncryptedCredential** ](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) rutiny k vytvoření souboru výstup JSON definice zašifrované přihlašovací údaje. Poté použijte aktualizované definice JSON pro vytvoření propojené služby.

## <a name="author-sql-server-linked-service"></a>Služba SQL Server propojené Autor
Vytvořte soubor JSON s názvem **SqlServerLinkedService.json** v libovolné složky s následujícím obsahem:  

Nahraďte `<servername>`, `<databasename>`, `<username>`, a `<password>` s hodnotami pro SQL Server před uložením souboru. A nahraďte `<integration runtime name>` s názvem vaší integrace modulu runtime. 

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
K šifrování citlivých dat z datové části JSON v modulu runtime s vlastním hostováním integrace místní, spusťte **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**a předat na datové části JSON. Tato rutina zajistí, že přihlašovací údaje jsou šifrované pomocí rozhraní DPAPI a uloženy na uzlu runtime vlastním hostováním integrace místně. Výstupní datové části můžete přesměrovat do jiného souboru JSON (v tomto případě "encryptedLinkedService.json"), který obsahuje zašifrované přihlašovací údaje.

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Použití formátu JSON s zašifrované přihlašovací údaje
Nyní, nastavit pomocí souboru JSON výstupu z předchozí příkaz, který obsahuje šifrovaný přihlašovací údaj **SqlServerLinkedService**.

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Další postup
Informace o aspektech zabezpečení pro přesun dat najdete v tématu [důležité informace o zabezpečení pro přesun dat](data-movement-security-considerations.md).

