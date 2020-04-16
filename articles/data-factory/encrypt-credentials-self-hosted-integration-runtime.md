---
title: Šifrování přihlašovacích údajů ve Službě Azure Data Factory
description: Zjistěte, jak šifrovat a ukládat přihlašovací údaje pro místní úložiště dat v počítači s prostředím runtime integrace s vlastním hostitelem.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 8ce26360aca8d8408135cbe89aabff4f923013b4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416373"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Šifrování přihlašovacích údajů pro místní úložiště dat v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Můžete šifrovat a ukládat přihlašovací údaje pro místní úložiště dat (propojené služby s citlivými informacemi) v počítači s prostředím runtime integrace s vlastním hostitelem. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Předáte definiční soubor JSON s pověřeními <br/>[**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) rutina k vytvoření výstupního souboru definice JSON se šifrovanými pověřeními. Potom použijte aktualizované definice JSON k vytvoření propojených služeb.

## <a name="author-sql-server-linked-service"></a>Autor SQL Server propojené služby
Vytvořte soubor JSON s názvem **SqlServerLinkedService.json** v libovolné složce s následujícím obsahem:  

Před uložením souboru `<servername>`nahraďte , `<username>`, a `<password>` hodnoty serveru SQL Server. `<databasename>` A nahraďte `<integration runtime name>` název integračního běhu. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Šifrování přihlašovacích údajů
Chcete-li zašifrovat citlivá data z datové části JSON v místním prostředí runtime vlastní integrace, spusťte **New-AzDataFactoryV2LinkedServiceEncryptedCredential**a předajte datovou část JSON. Tato rutina zajišťuje, že pověření jsou šifrována pomocí DPAPI a uložena místně v samostatném integračním runtime uzlu. Výstupní datová část obsahující šifrovaný odkaz na pověření může být přesměrována na jiný soubor JSON (v tomto případě "encryptedLinkedService.json").

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Použití jsonu se šifrovanými přihlašovacími údaji
Nyní použijte výstupní soubor JSON z předchozího příkazu obsahujícího šifrované pověření k nastavení **služby SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Další kroky
Informace o aspektech zabezpečení pro přesun dat naleznete v [tématu Důležité informace o zabezpečení pohybu dat](data-movement-security-considerations.md).

