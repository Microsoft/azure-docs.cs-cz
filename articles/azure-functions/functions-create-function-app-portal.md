---
title: Vytvoření aplikace funkcí z webu Azure Portal
description: Vytvořte novou aplikaci funkcí v Azure z portálu.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 086a543e75d083094d4dfa789e71afaba5da6d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368753"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Vytvoření aplikace funkcí na webu Azure Portal

Toto téma ukazuje, jak pomocí funkce Azure vytvořit aplikaci funkce na webu Azure Portal. Aplikace funkcí je kontejner, který hostuje provádění jednotlivých funkcí. 

## <a name="create-a-function-app"></a>Vytvoření Function App

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Po vytvoření aplikace funkcí můžete jednotlivé funkce vytvářet v jednom nebo několika různých jazycích. Funkce můžete vytvářet [pomocí portálu](functions-create-first-azure-function.md#create-function), [průběžného nasazování](functions-continuous-deployment.md) nebo prostřednictvím [nahrávání přes protokol FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Plány služby

Funkce Azure má tři různé plány služeb: plán spotřeby, plán Premium a vyhrazený plán (App Service). Je nutné zvolit plán služeb při vytvoření aplikace funkce a nelze jej následně změnit. Další informace najdete v tématu [Výběr plánu hostování služby Azure Functions](functions-scale.md).

Pokud plánujete spustit funkce JavaScriptu v plánu Vyhrazené (App Service), měli byste zvolit plán s menším počtem jader. Další informace najdete v tématu [Referenční informace k JavaScriptu pro službu Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

Při vytváření aplikace funkce musíte vytvořit nebo propojit účet úložiště Azure pro obecné účely, který podporuje úložiště objektů Blob, Fronta a Tabulka. Služba Functions interně používá službu Storage pro operace, jako jsou správa triggerů a protokolování provádění funkcí. Některé účty úložiště nepodporují fronty a tabulky, například účty úložiště pouze objektů blob, Azure Premium Storage a účty úložiště pro obecné účely s replikací zónově redundantního úložiště. Tyto účty jsou při vytváření aplikace funkcí vyfiltrované z okna Účet úložiště.

>[!NOTE]
>Pokud použijete plán hostování Consumption, kód vaší funkce a vázané konfigurační soubory se ukládají ve službě Azure File Storage v hlavním účtu úložiště. Když odstraníte hlavní účet úložiště, tento obsah se odstraní a není možné ho obnovit.

Další informace o typech účtů úložiště najdete v tématu [Seznámení se službami Azure Storage](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Další kroky

Zatímco portál Azure usnadňuje vytváření a vyzkoušení funkcí, doporučujeme [místní vývoj](functions-develop-local.md). Po vytvoření aplikace funkce na portálu, stále je třeba přidat funkci. 

> [!div class="nextstepaction"]
> [Přidání funkce aktivované protokolem HTTP](functions-create-first-azure-function.md#create-function)
