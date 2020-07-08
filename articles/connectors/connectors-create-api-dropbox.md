---
title: Připojit k Dropboxu
description: Automatizace úloh a pracovních postupů, které odesílají a spravují soubory v Dropboxu pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75665747"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Nahrávání a Správa souborů v Dropboxu pomocí Azure Logic Apps

Pomocí konektoru Dropboxu a Azure Logic Apps můžete vytvářet automatizované pracovní postupy, které odesílají a spravují soubory ve vašem účtu Dropboxu. 

Tento článek ukazuje, jak se připojit k Dropboxu z vaší aplikace logiky a pak přidat Dropbox **při vytvoření souboru a když** Dropbox **získá obsah souboru pomocí akce cesta** .

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet Dropboxu](https://www.dropbox.com/), který si můžete zaregistrovat zdarma. Přihlašovací údaje k účtu jsou nezbytné pro vytvoření připojení mezi vaší aplikací logiky a vaším účtem Dropbox.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) V tomto příkladu potřebujete prázdnou aplikaci logiky.

## <a name="add-trigger"></a>Přidání triggeru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. V poli Hledat vyberte možnost **vše**. Do vyhledávacího pole zadejte jako filtr "Dropbox".
V seznamu triggery vyberte tuto aktivační událost: **když se vytvoří soubor** .

   ![Výběr triggeru Dropboxu](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Přihlaste se pomocí přihlašovacích údajů k účtu Dropboxu a udělte přístup k datům Dropboxu pro Azure Logic Apps.

1. Zadejte požadované informace pro aktivační událost. 

   V tomto příkladu vyberte složku, ve které chcete sledovat vytvoření souboru. Chcete-li procházet složky, klikněte na ikonu složky vedle pole **Složka** .

## <a name="add-action"></a>Přidání akce

Teď přidejte akci, která získá obsah z libovolného nového souboru.

1. V části Trigger klikněte na **Další krok**. 

1. V poli Hledat vyberte možnost **vše**. Do vyhledávacího pole zadejte jako filtr "Dropbox".
V seznamu akce vyberte tuto akci: **získat obsah souboru pomocí cesty**

1. Pokud jste ještě nepovolili Azure Logic Apps přístup k Dropboxu, udělte se teď oprávnění k přístupu.

1. Chcete-li přejít k cestě k souboru, kterou chcete použít, klikněte vedle pole **cesta k souboru** na tlačítko se třemi tečkami (**...**). 

   Můžete také kliknout do pole **cesta k souboru** a ze seznamu dynamického obsahu vybrat možnost cesta k **souboru**, jejíž hodnota je k dispozici jako výstup z aktivační události, kterou jste přidali v předchozí části.

1. Až budete hotovi, uložte aplikaci logiky.

1. Pokud chcete aktivovat aplikaci logiky, vytvořte nový soubor v Dropboxu.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/dropbox/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
