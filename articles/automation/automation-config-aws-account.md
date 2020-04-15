---
title: Ověření runbooků Azure Automation pomocí amazonských webových služeb
description: Tento článek popisuje postup vytvoření a ověření přihlašovacích údajů Amazon Web Services (AWS) pro runbooky ve službě Azure Automation, které spravují prostředky AWS.
keywords: ověřování aws, konfigurace aws
services: automation
ms.subservice: process-automation
ms.date: 04/17/2018
ms.topic: conceptual
ms.openlocfilehash: 02ff9cedfbeaa36b2fafc84637ea3141b223a064
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310484"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>Ověření runbooků Azure Automation pomocí amazonských webových služeb

Automatizaci běžných úkolů pomocí prostředků ve službě Amazon Web Services můžete provést pomocí runbooků Automation v Azure. V AWS můžete automatizovat celou řadu úloh pomocí runbooků Automation, stejně to děláte s prostředky v Azure. Potřebujete jen dvě věci:

* Předplatné AWS a sadu přihlašovacích údajů. Zejména přístupový klíč a tajný klíč AWS. Další informace najdete v článku [Použití přihlašovacích údajů AWS](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Předplatné Azure a účet Automation.

Abyste mohli ověřovat pomocí AWS, musíte zadat sadu přihlašovacích údajů AWS a ověřit svoje runbooky spuštěné ve službě Azure Automation. Pokud již máte vytvořený účet Automation a chcete jej použít k ověření pomocí AWS, můžete postupovat podle kroků v následující části: Pokud chcete vyhradit účet pro runbooky zaměřené na prostředky AWS, měli byste nejprve vytvořit nový [účet Automation](automation-offering-get-started.md) (přeskočte možnost vytvořit instanční objekt) a použít následující kroky:

## <a name="configure-automation-account"></a>Konfigurace účtu Automation

Pokud má Azure Automation komunikovat s AWS, musíte nejdřív načíst přihlašovací údaje AWS a uložit je jako assety ve službě Azure Automation. Proveďte následující kroky, které jsou popsány v dokumentu AWS [Správa přístupových klíčů k vašemu účtu AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html), vytvořte přístupový klíč a zkopírujte **Access Key ID** (ID přístupového klíče) a **Secret Access Key** (Tajný přístupový klíč) (případně si můžete soubor s klíčem stáhnout a uložit na bezpečné místo).

Po vytvoření a zkopírování bezpečnostních klíčů AWS vytvořte pomocí účtu Azure Automation asset přihlašovacích údajů, bezpečně klíče uložte a odkazujte na ně ve svých runboocích. Postupujte podle kroků v části: **Chcete-li vytvořit nové přihlašovací údaje** v článku Přihlašovací údaje v azure [automatizace](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) a zadejte následující informace:

1. Do pole **Název** zadejte **AWScred** nebo odpovídající hodnotu v souladu se svými standardy pro vytváření názvů.
2. Do pole **Uživatelské jméno** zadejte **své Přístupové ID** a **tajný přístupový klíč do** pole **Heslo** a **Potvrdit heslo.**

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření runbooků pro automatizaci úloh v AWS najdete v článku [Automatizace nasazení virtuálního počítače ve službě Amazon Web Services](automation-scenario-aws-deployment.md).
