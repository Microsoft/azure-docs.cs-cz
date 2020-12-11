---
title: Vytvoření aplikace funkcí na webu Azure Portal
description: Vytvořte v Azure novou aplikaci Function App z portálu.
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 8d19a269903de309bf219c2546fa70c3abe7be10
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093584"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Vytvoření aplikace funkcí na webu Azure Portal

V tomto tématu se dozvíte, jak pomocí Azure Functions vytvořit aplikaci Function App v Azure Portal. Aplikace funkcí je kontejner, který hostuje provádění jednotlivých funkcí. 

## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Po vytvoření aplikace funkcí můžete jednotlivé funkce vytvářet v jednom nebo několika různých jazycích. Funkce můžete vytvářet [pomocí portálu](functions-create-first-azure-function.md#create-function), [průběžného nasazování](functions-continuous-deployment.md) nebo prostřednictvím [nahrávání přes protokol FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Plány služby

Azure Functions má tři různé plány služeb: plán spotřeby, plán Premium a vyhrazený plán (App Service). Při vytváření aplikace Function App musíte zvolit plán služby a následně ho nelze změnit. Další informace najdete v tématu [Výběr plánu hostování služby Azure Functions](functions-scale.md).

Pokud plánujete spustit funkce JavaScriptu na vyhrazeném (App Service) plánu, měli byste zvolit plán s méně jádry. Další informace najdete v tématu [Referenční informace k JavaScriptu pro službu Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

Při vytváření aplikace Function App musíte vytvořit nebo propojit s Azure Storage účet pro obecné účely, který podporuje úložiště objektů blob, front a tabulek. Služba Functions interně používá službu Storage pro operace, jako jsou správa triggerů a protokolování provádění funkcí. Některé účty úložiště nepodporují fronty a tabulky, například účty úložiště pouze objektů blob, Azure Premium Storage a účty úložiště pro obecné účely s replikací zónově redundantního úložiště. 

Účty nepodporovaného typu jsou odfiltrovány při vytváření aplikace Function App v Azure Portal. Portál taky umožňuje použít existující účet úložiště, jenom když je tento účet ve stejné oblasti jako aplikace Function App, kterou vytváříte. Pokud z nějakého důvodu chcete porušovat osvědčené postupy výkonu účtu úložiště, který aplikace Function App používá ve stejné oblasti, je nutné vytvořit aplikaci Function App mimo portál. 

>[!NOTE]
>Pokud použijete plán hostování Consumption, kód vaší funkce a vázané konfigurační soubory se ukládají ve službě Azure File Storage v hlavním účtu úložiště. Když odstraníte hlavní účet úložiště, tento obsah se odstraní a není možné ho obnovit. 

Další informace o typech účtů úložiště najdete v tématu [Seznámení se službami Azure Storage](../storage/common/storage-introduction.md#core-storage-services). 

## <a name="next-steps"></a>Další kroky

I když Azure Portal usnadňuje vytváření a vyzkoušení funkcí, doporučujeme [místní vývoj](functions-develop-local.md). Po vytvoření aplikace Function App na portálu stále potřebujete přidat funkci. 

> [!div class="nextstepaction"]
> [Přidání funkce aktivované protokolem HTTP](functions-create-first-azure-function.md#create-function)
