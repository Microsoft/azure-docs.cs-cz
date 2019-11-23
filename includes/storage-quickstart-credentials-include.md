---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: mhopkins
ms.custom: include file
ms.openlocfilehash: 7e6bdf8f2cede2d55163932bfe8ab58e9ee96e0f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422029"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Zkopírování přihlašovacích údajů z webu Azure Portal

When the sample application makes a request to Azure Storage, it must be authorized. To authorize a request, add your storage account credentials to the application as a connection string. Přihlašovací údaje účtu úložiště zobrazíte pomocí následujícího postupu:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** v přehledu účtu úložiště vyberte **Přístupové klíče**. Tady se zobrazí přístupové klíče vašeho účtu a úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a výběrem tlačítka **Kopírovat** zkopírujte připojovací řetězec. V dalším kroku přidáte hodnotu připojovacího řetězce do proměnné prostředí.

    ![Snímek obrazovky ukazující zkopírování připojovacího řetězce z webu Azure Portal](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Po zkopírování připojovacího řetězce ho zapište do nové proměnné prostředí na místním počítači, na kterém aplikaci spouštíte. Proměnnou prostředí nastavíte tak, že otevřete okno konzoly a budete postupovat podle pokynů pro váš operační systém. Replace `<yourconnectionstring>` with your actual connection string.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

After you add the environment variable in Windows, you must start a new instance of the command window.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Restart programs

After you add the environment variable, restart any running programs that will need to read the environment variable. For example, restart your development environment or editor before continuing.
