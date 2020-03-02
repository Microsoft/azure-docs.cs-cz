---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9333bb36971fc28a23a443e50d191abeef05b758
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78208256"
---
Zobrazení a zkopírování přístupových klíčů účtu úložiště nebo připojovacího řetězce z Azure Portal:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** vyberte **Přístupové klíče**. Zobrazí se přístupové klíče vašeho účtu a také úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Klíč** a kliknutím na tlačítko **Kopírovat** zkopírujte klíč účtu.
5. Alternativně můžete zkopírovat celý připojovací řetězec. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a kliknutím na tlačítko **Kopírovat** zkopírujte připojovací řetězec.

    ![Snímek obrazovky znázorňující zobrazení přístupových klíčů v Azure Portal](media/storage-view-keys-include/portal-connection-string.png)

Pro přístup k Azure Storage můžete použít libovolný klíč, ale obecně je dobrým zvykem použít první klíč a při střídání klíčů rezervovat použití druhého klíče.
