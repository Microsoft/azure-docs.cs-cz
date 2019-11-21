---
title: Create a function app from the Azure Portal
description: Create a new function app in Azure from the portal.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230769"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Vytvoření aplikace funkcí na webu Azure Portal

This topic shows you how to use Azure Functions to create a function app in the Azure portal. Aplikace funkcí je kontejner, který hostuje provádění jednotlivých funkcí. 

## <a name="create-a-function-app"></a>Vytvoření Function App

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Při vytváření aplikace funkcí zadejte platný **Název aplikace**, který může obsahovat pouze písmena, číslice a pomlčky. Podtržítko ( **_** ) není povolené znak.

Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Název vašeho účtu úložiště musí být jedinečný v rámci Azure. 

Po vytvoření aplikace funkcí můžete jednotlivé funkce vytvářet v jednom nebo několika různých jazycích. Funkce můžete vytvářet [pomocí portálu](functions-create-first-azure-function.md#create-function), [průběžného nasazování](functions-continuous-deployment.md) nebo prostřednictvím [nahrávání přes protokol FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Plány služby

Azure Functions has three different service plans: Consumption plan, Premium plan, and Dedicated (App Service) plan. You must choose your service plan when your function app is created, and it cannot subsequently be changed. Další informace najdete v tématu [Výběr plánu hostování služby Azure Functions](functions-scale.md).

If you are planning to run JavaScript functions on a Dedicated (App Service) plan, you should choose a plan with fewer cores. Další informace najdete v tématu [Referenční informace k JavaScriptu pro službu Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

When creating a function app, you must create or link to a general-purpose Azure Storage account that supports Blob, Queue, and Table storage. Služba Functions interně používá službu Storage pro operace, jako jsou správa triggerů a protokolování provádění funkcí. Některé účty úložiště nepodporují fronty a tabulky, například účty úložiště pouze objektů blob, Azure Premium Storage a účty úložiště pro obecné účely s replikací zónově redundantního úložiště. Tyto účty jsou při vytváření aplikace funkcí vyfiltrované z okna Účet úložiště.

>[!NOTE]
>Pokud použijete plán hostování Consumption, kód vaší funkce a vázané konfigurační soubory se ukládají ve službě Azure File Storage v hlavním účtu úložiště. Když odstraníte hlavní účet úložiště, tento obsah se odstraní a není možné ho obnovit.

Další informace o typech účtů úložiště najdete v tématu [Seznámení se službami Azure Storage](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Další kroky

While the Azure portal makes it easy to create and try out Functions, we recommend [local development](functions-develop-local.md). After creating a function app in the portal, you still need to add a function. 

> [!div class="nextstepaction"]
> [Add an HTTP triggered function](functions-create-first-azure-function.md#create-function)
