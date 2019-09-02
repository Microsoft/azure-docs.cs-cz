---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 364da4bb5f3f76f0a8a189db5aa59cca305470ea
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70197119"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Zkopírování přihlašovacích údajů z webu Azure Portal

Ukázková aplikace potřebuje ověřit přístup k vašemu účtu úložiště. Ověření provedete tak, že do aplikace přidáte přihlašovací údaje svého účtu úložiště v podobě připojovacího řetězce. Přihlašovací údaje účtu úložiště zobrazíte pomocí následujícího postupu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** v přehledu účtu úložiště vyberte **Přístupové klíče**. Tady se zobrazí přístupové klíče vašeho účtu a úplný připojovací řetězec pro jednotlivé klíče.   
4. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a výběrem tlačítka **Kopírovat** zkopírujte připojovací řetězec. V dalším kroku přidáte hodnotu připojovacího řetězce do proměnné prostředí.

    ![Snímek obrazovky ukazující zkopírování připojovacího řetězce z webu Azure Portal](media/storage-copy-connection-string-portal/portal-connection-string.png)
