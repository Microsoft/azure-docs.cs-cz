---
title: Připojení k Dropboxu – Azure Logic Apps
description: Nahrávání a Správa souborů pomocí rozhraní REST API Dropboxu a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 4e0689454ec074348fcbc775373a48d6825cfac4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050975"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Nahrávání a Správa souborů v Dropboxu pomocí Azure Logic Apps

Pomocí konektoru Dropboxu a Azure Logic Apps můžete vytvářet automatizované pracovní postupy, které odesílají a spravují soubory ve vašem účtu Dropboxu. 

Tento článek ukazuje, jak se připojit k Dropboxu z vaší aplikace logiky a pak přidat Dropbox **při vytvoření souboru a když** Dropbox **získá obsah souboru pomocí akce cesta** .

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet Dropboxu](https://www.dropbox.com/), který si můžete zaregistrovat zdarma. Přihlašovací údaje k účtu jsou nezbytné pro vytvoření připojení mezi vaší aplikací logiky a vaším účtem Dropbox.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) V tomto příkladu potřebujete prázdnou aplikaci logiky.

## <a name="add-trigger"></a>Přidat trigger

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. V poli Hledat vyberte možnost **vše**. Do vyhledávacího pole zadejte jako filtr "Dropbox".
V seznamu triggery vyberte tuto aktivační událost: **Při vytvoření souboru**

   ![Výběr triggeru Dropboxu](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Přihlaste se pomocí přihlašovacích údajů k účtu Dropboxu a udělte přístup k datům Dropboxu pro Azure Logic Apps.

1. Zadejte požadované informace pro aktivační událost. 

   V tomto příkladu vyberte složku, ve které chcete sledovat vytvoření souboru. Chcete-li procházet složky, klikněte na ikonu složky vedle pole **Složka** .

## <a name="add-action"></a>Přidat akci

Teď přidejte akci, která získá obsah z libovolného nového souboru.

1. V části Trigger klikněte na **Další krok**. 

1. V poli Hledat vyberte možnost **vše**. Do vyhledávacího pole zadejte jako filtr "Dropbox".
V seznamu akce vyberte tuto akci: **Získání obsahu souboru pomocí cesty**

1. Pokud jste ještě nepovolili Azure Logic Apps přístup k Dropboxu, udělte se teď oprávnění k přístupu.

1. Chcete-li přejít k cestě k souboru, kterou chcete použít, klikněte vedle pole **cesta k souboru** na tlačítko se třemi tečkami ( **...** ). 

   Můžete také kliknout do pole **cesta k souboru** a ze seznamu dynamického obsahu vybrat možnost cesta k **souboru**, jejíž hodnota je k dispozici jako výstup z aktivační události, kterou jste přidali v předchozí části.

1. Až budete hotovi, uložte aplikaci logiky.

1. Pokud chcete aktivovat aplikaci logiky, vytvořte nový soubor v Dropboxu.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce konektoru](/connectors/dropbox/).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
