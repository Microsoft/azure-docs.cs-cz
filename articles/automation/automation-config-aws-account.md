---
title: Ověření runbooků Azure Automation pomocí Amazon Web Services
description: Tento článek popisuje, jak ověřit Runbooky pomocí Amazon Web Services.
keywords: ověřování aws, konfigurace aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83837178"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Ověřování runbooků s využitím Amazon Web Services

Automatizaci běžných úkolů pomocí prostředků ve službě Amazon Web Services můžete provést pomocí runbooků Automation v Azure. V AWS můžete automatizovat celou řadu úloh pomocí runbooků Automation, stejně to děláte s prostředky v Azure. Pro ověřování musíte mít předplatné Azure.

## <a name="obtain-aws-subscription-and-credentials"></a>Získání předplatného AWS a přihlašovacích údajů

K ověřování pomocí AWS musíte získat předplatné AWS a zadat sadu přihlašovacích údajů AWS pro ověření vašich runbooků běžících na Azure Automation. Konkrétní požadovaná pověření jsou přístupová klávesa AWS a tajný klíč. Viz [použití přihlašovacích údajů AWS](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).

## <a name="configure-automation-account"></a>Konfigurace účtu Automation

K ověření pomocí AWS můžete použít existující účet Automation. Alternativně můžete vyhradit účet pro Runbooky cílící na AWS prostředky. V takovém případě vytvořte nový [účet Automation](automation-create-standalone-account.md).  

## <a name="store-aws-credentials"></a>Ukládat přihlašovací údaje AWS

Přihlašovací údaje AWS musíte uložit jako prostředky v Azure Automation. Pokyny k vytvoření přístupového klíče a tajného klíče najdete v tématu [Správa přístupových klíčů pro účet AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) . Až budou klíče k dispozici, zkopírujte na bezpečné místo ID přístupového klíče a tajného klíče. Soubor klíče si můžete stáhnout a bezpečně ho uložit.

## <a name="create-credential-asset"></a>Vytvořit prostředek přihlašovacích údajů

Po vytvoření a zkopírování klíčů zabezpečení AWS musíte vytvořit Asset přihlašovacích údajů s účtem Automation. Asset vám umožní bezpečně ukládat klíče AWS a odkazovat na ně ve svých sadách Runbook. Přečtěte si téma [Vytvoření nového assetu přihlašovacích údajů pomocí Azure Portal](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal). Do uvedených polí zadejte následující AWS informace:
    
* **Název**  -  **AWScred** nebo vhodnou hodnotu podle standardů pojmenovávání
* **Uživatelské jméno** – ID vašeho přístupu
* **Heslo** – název tajného klíče 

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření runbooků pro automatizaci úloh v AWS najdete v tématu [nasazení virtuálního počítače s Amazon Web Services pomocí sady Runbook](automation-scenario-aws-deployment.md).