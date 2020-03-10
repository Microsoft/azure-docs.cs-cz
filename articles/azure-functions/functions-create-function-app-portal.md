---
title: Vytvoření aplikace Function App na webu Azure Portal
description: Vytvořte v Azure novou aplikaci Function App z portálu.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370316"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Vytvoření aplikace funkcí na webu Azure Portal

V tomto tématu se dozvíte, jak pomocí Azure Functions vytvořit aplikaci Function App v Azure Portal. Aplikace funkcí je kontejner, který hostuje provádění jednotlivých funkcí. 

## <a name="create-a-function-app"></a>Vytvoření Function App

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Při vytváření aplikace funkcí zadejte platný **Název aplikace**, který může obsahovat pouze písmena, číslice a pomlčky. Podtržítko ( **_** ) není povolené znak.

Názvy účtů úložiště musí mít od 3 do 24 znaků a můžou obsahovat jenom čísla a malá písmena. Název vašeho účtu úložiště musí být jedinečný v rámci Azure. 

Po vytvoření aplikace funkcí můžete jednotlivé funkce vytvářet v jednom nebo několika různých jazycích. Funkce můžete vytvářet [pomocí portálu](functions-create-first-azure-function.md#create-function), [průběžného nasazování](functions-continuous-deployment.md) nebo prostřednictvím [nahrávání přes protokol FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Plány služby

Azure Functions má tři různé plány služeb: plán spotřeby, plán Premium a vyhrazený plán (App Service). Při vytváření aplikace Function App musíte zvolit plán služby a následně ho nelze změnit. Další informace najdete v tématu [Výběr plánu hostování služby Azure Functions](functions-scale.md).

Pokud plánujete spustit funkce JavaScriptu na vyhrazeném (App Service) plánu, měli byste zvolit plán s méně jádry. Další informace najdete v tématu [Referenční informace k JavaScriptu pro službu Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

Při vytváření aplikace Function App musíte vytvořit nebo propojit s Azure Storage účet pro obecné účely, který podporuje úložiště objektů blob, front a tabulek. Služba Functions interně používá službu Storage pro operace, jako jsou správa triggerů a protokolování provádění funkcí. Některé účty úložiště nepodporují fronty a tabulky, například účty úložiště pouze objektů blob, Azure Premium Storage a účty úložiště pro obecné účely s replikací zónově redundantního úložiště. Tyto účty jsou při vytváření aplikace funkcí vyfiltrované z okna Účet úložiště.

>[!NOTE]
>Pokud použijete plán hostování Consumption, kód vaší funkce a vázané konfigurační soubory se ukládají ve službě Azure File Storage v hlavním účtu úložiště. Když odstraníte hlavní účet úložiště, tento obsah se odstraní a není možné ho obnovit.

Další informace o typech účtů úložiště najdete v tématu [Seznámení se službami Azure Storage](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Další kroky

I když Azure Portal usnadňuje vytváření a vyzkoušení funkcí, doporučujeme [místní vývoj](functions-develop-local.md). Po vytvoření aplikace Function App na portálu stále potřebujete přidat funkci. 

> [!div class="nextstepaction"]
> [Přidání funkce aktivované protokolem HTTP](functions-create-first-azure-function.md#create-function)
