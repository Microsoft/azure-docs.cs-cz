---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: codemillmatt
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: masoucou
ms.custom: include file
ms.openlocfilehash: 02586d38903c60ba8982753ca0bd3e15192d5deb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "83006234"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Zkopírování přihlašovacích údajů z webu Azure Portal

Když ukázková aplikace odešle požadavek na Azure Storage, musí být ověřena. K autorizaci žádosti přidejte do aplikace přihlašovací údaje účtu úložiště jako připojovací řetězec. Přihlašovací údaje účtu úložiště zobrazíte pomocí následujícího postupu:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** v přehledu účtu úložiště vyberte **Přístupové klíče**. Tady se zobrazí přístupové klíče vašeho účtu a úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a výběrem tlačítka **Kopírovat** zkopírujte připojovací řetězec. V dalším kroku přidáte hodnotu připojovacího řetězce do proměnné prostředí.

    ![Snímek obrazovky ukazující zkopírování připojovacího řetězce z webu Azure Portal](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Po zkopírování připojovacího řetězce jej nastavte na proměnnou úrovně třídy v souboru *MainPage.XAML.cs* . Otevřete *MainPaage.XAML.cs* a najděte `storageConnectionString` proměnnou. Nahraďte `<yourconnectionstring>` skutečným připojovacím řetězcem.

Zde je kód:

```csharp
string storageConnectionString = "<yourconnectionstring>";
```